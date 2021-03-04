# 多线程

### GCD

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

使用**sync**函数往**当前串行队列**中添加任务，会卡住当前的串行队列（产生死锁）

## 线程死锁的四个条件

产生死锁的四个必要条件： 

1. 互斥条件：一个资源每次只能被一个进程使用。 
2. 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。 
3. 不剥夺条件:  进程已获得的资源，在末使用完之前，不能强行剥夺。 
4. 循环等待条件:  若干进程之间形成一种头尾相接的循环等待资源关系。

这四个条件是死锁的必要条件，只要系统发生死锁，这些条件必然成立，而只要上述条件之 一不满足，就不会发生死锁。

## 下面输出结果

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

## 锁

{% embed url="https://bestswifter.com/ios-lock/" %}

## 互斥锁和自旋锁的区别

* 互斥锁属于sleep-waiting类型的锁。例如在一个双核的机器上有两个线程\(线程A和线程B\)，它们分别运行在Core0和 Core1上。假设线程A想要通过pthread\_mutex\_lock操作去得到一个临界区的锁，而此时这个锁正被线程B所持有，那么线程A就会被阻塞 \(blocking\)，Core0 会在此时进行上下文切换\(Context Switch\)将线程A置于等待队列中，此时Core0就可以运行其他的任务\(例如另一个线程C\)而不必进行忙等待。 
* 自旋锁，它属于busy-waiting类型的锁，如果线程A是使用pthread\_spin\_lock操作去请求锁，那么线程A就会一直在 Core0上进行忙等待并不停的进行锁请求，直到得到这个锁为止

