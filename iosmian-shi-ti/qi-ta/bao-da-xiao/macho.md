> ## Mach-O

探秘Mach-O：[https://juejin.im/post/5ab47ca1518825611a406a39](https://juejin.im/post/5ab47ca1518825611a406a39)
深入剖析Mach-O：[https://satanwoo.github.io/2017/06/13/Macho-1/](https://satanwoo.github.io/2017/06/13/Macho-1/)
dyld源码下载地址：[https://opensource.apple.com/tarballs/dyld/](https://opensource.apple.com/tarballs/dyld/)

> ## 简介

Mach-O是针对不同运行时可执行文件的文件类型
Image：executable、dylib、bundle
Framework：包含Dylib以及资源文件和头文件的文件夹

> ## Mach-O结构

官方描述：https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/MachOTopics/0-Introduction/introduction.html

一个Mach-O文件包含3个主要区域：
- Header：文件类型、目标架构类型等
- Load commands：描述文件在虚拟内存中的逻辑结构、布局
- Raw segment data：在Load commands中定义的Segment的原始数据

> ## Header

> ## Load commands

> ## Raw segment data

> ## dyld和Mach-O

- app的可执行文件、动态库都是有dyld负责加载
- dyld用于加载一下类型的Mach-O文件：
    - MH_EXECUTE：应用的主要二进制
    - MH_DYLIB：动态链接库
    - MH_BUNDLE：不能被链接的Dylib，只能在运行时使用dlopen\(\)加载，可以作为MacOS的插件







