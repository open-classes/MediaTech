# 视频质量评价方法之VQM

当前，最常见的客观测试标准是PSNR和MSE等算法，但是他们的测试结果与主观评价结果的一致性较差，没有反映出人眼的主观感觉。标准和测试机构希望提出了与主观评价结果一致性较好的客观评价算法。很多算法应运而生，如VMAF和VQM算法，客观算法都是在模拟人眼视觉模型的基础上建立的。

美国国家电信和信息管理局(NTIA)提出的视频质量度量(VQM)模型,该模型利用统计学原理模拟实际的人眼视觉系统，在提取参考及其对应的测试图像中人眼能够感知的图像特征值(亮度、色彩、时空变化等信息)的基础上，计算得出视频质量客观评价值，该评价值为人眼可感知的模糊、块失真、不均匀/不自然的运动、噪音和错误块等损伤的综合反映，其值位于0~1之间，值越小越好。

## 1 VQM测试标准

VQM 客观测试标准是利用统计学原理模拟实际的人眼视觉系统，在提取参考及其对应的测试图像中人眼能够感知的图像特征值（亮度、色彩、时空变化等信息）的基础上，计算得出视频质量客观评价值，该评价值为人眼可感知的模糊、块失真、不均匀/不自然的运动、噪音和错误块等损伤的综合反映，其值位于0~1之间，值越小越好。

附录：差异平均意见得分(DMOS：Difference mean opinion score)的分级原理
DMOS 主观测试方案直接由视频质量观察人员同时对参考图像及测试图像进行打分，再对参考图像和测试图像的结果进行归一化（转换为0-100）；然后计算参考图像及测试图形之间的差值并求平均值，通过该平均值的分布来反映被测系统视频主力能力的好坏，其分布范围为0-100，值越小越好。分布范围与主观感觉的映射下表所示：
DMOS 值 | 主观感觉
---|---
0-20 | 好
20-40 | 还可以(中间值)
40-100 | 差
表-x DMOS与主观感觉对应表

## 2 VQM的具体处理过程

- 参考及测试视频流的采集保存及模拟/数字信号转换；
- 测试视频流的校准，依据参考视频，去除测试视频中的时间偏移、空间偏移、增益等非编解码引入的变化；
- 提取参考及测试视频图像中人眼可感知的特征：模糊、块失真、不均匀/不自然的运动、噪音和错误块等信息；
- 对于步骤3提取的每一种特征值进行一定的差分比较计算，依据视频测试序列的长短进行统计平均；
- 使用VQM合并计算公式合并步骤4计算的每种特征值的统计平均值，给出最终的视频质量客观评价值（VQM），最终计算获得的VQM值位于0到1之间，数值越小代表视频质量越好。

备注：测试步骤的数据来源: 参考文献[1]。

## 3 VQM客观和DMOS的一致性分析

DMOS 主观测试结果与 VQM 存在显性线性关系。 VQM的测试结果值可以通过乘以100线性转为 DMOS值，通过 DVQM 值的分布可以间接映射为用户的主观感觉。
编解码参数 | H263_2M | H263_2M | H263_2M | H264_2M | H264_2M | H264_2M
---|---|---|---|---|---|---
测试序列 | VQM | DMOS | 主观感受 | VQM | DMOS | 主观感受
摄像头场景 | 0.4321 | 43.21 | 差 | 0.4016 | 40.16 | 差
摄像头场景-人民币 | 0.3429 | 34.29 | 还可以 | 0.3509 | 35.09 | 还可以
标准图案 | 0.2935 | 29.35 | 还可以 | 0.2810 | 28.10 | 还可以
静态照片切换 | 0.2185 | 21.85 | 还可以 | 0.2299 | 22.99 | 还可以
商场展示片 | 0.2715 | 27.15 | 还可以 | 0.2579 | 25.79 | 还可以
小图片运动 | 0.1577 | 15.77 | 好 | 0.1901 | 19.01 | 好

表-X VQM测试结果映身为DMOS主观评价结果(数据来源:参考文献[1])

## 参考文献

[1 IP视频系统的视频质量客观测试](http://www.h3c.com/cn/d_201006/679125_30008_0.htm)