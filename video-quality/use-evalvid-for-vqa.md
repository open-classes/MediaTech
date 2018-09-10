# Evalvid视频质量评估

Use Evalvid for video quality assessment.

## 1 Evalvid 概述

视频质量评估工具Evalvid及源码 ，可用于视频质量评估。Evavid由是柏林工业大学的电气工程和计算机科学学院开发和维护。
Evalvid是一个对在真实或模拟的网络里传输的视频进行质量评价的框架和工具集。除了底层网络的QoS参数的测量，如丢包率，延迟，抖动，Evalvid还提供标准的视频质量评价算法如PSNR和SSIM。它视频编码方面支持H.264，MPEG-4和H.263。音频编码方面支持AAC

下载地址：http://www.tkn.tu-berlin.de/menue/research/evalvid/

## 2 具体评估步骤如下:

- 从官方网站下载Evalvid，注意操作系统的版本。
- 制作测试YUV序列，参考序列或测试序列。YUV序列可以通过ffmpeg命令行工具生成，或者从在线数据集中下载YUV序列。
- 了解Evalvid命令行的参数。
- 执行Evalvid命令行，得到视频质量评估的数据。

## 3 用ffmeg制作测试码流

```bash
#!/bin/bash
$ ffmpeg.exe -i H264-BluePlanet-1080p-30fps-4.0M.mp4 -vcodec hevc -s 720*480 blue-720x480.mp4
$ ffmpeg.exe -i blue-720x480.mp4 -c:v libx265 -crf 27 blue-720x480-crf27.mp4
$ ffmpeg.exe -i blue-720x480.mp4 -c:v libx265 -crf 51 blue-720x480-crf51.mp4
$ ffmpeg.exe -i blue-720x480.mp4 blue-480p-src.yuv
$ ffmpeg.exe -i blue-720x480-crf27.mp4 blue-480p-crf27.yuv
$ ffmpeg.exe -i blue-720x480-crf51.mp4 blue-480p-crf51.yuv
```

## 4 Evalvid做全参考客观评价

### 4.1 全参考视频客观评价原理

#### 峰值信噪比(PSNR)

峰值信噪比(PSNR)是一种评价图像的客观标准,用来评价图像压缩后和原图像相比质量的好坏。**评价标准：PSNR越高，压缩后失真越小**。
PSNR 计算公式：

```math
 PSNR=10*log10((2^n-1)^2/MSE) 
```

#### SSIM参数

结构相似性指标(Structural Similarity Index，SSIM index)是一种用以衡量两张数位影像相似程度的指标。当两张影像其中一张为无失真影像，另一张为失真后的影像，二者的结构相似性可以看成是失真影像的影像品质衡量指标。相较于传统所使用的影像品质衡量指标，像是峰值信噪比(英文：PSNR)，结构相似性在影像品质的衡量上更能符合人眼对影像品质的判断。**评价标准：相似度范围0-1，越大相似度越高。**

结构相似性理论认为，自然图像信号是高度结构化的，即像素间有很强的相关性，特别是空域中最接近的像素，这种相关性蕴含着视觉场景中物体结构的重要信息。人眼视觉HVS模型的主要特性包括视觉非线性、多通道、对比敏感度带通、掩盖效应、多通道不同激励的相互作用以及视觉生理心理特征。非线性、多通道、对比敏感度带通和掩盖效应的特性研究较多，有相应的计算模 型，而多通道不同激励的相互作用和视觉生理心理特性还没有精确的计算模型。HVS 模型是一个包含多学科的复合模型，它涉及到生物学、解剖学、生理学、心理学等多学科领域。对于非线性、多通道、对比敏感度带通和掩盖效应的特性的研究，相关理论和模型已经成熟；对于多通道不同激励的相互作用和视觉生理心理特性，相关理论和模型尚未建立。

### 4.2 Evalvid计算PSNR和SSIM

```bash
#!/bin/bash
$ psnr --help
psnr x y <YUV format> <src.yuv> <dst.yuv> [multiplex] [ssim]
  x             frame width
  y             frame height
  YUV format    420, 422, etc.
  src.yuv       source video
  dst.yuv       distorted video
  [multiplex]   optional
  [ssim]        optional: calculate structural similarity instead of PSNR
$ psnr 720 480 420 blue-480p-src.yuv blue-480p-crf27.yuv > crf27_psnr.txt
$ psnr 720 480 420 blue-480p-src.yuv blue-480p-crf51.yuv > crf51_psnr.txt
$ psnr 720 480 420 blue-480p-src.yuv blue-480p-crf27.yuv ssim > crf27_psnr.txt
$ psnr 720 480 420 blue-480p-src.yuv blue-480p-crf51.yuv ssim > crf51_psnr.txt
```

计算结束后，可将数据导入到Excel生成PSNR和SSIM数据图表。

## 参考资料

- [视频质量专家组-视频数据集和组织](https://www.its.bldrdoc.gov/vqeg/video-datasets-and-organizations.aspx)
- [ffmpeg-psnr](https://ffmpeg.org/ffmpeg-all.html#psnr)
- [ffmpeg-ssim](https://ffmpeg.org/ffmpeg-all.html#ssim)