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

> 上述描述转换成日常理解

1. 在keychain里的『从证书颁发机构请求证书』，在这里就生成一对公密钥，`本地公钥 = CertificateSigningRequest`，本地私钥保存在mac中。
2. 苹果处理
3. 把`CertificateSigningRequest`文件传到苹果后台合成证书，并且下载到本地。这时候本地有两个证书，第一个是第一步生成的，另外一个是刚刚下载的，同时keychain会把两个证书关联起来，因为它们的公私钥是对应的。在xcode选择下载下来的证书去打包的时候，实际上会找到keychain对应的私钥去验证签名。这个私钥只有这台mac才有，如果其他mac也需要编译怎么办？可以把当前mac的私钥导出，也就是`p12`文件给另外一台mac安装。
4. 这一步在苹果网站操作，配置APPID/权限/设备等，最后下载`Provisioning Profile`文件
5. xCode会通过**第③步**下载会的证书（存着公钥），找到本地对应的私钥（第一步生成的），用本地私钥去签名APP，并且把`Provisioning Profile`文件命名为`embedded.mobileprovision`一起打包进去。这时候APP签名数据保存分两部分，mach-o可执行文件会把签名直接写入这个文件里，其他资源文件则保存在`_CodeSignature`目录下
6. 苹果处理
7. 苹果处理

> 关键字介绍

1. 证书：内容是公钥或私钥，由其他机构对其签名组成的数据包。
2. Entitlements：包含了 App 权限开关列表。
3. CertificateSigningRequest：本地公钥。
4. p12：本地私钥，可以导入到其他电脑。
5. Provisioning Profile：包含了 证书 / Entitlements 等数据，并由苹果后台私钥签名的数据包。

> 备注

AppStore 的签名验证方式有些不一样，前面我们说到最简单的签名方式，苹果在后台直接用私钥签名 App 就可以了，实际上苹果确实是这样做的，如果去下载一个 AppStore 的安装包，会发现它里面是没有`embedded.mobileprovision`文件的，也就是它安装和启动的流程是不依赖这个文件。

