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



