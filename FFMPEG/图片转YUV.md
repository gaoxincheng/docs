## 使用FFmpeg

- 图片、YUV互转
```sh
 # 图片转yuv
 ffmpeg -i 1.jpg -s 640x480 -pix_fmt yuv420p 1-640x480.yuv 

 # 从MP4中取出一帧图片
 ffmpeg -i test.mp4 -y -f  image2  -ss 60 -vframes 1  test1.jpg
 # yuv转图片
 ffmpeg -pix_fmt yuv420p -s 640x480 -i 1-640x480.yuv 1-copy.jpg
 # yuvj444p转yuv420p
 ffmpeg -pix_fmt yuvj444p -s 320x320 -i 320.yuv -pix_fmt yuv420p 320-420p.yuv
```

- 带色彩空间的转换

```sh
enum AVColorRange {
 AVCOL_RANGE_UNSPECIFIED = 0,
 AVCOL_RANGE_MPEG        = 1, ///< the normal 219*2^(n-8) "MPEG" YUV ranges
 AVCOL_RANGE_JPEG        = 2, ///< the normal     2^n-1   "JPEG" YUV ranges
 AVCOL_RANGE_NB,              ///< Not part of ABI
};

ffmpeg -i default320.jpg  -pix_fmt yuv420p -color_range 1  320-yuv420p.yuv

ffmpeg -i default160.jpg  -pix_fmt yuv420p -color_range 1  160-yuv420p.yuv
```