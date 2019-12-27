> ## Mach-O

探秘Mach-O：[https://juejin.im/post/5ab47ca1518825611a406a39](https://juejin.im/post/5ab47ca1518825611a406a39)

深入剖析Mach-O：[https://satanwoo.github.io/2017/06/13/Macho-1/](https://satanwoo.github.io/2017/06/13/Macho-1/)

dyld源码下载地址：[https://opensource.apple.com/tarballs/dyld/](https://opensource.apple.com/tarballs/dyld/)

Mach-O是针对不同运行时可执行文件的文件类型

文件类型：

* Executable：应用的主要二进制
* Dylib：动态链接库
* Bundles：不能被链接的Dylib，只能在运行时使用dlopen\(\)加载，可以作为MacOS的插件

Image：executable、dylib、bundle

Framework：包含Dylib以及资源文件和头文件的文件夹

> ## Mach-O结构

官方描述：https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/MachOTopics/0-Introduction/introduction.html

一个Mach-O文件包含3个主要区域：
- Header
- Load commands
- Raw segment data

**Header**

**Load commands**

**Raw segment data**







