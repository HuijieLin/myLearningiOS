> ## 简介

- 在没有tagger pointer之前，NSNumber等小对象需要动态分配内存，维护引用计数，NSNumber指针存储的是堆中NSNumber对象的地址值
- 在64bit开始，iOS引入tagged pointer，用于优化NSNumber，NSData，NSString等小对象的存储（直接存在指针里面）
- 在使用tagger pointer之后，是通过Tag+Data的方式存储
- objc_msgSend可以识别是否tagger pointer，然后直接在指针里面取内容，减少调用开销
- 当指针存不下内容时，才通过动态分配内存的方式来存储数据


> ## 底层判断逻辑


```objectivec

#if TARGET_OS_OSX && __x86_64__
    // Mac 平台
#   define OBJC_MSB_TAGGED_POINTERS 0
#else
    // iOS 平台
#   define OBJC_MSB_TAGGED_POINTERS 1
#endif

#if OBJC_MSB_TAGGED_POINTERS
#   define _OBJC_TAG_MASK (1UL<<63)
#else
#   define _OBJC_TAG_MASK 1UL
#endif

// 传入指针地址和 _OBJC_TAG_MASK 进行按位与操作，判断是否是 Tagged Pointer
static inline bool 
_objc_isTaggedPointer(const void * _Nullable ptr) 
{
    return ((uintptr_t)ptr & _OBJC_TAG_MASK) == _OBJC_TAG_MASK;
}
```
