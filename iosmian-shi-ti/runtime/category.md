> # category

---

[https://tech.meituan.com/2015/03/03/diveintocategory.html](https://tech.meituan.com/2015/03/03/diveintocategory.html)

> ## category里面load方法的调用顺序

1. load方法也是会被拷贝到主类，但是load方法的调用是直接使用函数内存地址的方式`(*load_method)(cls, SEL_load);`而不是使用发送消息`objc_msgSend`的方式。
2. 调用顺序：主类 -&gt; 子类 -&gt; 分类，分类的顺序看编译顺序，也就是`Build Phases -> Compile Sources`的顺序

> ## 打印load方法调用和category的加载

在edit scheme中添加环境变量，如下所示：![](/assets/import2019030601.png)

> ## category如何添加@property

在category中声明@property只会生成setter和getter的方法声明，如果需要增加属性的实现需要借助`objc_setAssociatedObject`和`objc_getAssociatedObject`

> ## category为什么不可以添加实例变量

1. 在运行时内存布局已经固定，如果添加实例变量会破坏类的内部结构。
2. extension在编译时决定，category在运行时决定

> ## category底层逻辑

* category结构体

```
typedef struct category_t {
    const char *name; // 类名
    classref_t cls; // 类
    struct method_list_t *instanceMethods; // 实例方法
    struct method_list_t *classMethods; // 类方法
    struct protocol_list_t *protocols; // 协议列表
    struct property_list_t *instanceProperties; // 属性
} category_t;
```

1. 编译阶段把每个category封装成一个category\_t结构体放在一个数组里面，这个数组保存在DATA段下面的objc\_catlist section
2. category附加到类的时候是在map\_images发生，在new-abi的标准下，mapimages最终会调用到\_read\_image方法。
3. 在\_read\_image方法里面拿到objc\_catlist中编译期保存的category\_t数据，然后：
   1. 把实例方法、协议、属性添加到类上
   2. 把类方法、协议添加到metaclass上
4. category方法不会覆盖主类的方法，只是附加到主类的方法上，在搜索方法实现的时候，会优先拿到分类的方法实现，所以有一种被覆盖的错觉。



