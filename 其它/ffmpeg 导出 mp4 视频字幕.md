首先需要安装 ffmpeg，命令如下

```sh
brew install ffmpeg
```

检测 mp4 视频的字幕类型

```sh
ffmpeg -i FindAnyFile.mp4
```

### 🎯 检测结果：

✅ `FindAnyFile.mp4` 文件 **包含一个软字幕轨道**（非烧录在画面中）。

---

### 📊 详细分析：

#### 1️⃣ 文件基本信息

```
Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'FindAnyFile.mp4':
  Duration: 00:27:06.60, bitrate: 311 kb/s
```

- 视频时长：27 分 06 秒
- 格式：MP4 容器（isom）

---

#### 2️⃣ 视频流

```
Stream #0:0(und): Video: h264 (High), 1920x1080, 15 fps
```

- 视频编码：H.264
- 分辨率：1080p
- 帧率：15fps

---

#### 3️⃣ 音频流

```
Stream #0:1(und): Audio: aac (LC), 48000 Hz, stereo
```

- 音频格式：AAC
- 双声道

---

#### 4️⃣ 字幕流（重点）

```
Stream #0:2(eng): Subtitle: mov_text (tx3g / 0x67337874), 1920x60, 0 kb/s (default)
```

✅ 这一行表明：

- **字幕存在**，是一个独立轨道。
- 编码格式：`mov_text`（MP4 内嵌文本字幕格式）
- 语言：`eng`（英文）
- 轨道编号：`0:2`

👉 这类字幕可以直接用 ffmpeg 提取为 `.srt` 文件。

---

#### 5️⃣ 其他数据流（可忽略）

```
Stream #0:3 ... bin_data (gpmd)
Stream #0:4 ... bin_data (text)
```

这些是元数据或嵌入信息，不是字幕内容。

---

### 🧾 导出命令：

你可以直接执行以下命令导出字幕：

```sh
ffmpeg -i FindAnyFile.mp4 -map 0:s:0 FindAnyFile.srt
```

⚙️ 参数解释：

- `-map 0:s:0` → 选择第 0 个文件中的第一个字幕轨（`Stream #0:2`）。
- `FindAnyFile.srt` → 输出字幕文件。

执行完会在当前目录生成：

`FindAnyFile.srt`

---

是否希望我帮你写一个更智能的命令（自动检测并提取全部字幕轨道，比如有多语言时生成 `FindAnyFile.en.srt`、`FindAnyFile.zh.srt`）？
