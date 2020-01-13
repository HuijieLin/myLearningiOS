gmv                          # Load

> ## Catrgory中有load方法吗

有`load`方法

> ### load方法什么时候调用

在runtime加载类和分类的时候调用

> ### load方法能继承吗

* 能继承，一般不会手动调用（主动调用）`load`方法，都是让系统自动调用。
* 如果是子类没有写`load`方法，但是通过子类调用`load`的时候，走的是消息发送机制，所以回到父类里面寻找具体的实现。
* 默认的`load`方法是通过函数地址方式直接调用

> ### load 和 initialize有什么区别

* 调用方式
  * `load`是根据函数地址直接调用
  * `initialize`是通过消息机制`objc_msgSend`调用
* 调用时机
  * load是runtime加载类，分类的时候调用，只会调用一次
  * initialize是类第一次接收到消息的时候调用，每个类只会被initialize一次，但是父类的initialize方法可能会被调用多次
* 调用顺序
  * load
    * 先调用类的load
      * 先编译的类，优先调用load
      * 调用子类的load之前，会先调用父类的load
    * 在调用分类的load
      * 先编译的分类，优先调用load
 * initialize
    * 先初始化父类         
    * 在初始化子类（可能最终调用的还是父类的initialize方法）
    * 直接调用子类的时候，会判断父类是否已经初始化，如果没有就会先调用父类的initialize
    * 如果分类实现了initialize，会覆盖类本身的initialize调用

