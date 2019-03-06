> ## category

[https://tech.meituan.com/2015/03/03/diveintocategory.html](https://tech.meituan.com/2015/03/03/diveintocategory.html)

> ## category里面load方法的调用顺序

1. load方法也是会被拷贝到主类，但是load方法的调用是直接使用函数内存地址的方式`(*load_method)(cls, SEL_load);`而不是使用发送消息`objc_msgSend`的方式。
2. 调用顺序：主类 -&gt; 子类 -&gt; 分类，分类的顺序看编译顺序，也就是`Build Phases -> Compile Sources`的顺序

> ## category如何添加@property

在category中声明@property只会生成setter和getter的方法声明，如果需要增加属性的实现需要借助`objc_setAssociatedObject`和`objc_getAssociatedObject`

> category底层逻辑



