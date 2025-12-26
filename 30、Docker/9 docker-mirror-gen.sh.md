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
  # 以下为个人站点/小型镜像站（稳定性可能较低）
  "docker.1ms.run"              # 阿里云镜像加速器（旧版，个人维护版本）
  "dytt.online"                 # 第三方镜像站（名称取自电影天堂dytt）
  "docker-0.unsee.tech"         # 个人开发者镜像站
  "lispy.org"                   # Lisp社区相关镜像站
  "docker.xiaogenban1993.com"   # 个人开发者"小跟班"的镜像站
  "666860.xyz"                  # 数字域名镜像站
  "demo.52013120.xyz"           # 个人演示站点镜像源
  "proxy.vvvv.ee"               # 个人代理服务镜像站

  # 以下为企业级/社区镜像服务（相对稳定）
  "docker.1panel.live"          # 1Panel面板官方Docker镜像源
  "docker.m.daocloud.io"        # DaoCloud道客加速器（企业级）
  "registry.cyou"               # 通用镜像注册服务
  "mirror.ccs.tencentyun.com"   # 腾讯云容器镜像服务（企业级）

  # 以下为官方站点
  "registry-1.docker.io"        # Docker Hub官方主镜像仓库
)

IMAGE="library/alpine"
BLOB="sha256:f6b4fb9446345fcad2db26eac181fef6c0a919c8a4fcccd3bea5deb7f6dff67e"

TMP_RESULT="$(mktemp)"
trap 'rm -f "$TMP_RESULT"' EXIT

echo "🔍 开始测试镜像站点…"

for mirror in "${mirrors[@]}"; do
  echo -e "\n== 测试对象: $mirror =="

  # 1. 基础连通性测试，允许重定向
  headers="$(mktemp)"
  http_code="$(
    curl -s -o /dev/null -D "$headers" -w "%{http_code}" \
      --max-time 10 \
      "https://$mirror/v2/" || echo "000"
  )"

  echo "状态: $http_code"

  # 接受 200, 401, 302 (重定向)
  if [[ ! "$http_code" =~ ^(200|401|302)$ ]]; then
    echo "跳过：异常状态 ($http_code)"
    rm -f "$headers"
    continue
  fi

  # 2. 检查认证头，先检查重定向后的最终地址
  final_url=""
  if [[ "$http_code" == "302" ]]; then
    # 提取重定向地址
    location="$(grep -i '^Location:' "$headers" | head -1 | tr -d '\r\n' | sed 's/Location: //i')"
    if [[ -n "$location" ]]; then
      final_url="$location"
      echo "重定向到: $final_url"
    fi
  fi

  # 获取认证头
  auth_header="$(grep -i '^WWW-Authenticate:' "$headers" || true)"
  rm -f "$headers"

  # 3. 如果当前地址没有认证头，可能是需要先访问重定向地址
  if [[ -z "$auth_header" && -n "$final_url" ]]; then
    headers2="$(mktemp)"
    # 尝试访问重定向地址
    http_code2="$(
      curl -s -o /dev/null -D "$headers2" -w "%{http_code}" \
        --max-time 10 \
        "$final_url" || echo "000"
    )"

    if [[ "$http_code2" =~ ^(200|401)$ ]]; then
      auth_header="$(grep -i '^WWW-Authenticate:' "$headers2" || true)"
    fi
    rm -f "$headers2"
  fi

  if [[ -z "$auth_header" ]]; then
    echo "跳过：无认证头，可能是公开仓库或配置错误"
    continue
  fi

  # 4. 解析认证信息
  realm="$(echo "$auth_header" | sed -n 's/.*realm="\([^"]*\)".*/\1/p')"
  service="$(echo "$auth_header" | sed -n 's/.*service="\([^"]*\)".*/\1/p')"

  if [[ -z "$realm" || -z "$service" ]]; then
    echo "跳过：认证信息解析失败"
    continue
  fi

  echo "认证服务: $service"

  # 5. 获取 token，增加错误处理
  token_response=""
  token=""

  # 尝试获取 token，增加超时和错误处理
  token_response="$(curl -s --max-time 10 "$realm?service=$service&scope=repository:$IMAGE:pull" 2>/dev/null || echo "")"

  if [[ -n "$token_response" ]]; then
    # 检查是否是有效的 JSON
    if echo "$token_response" | jq -e . >/dev/null 2>&1; then
      token="$(echo "$token_response" | jq -r '.token // empty')"
    else
      echo "跳过：认证服务器返回非 JSON 数据"
      echo "响应: ${token_response:0:100}..."
      continue
    fi
  fi

  if [[ -z "$token" ]]; then
    echo "跳过：token 获取失败"
    continue
  fi

  # 6. Blob 下载测速
  speed=""
  # 确定要使用的下载地址
  download_url=""
  if [[ -n "$final_url" ]]; then
    # 如果之前有重定向，使用重定向的域名
    domain="$(echo "$final_url" | sed 's|^https://||; s|/.*||')"
    download_url="https://$domain/v2/$IMAGE/blobs/$BLOB"
  else
    download_url="https://$mirror/v2/$IMAGE/blobs/$BLOB"
  fi

  echo "下载地址: $download_url"

  # 尝试下载，增加超时和重试
  for i in {1..2}; do
    speed="$(
      curl -L -o /dev/null \
        -H "Authorization: Bearer $token" \
        -w "%{speed_download}" \
        --max-time 30 \
        --retry 1 \
        "$download_url" \
        2>/dev/null || echo ""
    )"

    if [[ -n "$speed" && "$speed" != "0" ]]; then
      break
    elif [[ $i -eq 1 ]]; then
      echo "  第 $i 次下载失败，重试..."
    fi
  done

  if [[ -z "$speed" || "$speed" == "0" ]]; then
    echo "跳过：下载失败或速度为 0"
    continue
  fi

  # 转换速度为可读格式
  speed_readable=""
  if (( speed > 1048576 )); then
    speed_readable="$(printf "%.2f MB/s" "$(echo "scale=2; $speed / 1048576" | bc)")"
  elif (( speed > 1024 )); then
    speed_readable="$(printf "%.2f KB/s" "$(echo "scale=2; $speed / 1024" | bc)")"
  else
    speed_readable="$(printf "%.0f B/s" "$speed")"
  fi

  echo "✅ 测速成功：$speed_readable"
  echo "$speed $mirror" >> "$TMP_RESULT"
done

if [[ ! -s "$TMP_RESULT" ]]; then
  echo -e "\n❌ 没有任何可用镜像，未生成配置文件"
  exit 1
fi

echo -e "\n📊 可用镜像测速结果（按速度降序）："
echo "======================================"
sort -nr "$TMP_RESULT" | while read -r speed mirror; do
  speed_readable=""
  if (( speed > 1048576 )); then
    speed_readable="$(printf "%.2f MB/s" "$(echo "scale=2; $speed / 1048576" | bc)")"
  elif (( speed > 1024 )); then
    speed_readable="$(printf "%.2f KB/s" "$(echo "scale=2; $speed / 1024" | bc)")"
  else
    speed_readable="$(printf "%.0f B/s" "$speed")"
  fi
  printf "%-35s %s\n" "$mirror" "$speed_readable"
done
echo "======================================"

# ========================
# 生成 Podman 配置
# ========================
echo -e "\n📝 生成 Podman registries.conf"

cat > registries.conf <<EOF
# 自动生成的镜像源配置
# 生成时间: $(date)
# 按测速结果排序

unqualified-search-registries = ["docker.io"]

[[registry]]
prefix = "docker.io"
location = "docker.io"

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

mirror_list=""
while read -r speed mirror; do
  mirror_list="${mirror_list}\"$mirror\","
done < <(sort -nr "$TMP_RESULT")

# 移除最后一个逗号
mirror_list="${mirror_list%,}"

cat > daemon.json <<EOF
{
  "registry-mirrors": [${mirror_list}]
}
EOF

echo "✅ 已生成：$(pwd)/daemon.json"

# 使用说明
echo -e "\n💡 使用说明："
echo "1. Docker: 将 daemon.json 复制到 /etc/docker/ 并重启 docker 服务"
echo "2. Podman: 将 registries.conf 复制到 /etc/containers/registries.conf.d/ 或 ~/.config/containers/"
echo -e "\n🏁 脚本执行完成"

```

### 生成的配置文件怎么用？

#### macOS 配置 Podman

已在 [Mac 配置 podman](30、Docker/9%20Mac%20配置%20podman.md) 一文中详细记录过了，此处不再赘述。

#### CentOS 配置 Podman

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

#### CentOS 配置 Docker 

放置位置

```sh
sudo cp daemon.json /etc/docker/daemon.json
sudo systemctl restart docker
```

#### macOS 配置 Docker Desktop

```json
Settings → Docker Engine → 粘贴 daemon.json 内容 → Apply
```

验证

```sh
docker info | grep -A5 Registry
```
