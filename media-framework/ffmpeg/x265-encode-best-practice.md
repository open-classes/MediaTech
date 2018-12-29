# 基于ffmpeg的X265编码最佳实践

## 1 X265编码最佳实践

X265显式支持的一趟码率控制方法有：ABR, CQP, CRF. 缺省方法是CRF。这三种方式的优先级是ABR > CQP > CRF.

### 1.1 Average Bit Rate(ABR)

#### 恒定目标码率(Constant Bit Rate-CBR)

CBR编码策略：比特率基本保持恒定在目标比特率，有利于流式播放。CBR编码对于复杂场景码率不足，对于简单场景码率太多，因此编码内容的视觉质量不稳定。

ffmpeg需要将-minrate和-maxrate设置为和-bitrate参数设置为同一个值。

```bash
#!/bin/bash
$ ffmpeg -i 480p-src.mp4 -c:v libx265 -b:v 4000k -minrate 4000k -maxrate 4000k -bufsize 1835k H264-cbr-4m.mp4
```

#### 可变目标码率(Variable Bit Rate-VBR)

VBR编码策略：为简单场景分配较大的QP，为复杂的场景分配较小的QP，得到基本稳定的输出视觉质量。VBR的缺点在于输出码流大小不可控。同时对于复杂度恒定的内容(例如新闻播音)没什么优势。2 pass VBR:适用于有目标码率限制而又有时间可以进行二次编码的非实时应用。

```bash
#!/bin/bash
$ ffmpeg -y -i 480p-src.mp4 -c:v libx265 -b:v 3000k -pass 1 -an -f mp4 /dev/null && \
$ ffmpeg -i 480p-src.mp4 -c:v libx265 -b:v 3000k -pass 2 H264-abr-3m.mp4
```

#### 平均目标码率(Average Bit Rate-ABR)

ABR编码策略：对简单场景少分配比特；对复杂场景多分配比特；对不同的场景合理分配比特，这种策略类似VBR。在一定时间内，平均码率趋近于目标码率，这样可以控制输出文件大小，这种策略类似CBR。ABR是CBR和VBR的一种折中优化方案。 1 pass ABR: 适用于流媒体或者目标码率受限的实时应用场景。

```bash
#!/bin/bash
$ ffmpeg -i 480p-src.mp4 -c:v libx265 -b:v 3000k -pass 1 H264-abr-3m.mp4
```

码率控制 | 视觉质量 | 输出码率 | 文件大小
---|--- |--- |---
CBR | 不稳定 | 恒定 | 可控
VBR | 稳定 | 变化 | 不可控
CBR | 相对稳定 | 变化 | 相对可控

### 1.2 Constant Picture Quality(CPQ)

CPQ(Constant Picture Quality)每帧图像都按照一个特定的QP来编码，每帧编码后的数据量有多大是未知的。在研究编码算法的时候，一般会选用CQP方法，设定QP为24，28，32，36，40等(一般选4个QP值)，编码得到RD曲线，然后比较算法优劣。 

相同视觉质量时，CQP编码输出的文件会比CRF模式更大。帧的重要级别为：IDR帧 > I帧 > P帧 > 做参考的B帧 > 不做参考的B帧。QP可以依次增大。  QP范围是[0,51]。QP值越小，编码视觉质量越好。QP=0为无失真编码

```bash
#!/bin/bash
$ ffmpeg -i 480p-src.mp4 -c:v libx265 -cqp 24 HEVC-cpq24.mp4
$ ffmpeg -i 480p-src.mp4 -c:v libx265 -cqp 28 HEVC-cpq28.mp4
$ ffmpeg -i 480p-src.mp4 -c:v libx265 -cqp 32 HEVC-cpq32.mp4
$ ffmpeg -i 480p-src.mp4 -c:v libx265 -cqp 36 HEVC-cpq36.mp4
$ ffmpeg -i 480p-src.mp4 -c:v libx265 -cqp 40 HEVC-cpq40.mp4
```

### 1.3 Constant Rate Factor(CRF)

CRF(Constant Rate Factor)将制定的图像质量作为控制目标。CRF可以提供跟QP一样的视觉质量，并将过于复杂的帧(消耗高码率)降低质量，其他的帧保持质量。

CRF量化比例的范围为0~51，其中0为无损模式，23为缺省值，51可能是最差的。该数字越小，图像质量越好。从主观上讲，18~28是一个合理的范围。

```bash
#!/bin/bash
# 使用 -crf 参数
$ ffmpeg.exe -i 480p-src.mp4 -c:v libx265 -crf 24 HEVC-crf24.mp4
$ ffmpeg.exe -i 480p-src.mp4 -c:v libx265 -crf 28 HEVC-crf28.mp4
# 使用 -qp 参数
$ ffmpeg.exe -i 480p-src.mp4 -c:v libx265 -qp 24 HEVC-qp24.mp4
$ ffmpeg.exe -i 480p-src.mp4 -c:v libx265 -qp 28 HEVC-qp28.mp4
```

通过显示的设置-crf和-maxrate参数来使用带有最大比特率crf模式

```bash
#!/bin/bash
ffmpeg -i input -c:v libx265 -crf 20 -maxrate 400k -bufsize 1835k
```

这将会有效的将crf值锁定在20，但是如果输出码率超过400kbps，在这种情况下编码器会将质量降低到低于crf 20。

## 2 X265编码性能测试