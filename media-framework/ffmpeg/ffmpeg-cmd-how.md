# ffmpeg命令大全

## 1 ffprobe多媒体基本信息

```shell
$ ffprobe.exe -show_packets -select_streams v:0 .\H265_3840x2160_25F_35M_8B_AAC_2016.ts
$ ffprobe.exe -show_frames -select_streams v:0 .\H265_3840x2160_25F_35M_8B_AAC_2016.ts
$ ffprobe.exe -show_packets -select_streams v:0 -of xml .\H265_3840x2160_25F_35M_8B_AAC_2016.ts
$ ffprobe.exe -show_packets -select_streams v:0 -of csv .\H265_3840x2160_25F_35M_8B_AAC_2016.ts
$ ffprobe.exe -show_format video_name.ts
$ ffprobe.exe -hide_banner video_name.ts
$ ffprobe.exe -show_streams video_name.ts
```

## 2 ffmepg基本命令

主要参数： -i 设定输入流 -f 设定输出格式 -ss 开始时间 视频参数： -b 设定视频流量，默认为200Kbit/s -r 设定帧速率，默认为25 -s 设定画面的宽与高 -aspect 设定画面的比例 -vn 不处理视频 -vcodec 设定视频编解码器，未设定时则使用与输入流相同的编解码器 音频参数： -ar 设定采样率 -ac 设定声音的Channel数 -acodec 设定声音编解码器，未设定时则使用与输入流相同的编解码器 -an 不处理音频

$  ffmpeg -f image2 -stream_loop 120 -i ../test.jpg -vcodec libx264 -b:v 200k -r 24 -s 1024x768 -y multi_jpg.mp4​

### 2.1 ffmpeg解封装并提取基本流(es流)

```shell
$ ffmpeg -i sh_moment.mp4 -vcodec copy -an sh_moment.mp4
$ ffmpeg -i sh_moment.mp4 -vcodec copy -an -f m4v sh_moment.h264  (FAIL)
$ ffmpeg -i sh_moment.mp4 -vcodec copy -an -f rawvideo sh_moment.h264  (FAIL)
$ ffmpeg -i sh_moment.mp4 -vcodec copy -f rawvideo -vbsf h264_mp4toannexb sh_moment.h264 (OK)
$ ffmpeg -i sh_moment.mp4 -vcodec copy -f h264 -vbsf h264_mp4toannexb sh_moment.h264  (OK)
$ ffmpeg -i sh_moment.mp4 -vcodec copy -f m4v -vbsf h264_mp4toannexb sh_moment.h264  (OK)
$ ffmpeg -i sh_moment.mp4 -vcodec copy -an sh_moment.ts  (OK)
B1.2 分离音频流(-vn 去掉视频)
$ ffmpeg -i test.mp4 -acodec copy -vn output.aac
$ ffmpeg -i test.mp4 -acodec aac -vn output.aac
```

### 2.2 ffmpeg转码并换封装格式

```shell
# adjust codec or muxer format
$ ffmpeg -i test.mp4 -vcodec h264 -s 352*278 -an -f m4v test.264              //转码为码流原始文件
$ ffmpeg -i test.mp4 -vcodec h264 -bf 0 -g 25 -s 352*278 -an -f m4v test.264  //转码为码流原始文件
$ ffmpeg -i test.avi -vcodec mpeg4 -vtag xvid -qsame test_xvid.avi            //转码为封装文件
$ ffmpeg -i money.mp4 -c:v copy(hevc) -f mpegts money.ts
$ ffmpeg -i money.mp4 -vcodec mjpeg  mjepg_money.mp4
$ ffmpeg -i money.h265 -vcodec h264 -vf "transpose=1" money.mp4
$ ffmpeg -i input.avi output.mp4
$ ffmpeg -i input.mp4 output.ts
```

### 2.3 ffmpeg视频剪切/截断

```shell
相关参数说明: -r 提取图像的频率，-ss 开始时间，-t 持续时间
$ ffmpeg -i test.avi -r 1 -f image2 image-%3d.jpeg        //提取图片
$ ffmpeg -ss 0:1:30 -t 0:0:20 -i input.avi -vcodec copy -acodec copy output.avi    //剪切视频
$ ffmpeg -i H264_1080P_6M_GEM_Moment.mp4 -c:v copy  -t 00:01:30 H264_1080P_6M_90s_GEM_Moment.mp4
$ ffmpeg  -i  xxxxxx.ts   -c:v copy xxxxx_patch.ts
$ ffmpeg  -i  xxxxxx.ts   -c:v copy -s 1080x1920 xxxxx_patch.ts
$ ffmpeg  -i  xxxxxx.ts   -c:v copy  -t 00:01:00  xxxxx_patch.ts
```

### 2.4  ffmpeg调整视频帧率(统一帧率)

```shell
# adjust FPS
$ ffmpeg -i input.mov -sameq -r 60 -y output.mov
$ ffmpeg -i input.mov -qscale 0 -r 60 -y output.mov
```

### 2.5  ffmpeg视频旋转

```shell
# ratote local video
$ ffmpeg -i success.mp4 -metadata:s:v rotate="90" -codec copy output_success.mp4
$ ffmpeg -i demo.mp4 -vf "transpose=1" out.mp4
```

### 2.6  ffmpeg录制网络流

```shell
# record online stream
$ ffmpeg -i rtsp://192.168.3.205:5555/test -vcodec copy out.avi # 
```

### 2.7 ffmpeg的YUV相关操作(YUV生成，对比和图像质量评估)

```shell
$ ffmpeg -pix_fmts    # 查看ffmpeg支持的pix-format
$ ffplay -f rawvideo -video_size 1920x1080 input.yuv  # 播放YUV的RawVideo
B7.3 假设视频输入为H264基本流为: china_416x739.h264, ffmpeg解码器输出为
china_416x738_nv12_ffmepg.yuv，对比解码器on2的输出为:china_416x752_nv12_on2.yuv。
$ ffmpeg -i china_416x738.h264 -pix_fmt nv12 -vframes 20 china_416x738_nv12_ffmepg.yuv
$ ffmpeg -pix_fmt nv12 -s 416x738 -i china_416x752_nv12_on2.yuv -pix_fmt nv12 china_416x738_nv12_on2.yuv
```

## 3 ffmepg命令--网络用法

### 3.1 制作m3u8在线测试码流

```shell
# C1.1 使用ffmpeg分离码流中音频和视频(次要步骤)
$ ffmpeg -i sh_moment.mp4 -vcodec copy -an -f h264 sh_moment.h264
$ ffmpeg -i sh-moment.mp4 -acodec copy -vn sh_moment.aac
# C1.2 使用ffmpeg生成hls切片和m3u8表
$ ffmpeg -i bbc_china.mkv -codec:v copy -an -bsf:v h264_mp4toannexb -map 0 -f segment -segment_time 10 -segment_format mpegts -segment_list bbc_china/bbc_china_video_only.m3u8 bbc_china/bbc_china_video_%03d.ts
$ ffmpeg -i bbc_china.mkv -codec:a aac -vn -map 1 -f segment -segment_time 10 -segment_format mpegts -segment_list bbc_china/bbc_china_audio_aac.m3u8 bbc_china/bbc_china_audio_aac_%03d.ts    (mpegts容器)
$ ffmpeg -i bbc_china.mkv -codec:a aac -vn -map 1 -f segment -segment_time 10 -segment_format adts -segment_list bbc_china/bbc_china_audio_aac.m3u8 bbc_china/bbc_china_audio_aac_%03d.ts   (adts 容器)
# C1.3 用HTTP服务器部署一下，M3U8表和切片即可
$ sync; stop media; start media;
$ am start -n com.himedia.android.videoplayer/com.himedia.android.videoplayer.activity.VideoActivity  /data/goertek.h264 http://192.168.0.104/bbc_china.m3u8
```

### 3.2 ffmpeg其他网络操作

```shell
# C2.x 将文件当做直播送至live
$ ffmpeg -re -i localFile.mp4 -c copy -f flv rtmp://server/live/streamName
# C2.x  将直播媒体保存至本地文件
$ ffmpeg -i rtmp://server/live/streamName -c copy dump.flv
# C2.x 将其中一个直播流，视频改用h264压缩，音频不变，送至另外一个直播服务流
$ ffmpeg -i rtmp://server/live/originalStream -c:a copy -c:v libx264 -vpre slow -f flv rtmp://server/live/h264Stream
# C2.x 将其中一个直播流，视频改用h264压缩，音频改用faac压缩，送至另外一个直播服务流
$ ffmpeg -i rtmp://server/live/originalStream -c:a libfaac -ar 44100 -ab 48k -c:v libx264 -vpre slow -vpre baseline -f flv rtmp://server/live/h264Stream
# C2.x 将其中一个直播流，视频不变，音频改用faac压缩，送至另外一个直播服务流
$ ffmpeg -i rtmp://server/live/originalStream -acodec libfaac -ar 44100 -ab 48k -vcodec copy -f flv rtmp://server/live/h264_AAC_Stream
# C2.x 将一个高清流，复制为几个不同视频清晰度的流重新发布，其中音频不变
$ ffmpeg -re -i rtmp://server/live/high_FMLE_stream -acodec copy -vcodec x264lib -s 640×360 -b 500k -vpre medium -vpre baseline rtmp://server/live/baseline_500k -acodec copy -vcodec x264lib -s 480×272 -b 300k -vpre medium -vpre baseline rtmp://server/live/baseline_300k -acodec copy -vcodec x264lib -s 320×200 -b 150k -vpre medium -vpre baseline rtmp://server/live/baseline_150k -acodec libfaac -vn -ab 48k rtmp://server/live/audio_only_AAC_48k
# C2.x 功能一样，只是采用-x264opts选项
$ ffmpeg -re -i rtmp://server/live/high_FMLE_stream -c:a copy -c:v x264lib -s 640×360 -x264opts bitrate=500:profile=baseline:preset=slow rtmp://server/live/baseline_500k -c:a copy -c:v x264lib -s 480×272 -x264opts bitrate=300:profile=baseline:preset=slow rtmp://server/live/baseline_300k -c:a copy -c:v x264lib -s 320×200 -x264opts bitrate=150:profile=baseline:preset=slow rtmp://server/live/baseline_150k -c:a libfaac -vn -b:a 48k rtmp://server/live/audio_only_AAC_48k
```

## 4 ffmepg命令--设备用法(Camera, Screen)

D1 ffmpeg采集摄像头图像  
相关参数说明: -r 视频帧率，-t 持续时间，-f 强制格式

### 4.1 DDSHOW采集摄像头

```shell
# demo for dshow
$ ffmpeg -r 25 -f dshow -s 640×480 -i video=”video source name”:audio=”audio source name” -vcodec libx264 -b 600k -vpre slow -acodec libfaac -ab 128k -f flv rtmp://server/application/stream_name
```

### 4.2 采集usb摄像头视频命令

```shell
$ ffmpeg -t 20 -f vfwcap -i 0 -r 8 -f mp4 camera_r8.mp4
$ ffplay -t 20 -f   show -i 0 -r 8 -f mp4 camera_r8.mp4
具体说明如下：我们采集10秒，采集设备为vfwcap类型设备，第0个vfwcap采集设备（如果系统有多个vfw的视频采集设备，可以通过-i num来选择），每秒8帧，输出方式为文件，格式为mp4。
```

### 4.3 采集屏幕/桌面图像

相关参数说明: -r 视频帧率，-t 持续时间，-f 强制格式

```shell
# 最简单的抓屏
$ ffmpeg -f gdigrab -i desktop out.mpg
#从屏幕的（10,20）点处开始，抓取640x480的屏幕，设定帧率为5 ：
$ ffmpeg -f gdigrab -i desktop  -framerate 5 -offset_x 10 -offset_y 20 -video_size 640x480 out.mpg
```

## 5 ffmepg命令--过滤器用法(Filter)

### 5.1 使用摄像头作为演示视频

```shell
#  camera: 查看视频输入设备
$ ffmpeg -f dshow -list_devices true -i dummy
$ ffmpeg -list_options true -f dshow -i video="HP Wide Vision HD Camera"
$ ffplay -list_devices true -f dshow video=0  //
$ ffplay -f dshow -i video="HP Wide Vision HD Camera"     // camera: 显示视频输入
```

### 5.2 使用测试图像作为演示视频

```shell
# demo for testsrc2
$ ffplay -f lavfi -i testsrc2=size  = 720x480
$ ffplay -f lavfi -i mptestsrc=size = 720x480
```

### 5.3 玩玩过滤器(Filter)

```shell
$ ffplay -filter //查看支持的filter
$ ffplay.exe  -f lavfi -i testsrc2=size=720x480 -vf  “noise;gblur;drawgrid;edgedetect;fftfilt”
$ ffplay.exe  -f lavfi -i testsrc2=size=720x480 -vf hue="H=2*PI*t: s=sin(2*PI*t)+1" 
$ ffplay.exe  -f lavfi -i testsrc2=size=720x480 -vf curves=vintage // filter-curves 视频色彩均衡
$ ffplay.exe  -f lavfi -i testsrc2=size=720x480 -vf crop="in_w/2:in_h/2:(in_w-out_w)/2+((in_w-out_w)/2)*si
n(n/10):(in_h-out_h)/2 +((in_h-out_h)/2)*sin(n/7)"  // filter-crop 视频裁剪抖动
$ ffplay -f lavfi -i smptebars=duration=5:size=720x480:rate=30
$  -vf drawbox=10:20:200:60: red@0.5
$  -vf drawgrid=width=50:height=50:thickness=0.5:color=red@0.5
$  -vf fftfilt=dc_Y=128:weight_Y='squish(1-(Y+X)/100)'      // High-pass
$  -vf fftfilt=dc_Y=0:weight_Y='squish((Y+X)/100-1)'          // Low-pass
$  -vf fftfilt=dc_Y=0:weight_Y='1+squish(1-(Y+X)/100)'     // Sharpen
$  -vf fftfilt=dc_Y=0:weight_Y='exp(-4 * ((Y+X)/(W+H)))'   // Blur
$  -vf histogram=display_mode=parade:levels_mode=logarithmic
$ ffplay -f lavfi -i testsrc -vf "split[src-left][src-copy];[src-copy]edgedetect[right];[src-left][right] hstack"
$ ffplay -f lavfi -i testsrc -vf "split[src-left][src-copy];[src-copy]fftfilt=dc_Y=0:weight_Y='exp(-4 * ((Y+X)/(W+H)))'[right];[src-left][right] hstack"
$ ffplay -f lavfi -i testsrc -vf "split[src-left][src-copy];[src-copy]fftfilt=dc_Y=128:weight_Y='squish(1-(Y+X)/100)'[right];[src-left][right] hstack"
$ ffplay -f lavfi -i testsrc -vf "split[src-left][src-copy];[src-copy]fftfilt=dc_Y=100:dc_U=100[right];[src-left][right] hstack"
$ ffplay -f lavfi -i testsrc -vf "split[left][left-copy];[left-copy]edgedetect[right];[left][right] hstack"
$ ffplay -f lavfi -i testsrc -vf "format=yuv444p,split[left][left-copy];[left-copy]waveform=m=0[right];[left][right] hstack"
$ ffplay -f lavfi -i mandelbrot=size=640x480  -vf "format=yuv444p,split[left][left-copy];[left-copy]histogram=display_mode=0:level_height=468[right];[left][right] hstack"

$ ffmpeg -f lavfi -i testsrc  -f lavfi -i testsrc -filter_complex psnr -f null -
$ ffplay -f lavfi -i mandelbrot -i clut.nut -filter_complex '[0][1] haldclut'
```

### 5.4 其它滤镜示例

Fancy Filtering Examples （http://trac.ffmpeg.org/wiki/FancyFilteringExamples）
– 各种迷幻效果和怪异滤镜的示例

### 5.5 ffmpeg的filter文档

http://www.ffmpeg.org/ffmpeg-filters.html
 
## 6 ffmpeg将图片转换为视频

http://blog.sina.com.cn/s/blog_40d73279010113c2.html

## 7 音频视频重新编码muxer

```shell
ffmpeg -i rkdc2019_allday.mov -pix_fmt yuv420p -c:v libx264 -c:a aac rkdc2019_h264_yuv420p.mp4
```