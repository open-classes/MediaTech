# Cocos2d-x引擎的架构

## 1 Cocos2d-x架构概述

![Cocos2d-x架构概述](../images/arch-cocos2d-x.png)

## 2 渲染后端架构

窥探一下其渲染后端的设计。cocos2d-x的渲染后端具有以下特征:

- 使用[GLFW/glfw](https://github.com/glfw/glfw)解决了桌面操作系统(Windows/Linux/MAC)的本地窗口管理的接口统一。
- 使用[GLAD]导出桌面操作系统(Windows/Linux/MAC)的本地OPENGL接口，屏蔽了显示驱动的版本差异。
- 使用[EGL/OpenGLES]操控移动操作系统的本地显示接口。

## 3 渲染前端架构

引擎封装层模块

## 4 游戏应用架构

### 4.1 游戏数据管理模块

### 应用程序配置模块

### 日志记录模块

### 网络管理模块

### 消息事件机制模块

### 输入输出控制模块

### 音效管理模块

### GUI系统模块

### 逻辑系统处理模块

### 调试器控制模块