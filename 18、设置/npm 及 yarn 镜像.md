npm config get registry 查看当前使用的镜像源
npm config set registry http://registry.npmmirror.com 使用这个
npm config set registry http://npmmirror.com 或者使用这个！
npm config set registry https://registry.npm.taobao.org 淘宝镜像（已经过期）

npm config delete registry 删除镜像（自动恢复到国外镜像）

### 注意

原 taobao.org 域名过期，使用 npmmirror.com 替代
http://npm.taobao.org => http://npmmirror.com
http://registry.npm.taobao.org => http://registry.npmmirror.com

### yarn 镜像

yarn 可以使用与 npm 相同的镜像，镜像操作的相关命令与 npm 的几乎完全相同。

yarn config get registry 查看当前使用的镜像源
yarn config set registry http://registry.npmmirror.com 使用这个
npm config delete registry
