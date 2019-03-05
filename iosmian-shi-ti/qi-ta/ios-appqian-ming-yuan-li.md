> # iOS APP签名原理

[https://wereadteam.github.io/2017/03/13/Signature/](https://wereadteam.github.io/2017/03/13/Signature/)

> ## 非对称加密

---

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

---

> appstore签名

在appstore用的是双层签名，流程如下：

1. 苹果官方生成一对公密钥，公钥内置在iOS当中，私钥在苹果的后台保存。
2. 当上传APP到Appstore的时候，苹果后来用私钥对APP数据进行签名。
3. 当APP下载到iOS设备上时，用iOS内置的公钥进行签名验证。
4. 若签名正确，就代表这个APP是可信任的。

> 日常开发安装包签名

1. mac本地生成一对公密钥，称为`本地公钥`和`本地私钥`。
2. 苹果也有一套自己的公密钥，公钥在内置在iOS，私钥在苹果的服务器，称为`苹果公钥`和`苹果私钥`。
3. 把`本地公钥`上传到苹果的后台，`苹果私钥`对`本地公钥`进行签名，得到一份数据包含`本地公钥`和签名，我们把这个称之为证书。
4. 在苹果后台申请AppID，配置好设备ID列表和APP可使用的权限，在加上**第③步**生成的证书，组成的数据用`苹果私钥`进行签名，然后把数据和签名组成一个`Provisioning Profile`文件，下载到mac本地。
5. 在开发的时，编译完一个APP后，用`本地私钥`对APP进行签名，同时把**第④步**得到的`Provisioning Profile`文件打到APP包里面，文件名为`embedded.mobileprovision`，然后把app安装到手机上。
6. 在安装时，iOS系统取得证书，通过`苹果公钥`去验证APP里面的`embedded.mobileprovision`数字签名是否正确，里面的证书签名也会在验一遍。
7. 验证完`embedded.mobileprovision`里面的数据都是经过苹果授权之后，就可以取出里面的数据做其他验证，包括用`本地公钥`验证APP签名，验证设备ID是否在ID列表上，AppID是否对应得上，授权开关是否跟APP里的Entitlements吻合等。



