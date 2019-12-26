> ## LinkMap

它是Xcode生成可执行文件时生成的链接信息文件。 它用来描述可执行文件的构造部分，包括代码段和数据段的分布。

> ## 如何开启LinkMap

* Write Link Map File：设置是否开启LinkMap
* Path to Link Map File：设置LinkMap文件生成路径![](/assets/2019122701.png)

> ## LinkMap结构

**Path**

* 可执行文件路径：
  `# Path: /Users/linhuijie/Library/Developer/Xcode/DerivedData/test-gjocatkmtbmvawcegstrzwyevagk/Build/Products/Debug-iphonesimulator/test.app/test`

**Arch**

* 架构类型
  `# Arch: x86_64`

**Object files**

* 目标文件列表

```c
# Object files:
[  0] linker synthesized
[  1] /Users/linhuijie/Library/Developer/Xcode/DerivedData/test-gjocatkmtbmvawcegstrzwyevagk/Build/Intermediates.noindex/test.build/Debug-iphonesimulator/test.build/test.app-Simulated.xcent
[  2] /Users/linhuijie/Library/Developer/Xcode/DerivedData/test-gjocatkmtbmvawcegstrzwyevagk/Build/Intermediates.noindex/test.build/Debug-iphonesimulator/test.build/Objects-normal/x86_64/ViewController.o
[  3] /Users/linhuijie/Library/Developer/Xcode/DerivedData/test-gjocatkmtbmvawcegstrzwyevagk/Build/Intermediates.noindex/test.build/Debug-iphonesimulator/test.build/Objects-normal/x86_64/baseViewController.o
[  4] /Users/linhuijie/Library/Developer/Xcode/DerivedData/test-gjocatkmtbmvawcegstrzwyevagk/Build/Intermediates.noindex/test.build/Debug-iphonesimulator/test.build/Objects-normal/x86_64/AppDelegate.o
[  5] /Users/linhuijie/Library/Developer/Xcode/DerivedData/test-gjocatkmtbmvawcegstrzwyevagk/Build/Intermediates.noindex/test.build/Debug-iphonesimulator/test.build/Objects-normal/x86_64/NSObject+_1.o
[  6] /Users/linhuijie/Library/Developer/Xcode/DerivedData/test-gjocatkmtbmvawcegstrzwyevagk/Build/Intermediates.noindex/test.build/Debug-iphonesimulator/test.build/Objects-normal/x86_64/main.o
[  7] /Users/linhuijie/Library/Developer/Xcode/DerivedData/test-gjocatkmtbmvawcegstrzwyevagk/Build/Intermediates.noindex/test.build/Debug-iphonesimulator/test.build/Objects-normal/x86_64/NSObject+_2.o
[  8] /Users/linhuijie/Library/Developer/Xcode/DerivedData/test-gjocatkmtbmvawcegstrzwyevagk/Build/Intermediates.noindex/test.build/Debug-iphonesimulator/test.build/Objects-normal/x86_64/SceneDelegate.o
[  9] /Users/linhuijie/Library/Developer/Xcode/DerivedData/test-gjocatkmtbmvawcegstrzwyevagk/Build/Intermediates.noindex/test.build/Debug-iphonesimulator/test.build/Objects-normal/x86_64/blocktest.o
[ 10] /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator13.2.sdk/System/Library/Frameworks//Foundation.framework/Foundation.tbd
[ 11] /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator13.2.sdk/usr/lib/libobjc.tbd
[ 12] /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneSimulator.platform/Developer/SDKs/iPhoneSimulator13.2.sdk/System/Library/Frameworks//UIKit.framework/UIKit.tbd
```

**Sections**

* 描述各个段在最后编译成可执行文件中的偏移位置以及大小

```c
// Address：数据在文件的偏移位置
// Size：占用的大小
// Segment：段类型，代码段还是数据段
// Section：段名称
// -----------------------------
// __text：编译后的程序执行语句
// __data：已经初始化的全局变量和局部静态变量
// __cstring：代码里面的字符串常量
// __bss：没有初始化的全局变量和局部静态变量
// -----------------------------
# Sections:
# Address    Size        Segment    Section
0x100001490    0x00000D1D    __TEXT    __text
0x1000021AE    0x0000006C    __TEXT    __stubs
0x10000221C    0x000000C4    __TEXT    __stub_helper
0x1000022E0    0x00000E42    __TEXT    __objc_methname
0x100003122    0x00000169    __TEXT    __cstring
0x10000328B    0x0000009A    __TEXT    __objc_classname
0x100003325    0x00000B12    __TEXT    __objc_methtype
0x100003E37    0x00000178    __TEXT    __entitlements
0x100003FB0    0x00000048    __TEXT    __unwind_info
0x100004000    0x00000018    __DATA_CONST    __got
0x100004018    0x00000120    __DATA_CONST    __cfstring
0x100004138    0x00000028    __DATA_CONST    __objc_classlist
0x100004160    0x00000008    __DATA_CONST    __objc_catlist
0x100004168    0x00000020    __DATA_CONST    __objc_protolist
0x100004188    0x00000008    __DATA_CONST    __objc_imageinfo
0x100005000    0x00000090    __DATA    __la_symbol_ptr
0x100005090    0x00001870    __DATA    __objc_const
0x100006900    0x00000068    __DATA    __objc_selrefs
0x100006968    0x00000020    __DATA    __objc_classrefs
0x100006988    0x00000010    __DATA    __objc_superrefs
0x100006998    0x00000040    __DATA    __objc_ivar
0x1000069D8    0x00000190    __DATA    __objc_data
0x100006B68    0x00000188    __DATA    __data
```

Symbols

* 每个文件对应的字段位置和占用空间

```c
// Address：数据在文件的偏移位置
// Size：占用的大小
// Fine：Object files的第一列关联，对应所属的文件序号
// name：符号名字
# Symbols:
# Address    Size        File  Name
0x100001490    0x00000090    [  2] -[ViewController viewDidLoad]
0x100001520    0x00000070    [  2] -[ViewController viewDidAppear:]
0x100001590    0x000000D0    [  2] -[ViewController touchesBegan:withEvent:]
0x100001660    0x00000030    [  2] -[ViewController name]
0x100001690    0x00000040    [  2] -[ViewController setName:]
```

Dead Stripped Symbols

* 链接器认为无用的符号，链接的时候不会计记入
* 计算包大小的时候，需要把这块的大小减去

```
# Dead Stripped Symbols:
#            Size        File  Name
<<dead>>     0x00000018    [  2] CIE
<<dead>>     0x0000000C    [  3] literal string: viewDidLoad
<<dead>>     0x00000009    [  3] literal string: setName:
<<dead>>     0x00000018    [  3] literal string: touchesBegan:withEvent:
<<dead>>     0x0000000E    [  3] literal string: .cxx_destruct
<<dead>>     0x00000007    [  3] literal string: button
<<dead>>     0x0000000B    [  3] literal string: setButton:
```



