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

  * **无用资源的删除**
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
  * **重复资源的删除**
    * 指文件名不一样，内容一样的文件
    * 可以借助开源工具fdupes，检验资源文件的md5
    * 文件对比顺序：
      * 大小对比 -&gt; 部分MD5签名对比 -&gt; 完整MD5签名对比 -&gt; 逐个字节对比
  * **大文件压缩**

    * 工具：ImageOptim
    * Xcode build setting：

      * Compress PNG FIles

        * 打包的时候使用pngcrush进行无损压缩，
        * 但是有一些图片压缩之后反而会变大，可以对单个图片进行关闭压缩，Type设置为Data

        ![](/assets/2019122601.png)

      * Remove Text Medadata From PNG Files

        * 移除PNG资源的文本字符，包括：图像名称、创建时间、作者、版权、注视等

  * **图片管理方式规范**

    * Asset Catlog：
      * 工程中用到Asset Catlog管理图片，最终会输出到Asset.car内，不再Asset.car内的都都归Bundle管理
      * xcassets里面的2x、3x图片会更怒具体的设备分发，不会同时包含，Bundle不会
      * xcassets内可以对图片进行Slicing，即裁剪、拉伸，Bundle不支持
      * Bundle内支持多语言，Image.xcassets不支持
      * 使用ImageNamed创建的UIimage（解码后的Image Buffer）会被加入到NSCache中，收到内存警告才会释放不适用的UIImage
      * 使用imageWithContentOfFile会每次重新申请内存，不会缓存
      * 所以常用的图片适合放在xcassets管理，大文件适合放在Bundle中
      * 放在xcassets的图片不要额外压缩，有时候Compress PNG Files二次压缩会导致图片变大
    * CocoPods中的资源引用方式：
      * 也可以使用Asset Catlog进行管理
      * resource\_bundles
        * 保存在各自的Bundle中
        * 获取资源的方式需要指定Bundle
        * podspec文件描述：
        * ```
          s.resource_bundles = {
              'Huijie' => ['Huijie/Assets/*.xcassets']
          }
          ```
      * resource
        * 资源会合并到main Bundle，可能会有文件名重复
    * 对于单色的图标可以使用尺量图

> ## 编译选项

编译选项的解释：https://gist.github.com/NSExceptional/2cd98300f7297b75d7b2d929effa2f15

- Generate Debug Symbols
  - 调试符号是在编译时形成的
  - 当设置为YES
    - 每个源文件在编译成.o文件时，编译参数多了\-g和\-gmodules参数，打包会生成symbols文件
  - 当设置为NO
    - 不会生成symbol文件，可以减少ipa大小，但是会影响crash的定位
  - 建议：保持默认开启，不做修改
- Asset Catalog Compiler
- Dead Code Stripping
- Apple Clang - Code Generation
- Swift Compiler - Code Generation
- Strip Symbol Information
  - Deployment Postprocessing 
  - Strip Linked Product 
  - Strip Debug Symbols During Copy 
  - Symbols hidden by default
- Exceptions
- Link-Time Optimization


