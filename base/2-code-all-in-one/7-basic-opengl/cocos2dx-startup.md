# Cocos2d-x游戏启动流程分析

## 1 分析的目标

本文分析Cocos2d-x游戏启动流程分析，分析的着眼一下几个方面，带问题深入分析:

- 启动的主要步骤。
- OpenGL后后端是如何启动的。

## 2 启动的主要步骤

Cocos2d-x从主程序入口函数，创建App代理并执行App代理主任务。AppDelegate采用单例设计模式。

``` C++
int APIENTRY _tWinMain(HINSTANCE hInstance...) { 
    // create the application instance
    AppDelegate app;
    return Application::getInstance()->run();
}
```

Application里的run()循环调用了导演类Director的mainLoop(); mainLoop()主要做下面的事情。

- 创建 CCDirector/CC 对象
 
 while(!glview->windowShouldClose())
    {
        QueryPerformanceCounter(&nNow);
        if (nNow.QuadPart - nLast.QuadPart > _animationInterval.QuadPart)
        {
            nLast.QuadPart = nNow.QuadPart;
            director->mainLoop();
            glview->pollEvents();
        }
        else
        {
            Sleep(0);
        }
    }

_animationInterval是我们设定的循环间隔，每次前后两个时间的差大于这个间隔，就执行一次主循环director->mainLoop()，小于这个间隔呢，就一直获取当前的时间，直到大于这个间隔。
 
再看mainLoop，
void DisplayLinkDirector::mainLoop()
{
    if (_purgeDirectorInNextLoop)
    {
        _purgeDirectorInNextLoop = false;
        purgeDirector();
    }
    else if (! _invalid)
    {
        drawScene();
     
        // release the objects
        PoolManager::getInstance()->getCurrentPool()->clear();
    }
}

 
调用drawScene()方法绘制场景，并弹出自动回收池，使这一帧被放入自动回收池里的对象全部被释放。
--------------------- 
作者：宇智波_呵呵 
来源：CSDN 
原文：https://blog.csdn.net/h1051760124/article/details/41083253 
版权声明：本文为博主原创文章，转载请附上博文链接！