# Live 数据集使用指南

[Image & Video Quality Assessment at LIVE](http://live.ece.utexas.edu/research/Quality/)。Live数据集由德州大学奥斯丁分校的图像和视频工程实验室(Laboratory for Image and Video Engineering)主持构建和维护。

## Live 数据集简介

图像和视频处理数据一般通过数据通信网络传输。在呈现给人类观察者之前，图像或视频可能经历许多处理阶段，并且每个处理阶段可能引入可能降低最终显示质量的失真。例如，图像和视频由相机设备获取，这可能由于光学，传感器噪声，颜色校准，曝光控制，相机运动等而引入失真。在获取之后，图像或视频可以进一步通过压缩算法来处理，该算法减少了存储或传输的带宽要求。这种压缩算法通常被设计为通过使信号发生某些失真来实现更大的带宽节省。同样的，在通过信道传输图像时或在存储图像时（很少）发生的比特错误也会引入失真。最后，用于渲染最终输出的显示设备可能会引入一些自身的失真，例如低再现分辨率，不良校准等。每个阶段可能添加的失真量主要取决于物理限制。

Live 数据集考虑了每个阶段的失真的视频源，用于视频质量评估。支持的子数据库列表见：[Image & Video Quality Assessment at LIVE](http://live.ece.utexas.edu/research/Quality/)

## MCL-V 数据集的特征

Live 数据集考虑了每个阶段的失真的视频源，用于视频质量评估。支持的子数据库列表见：[Image & Video Quality Assessment at LIVE](http://live.ece.utexas.edu/research/Quality/)。子数据库视频序列和相关论文可直接下载。子数据库具有单一特征，并有说明文档。

人们显然对能够测量图像或视频的质量，以及衡量在不同阶段添加到其中的失真感兴趣。确定图像或视频质量的一种显而易见的方法是征求人类观察者的意见。客观质量测量（与人类观察者的主观质量评估相反）旨在通过算法确定图像或视频的质量。客观质量评估（QA）研究的目标是设计算法，其质量预测与人类观察者的主观评分非常一致。

## 参考资料

* Chao Chen, L. K. Choi, G. de Veciana, C. Caramanis, R. W. Heath, A. C. Bovik, "A dynamic system model of time-varying subjective quality of video streams over HTTP," 2013 IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP), pp.3602-3606, 26-31 May 2013.
* Chao Chen, L. K. Choi, G. de Veciana, C. Caramanis, R. W. Heath, A. C. Bovik , "Modeling the Time-Varying Subjective Quality of HTTP Video Streams With Rate Adaptations," IEEE Transactions on Image Processing, vol.23, no.5, pp.2206,2221, May 2014.