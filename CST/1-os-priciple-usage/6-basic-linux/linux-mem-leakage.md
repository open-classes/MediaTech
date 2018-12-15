# Linux 程序随机内存问题排查方法[终极版]

## 1 随机内存问题的主要类型

## 2 几种常见的解决方案

## 3 内存问题排查案例分析

### 3.1 使用valgrind排查内存问题()

### 3.2 使用gdb排查内存问题

### 3.3 使用core dump + gdb 排查内存问题

> martin, 直接gdb, 你搞这么多飞机干嘛呢？

gdb 支持多线程调试，但是gdb会追踪可能会重新给线程堆栈排队，最终导致gdb不能复现某些低概率问题。martin就遇到了这样的问题。所以才想到程序自由跑，低概率崩溃之后，生成个core dump文件; gdb 加载core dump文件导出崩溃堆栈。

```shell
fire_box:/ # ulimit -c              // 查看core dump是否为关闭状态
fire_box:/ # ulimit -a              // 查看core dump是否为关闭状态
fire_box:/ # ulimit -c 1024         // 设置core dump文件大小
fire_box:/ # ulimit -c unlimited    // 设置core dump文件大小
fire_box:/ # etc/security/limits.conf
fire_box:/ # cat /etc/group                     // 查看用户组
fire_box:/ # chown -R root.audio qplay_client   // 修改用户组 user:root/group:audio
fire_box:/ (gdb) bt full： 打印堆栈
fire_box:/ (gdb) info threads： 查看所有线程的信息
fire_box:/ (gdb) thread apply all bt： 打开所有线程的堆栈信息
fire_box:/ (gdb) thread apply threadID bt： 查看某个线程的堆栈信息

关于出现崩溃时gdb打印不出堆栈的问题？？
在rockit.mk中加入-DCMAKE_CXX_FLAGS=-fstack-protector-all选项

fire_box:/ # double free or corruption (fasttop)
fire_box:/ # Aborted (core dumped)
fire_box:/ # gdb qplay_client core-19228        // gdb加载core文件
Reading symbols from qplay_client...done.
BFD: Warning: /oem/core is truncated: expected core file size >= 149352448, found: 524288.
fire_box:/ # (gdb) bt/where                     // 使用bt或者where命令, 查看堆栈
```

看到没有，限制无处不再，martin的嵌入式系统flash只有128M, core文件预计是150M, 本来想core文件限制成1M, 结果崩溃是出现了，core文件也生成了，但是啥调试信息都没有。martin再想其他的解决方法吧。

### 3.4 使用 signal + backtrack 排查内存问题

```shell
#include <execinfo.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

const int MAX_STACK_FRAMES = 32;

void onCrashHandler(int signum) {
    char** appstack = nullptr;
    void *buffer[100];

    signal(signum, SIG_DFL);
    size_t size = backtrace(buffer, MAX_STACK_FRAMES);
    appstack = (char**)backtrace_symbols(buffer, size);
    for (size_t idx = 0; idx < size; idx++){
        printf("%d %s\n", idx, appstack[idx]);
    }
    free(appstack);
}

void installCrashListener() {
    signal(SIGSEGV, onCrashHandler);
    signal(SIGABRT, onCrashHandler);
}
```

## 4 Signal信号编号

```shell
fire_box:/ # kill -l
 1) SIGHUP  2) SIGINT    3) SIGQUIT  4) SIGILL   5) SIGTRAP
 6) SIGABRT 7) SIGBUS    8) SIGFPE   9) SIGKILL 10) SIGUSR1
11) SIGSEGV 12) SIGUSR2 13) SIGPIPE 14) SIGALRM 15) SIGTERM
16) SIGSTKFLT 17) SIGCHLD  18) SIGCONT 19) SIGSTOP 20) SIGTSTP
21) SIGTTIN 22) SIGTTOU 23) SIGURG 24) SIGXCPU 25) SIGXFSZ
26) SIGVTALRM 27) SIGPROF 28) SIGWINCH 29) SIGIO 30) SIGPWR 31) SIGSYS
```