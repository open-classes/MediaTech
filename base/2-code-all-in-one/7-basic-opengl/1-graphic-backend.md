# 统一硬件渲染后端的解决方案调研

## 1 操作系统绘图接口乱象

目前终端的设备的互联互通的要求越来越高。开源软件开发者希望屏蔽硬件异构和操作系统API差异，用一套代码能够在所有的平台运行。一次代码编码，多平台编译和多平台运行，是开源软件开发者的终极目标。目前差异性最大的就是终端的硬件加速显示硬件及其相关的操作接口。目前流行的解决方案就是混合渲染后端，渲染后端可配置。

## 2 统一渲染后端的解决方案

目前大型的开源软件、中大型游戏引擎、绘图引擎等，等将统一的渲染后端作为软件架构的核心着眼点。没有统一的渲染后端，所有的前端代码将不堪入目。一次代码编码，多平台编译和多平台运行将成为噩梦，难以实现。

### 2.1 QT的混合渲染后端方案

QT是领先的跨平台C++程序开发环境。QT使用Google/Angle管理渲染后端，屏蔽操作系统和接口的差异。

### 2.2 Google/Angle渲染后端方案

![Angle渲染后端管理](../images/arch-render-backend.png)

### 2.3 Google/Skia渲染后端方案

Google/Skia是领先的图形库。Google/Skia使用Google/Angle管理渲染后端，屏蔽操作系统和接口的差异。

## 3 开源项目

### 3.1 Skia项目

### 3.2 Cocos2d-x

[cocos2d/cocos2d-x](https://github.com/cocos2d/cocos2d-x): 是一款功能强大的开源游戏引擎。允许开发人员使用 C++、Javascript 及 Lua 三种语言来进行游戏开发。支持所有常见平台，包括 iOS、Android、Windows、macOS、Linux。现代化的 C++ API。完善的游戏功能支持，包含精灵、动作、动画、粒子特效、场景转换、事件、文件 IO、数据持久化、骨骼动画。

当然我们不是剖析游戏引擎，而是窥探一下其渲染后端的设计。cocos2d-x的渲染后端具有以下特征:

- 使用[GLFW/glfw](https://github.com/glfw/glfw)解决了桌面操作系统(Windows/Linux/MAC)的本地窗口管理的接口统一。
- 使用[GLAD]导出桌面操作系统(Windows/Linux/MAC)的本地OPENGL接口，屏蔽了显示驱动的版本差异。
- 使用[EGL/OpenGLES]操控移动操作系统的本地显示接口。

### 3.3 ANGLE项目

[Google/Angle](https://github.com/google/angle) 提供了面向Windows, Mac和Linux操作系统的OpenGL ES 2.0和EGL 1.4规范标准的实施。该项目为第三开发者在传统桌面系统上构建和运行GLES-2.0程序提供了便利。

ANGLE的目标是允许Windows用户通过将OpenGL ES API调用转换为DirectX 9或DirectX 11 API调用来无缝运行WebGL和其他OpenGL ES内容。ANGLE用作Windows平台上Google Chrome和Mozilla Firefox的默认WebGL后端。ANGLE v1.0支持OpenGL ES 2.0规范，ANGLE还提供了EGL 1.4规范的实现。

Linux和MAC操作系统，官方支持的绘图接口是OPENGL; Windows操作系统，官方支持的绘图接口是D3D API而不是OpenGL，所以OpenGL驱动经常都找不到。Google新的开源项目ANGLE就是要来解决这个问题的，它允许Windows用户运行WebGL内容而无需到处寻找安装新的OpenGL驱动程序。ANGLE的核心目标是实施 OpenGL ES 2.0 API；使所有桌面操作系统支持OpenGL ES 2.0 API；以此保证桌面操作系统和移动操作系统能够使用同一套渲染API，简化跨平台程序开发。

### 3.4 GLFW项目

[GLFW/glfw](https://github.com/glfw/glfw)是一个开源，跨平台的库，用于桌面上的OpenGL，Op​​enGL ES和Vulkan开发。它提供了一个简单的API，用于创建Window，Context和Surface，接收输入和事件。GLFW是用C语言编写的，并且使用X Window系统（例如Linux和FreeBSD）对Windows，macOS和许多类Unix系统提供原生支持。

