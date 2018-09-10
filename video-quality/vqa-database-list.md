# 视频质量评估相关数据库

## 1 视频测试序列库

[CDVL(TheConsumer Digital Video Library)](http://www.cdvl.org/) 消费者数字视频库对外提供高质量的源视频序列，可供研究和开发免费使用。CDVL还托管了几个视频质量数据集，包括五个VQEG HD Phase I数据集，BVI-HD，CCRIQ，its4s和T1A1。

## 2 图像质量评估测试序列库

### 2.1 TID2008 database

[TID2008 database](http://www.ponomarenko.info/tid2008.htm)

### 2.2 CSIQ database

[CSIQ database](http://vision.okstate.edu/csiq)

### 2.3 LIVE database

[LIVE database](http://live.ece.utexas.edu/research/Quality/) 出自德克萨斯大学的图像&视频工程实验室。该实验室的视觉科学家和视频工程师对图片和视频质量进行大规模主观和客观研究，对相关数据库做了严格的视觉检测/筛选。该数据库包含15+细分的自数据库。该数据库还包含若干视频质量评估的背景知识。

### 2.4 Research on Image Quality Assessment

[Research on Image Quality Assessment](http://sse.tongji.edu.cn/linzhang/IQA/IQA.htm) 同济大学张林博士在个人网站提供了大量主观评价算法的数据。

MOS(Mean Opnion Score, 平均意见分)是主观评价实验之后，得到的主观分数，取值0-100，值越大，代表主观感受越好。

主观感受与客观分数(PSNR)之间的关系: 

```
MOS = -24.3816 *( 0.5 - 1./(1 + exp(-0.56962 * (PSNR - 27.49855)))) + 1.9663*PSNR   -2.37071;
```

主观感受与客观分数(PSNR)之间的关系: 

```
MOS = 2062.3 * (1/(1 + EXP(-11.8 * (SSIM - 1.3)))+0.5) + 40.6*SSIM -1035.6
```

## 3 视频质量评估的研究机构

### 3.1 Video Quality Experts Group (VQEG)

[Video Quality Experts Group (VQEG)](https://www.its.bldrdoc.gov/vqeg/vqeg-home.aspx) 视频质量专家组首先基于视频质量评估的基本主观方法和客观工具开发/验证，以便行业可以通过标准化和实施向前推进。视频质量专家组拥有庞大的生态圈，利用来自多个组织的资源并将个人研究工作与一般解决方案相结合。视频质量专家组拥有以下使命：开发新的和改进的质量评估方法；证明质量评估方法有效性；分析尖端技术对感知质量的影响；推动新的和改进的国际标准。

### 3.2 Research on Image Quality Assessment

[Research on Image Quality Assessment](http://sse.tongji.edu.cn/linzhang/IQA/IQA.htm)

## 4 视频数据集

- [Stefan Winkler](http://stefan.winkler.net/resources.html) 提供了许多视频数据库的摘要。
- AWS Elemental [4K测试序列](http://www.elemental.com/resources/4k-test-sequences) 
- [CableLabs](http://www.cablelabs.com/resources/4k/)具有4K和UHD视频序列。
- [消费者数字视频库(CDVL)](https://www.cdvl.org/)具有高质量的源视频序列，可供研究和开发免费使用。CDVL还托管了几个视频质量数据集，包括五个VQEG HD Phase I数据集，BVI-HD，CCRIQ，its4s和T1A1。
- [EBU UHD视频序列](https://tech.ebu.ch/testsequences/uhd-1) 
- [Elecard视频序列](https://www.elecard.com/videos) 
- [Harmonics](http://www.harmonicinc.com/resources/videos/4k-video-clip-center#4k-clip-center)有4K视频序列。
- [图像和视频工程实验室(LIVE)](http://live.ece.utexas.edu/research/quality/) - 图像和视频质量评估研究。数据库可供下载。
- [NASA](https://svs.gsfc.nasa.gov/index.html)
- [QUALINET数据库门户](http://dbq.multimediatech.cz/)
- [SJTU 4K视频序列](http://medialab.sjtu.edu.cn/web4k/index.html) 
- [Stefan Winkler](https://stefan.winklerbros.net/resources.html) - 关于视频质量的个人研究页面。
- [超视频组](http://ultravideo.cs.tut.fi/#testsequences)
- 南加州大学媒体传播实验室：[VideoSet](https://ieee-dataport.org/documents/videoset);基于JND的压缩视频[JCV数据集](http://mcl.usc.edu/mcl-jcv-dataset/)和  基于JND的压缩图像[JCI数据集](http://mcl.usc.edu/mcl-jci-dataset/)

## 参考文献

- [视频质量专家组-视频数据集和组织](https://www.its.bldrdoc.gov/vqeg/video-datasets-and-organizations.aspx)