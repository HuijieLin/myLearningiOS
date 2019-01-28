# Runtime

---

> ## 相关链接

[http://yulingtianxia.com/blog/2014/11/05/objective-c-runtime/](#httpyulingtianxiacomblog20141105objective-c-runtime)

> ## 类的数据结构

```objectivec
struct objc_class {
Class isa OBJC_ISA_AVAILABILITY;

#if !__OBJC2__
    Class super_class OBJC2_UNAVAILABLE; // 父类。
    const char *name OBJC2_UNAVAILABLE; // 类名。
    long version OBJC2_UNAVAILABLE; // 类的版本信息。
    long info OBJC2_UNAVAILABLE; // 类信息，供运行时使用的一些位标识。
    long instance_size OBJC2_UNAVAILABLE; // 类的实例变量大小。
    struct objc_ivar_list *ivars OBJC2_UNAVAILABLE; // 类的成员变量列表。
    struct objc_method_list **methodLists OBJC2_UNAVAILABLE; // 方法定义列表。
    struct objc_cache *cache OBJC2_UNAVAILABLE; // 方法缓存。
    struct objc_protocol_list *protocols OBJC2_UNAVAILABLE; // 协议列表。
#endif

} OBJC2_UNAVAILABLE;
```

> ## 类的实例的数据结构

```objectivec
// Represents an instance of a class.
struct objc_object {
    Class isa  OBJC_ISA_AVAILABILITY;
};
```

> ## 成员变量的数据结构

```objectivec
struct objc_ivar {
    char *ivar_name OBJC2_UNAVAILABLE; // 变量名。
    char *ivar_type OBJC2_UNAVAILABLE; // 变量类型。
    int ivar_offset OBJC2_UNAVAILABLE; // 基地址偏移量，在对成员变量寻址时使用。
#ifdef __LP64__
    int space OBJC2_UNAVAILABLE;
#endif
}
```

> ## 属性的数据结构

```objectivec
/// Defines a property attribute
typedef struct {
    const char *name; /**< The name of the attribute */
    const char *value; /**< The value of the attribute (usually empty) */
} objc_property_attribute_t;
```

> ## 方法的数据结构

```objectivec
typedef struct objc_method *Method;

struct objc_method {
    SEL method_name OBJC2_UNAVAILABLE;
    char *method_types OBJC2_UNAVAILABLE;
    IMP method_imp OBJC2_UNAVAILABLE;
} OBJC2_UNAVAILABLE;
```

> ## 消息调用流程

```objectivec
objc_msgSend(receiver, selector, arg1, arg2, ...)
```

`objc_msgSend(obj,foo)`解释：

将foo消息发送给obj对象，首先在 Class 中的缓存查找 IMP （没缓存则初始化缓存），如果没找到，则向父类的 Class 查找。如果一直查找到根类仍旧没有实现，则用\_objc\_msgForward函数指针代替 IMP 。最后执行这个 IMP ，走消息转发流程

> ## 消息转发流程

![](/assets/2019011002.png)

* 动态方法解析

`resolveInstanceMethod` 和 `resolveClassMethod`

* 快速转发

`forwardingTargetForSelector`

* 完整转发

`methodSignatureForSelector` 和 `forwardInvocation`

