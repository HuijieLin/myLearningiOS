> # iOS APP签名原理

[https://wereadteam.github.io/2017/03/13/Signature/](https://wereadteam.github.io/2017/03/13/Signature/)

> ## 非对称加密

[http://www.ruanyifeng.com/blog/2013/06/rsa\_algorithm\_part\_one.html](http://www.ruanyifeng.com/blog/2013/06/rsa_algorithm_part_one.html)

[http://www.ruanyifeng.com/blog/2013/07/rsa\_algorithm\_part\_two.html](http://www.ruanyifeng.com/blog/2013/07/rsa_algorithm_part_two.html)

* 大致流程如下：

* 选出两个质数`p`和`q`，相乘得出一个大整数`n`

* 在`1-n`中随机选一个质数`e`
* 通过一些数学计算出`d`，需要满足：
  1. 通过`n`和`e`这两个数据一组数据进行数学运算后，可以通过`n`和`d`去反解运算，反过来也行
  2. 如果只知道`n`和`e`，要推导出`d`，需要知道`p`和`q`，也就是需要把`n`进行因数分解

上述的`（n，e）`这两个数据组成公钥，`（n，d）`这两个数据组成私钥。非对称加密的原理导致加密的数据不能太大

，不能大于上述`n`的位数，也就是一般不能大于1024位/2048位。

> ## 数字签名

> appstore签名

在appstore用的是双层签名，流程如下：

1. 苹果官方生成一对公密钥，公钥内置在iOS当中，私钥在苹果的后台保存。
2. 当上传APP到Appstore的时候，苹果后来用私钥对APP数据进行签名。
3. 当APP下载到iOS设备上时，用iOS内置的公钥进行签名验证。
4. 若签名正确，就代表这个APP是可信任的。

> 日常开发安装包签名

1. mac本地生成一对公密钥，称为本地公钥和本地私钥
2. 苹果也有一套自己的公密钥，公钥在内置在iOS，私钥在苹果的服务器，称为苹果公钥和苹果私钥
3. 把本地的公钥上传到苹果的后台，



