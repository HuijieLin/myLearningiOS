> ## 简介

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
