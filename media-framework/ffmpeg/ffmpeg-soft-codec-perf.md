# ffmpeg制作软解性能评测样例

## 1 确定片源库的特征

- H264 24/30/60FPS的中等质量的片源
- H265 24/30/60FPS的中等质量的片源
- VP09 24/30/60FPS的中等质量的片源

**制作方法**：

- 从网络上下载H264-1080P的片源
- 使用ffmpeg截短H264-1080P的片源到24秒作为片源种子
- 使用ffmpeg，用种子片源转码出H264 24/30/60FPS的中等质量的片源
- 使用ffmpeg，用种子片源转码出H265 24/30/60FPS的中等质量的片源
- 使用ffmpeg，用种子片源转码出VP9 24/30/60FPS的中等质量的片源

## 2 下载原始片源

- 浏览在线视频，寻找满意的片源
- 使用youtube-dl下载网络片源

## 3 制作片源库

ffmpeg主要参数：

- -i 设定输入流
- -f 设定输出格式
- -ss 开始时间

视频主要参数：

- -b 设定视频流量，默认为200Kbit/s
- -r 设定帧速率，默认为25
- -s 设定画面的宽与高
- -aspect 设定画面的比例
- -vn 不处理视频
- -vcodec 设定视频编解码器，未设定时则使用与输入流相同的编解码器

音频参数：

- -ar 设定采样率
- -ac 设定声音的Channel数
- -acodec 设定声音编解码器，未设定时则使用与输入流相同的编解码器
- -an 不处理音频

**使用ffmpeg命令行制作片源库**:  

- 截短片源

```shell
$ ffmpeg -i .\video-hub\blue-planet.mp4 -c:v copy -t 00:00:24 H264-B
luePlanet-1080p-24fps.mp4  # 前24秒钟
```

- 转换出多种帧率

```shell
$ ffmpeg -i H264-BluePlanet-1080p-24fps.mp4 -qscale 0 -r 60 H264-BluePlanet-1080p-30fps.mp4
$ ffmpeg -i H264-BluePlanet-1080p-24fps.mp4 -qscale 0 -r 60 H264-BluePlanet-1080p-60fps.mp4
$ ffmpeg -i H264-Clock-1080p-30fps.mp4 -qscale 0 -r 24 H264-Clock-1080p-24fps.mp4
```

- 转换出H265和VP9格式

```shell
$ ffmpeg -i H264-BluePlanet-1080p-24fps.mp4 -vcodec hevc H265-BluePlanet-1080p-24fps.mp4
$ ffmpeg -i H264-BluePlanet-1080p-30fps.mp4 -vcodec hevc H265-BluePlanet-1080p-30fps.mp4
$ ffmpeg -i H264-BluePlanet-1080p-60fps.mp4 -vcodec hevc H265-BluePlanet-1080p-60fps.mp4

$ ffmpeg -i H264-BluePlanet-1080p-24fps.mp4 -vcodec vp9 VP9-BluePlanet-1080p-24fps.mp4
$ ffmpeg -i H264-BluePlanet-1080p-30fps.mp4 -vcodec vp9 VP9-BluePlanet-1080p-30fps.mp4
$ ffmpeg -i H264-BluePlanet-1080p-60fps.mp4 -vcodec vp9 VP9-BluePlanet-1080p-60fps.mp4
```

必要时限制一下码率,否则图像质量比较低

```shell
$ ffmpeg -i H264-BluePlanet-1080p-24fps.mp4 -vcodec vp9 -b 1024k VP9-BluePlanet-1080p-24fps.mp4
```

## 4 软解性能测试

### 4.1 系统参数

```shell
# 获取CPU信息
$ cat /proc/cpuinfo

# 获取温度/频率/帧率和CPU使用率
$ while(true); do temp=`cat /sys/class/thermal/thermal_zone*/temp`; freq=`cat /sys/bus/cpu/devices/cpu0/cpufreq/cpuinfo_cur_freq`; fps=`dumpsys SurfaceFlinger | grep fps=[1-6]`; cpu=`dumpsys cpuinfo | grep TOTAL`; printf "temp=%-5s freq=%-7s fps=%-7s cpu=%-4s\n"  $temp $freq ${fps##*fps=} ${cpu:0:4}; sleep 1; done
```