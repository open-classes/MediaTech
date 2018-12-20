# Android：性能分析工具(systrace， starce, atrace)

## 1 超级神器 systrace

Systrace 允许你在系统级别收集和检查设备上运行的所有进程的计时信息。Systrace采集内核、Android系统和应用层的信息，然后生成HTML图像化报告。如果想分析Android系统或者app的问题，首先我们需要抓取Systrace文件分析并找出引起系统卡顿，瞬间就可定位出卡顿的原因。调试Native层的卡顿问题，定制化加些ATRACE标记，找出卡顿原因也轻松。

工具位置：${Android-SDK}\platform-tools\systrace 文档：https://source.android.com/devices/tech/debug/systrace。

systrace集成在Android Device Monitor中，工具位置: Android-SDK\tools\monitor.bat。Android Device Monitor可以进行屏幕截图、dump视图结构和抓取systrace。除了通过Android Device Monitor使用systrace, 也可以直接调用Android-SDK\platform-tools\systrace\systrace.py

### 1.1 查看支持哪些trace类型

``` shell
rk3328_box:/ # python systrace.py --list-categories
rk3328_box:/ # py -2 systrace.py --list-categories
```

### 1.2 抓trace并输出

``` shell
rk3328_box:/ # #包含问题现象的，才有分析价值
rk3328_box:/ # py -2 systrace.py -o mynewtrace.html -t 10 audio video network mmc sync sched freq idle am wm gfx view hal
```

### 1.3 使用Android-Studio的systrace

Android Studio 3.1以后，DDMS 和 Systrace、Hierarchy Viewer都不用了。
使用Android Profiler替代DDMS 和 Systrace，Layout Inspector 替代Hierarchy Viewer。

### 1.4 目标板上使用atrace

``` shell
rk3328_box:/ # atrace -o /data/new_trace.html -t 15 -b 4096 audio video network mmc sync sched
rk3328_box:/ # atrace -t 10 -b 4096 audio video network mmc sync sched
```

### 1.5 chrome查看trace

在chrome的地址栏中敲入chrome://tracing/ 然后通过load按钮加载你的trace文件

### 1.6 代码中添加Trace

``` c
#define ATRACE_TAG ATRACE_TAG_GRAPHICS
#include "utils/Trace.h"
ATRACE_CALL();
ATRACE_BEGIN(name);
ATRACE_END();
ATRACE_NAME(name);
```

## 2 strace 查看系统调用

strace 是Linux下的调试利器，可以跟踪所有的系统调用，打印系统调用的参数和返回值； strace还可以指定跟踪子线程/子进程，支持多线程程序的调试。Android的底层是Linux操作系统, strace一般固件默认支持。strace本身不依赖于系统，从一个机器拷贝到另一个机器直接能用。strace 依赖三个最基本的库：libc.so, libstdc++.so 和 libm.so。放心使用就是了。

``` shell
rk3328_box:/ # strace  -h
Filtering:
  -e expr        a qualifying expression: option=[!]all or option=[!]val1[,val2]...
     options:    trace, abbrev, verbose, raw, signal, read, write, fault
  -P path        trace accesses to path

Tracing:
  -b execve      detach on execve syscall
  -D             run tracer process as a detached grandchild, not as parent
  -f             follow forks

rk3328_box:/ # ps -A | grep mediaserver
rk3328_box:/ # strace -f -p 6605
rk3328_box:/ # strace -f -p `ps -A | grep mediaserver | awk '{print $2}'`

rk3328_box:/ # ps -A | grep surface
rk3328_box:/ # strace -f -p 21962
rk3328_box:/ # strace -f -tt -T -p `ps -A | grep surface | awk '{print $2}'`

rk3328_box:/ # strace -f -tt -T -p <pid> ## 如:语音识别的pid
rk3328_box:/ # strace -p <pid> -c
```

## 3 top 和 iotop 分析程序性能

``` shell
rk3328_box:/ # top -m 10
rk3328_box:/ # iotop -m 10
```
