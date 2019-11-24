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
- RunLoop保存在一个全局的字典里，key=线程，value=RunLoop
- RunLoop会在线程结束的时候销毁
- 主线程的RunLoop默认开启，子线程的RunLoop需要手动开启

> ## Runloop内部结构

```objectivec

typedef struct __CFRunLoop * CFRunLoopRef;

struct __CFRunLoop {
    CFRuntimeBase _base;
    pthread_mutex_t _lock;			/* locked for accessing mode list */
    __CFPort _wakeUpPort;			// used for CFRunLoopWakeUp 
    Boolean _unused;
    volatile _per_run_data *_perRunData;              // reset for runs of the run loop
    pthread_t _pthread;
    uint32_t _winthread;
    CFMutableSetRef _commonModes;
    CFMutableSetRef _commonModeItems;
    CFRunLoopModeRef _currentMode;
    CFMutableSetRef _modes;
    struct _block_item *_blocks_head;
    struct _block_item *_blocks_tail;
    CFAbsoluteTime _runTime;
    CFAbsoluteTime _sleepTime;
    CFTypeRef _counterpart;
};
```

