### 图片转MP4： 时长25秒，帧率30
```sh
ffmpeg -ss 0 -t 25 -f lavfi -i color=c=0x000000:s=640x480:r=30 -i frame.png -filter_complex "[1:v]scale=640:480[v1];[0:v][v1]overlay=0:0[outv]"-map [outv] -c:v libx264 out1.mp4 -y
```

### 合并视频
```sh
ffmpeg -i "concat:out.mp4|out1.mp4" -c copy merge.mp4


ffmpeg -i int1.mp4 -i int2.mp4 -filter_complex '[0:0] [0:1] [1:0] [1:1] concat=n=2:v=1:a=1 [v] [a]' -map '[v]' -map '[a]' merge.mp4
```