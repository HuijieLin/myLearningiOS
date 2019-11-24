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
  - 事件发生的地方
  - Source0:
    - 只包含一个回调（函数指针），它不能主动触发事件
    - 常见的场景：
      - 触摸事件处理
      - `performSelector:onThread:`
  - Source1:
    - 包含一个mach_port和一个回调（函数指针）
    - 被用于通过内核和其他线程互相发送消息
    - 这个source可以主动唤醒RunLoop
    - 常见的场景：
      - 基于Port的线程间通信
      - 系统事件的捕捉
- CFRunLoopTimerRef
  - 基于时间的触发器
  - 其包含了一个时间长度和一个回调，当加入RunLoop的时候，RunLoop会注册对应的时间点，当时间点到的时候，RunLoop会被唤醒处理对应的回调
  - 常见的场景：
    - NSTimer
    - `performSelector:withObject:afterDelay:`
- CFRunLoopObserverRef
  - 当RunLoop的状态发生变化的时候，观察者就可以接收到回调

**内部结构（精简）如下：**

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
- 苹果公开提供的 Mode 有两个：`kCFRunLoopDefaultMode (NSDefaultRunLoopMode)` 和 `UITrackingRunLoopMode`
- 苹果还提供了一个操作 Common 标记的字符串：`kCFRunLoopCommonModes (NSRunLoopCommonModes)`，这个不是真正的mode，只是作为一个标志符。被标记的item，可以在包含 Common 标记符的Mode下面运行，苹果公开的两个 Mode ，都属于带有 Common 标记的 Mode


