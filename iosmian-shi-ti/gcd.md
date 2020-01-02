# 多线程

> ## GCD

源码地址：[https://github.com/apple/swift-corelibs-libdispatch](https://github.com/apple/swift-corelibs-libdispatch)

深入理解GCD：[https://bestswifter.com/deep-gcd/](https://bestswifter.com/deep-gcd/)

* 关键术语：
  * 同步：
    * `dispatch_sync`
    * 在**当前线程**中执行任务，**不具备**开启新线程的能力
  * 异步：
    * `dispatch_async`
    * 在**新的线程**中执行任务，**具备**开新线程的能力
  * 串行队列：
    * Serial Dispatch Queue
  * * 一个任务执行完毕后，在执行下一个任务
  * 并发队列：
    * Concurrent Dispatch Queue
  * * 多个任务并发执行

> # 下面输出结果

```c
dispatch_async(dispatch_get_global_queue(0, 0), ^{
    NSLog(@"1");
    [self performSelector:@selector(test) withObject:nil afterDelay:1];
    NSLog(@"3");
});

- (void)test {
    NSLog(@"2");
}

// 输出1、3
// 原因：
// performSelector:withObject:afterDelay:的本质是往Runloop中添加定时器
// 子线程默认没有启动Runloop
```

```c
NSThread *thread = [[NSThread alloc] initWithBlock:^{
    NSLog(@"1");
}];
[thread start];

[self performSelector:@selector(test) onThread:thread withObject:nil waitUntilDone:YES];

- (void)test {
    NSLog(@"2");
}

// crash
// 原因：
// waitUntileDone = YES：表示等待线程任务执行完，但是线程执行完就销毁了，target thread exited while waiting for the perform
// 解决方案：
// 把 waiteUntileDone 设置为 NO

```



