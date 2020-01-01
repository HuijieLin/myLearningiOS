# Tagged Pointer

> ## 简介

* 在没有tagger pointer之前，NSNumber等小对象需要动态分配内存，维护引用计数，NSNumber指针存储的是堆中NSNumber对象的地址值
* 在64bit开始，iOS引入tagged pointer，用于优化NSNumber，NSData，NSString等小对象的存储（直接存在指针里面）
* 在使用tagger pointer之后，是通过Tag+Data的方式存储
* objc\_msgSend可以识别是否tagger pointer，然后直接在指针里面取内容，减少调用开销
* 当指针存不下内容时，才通过动态分配内存的方式来存储数据
* 在MAC下，如果是tagged pointer，它对应的引用计数就是`-1`

> ## 底层判断逻辑

如何判断是否tagged pointer：

* iOS平台，判断**最高**有效位是否为1
* MAC平台，判断**最低**有效位是否为1

```objectivec
#if TARGET_OS_OSX && __x86_64__
    // Mac 平台
#   define OBJC_MSB_TAGGED_POINTERS 0
#else
    // iOS 平台
#   define OBJC_MSB_TAGGED_POINTERS 1
#endif

#if OBJC_MSB_TAGGED_POINTERS
#   define _OBJC_TAG_MASK (1UL<<63) // iOS平台获取最高位（第64bit），如果是1，就表示tagged pointer
#else
#   define _OBJC_TAG_MASK 1UL // MAC平台获取最低位，如果是1，就表示tagged pointer
#endif

// 传入指针地址和 _OBJC_TAG_MASK 进行按位与操作，判断是否是 Tagged Pointer
static inline bool 
_objc_isTaggedPointer(const void * _Nullable ptr) 
{
    return ((uintptr_t)ptr & _OBJC_TAG_MASK) == _OBJC_TAG_MASK;
}
```

> ## 相关面试题

```objectivec
// 问题：以下两段代码的运行结果

// 代码1: 运行会crash（EXC_BAD_ACCESS），crash在objc_release
dispatch_queue_t queue = dispatch_get_global_queue(0, 0);
for (int i = 0 ; i<1000; i++) {
    dispatch_async(queue, ^{
        self.name = [NSString stringWithFormat:@"asdfghjklqwe"];
    });
}

// 原因：多条线程有可能同时执行release这一步，所以造成release多次
// 解决方案：
// 1. 可以对属性设置为atomic
// 2. 在外面setName的时候，进行加锁操作
- (void)setName:(NSString *)name {
    if (_name != name) {
        [_name release];
        _name = [name copy];
    }
}

/* ============================================== */

// 代码2: 没问题 
// 原因：@“abc” 内容比较小，是一个tagger pointer，不是OC对象，直接进行指针赋值，不需要进行retain或者release操作
dispatch_queue_t queue = dispatch_get_global_queue(0, 0);
for (int i = 0 ; i<1000; i++) {
    dispatch_async(queue, ^{
        self.name = [NSString stringWithFormat:@"abc"];
    });
}
```

