# Runloop
---
> ## 深入理解Runloop

https://blog.ibireme.com/2015/05/18/runloop/#base

> ## Runloop简介

runloop也是一个对象，这个对象是管理着它需要处理的事件和消息，并提供一个入口的开启函数。当线程调用了这个开启函数，这个对象就会一直处于『接受消息 - 等待 - 处理消息』的循环当中，直到循环处理结束，函数才会返回。

iOS平台下有两个这样的对象`NSRunLoop`和`CFRunLoopRef`：
- `NSRunLoop`：是基于`CFRunLoopRef`封装，提供的是面向对象的API，API是非线程安全的。
- `CFRunLoopRef`：是在`CoreFoundation`框架内，纯C的API，API是线程安全的。
- `CFRunLoopRef`源码地址：https://opensource.apple.com/tarballs/CF/

> ## Runloop和线程的关系

- 每条线程都有唯一的一个RunLoop对象
- RunLoop保存在一个全局的字典里，key = pthread_t， value = CFRunLoopRef
- RunLoop会在线程结束的时候销毁
- 主线程的RunLoop默认开启，子线程的RunLoop需要手动开启

> ## Runloop内部结构

主要的类：
- CFRunLoopRef
- CFRunLoopModeRef
- CFRunLoopSourceRef
- CFRunLoopTimerRef
- CFRunLoopObserverRef

```objectivec

typedef struct __CFRunLoop * CFRunLoopRef;

struct __CFRunLoop {
    pthread_t _pthread; // 所在的线程
    CFMutableSetRef _commonModes; // 带有commonMode标记的mode
    CFMutableSetRef _commonModeItems; // 标记了commonMode的时间
    CFRunLoopModeRef _currentMode; // 当前的mode
    CFMutableSetRef _modes; // 所有的mode列表
};

typedef struct __CFRunLoopMode *CFRunLoopModeRef;

struct __CFRunLoopMode {
    CFStringRef _name;
    CFMutableSetRef _sources0;
    CFMutableSetRef _sources1;
    CFMutableArrayRef _observers;
    CFMutableArrayRef _timers;
};
```

> ## Runloop的Mode

- CFRunLoopModeRef代表RunLoop的运行模式
- 一个RunLoop可以包含多个Mode，每个Mode又包含多个Source0/Source1/Timer/Observer
- 如果需要切换Mode，只能退出当前的Mode，重新再次进入
  - 好处：每个Mode里面的Source0/Source1/Timer/Observer可以区分开来，互不干扰
- 如果Mode里面没有任何Source0/Source1/Timer/Observer，RunLoop就会退出


