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
  - 建议：保持默认，不做修改
- Asset Catalog Compiler
  - optimization设置为space，可以减少包大小
  - 建议：保持默认，不做修改
- Dead Code Stripping
  - 删除静态链接的可执行文件中未应用的代码，但是oc是动态语言，所以无法判定
  - 建议：保持默认，不做修改
- Apple Clang - Code Generation
- Swift Compiler - Code Generation
- Strip Symbol Information
  - Deployment Postprocessing 
  - Strip Linked Product 
  - Strip Debug Symbols During Copy 
  - Symbols hidden by default
  - 设置为YES
    - 可以去掉不必要的符号信息，但是只能使用dSYM来进行符号化
  - 建议：Release设置为YES，Debug设置为NO
- Exceptions
  - Enable C++ Exceptions = NO
  - Enable Objective-C Exceptions = NO
  - Other C Flags添加-fno-exceptions
  - 对于某些文件单独支持异常，编译选项加上-fexceptions
  - 带来问题：假如ABC三个文件，AC文件支持了异常，B不支持，如果C抛了异常，在模拟器下A还是能捕获异常不至于Crash，但真机下捕获不了
  - 建议：保持默认，不做修改
- Link-Time Optimization
  - 用于再link中间代码时，对全局代码进行优化
  - 包括：
    - 多余代码取出
    - 跨过程优化
    - 内敛优化
  - 建议：开启 
- Make Strings Read-Only
  - 建议：YES
   
> ## 代码瘦身

- 基于clang扫描
  - 基于clang AST，追溯到函数的调用层级，记录所有定义的方法和所有调用的方法，取差集
- 基于可执行文件扫描（linkmap结合Mach-O）
  - objc_msgSend在Mach-O文件里面是通过_objc_selrefs这个section来获取selector这个参数的
  - _objc_selrefs记录是调用的方法
  - _objc_classrefs记录被调用过的类
  - __objc_classlist记录所有的类
  - _objc_superrefs记录调用过的super的类（继承关系）
  - 没用的方法检测（类也类似）：
    1. 使用otool获取所有的方法：otool -oV 二进制路径
    2. 使用otool获取被调用的方法：otool -v -s __DATA __objc_selrefs 二进制路径
    3. 两者的差值就是无用的方法
- 查找重复代码
  - 第三方工具simian
- 运行时检测类是否初始化过
  - runtime判断类是否初始化过
  
  ```objectivec
  // isInitialized 的结果会保存到元类的 class_rw_t 结构体的 flags 信息里， flags 的 1<<29 位记录的就是这个类是否初始化了的信息
  #define RW_INITIALIZED (1<<29)
  bool isInitialized() {
     return getMeta()->data()->flags & RW_INITIALIZED;
  }
  
  // 其他位的信息
  // 类的方法列表已修复
  #define RW_METHODIZED         (1<<30)
  
  // 类已经初始化了
  #define RW_INITIALIZED        (1<<29)
  
  // 类在初始化过程中
  #define RW_INITIALIZING       (1<<28)
  
  // class_rw_t->ro 是 class_ro_t 的堆副本
  #define RW_COPIED_RO          (1<<27)
  
  // 类分配了内存，但没有注册
  #define RW_CONSTRUCTING       (1<<26)
  
  // 类分配了内存也注册了
  #define RW_CONSTRUCTED        (1<<25)
  
  // GC：class 有不安全的 finalize 方法
  #define RW_FINALIZE_ON_MAIN_THREAD (1<<24)
  
  // 类的 +load 被调用了
  #define RW_LOADED             (1<<23)
  ```

> ## App Extension

App Extension的占用都是放在Plugin文件夹里面，它是独立打包，然后在拷贝到target app Bundle中。有两个注意点：
- 如果App Extension依赖了第三方静态库，同时主工程也依赖了同样的静态库，最终App包中可能会包含两份三方静态库的体积
- 动态库是共享的，所以可以修改动态库的加载路径Runpath Search Paths和主工程一致，就可以共享动态库

> ## 静态库瘦身

- 通过lipo命令进行查看支持架构，然后进行静态库拆拆分
- 如果使用cocoapods管理可以使用两份Podfile文件，一份包含所有的架构，一份只包含Release版本使用的架构
- dSYM文件（符号表文件）
  - 是从Mach-O文件里面抽取调式信息而得到的文件目录，实际用于保存调试信息的是dwarf文件
  - 可以通过工具dsymutil生成
- dwarf文件（DebuggingWith Arbitrary Record Formats）
  - 是ELF和Mach-O等文件格式中用来存储和处理调试信息的标准格式
  - dSYM文件中真正保存符号表数据的是dwarf文件
  - dwarf文件中不同的数据都保存在相应的section中


