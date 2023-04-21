---
title: ffmpeg编辑视频
date: 2021-02-22 09:41:15
tags: [video]
---

1. 压缩视频
```bash
ffmpeg -i input.mp4 -vcodec libx265 -crf 28 output.mp4
ffmpeg -i input.mp4 -vcodec libx265 -crf 28 -ar 8000 output.mp4
```
使用h.265编码，crf会影响视频质量，crf越高，压缩率越高，损失越严重．ar是音频的重采样，8000Hz．

2. 抽取音频
```bash
ffmpeg -i input.mp4 -vn -y -acodec copy output.aac
```

3. 提取视频
```bash
ffmpeg -i input.mkv -vcodec copy –an output.mp4
```

4. 音视频合成
```bash
ffmpeg -i video.avi -i audio.mp3 -vcodec copy -acodec copy output.avi
```

5. 查看音视频信息
```bash
ffmpeg -i input.mp4
```

6. 剪切
用-ss和-t选项，从第30秒开始，向后截取10秒(相对时间)：
```bash
ffmpeg -i input.wmv -ss 00:00:30.0 -c copy -t 00:00:10.0 output.wmv
ffmpeg -i input.wmv -ss 30 -c copy -t 10 output.wmv
```
也可以用-ss和-to选项，从第9分30秒截取到第9分40秒(绝对时间)：
```bash
ffmpeg -i input.wmv -ss 9:30 -c copy -to 9:40 output.wmv
```
注意，ffmpeg为了加速，会使用关键帧技术，所以有时剪切出来的结果在起止时间上未必准确。通常来说，把-ss选项放在-i之前，会使用关键帧技术；把-ss选项放在-i之后，则不使用关键帧技术：

```bash
ffmpeg -ss 00:01:00 -i video.mp4 -to 00:02:00 -c copy cut.mp4
```
反正都试一下呗
