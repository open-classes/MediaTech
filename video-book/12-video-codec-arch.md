# H264-视频压缩编码技术的通用架构

## 基本结构

视频编码方法与可采用的信源模型有关。如果采用“一幅图像由许多像素构成”的信源模型，这种信源模型的参数就是每个像素的亮度和色度的幅度值。对这些参数进行压缩编码技术称为基于波形的编码。如果采用一个分量有几个物体构成的信源模型，这种信源模型的参数就是各个物体的形状、纹理和运动。对这些参数进行压缩编码的技术被称为基于内容的编码。

由此可见，根据采用信源模型，视频编码可以分为两大类，基于波形的编码和基于内容的编码。它们利用不同的压缩编码方法，得到相应的量化前的参数；再对这些参数进行量化，用二进制码表示其量化值；最后，进行无损熵编码进一步提高码率。解码则为编码的逆过程。

## 基于波形的编码

如上所述，利用像素间的空间相关性和帧间的时间相关性，采用预测编码和变换编码技术可大大减少视频信号的相关性，从而显著降低视频序列的码率，实现压缩编码的目标。

基于波形的编码采用了把预测编码和变换编码组合起来的基于块的混合编码方法。为了减少编码的复杂性，使视频编码操作易于执行，采用混合编码方法时，首先把一幅图像分成固定大小的块，例如块 8×8（即每块 8 行，每行 8 个像素）、块 16×16（每块 16 行，每行 16 个像素）等等，然后对块进行压缩编码处理。

自 1989 年 ITU-T 发布第一个数字视频编码标准——H.261 以来，已陆续发布了 H.263 等视频编码标准及 H.320、H.323 等多媒体终端标准。ISO 下属的运动图像专家组（MPEG）定义了 MPEG-1、MPEG-2、MPEG-4 等娱乐和数字电视压缩编码国际标准。2003 年 3 月份，ITU-T 颁布了 H.264 视频编码标准。它不仅使视频压缩比较以往标准有明显提高，而且具有良好的网络亲和性，特别是对 IP 互联网、无线移动网等易误码、易阻塞、QoS 不易保证的网络视频传输性能有明显的改善。本书的主要内容即 H.264 视频编码。所有这些视频编码都采用了基于块的混合编码法，都属于基于波形的编码。

## 基于内容的编码

如上所述，基于块的编码易于操作，但由于人为地把一幅图像划分成许多固定大小的块，当包含边界的块属于不同物体时，它们分别具有不同的运动，便不能用同一个运动矢量表示该边界块的运动状态。如果强制划分成固定大小的块，这种边界块必然会产生高的预测误差和失真，严重影响了压缩编码信号的质量。于是产生了基于内容的编码技术。这时先把视频帧分成对应于不同物体的区域，然后对其编码。

具体说来，即对不同物体的形状、运动和纹理进行编码。在最简单情况下，利用二维轮廓描述物体的形状；利用运动矢量描述其运动状态；而纹理则用颜色的波形进行描述。当视频序列中的物体种类已知时，可采用基于知识或基于模型的编码。例如，对人的脸部，已开发了一些预定义的线框对脸的特征进行编码，这时编码效率很高，只需少数比特就能描述其特征。对于人脸的表情（如生气、高兴等），可能的行为可用语义编码，由于物体可能的行为数目非常小，可获得非常高的编码效率。

MPEG-4 采用的编码方法就既基于块的混合编码，又有基于内容的编码方法。