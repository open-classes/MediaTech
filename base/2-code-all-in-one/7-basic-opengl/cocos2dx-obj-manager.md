# Cocos2d-x的对象管理

## 1 Cocos2d-x内存管理机制

### 1.1 c++显式堆内存管理

这引擎的内存管理机制是源于object-c的，了解之前得了解c++的管理方式：c++使用new分配内存，delete释放内存，虽然看起来方便，却无法完全保证此应用正确。不能正确处理的话会出现几个问题：

- 野指针：指向的内存已被释放，却还有其他指向它的指针还在使用
- 重复释放
- 内存泄漏：不再使用的内存没有释放，导致内存占用过高

### 1.2 c++11中的智能指针

c++11加入智能指针,对*进行了重载，所以可以通过*ptr访问到其所分配的堆内存，通过调用reset成员释放其内存：

- unique_ptr：不能和其他智能指针共享其指向的内存，直接赋值给其他指针会报错，但可以通过标准库的move来转移，一但转移，原来的指针会失效
- shared_ptr：多个共享，采用引用计数，reset只会引用-1
- weak_ptr：指向shared_ptr指针的内存，但不拥有它，我们通过lock方法来访问其指向的shared_ptr对象，如果指向对象失效，返回nullptr（就是弱引用啊斌！）

### 1.3 为什么不使用智能指针

但cocos2dx不使用智能指针，原因：

- 性能损失，shared_ptr线程安全，自带互斥锁
- 仍要显示声明指针，不优雅

### 1.4 垃圾回收机制　　

垃圾回收机制，一般通过：

- 引用计数：实现简单
- 跟踪处理：以目标为根，在其引用的空间上做标记，完成后没标记的在第二阶段清理

### 1.5 cocos2dx内存管理机制

引擎使用的是一个可称为智能指针的变体

``` C++
class Ref:
    def retain():pass
    def release():pass
    def autorelease():pass
    def getRefreenceCount():pass
    self._referenceCount = 0
    #friend class AutoreleasePool
```

retain增加引用，release减少引用，但很少直接使用它们来管理，不然就和c++的一样了。我们知道，智能指针是通关关联一个自动变量来实现，autorelese其实就是大概做类似的工作，调用它的时候，它会声明对象为智能指针，但它不关联自动变量，而和加入一个AutoreleasePool里，这个池在每一帧结束对池里对象进行清理

实现机制是，AutoReleasePool对池里对象release一下，如果没被使用，引用会为0，则后面被释放。

``` C++
auto node = new Node()
node->autorelease()
#帧结束后被释放

auto node = new Node()
node->autorelease()
addChild(node)
#不会被释放
```

cocos2dx为了不用显式调用AutoRelease，一般让程序员通过create来获取对象，而create里帮我们调用好了AutoRelease。autorelease主要应用在ui元素上。

### 1.6 autoreleasepool队列

为了支持对自定义数据对象的自动释放的支持，我们需要能够自定义AutoreleasePool的生命周期，引擎是通过一个pool队列来处理它，并且交由PoolManager来管理，里面至少有一个pool来处理ui元素的问题。我们能自己创建一个，为了达到和智能指针一样的内存管理，AutoreleasePool的构造和析构函数进行了特殊处理：把本身加进自己的池里，当执行clear的时候，就会对本身进行release，从而达到管理自己的工作。