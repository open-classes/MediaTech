# 流媒体的视频质量评价方法

## 1 前言

随着数据通信技术和移动互联网的发展，流媒体业务(HTTP/HLS/DASH)、IPTV业务、直播业务和视频会议业务成为互联网承载的主体流量。移动互联网巨头和电商巨头都将移动流媒体领域跑马圈地。流媒体成为流量王，给网络运行商、内容运行商、CDN运行商和终端厂商提出层层精进的技术要求，解决流媒体流量和用户体验的平衡的问题，成为一个日益严峻的需求。本文从视频质量评价的角度，给出在网络侧(服务器侧)流媒体流量和质量均衡的最佳实践，并给出针对HEVC流媒体流量和质量均衡的相关指标量化关系。

## 2 视频质量评价系统的架构

基于视频若干客观特征评估视频质量的方法包括：均方误差(MSE)、峰值信噪比(PSNR)、结构相似性指数(SSIM)、多尺度结构相似性指数(MS-SSIM)、视觉信息保真度(VIF)、VQM和最明显失真(MAD)等。基于人群主观评测得分的平均值评估视频质量的包括:双激励损伤量表(DSIS)、双激励连续质量量表(DSCQS)和单激励方法(SSM)。由于客观评价方法没有充分反映出人眼的视觉特征，客观评价方法与主观评价方法的一致性较差。VQM 客观测试标准是利用统计学原理模拟实际的人眼视觉系统，在提取参考及其对应的测试图像中人眼能够感知的图像特征值(亮度、色彩、时空变化等信息)的基础上，计算得出视频质量客观评价值，该评价值为人眼可感知的模糊、块失真、不均匀/不自然的运动、噪音和错误块等损伤的综合反映，其值位于0~1之间，值越小越好。

- 优选NS2作为网络模拟软件模拟网络状况;
- 使用X264和X265作为基准编解码软件;
- 使用ffmpeg作为基准转码软件;
- 优选VQM作为视频质量评价方法;

## 3 传输损耗的分析与评估

视频的时域损伤主要由实时流媒体在网络中传输产生的时延和时延抖动造成。

- 误码率对视频质量评价的影响
- MTU对视频质量评价的影响

要探讨封包长度对图像传输质量的影响，我们可以通过更改封包大小mtu的值，保持其它参量不变，进行仿真，可得到不同封包大小相对应的平均 PSNR 值。

## 4 编码损耗的分析与评估

视频的空域损伤主要是指图像的损伤，造成的原因一般有编解码方式带来的图像损伤，由网络传输造成的丢包引起的图像损伤等。本文内容主要从以下几个方面对视频质量进行分析：H.264编码方案的不同帧重要性程度不同，不同宏块大小和不同运动向量对于一帧图像的重要性程度也不同，最后结合网络时延对视频带来的时域损伤进行分析，得到相应的视频质量评价算法。

- [HEVC] 量化参数QP对对视频质量评价的影响。不同视频场景和不同QP情况下，QP和视频质量的关系。
- [HEVC] GOP对视频质量评价的影响: GOP pattern 对图像传输质量的影响，我们可以通过设置GOP参数，计算VQM值，进行比较，得出GOP与图像传输质量之间的关联性。

## 5 结语

经过上面的分析，基于视频质量评价的方法，在网络侧(服务器侧)流媒体流量和质量均衡我们给出以下最佳实践:

- 服务器端需要选择高性能的编码器，以保证视频流低码率和高质量;
- 编码器的QP和GOP对视频质量有显著影响，通过对比试验确定最佳的QP和GOP;
- 数据通信网络的误码率对视频质量有显著影响，需要技术手段是误码率在可接受范围;
- 数据通信网络的MTU与误码率有叠加效应，大的MTU有利于提高视频质量

## 参考文献

- [BT.500 : Methodology for the Subjective Assessment of the Quality of Television Pictures](https://www.itu.int/rec/R-REC-BT.500)
- 段琳, "基于流媒体的视频质量评价模型", 中山大学, 2014
- 范少芬, "基于网络损伤和流媒体特征的IPTV视频QoE评估方法的研究和应用", 华东师范大学, 2010
- 石然, "网络损伤对RTP/RTCP流媒体视频QoE影响的研究及应用", 北京邮电大学, 2014
- 屈茜, "实时流媒体质量评价方法研究", 电子科技大学, 2013
- 朱元庆, "基于网络QoS参数的视频QoE评估模型", 南京邮电大学, 2014
- 耿杨,金家义, "基于压缩编码和传输损伤的视频QoE评估方法", 北京邮电大学学报, 2016
- 许雯, "基于HLS流媒体视频直播的QoE分析模型设计与实现", 西安航空学院学报, 2017