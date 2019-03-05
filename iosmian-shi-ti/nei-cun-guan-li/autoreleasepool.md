> ## Autorelease对象什么时候释放

[http://blog.sunnyxx.com/2014/10/15/behind-autorelease/](http://blog.sunnyxx.com/2014/10/15/behind-autorelease/)

| 添加AutoreleasePool类型 | 释放时机 |
| :--- | :--- |
| 系统默认的AutoreleasePool | 在每个runloop迭代结束的时候释放 |
| 手动添加的AutoreleasePool | 在超出作用域的时候释放（也就是大括号外面） |



