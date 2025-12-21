---
author:
  - ChatGPT
created: 2025-12-20
source: https://chatgpt.com/c/69460ffe-514c-8323-8226-02b0be31a5f7
---

### 测速并生成配置文件

```sh
#!/usr/bin/env bash
# docker-mirror-gen.sh
# Test Docker registry mirrors and generate Docker / Podman configs

set -euo pipefail

mirrors=(
  "docker.m.daocloud.io"
  "docker.1ms.run"
  "mirror.ccs.tencentyun.com"
  "docker.xuanyuan.me"
  "registry-1.docker.io"
)

IMAGE="library/alpine"
BLOB="sha256:f6b4fb9446345fcad2db26eac181fef6c0a919c8a4fcccd3bea5deb7f6dff67e"

TMP_RESULT=$(mktemp)
trap 'rm -f "$TMP_RESULT"' EXIT

echo "🔍 开始测试镜像站点…"

for mirror in "${mirrors[@]}"; do
  echo -e "\n== 测试对象: $mirror =="

  headers=$(mktemp)
  http_code=$(curl -s -o /dev/null -D "$headers" -w "%{http_code}" \
    "https://$mirror/v2/" || echo "000")

  echo "状态: $http_code"

  if [[ "$http_code" != "401" && "$http_code" != "200" ]]; then
    echo "跳过：异常状态"
    rm -f "$headers"
    continue
  fi

  auth_header=$(grep -i '^WWW-Authenticate:' "$headers" || true)
  rm -f "$headers"

  [[ -z "$auth_header" ]] && echo "跳过：无认证头" && continue

  realm=$(echo "$auth_header" | sed -n 's/.*realm="\([^"]*\)".*/\1/p')
  service=$(echo "$auth_header" | sed -n 's/.*service="\([^"]*\)".*/\1/p')

  [[ -z "$realm" || -z "$service" ]] && echo "跳过：解析失败" && continue

  token=$(curl -s "$realm?service=$service&scope=repository:$IMAGE:pull" \
    | jq -r '.token // empty')

  [[ -z "$token" ]] && echo "跳过：token 获取失败" && continue

  speed=$(curl -L -o /dev/null \
    -H "Authorization: Bearer $token" \
    -w "%{speed_download}" \
    "https://$mirror/v2/$IMAGE/blobs/$BLOB")

  echo "测速成功：$speed bytes/s"
  echo "$speed $mirror" >> "$TMP_RESULT"
done

echo -e "\n📊 可用镜像测速结果："
sort -nr "$TMP_RESULT"

# ========================
# 生成 Podman 配置
# ========================
echo -e "\n📝 生成 Podman registries.conf"

cat > registries.conf <<EOF
unqualified-search-registries = ["docker.io"]

[[registry]]
prefix = "docker.io"
location = "registry-1.docker.io"

EOF

sort -nr "$TMP_RESULT" | while read -r speed mirror; do
cat >> registries.conf <<EOF
[[registry.mirror]]
location = "$mirror"
EOF
done

echo "✅ 已生成：$(pwd)/registries.conf"

# ========================
# 生成 Docker 配置
# ========================
echo -e "\n📝 生成 Docker daemon.json"

jq -n --argjson mirrors "$(sort -nr "$TMP_RESULT" | awk '{print $2}' | jq -R . | jq -s .)" '
{
  "registry-mirrors": $mirrors
}
' > daemon.json

echo "✅ 已生成：$(pwd)/daemon.json"
```

### 生成的配置文件怎么用？

#### Podman / Buildah / Skopeo

系统级或者用户级，二选一

```sh
# 系统级
sudo cp registries.conf /etc/containers/registries.conf

# 用户级
mkdir -p ~/.config/containers
cp registries.conf ~/.config/containers/registries.conf
```

验证

```sh
podman pull alpine
```

#### Docker

放置位置

```sh
sudo cp daemon.json /etc/docker/daemon.json
sudo systemctl restart docker
```

macOS Docker Desktop：

```json
Settings → Docker Engine → 粘贴 daemon.json 内容 → Apply
```

验证

```sh
docker info | grep -A5 Registry
```

