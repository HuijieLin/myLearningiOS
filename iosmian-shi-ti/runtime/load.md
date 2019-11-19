# Load

---

> # Catrgory中有load方法吗

有load方法

> ## load方法什么时候调用

在runtime加载类和分类的时候调用

> ## load方法能继承吗

* 能继承，一般不会手动调用（主动调用）`load`方法，都是让系统自动调用。
* 如果是子类没有写`load`方法，但是通过子类调用`load`的时候，走的是消息发送机制，所以回到父类里面寻找具体的实现。
* 默认的`load`方法是通过内存地址方式直接调用

> ## load 和 initialize有什么区别

* 调用方式
  * load是根据函数地址直接调用
  * initialize是通过消息机制`objc_msgSend`调用




