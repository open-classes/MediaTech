# ffmpeg视频质量评估

Use ffmpeg for video quality assessment.

## 1 ffmpeg 概述

FFmpeg是领先的多媒体框架，能够解码，编码， 转码，复用，解复用，流式传输，过滤和播放任何多媒体内容。它支持最晦涩难懂的老旧多媒体格式，也支持最前沿多媒体格式。它还具有高度的可移植性：FFmpeg 在各种构建环境，机器架构和配置下，跨Linux，Mac OS X，Microsoft Windows，BSD，Solaris等编译和运行。

下载地址：http://ffmpeg.org/download.html

## 2 具体评估步骤

- 从官方网站下载ffmpeg，注意操作系统的版本。
- 制作测试YUV序列，参考序列或测试序列。YUV序列可以通过ffmpeg命令行工具生成，或者从在线数据集中下载YUV序列。
- 了解ffmpeg命令行的参数。
- 执行ffmpeg命令行，得到视频质量评估的数据。

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

## 4 ffmpeg做全参考视频质量评价

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

### 4.2 ffmpeg计算PSNR和SSIM

```bash
#!/bin/bash
$ ffmpeg -i blue-720x480.mp4 -i blue-720x480-crf27.mp4 -i blue-720x480-crf51.mp4 -lavfi  ssim="stats_file=ssim.log" -f null -
$ ffmpeg -i blue-720x480.mp4 -i blue-720x480-crf27.mp4 -i blue-720x480-crf51.mp4 -lavfi  psnr="stats_file=psnr.log" -f null -

$ ffmpeg -i blue-720x480.mp4 -i blue-720x480-crf27.mp4 -lavfi  "ssim;[0:v][1:v]psnr" -f null -

$ ffmpeg -i blue-720x480.mp4 -i blue-720x480-crf27.mp4 -lavfi  ssim="stats_file=ssim.log" -f null -v info -
$ ffmpeg -i blue-720x480.mp4 -i blue-720x480-crf27.mp4 -lavfi  psnr="stats_file=psnr.log" -f null -v info -

$ ffmpeg -i blue-720x480.mp4 -c:v libx264 -crf 0 -an avc-480p-lossless.mp4
$ ffmpeg -i blue-720x480.mp4 -c:v libx265 -crf 0 -an hevc-480p-lossless.mp4
```

计算结束后，可将数据导入到Excel生成PSNR和SSIM数据图表。

## 参考资料

- [视频质量专家组-视频数据集和组织](https://www.its.bldrdoc.gov/vqeg/video-datasets-and-organizations.aspx)
- [ffmpeg-psnr](https://ffmpeg.org/ffmpeg-all.html#psnr)
- [ffmpeg-ssim](https://ffmpeg.org/ffmpeg-all.html#ssim)