![K8s API](https://lib.zhaiduting.work.gd/uPic/K8s%20API.png)

### 推荐：使用单个聚合的 Swagger 文件

K8s 提供了一个聚合后的文件，包含所有核心 API。对于 Apifox 2.8.1，导入这个文件最稳妥：

**最新版 (master branch) 下载地址：**

> `https://raw.githubusercontent.com/kubernetes/kubernetes/master/api/openapi-spec/swagger.json`

### 导入步骤

1. 在 Apifox 中点击 **“项目设置” -> “导入数据”**。
2. 点击 **“OpenAPI/Swagger”**
3. 选择 **“URL 导入”**。
4. 将上面的 `raw.githubusercontent.com` 链接粘贴进去。
5. 点击 **“提交”**。
6. 稍等片刻弹出预览界面，点选“创建新模块”，模块名称保持默认，最后点击确定导入。
