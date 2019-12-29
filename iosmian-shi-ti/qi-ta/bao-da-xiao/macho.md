> ## Mach-O

探秘Mach-O：[https://juejin.im/post/5ab47ca1518825611a406a39](https://juejin.im/post/5ab47ca1518825611a406a39)  
深入剖析Mach-O：[https://satanwoo.github.io/2017/06/13/Macho-1/](https://satanwoo.github.io/2017/06/13/Macho-1/)  
dyld源码下载地址：[https://opensource.apple.com/tarballs/dyld/](https://opensource.apple.com/tarballs/dyld/)

探秘 Mach-O 文件：[https://juejin.im/post/5ab47ca1518825611a406a39\#heading-7](https://juejin.im/post/5ab47ca1518825611a406a39#heading-7)

> ## 简介

Mach-O是针对不同运行时可执行文件的文件类型  
image：executable、dylib、bundle  
Framework：包含Dylib以及资源文件和头文件的文件夹

> ## Mach-O结构

官方描述：[https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/MachOTopics/0-Introduction/introduction.html](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/MachOTopics/0-Introduction/introduction.html)  
文件定义：[https://opensource.apple.com/source/xnu/xnu-1456.1.26/EXTERNAL\_HEADERS/mach-o/loader.h](https://opensource.apple.com/source/xnu/xnu-1456.1.26/EXTERNAL_HEADERS/mach-o/loader.h)

**一个Mach-O文件包含3个主要区域：**

* Header：文件类型、目标架构类型等
* Load commands：描述文件在虚拟内存中的逻辑结构、布局
* Raw segment data：在Load commands中定义的Segment的原始数据

> ## Header

```
// 32位
struct mach_header {
    uint32_t    magic;        /* mach magic number identifier */
    cpu_type_t    cputype;    /* cpu specifier */
    cpu_subtype_t    cpusubtype;    /* machine specifier */
    uint32_t    filetype;    /* type of file */
    uint32_t    ncmds;        /* number of load commands */
    uint32_t    sizeofcmds;    /* the size of all the load commands */
    uint32_t    flags;        /* flags */
};

// 64位
struct mach_header_64 {
    uint32_t    magic;        /* mach magic number identifier */
    cpu_type_t    cputype;    /* cpu specifier */
    cpu_subtype_t    cpusubtype;    /* machine specifier */
    uint32_t    filetype;    /* type of file */
    uint32_t    ncmds;        /* number of load commands */
    uint32_t    sizeofcmds;    /* the size of all the load commands */
    uint32_t    flags;        /* flags */
    uint32_t    reserved;    /* reserved */
}

// magic: 用于标识当前设备的是大端序还是小端序。如果是0xfeedfacf(MH_MAGIC_64)就是大端序，而0xcffaedfe(MH_CIGAM_64)是小端序，iOS系统上是小端序
// cputype: 标识CPU的架构，比如ARM，X86，i386等等，进行了宏观划分
// cpusubtype: 具体的CPU类型，区分不同版本的处理器, 比如arm64、armv7
// filetype: 文件类型，比如可执行文件、库文件、Dsym文件
// ncmds: 有几个LoadCommands，每个LoadCommands代表了一种Segment的加载方式
// sizeofcmds: LoadCommand的大小，主要用于划分Mach-O文件的‘区域’
// flags: 标记了dyld过程中的参数
// reserved: 保留字段
```

**常见的文件类型（filetype）：**

* MH\_OBJECT
  * 目标文件（.o）
  * 静态库文件（.a），静态库是由多个.o文件合并在一起
* MH\_EXECUTE
  * 可执行文件
* MH\_DYLIB
  * 动态库文件（.dylib, .framework/xx）
* MH\_DYLINKER
  * 动态连接器（dyld）
* MH\_DSYM
  * 存储二进制文件符号信息的文件（.dSYM文件）

> ## Load commands

```c
struct load_command {
    uint32_t cmd;        // load command类型
    uint32_t cmdsize;    // load command占用的大小，主要是用于计算出下一个command的偏移量
};

// 部分load command类型
LC_SEGMENT、LC_SEGMENT_64：将 segment 映射到进程的内存空间，
LC_UUID：二进制文件 id，与符号表 uuid 对应，可用作符号表匹配，
LC_LOAD_DYLINKER：启动动态加载器，
LC_SYMTAB：描述在 __LINKEDIT 段的哪找字符串表、符号表，
LC_CODE_SIGNATURE：代码签名等
```

> ## Raw segment data

---

> ### Segment

```
// 32位
struct segment_command { /* for 32-bit architectures */
    uint32_t    cmd;        // load command类型
    uint32_t    cmdsize;    // load command的大小
    char        segname[16];    // segment的名称
    uint32_t    vmaddr;        // 未偏移的虚拟内存地址
    uint32_t    vmsize;        // 虚拟内存的段大小
    uint32_t    fileoff;    // 段在文件的偏移
    uint32_t    filesize;    // 段在文件的大小
    vm_prot_t    maxprot;    
    vm_prot_t    initprot;    
    uint32_t    nsects;        // 段中有多少个section
    uint32_t    flags;        
};

// 64位
struct segment_command_64 { /* for 64-bit architectures */
    uint32_t    cmd;        /* LC_SEGMENT_64 */
    uint32_t    cmdsize;    /* includes sizeof section_64 structs */
    char        segname[16];    /* segment name */
    uint64_t    vmaddr;        /* memory address of this segment */
    uint64_t    vmsize;        /* memory size of this segment */
    uint64_t    fileoff;    /* file offset of this segment */
    uint64_t    filesize;    /* amount to map from the file */
    vm_prot_t    maxprot;    /* maximum VM protection */
    vm_prot_t    initprot;    /* initial VM protection */
    uint32_t    nsects;        /* number of sections in segment */
    uint32_t    flags;        /* flags */
}
```

**segname定义：**

* \_\_TEXT：代码段/只读数据段
* \_\_PAGEZERO：Catch访问NULL指针的非法操作的段
* \_\_DATA：数据段
* \_\_LINKEDIT：包含需要被动态链接器使用的信息，包括符号表、字符串表、重定位项表等。

> ### Section

```
// 32位
struct section { /* for 32-bit architectures */
    char        sectname[16];    // 所在段（segment）的名称
    char        segname[16];    // section名称
    uint32_t    addr;        // 在内存的起始位置
    uint32_t    size;        // section的大小
    uint32_t    offset;        // section的文件偏移
    uint32_t    align;        // 字节大小对其
    uint32_t    reloff;        // 重定位入口的文件偏移
    uint32_t    nreloc;        // 需要重定位的入口数量
    uint32_t    flags;        
    uint32_t    reserved1;    
    uint32_t    reserved2;    
};

// 64位
struct section_64 { /* for 64-bit architectures */
    char        sectname[16];    /* name of this section */
    char        segname[16];    /* segment this section goes in */
    uint64_t    addr;        /* memory address of this section */
    uint64_t    size;        /* size in bytes of this section */
    uint32_t    offset;        /* file offset of this section */
    uint32_t    align;        /* section alignment (power of 2) */
    uint32_t    reloff;        /* file offset of relocation entries */
    uint32_t    nreloc;        /* number of relocation entries */
    uint32_t    flags;        /* flags (section type and attributes)*/
    uint32_t    reserved1;    /* reserved (for offset or index) */
    uint32_t    reserved2;    /* reserved (for count or sizeof) */
    uint32_t    reserved3;    /* reserved */
};
```

**常见的section名称**

* TEXT.text：只有可执行的机器码
* TEXT.cstring：去重后的C字符串
* TEXT.const：初始化过的常量
* TEXT.stubs：符号桩。本质上是一小段会直接跳入lazybinding的表对应项指针指向的地址的代码。
* TEXT.stub\_helper：辅助函数。上述提到的lazybinding的表中对应项的指针在没有找到真正的符号地址的时候，都指向这。
* TEXT.unwind\_info：用于存储处理异常情况信息
* TEXT.eh\_frame：调试辅助信息
* DATA.data：初始化过的可变的数据
* DATA.nl\_symbol\_ptr：非lazy-binding的指针表，每个表项中的指针都指向一个在装载过程中，被动态链机器搜索完成的符号
* DATA.la\_symbol\_ptr：lazy-binding的指针表，每个表项中的指针一开始指向stub\_helper
* DATA.const：没有初始化过的常量
* DATA.mod\_init\_func：初始化函数，在main之前调用
* DATA.mod\_term\_func：终止函数，在main返回之后调用
* DATA.bss：没有初始化的静态变量
* DATA.common：没有初始化过的符号声明

**la\_symbol\_ptr 和 nl\_symbol\_ptr**

* la\_symbol\_ptr 中的数据被第一次调用时会通过 dyld\_stub\_binder 进行相关绑定，而 nl\_symbol\_ptr 中的数据就是在动态库绑定时进行加载
* la\_symbol\_ptr 中的数据在初始状态都被 bind 成  stub\_helper，接着 dyld\_stub\_binder 会加载相应的动态链接库，执行具体的函数实现，此时 la\_symbol\_ptr 也获取到了函数的真实地址，完成了一次近似懒加载的过程

> ## dyld和Mach-O

* app的可执行文件、动态库都是有dyld负责加载
* dyld用于加载一下类型的Mach-O文件：
  * MH\_EXECUTE：应用的主要二进制
  * MH\_DYLIB：动态链接库
  * MH\_BUNDLE：不能被链接的Dylib，只能在运行时使用dlopen\(\)加载，可以作为MacOS的插件



