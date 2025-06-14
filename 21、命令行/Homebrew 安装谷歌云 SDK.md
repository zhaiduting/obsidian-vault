### 安装命令

```shell
brew install --cask google-cloud-sdk
```

以上命令自动安装了以下组件

| 组件              | 命令         | 用途                                    |
| ----------------- | ------------ | --------------------------------------- |
| BigQuery CLI      | `bq`         | 管理 BigQuery 数据仓库                  |
| Cloud Storage CLI | `gsutil`     | 上传/下载/管理 Cloud Storage 文件       |
| Cloud CLI 核心    | `gcloud`     | 管理 GCP 所有资源（项目、计算、IAM 等） |
| CRC32C Hash Tool  | （自动使用） | 文件传输完整性校验（用于 `gsutil` 等）  |

### 注意

谷歌云官方文档给出 [gcloud CLI](https://cloud.google.com/sdk/docs/install?hl=zh-cn) 的详细安装步骤，这里已经无需单独安装了，因为 Homebrew 安装的 google-cloud-sdk 已经包括了 Cloud CLI 组件（上表已列出）。
### 初始化
```shell
gcloud init
```