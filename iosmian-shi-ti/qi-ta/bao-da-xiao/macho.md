> ## Mach-O

探秘Mach-O：[https://juejin.im/post/5ab47ca1518825611a406a39](https://juejin.im/post/5ab47ca1518825611a406a39)
深入剖析Mach-O：[https://satanwoo.github.io/2017/06/13/Macho-1/](https://satanwoo.github.io/2017/06/13/Macho-1/)
dyld源码下载地址：[https://opensource.apple.com/tarballs/dyld/](https://opensource.apple.com/tarballs/dyld/)

> ## 简介

Mach-O是针对不同运行时可执行文件的文件类型
image：executable、dylib、bundle
Framework：包含Dylib以及资源文件和头文件的文件夹

> ## Mach-O结构

官方描述：https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/MachOTopics/0-Introduction/introduction.html

一个Mach-O文件包含3个主要区域：
- Header：文件类型、目标架构类型等
- Load commands：描述文件在虚拟内存中的逻辑结构、布局
- Raw segment data：在Load commands中定义的Segment的原始数据

> ## Header

```
// 32位
struct mach_header {
	uint32_t	magic;		/* mach magic number identifier */
	cpu_type_t	cputype;	/* cpu specifier */
	cpu_subtype_t	cpusubtype;	/* machine specifier */
	uint32_t	filetype;	/* type of file */
	uint32_t	ncmds;		/* number of load commands */
	uint32_t	sizeofcmds;	/* the size of all the load commands */
	uint32_t	flags;		/* flags */
};

// 64位
struct mach_header_64 {
	uint32_t	magic;		/* mach magic number identifier */
	cpu_type_t	cputype;	/* cpu specifier */
	cpu_subtype_t	cpusubtype;	/* machine specifier */
	uint32_t	filetype;	/* type of file */
	uint32_t	ncmds;		/* number of load commands */
	uint32_t	sizeofcmds;	/* the size of all the load commands */
	uint32_t	flags;		/* flags */
	uint32_t	reserved;	/* reserved */
}

// magic: 用于标识当前设备的是大端序还是小端序。如果是0xfeedfacf(MH_MAGIC_64)就是大端序，而0xcffaedfe(MH_CIGAM_64)是小端序，iOS系统上是小端序
// cputype: 标识CPU的架构，比如ARM，X86，i386等等，进行了宏观划分
// cpusubtype: 具体的CPU类型，区分不同版本的处理器, 比如arm64、armv7
// filetype: 
// ncmds: 
// sizeofcmds: 
// flags: 
// reserved: 
```

> ## Load commands

> ## Raw segment data

> ## dyld和Mach-O

- app的可执行文件、动态库都是有dyld负责加载
- dyld用于加载一下类型的Mach-O文件：
    - MH_EXECUTE：应用的主要二进制
    - MH_DYLIB：动态链接库
    - MH_BUNDLE：不能被链接的Dylib，只能在运行时使用dlopen\(\)加载，可以作为MacOS的插件







