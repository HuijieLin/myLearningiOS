> ## 包大小

---

1. 头条包大小瘦身：[https://techblog.toutiao.com/2018/06/04/gan-huo-jin-ri-tou-tiao-iosduan-an-zhuang-bao-da-xiao-you-hua-si-lu-yu-shi-jian/](https://techblog.toutiao.com/2018/06/04/gan-huo-jin-ri-tou-tiao-iosduan-an-zhuang-bao-da-xiao-you-hua-si-lu-yu-shi-jian/)

2. 如何精简SDK包大小：[https://wellphone.me/post/2018/how\_reduce\_ios\_sdk\_file\_size/](https://wellphone.me/post/2018/how_reduce_ios_sdk_file_size/)

3. iOS 瘦身之道：[https://juejin.im/post/5cdd27d4f265da036902bda5](https://juejin.im/post/5cdd27d4f265da036902bda5)

> ## APP Thining

* iOS9以后引入的优化
* 主要包括：
  * Slicing
    * 仅向设备传输与之相关的资源，取决于屏幕分辨率、系统架构等
    * 创建、分发不同变体用来适应不同目标设备的过程
  * Bitcode
    * 是一种程序中间码，开启bitcode配置的程序在App Store Connect阶段会被重新编译和链接，对可执行文件进行优化，这部分是在服务进行的
  * * 开启步骤：Build Setting -&gt; Enable Bitcode -&gt; YES
    * Xcode7之后默认YES，对于iOS Bitcode是可选的，watchOS、tvOS是必选的
    * 作用：App Store会再按需将这个bitcode编译进32/64位的可执行文件中，也可以理解为对具体架构的拆分
    * 开启Bitcode注意事项：
      * 依赖的动态库、静态库、第三方库都需要开启支持
      * 因为开始Bitcode的可执行文件在Apple的服务端进行了重新编译，所以对应的dsym文件需要在App Store Connect中获取
  * On-Demand Resources
    * 一部分图片资源不随着App的安装而下载，直到用户进入某个页面时财进行下载对应的文件
    * 应用场景：相机应用的贴纸或者滤镜、关卡游戏等

> ## 包体积

* 资源文件
  * 包括：图片、内置素材、多语言、字体、音视频等
* 资源瘦身方式：
  * 无用资源的删除
    * 可以借鉴：LSUnusedResources
    * 处理步骤：
      * 搜索app包中的所有资源问题，例如find命令
      * 通过正则搜索出代码中使用到的资源名称
      * 取出两者的差集就是无用的文件资源了
    * 图片资源的压缩
      * 可以使用WebP，使用Google的开源工具cwebp
      * 压缩完的图片还需要使用libwebp进行解析
      * 缺点：
        * WebP在CPU的消耗和解码时间会比PNG多很多（2倍）
  * 重复资源的删除
    * 指文件名不一样，内容一样的文件
    * 可以借助开源工具fdupes，检验资源文件的md5
    * 文件对比顺序：
      * 大小对比 -&gt; 部分MD5签名对比 -&gt; 完整MD5签名对比 -&gt; 逐个字节对比
  * 大文件压缩
    * 工具：ImageOptim
    * Xcode build setting：
      * Compress PNG FIles
        * 打包的时候使用pngcrush进行无损压缩，
        * 但是有一些图片压缩之后反而会变大，可以对单个图片进行关闭压缩，Type设置为Data

        ![](/assets/2019122601.png)
      * Remove Text Medadata From PNG Files
  * 图片管理方式规范



