# os-android: 调试多媒体性能(Crack Media Perf)

Android在中国市场野蛮生长，厂商水平参差不齐。手机厂商大品牌较多，Android的硬件和软件稳定性自然很高。Android在很多细分领域也有广泛应用，如: 机顶盒、广告机、收银机、游戏盒子、VR/AR、手持终端、扫地机器人、智能音箱等，设备厂商技术和人才储备不足，芯片供应商也是国内中等实力的IC公司，在这类设备上调试多媒体性能，还是非常难的。

## 1 命令行启动的app

这里有台友商样机，同款芯片同款操作系统能够达到视频4K@P60；我们的同款芯片同款操作系统只能达到视频4K@P30。Crack一下，找找差距。友商样机不小心被我玩坏了，换了新内核后起不来了，侥幸只是Launcher起不来。

Launcher起不来，可以安装一个第三方的Launcher。Launcher的作用无非就是一堆程序的快捷方式。使用am命令也能开启需要打开的程序。

下面查找一下设置APP的完整包名，并使用am start命令打开设置APP界面

```shell
fire_box:/ # dumpsys package | grep settings/.Settings
fire_box:/ # am start -n com.android.settings/.Settings
fire_box:/ # am start -n com.android.tv.settings/.MainSettings
fire_box:/ # am start -n com.rockchips.mediacenter/.activity.MainActivity

# 打开设置APP设置一下HDMI的分辨率为4K@P60。设置APP有平板的设置版本和TV的设置版本。
fire_box:/ # cat /d/dri/0/summary  # 查看VOP，确认设置是否生效
```

## 2 分析友商的多媒体播放性能(Crash Media Perfermance)

市场情报，友商同款芯片同款操作系统能够达到视频4K@P60；我们的同款芯片同款操作系统只能达到视频4K@P30。Crash一下友商系统，找点底气，找芯片原厂算账(O(∩_∩)O哈哈~，其实是寻求技术支持)。

```shell
fire_box:/ # am start -n com.rockchips.mediacenter/.activity.MainActivity
```

视频4K@P60播放，整个系统链路包括: 文件系统读写性能(视频解复用) --> 视频硬解(DDR, VPU) --> 显示系统(GPU/Overlay) --> 显示驱动(VOP/LCDC)等 --> 物理显示设备。

特别提示：芯片原厂的系统内核是Linux-4.4, 如果是Linux-3.1部分解决略有不同。注意适用性。

### 检查点：系统负载

查看空闲的CPU。媒体应用CPU使用率不能太高，否则会出现调度问题。CPU使用率高会出现掉帧问题或概率性卡顿问题。使用下面命令观察一段时间内的CPU平均的空闲率或使用率。

```shell
fire_box:/ # top -m 10 -t 1| grep 400% | awk '{print $5}'
fire_box:/ # logcat -c;logcat -v time  --pid `ps -A | grep mediaserver | awk '{print $2}'`
```

假如经过一段时间的统计，频率CPU使用率为200%，这时候视频肯定会卡顿，需要进一步分析是什么消耗了CPU。造成卡顿的原因很多，建议抓一下系统的Trace分析一下。Trace一定要包含问题现象才有意义。通过Trace本人有发现过两种卡顿问题:

- DDR中断驱动问题，导致中断处理异常耗时，而造成卡顿
- 系统GMS包一直崩溃和重启，造成系统卡顿。这种情况在设置里面把(App>Google Play Services)禁用掉在测试就知道了。

### 检查点：文件系统读写性能(视频解复用)

```shell
# 看看有没有异常的io读写
fire_box:/ # iotop -m 5
```

### 检查点：视频硬解(DDR, VPU)

```shell
# 视频解码对DDR和VPU使用异常频繁，DDR和VPU的频率是检查点。DDR在800M，VPU在500M为佳。
fire_box:/ # ls /sys/class/devfreq
dmc ff300000.gpu ff360000.rkvdec
fire_box:/ # cat /sys/class/dmc/*/cur_freq    # ddr freqency
fire_box:/ # cat /sys/class/ff360000.rkvdec/*/cur_freq  # vpu freqency
# 视频单帧解码时间直接关注，播放几个4K@P60片源，然后查看
fire_box:/ # echo 0x100 > /sys/module/rk_vcodec/parameters/debug
fire_box:/ # cat /proc/kmsg
```

### 检查点：显示系统(GPU/Overlay)

```shell
# 查看当前应用是GLES显示还是Overlay显示。从SurfaceFlinger或VOP中可以查看。显示的帧率是否充足都可以看看。
fire_box:/ # dumpsys SurfaceFlinger
fire_box:/ # dumpsys SurfaceFlinger | grep fps
fire_box:/ # cat /d/dri/0/summary # VOP summary, 奇怪的节点名.
```

### 检查点：显示驱动(VOP/LCDC)

```shell
播放4K视频也和显示系统和CPU有关系，他们的频率也看看。如果是GPU渲染需要关注GPU频率。
fire_box:/ # ls /sys/class/devfreq
dmc ff300000.gpu ff360000.rkvdec
fire_box:/ # cat /sys/class/ff300000.gpu/*/cur_freq    # GPU freqency
fire_box:/ # cat /d/clk/clk_summary | grep vop
          aclk_vop_pre                    1            1   400000000          0 0
             aclk_vop                     2            2   400000000          0 0
fire_box:/ # cat /sys/bus/cpu/devices/cpu*/cpufreq/cpuinfo_cur_freq
1008000
1008000
1008000
1008000
```

### 检查点：多媒体物理设备节点

```shell
# 获取声卡信息
cat /proc/asound/card*/pcm0p/sub0/hw_params
cat /sys/class/drm/card0-HDMI-A-1/audioformat

# HDMI 节点信息
Linux-3.1
$ cat /sys/class/display/HDMI/mode
$ cat /sys/class/graphics/fb*/disp_info
$ cat /sys/devices/virtual/display/HDMI/mode
$ ls /sys/devices/virtual/display/HDMI/
3dmode  color  debug  mode modes   power  scale  type
Linux-4.4
$ cat /sys/class/drm/card0-HDMI-A-1/modes (linux-4.4-drm)
$ cat /d/dri/0/summary
```

从设置里面看下，显示系统的参数，记录一下。

## 3 自行解决还是抛出问题

综合以上分析。比较好解决的是DDR频率、GPU频率和VPU频率不足。渲染的图层比较多也容易解决。改改内核配置一般能解决。如果这些参数均一致，但是自家的表现不如友商，只能系统日志配合上述日志，抛出来给芯片原厂解决了。

```shell
fire_box:/ # ps -A | grep mediaserver | awk '{print $2}'
fire_box:/ # logcat -c; logcat -v time --pid=${pid-mediaserver}
```

## 4 特别关注DDR问题

DDR颗粒的问题也需要关注。DDR颗粒的问题分几种情况:(1) 有些低端的板子用4颗4位的拼成16位总线，DDR总带宽受限，播放4K肯定吃力，大罗神仙也回天无力。(2) DDR种类多，配置模板多，配置错一点都可能带来性能和稳定性问题。(3) DDR颗粒虚焊和其他重要元器件虚焊，可能带来性能和稳定性问题。