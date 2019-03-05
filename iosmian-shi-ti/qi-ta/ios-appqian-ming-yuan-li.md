> # iOS APP签名原理

[https://wereadteam.github.io/2017/03/13/Signature/](https://wereadteam.github.io/2017/03/13/Signature/)

> ## 非对称加密

[http://www.ruanyifeng.com/blog/2013/06/rsa\_algorithm\_part\_one.html](http://www.ruanyifeng.com/blog/2013/06/rsa_algorithm_part_one.html)

[http://www.ruanyifeng.com/blog/2013/07/rsa\_algorithm\_part\_two.html](http://www.ruanyifeng.com/blog/2013/07/rsa_algorithm_part_two.html)

> 大致流程如下：

1. 选出两个质数`p`和`q`，相乘得出一个大整数`n`
2. 在`1-n`中随机选一个质数`e`
3. 通过一些数学计算出`d`，需要满足：
   1. 通过`n`和`e`这两个数据一组数据进行数学运算后，可以通过`n`和`d`去反解运算，反过来也行
   2. 如果只知道`n`和`e`，要推导出`d`，需要知道`p`和`q`，也就是需要把`n`进行因数分解

上述的`（n，e）`这两个数据组成公钥，`（n，d）`这两个数据组成私钥。非对称加密的原理导致加密的数据不能太大

，不能大于上述`n`的位数，也就是一般不能大于1024位/2048位。

