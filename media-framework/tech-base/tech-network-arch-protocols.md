# 技术基础: 互联网网络体系结构(OSI/TCP/IP等)

## 1 网络的体系结构

Universal device network architecture and protocols.

计算机网络的各层以及其协议的结合，称为网络的体系结构。换言之，计算机网络的体系结构即是对计算机网络及其部件所应该完成的功能的精确定义。即计算机网络应设置哪几层，每层应提供哪些功能的精确定义,至于功能如何实现，则不属于网络体系结构讨论的范围。换句话说，网络体系结构只是从功能上描述计算机网络的结构，不涉及每层硬件和软件的组成，也不涉及这些硬件或软件的实现问题。

### 1.1 OSI参考模型

70年代以来，国外一些主要计算机生产厂家先后推出了各自的网络体系结构，但都属于专用的。为使不同计算机厂家的计算机能够互相通信，以便在更大的范围内建立计算机网络，有必要建立一个国际范围的网络体系结构标准。国际标准化组织ISO于1981年正式推荐了一个网络系统结构——开放系统互连模型(Open System Interconnection reference model )OSI/RM,简称OSI。由于这个标准模型的建立，使得各种计算机网络向它靠拢，大大推动了网络通信的发展。“开放”这个词表示：只要遵循OSI标准，一个系统可以和位于世界上任何地方的、也遵循OSI标准的其他任何系统进行连接。

### 1.2 计算机网络的分层模型

一个功能完善的计算机网络需要制定一套复杂的协议集合，对于这种协议集合，最好的组织方式是层次结构模型。这样分层的好处在于：每一层都实现相对的独立功能，因而可以将一个难以处理的复杂问题分解为若干个较容易处理的更小一些的问题。

在网络互连中，有两个标准可以考虑：合法的和事实的。合法的意味着用权力或法律建立。事实的意味着用实际的事实建立。尽管没有得到官方或法律上的承认，但TCP/IP为网络协议创建了一个事实标准，尽管它在得到广泛接受之前并没有成为标准。OSI参考模型是一个合法的标准。国际标准化组织( ISO )创建了OSI模型，并在1984年发布，为供应商提供一个网络模型，这样它们的产品可以在网络上协调工作。

#### 1.2.1 分层模型的优点

- 解决通信的异质性(heterogeneity)问题
- 高层屏蔽低层细节问题
- 降低协议设计的复杂性：各层相对独立，各层不必关心其它层的具体实现，只需知道上下层接口；
- 标准化接口：任何一层发生修改，只要接口关系不变，其它层均不受影响；
- 模块化结构：结构分层，各层都可独立实现；便于网络模型的设计；
- 保证技术的互操作性：各层向下单向依赖，易于实现和维护
- 最重要的一点是不同厂商生产设备时有一个共同操作的标准，使不同产商设备互相兼容。

### 1.3 计算机网络协议

网络协议定义：即网络中（包括互联网）传递、管理信息的一些规范。如同人与人之间相互交流是需要遵循一定的规矩一样，计算机之间的相互通信需要共同遵守一定的规则，这些规则就称为网络协议。

网络协议三个要素

- 语法：即数据与控制信息的结构或格式。
- 语义：即需要发出何种控制信息、完成何种协议以及做出何种应答。
- 同步：即事件实现顺序的详细说明。
  
## 2 主要的协议


### 2.1 IP协议

### 2.2 TCP协议

### 2.3 HTTP协议