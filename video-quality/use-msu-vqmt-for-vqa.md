# MSU VQMT

Use [MSU VQMT](http://www.compression.ru/video/quality_measure/) for video quality assessment.

## 1 MSU VQMT简介

[MSU Video Quality Measurement Tool (MSU VQMT)](http://www.compression.ru/video/quality_measure/) 是莫斯科国立大学 (Moscow State University)的 [Graphics and Media Lab (GML)](http://graphics.cs.msu.ru/)开发一款客观视频质量评价程序。[Graphics and Media Lab (GML)]((http://graphics.cs.msu.ru/))在计算机图形学，计算机视觉，图像和视频处理等许多领域拥有扎实的研究经验。研究由俄罗斯基础研究基金，国家合同以及英特尔，微软，三星等国际公司提供支持。

MSU VQMT 提供了多种全参考视频质量评价方法(对比分析配对的视频)和无参考视频质量评价方法(分析单个视频)。

MSU VQMT提供了丰富的度量方法：常规的PSNR，Delta，VQM，SSIM和其开发的SSIM和我们自己的度量模糊和阻塞指标。

## 2 评估MSU VQMT工具

**具体评估步骤如下**:

- 从官方网站下载免费版本MSU VQMT工具,[下载地址](http://www.compression.ru/video/quality_measure/video_measurement_tool.html)。
- 制作测试YUV序列，参考序列或测试序列。YUV序列可以通过ffmpeg命令行工具生成，或者从在线数据集中下载YUV序列。注意：免费版本要求YUV序列小于1080P。
- MSU VQMT工具根据YUV序列参数配置软件参数。
- 执行视频质量评估，并生成图像。

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

## 4 效果展示

```bash
#!/bin/bash
$ report-vqmt/3ssim-blue-480p-crf27-vs-crf51.png  
$ report-vqmt/delta-blue-480p-crf27-vs-crf51.png
$ report-vqmt/niqe-blue-480p-crf27-vs-crf51.png
$ report-vqmt/vqm-blue-480p-crf27-vs-crf51.png
$ report-vqmt/bi-psnr-blue-480p-crf27-vs-crf51.png
$ report-vqmt/msad-blue-480p-crf27-vs-crf51.png
$ report-vqmt/noise-estimation-blue-480p-crf27-vs-crf51.png
$ report-vqmt/blocking-blue-480p-crf27-vs-crf51.png 
$ report-vqmt/mse-blue-480p-crf27-vs-crf51.png
$ report-vqmt/psnr-blue-480p-crf27-vs-crf51.png
$ report-vqmt/blurring-blue-480p-crf27-vs-crf51.png 
$ report-vqmt/ms-ssim-blue-480p-crf27-vs-crf51.png 
$ report-vqmt/ssim-blue-480p-crf27-vs-crf51.png
```