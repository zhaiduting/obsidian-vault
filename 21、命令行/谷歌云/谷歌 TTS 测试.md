本文只讲测试，不讲谷歌云 TTS API 的申请、设置等前期步骤。

### 准备需要合成音频的文本

直接用终端生成一个 request.json 文件，其中包含需要合成音频的字符串 Hello

```bash
echo '{
  "input": {
    "text": "Hello!"
  },
  "voice": {
    "languageCode": "en-gb",
    "name": "en-GB-Standard-A",
    "ssmlGender": "FEMALE"
  },
  "audioConfig": {
    "audioEncoding": "MP3"
  }
}' > request.json
```

### 发送请求

然后将此文件包含的 json 数据发送给谷歌，尝试调用谷歌的 TTS 服务，看看有什么错误

```bash
curl -X POST \
     -H "Authorization: Bearer $(gcloud auth print-access-token)" \
     -H "x-goog-user-project: zdt-tts-try" \
     -H "Content-Type: application/json; charset=utf-8" \
     -d @request.json \
     "https://texttospeech.googleapis.com/v1/text:synthesize"
```

得到如下错误
**ERROR:** `(gcloud.auth.print-access-token) You do not currently have an active account selected.`
这是一个未登录的问题，敲入以下命令解决这个问题

```bash
gcloud auth login
```

以上命令会弹出一个浏览器，在浏览器中点击谷歌账号完成登录操作。之后终端会显示
`You are now logged...`
这表示登录成功了，此时再次尝试之前的 curl 命令就会返回正确响应了！
![curl_GCP_TTS](https://lib.zhaiduting.work.gd/uPic/curl_GCP_TTS.png)

### 拿到音频

如果 curl 命令成功响应，将会包含一个键名为 audioContent 字符串，这就是音频文件的 base64 编码。只需借助工具，将此编码转换为 mp3 文件就可以了。具体步骤就此略过，因为更简单的方法将在 [单文件转音频](./单文件转音频.md) 一文中讲解。

### 官方文档

谷歌中文文档链接 https://cloud.google.com/text-to-speech/docs/create-audio-text-command-line?hl=zh-cn
