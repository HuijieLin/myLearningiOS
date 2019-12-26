> ## LinkMap

它是Xcode生成可执行文件时生成的链接信息文件。 它用来描述可执行文件的构造部分，包括代码段和数据段的分布。

> ## 如何开启LinkMap

* Write Link Map File：设置是否开启LinkMap
* Path to Link Map File：设置LinkMap文件生成路径![](/assets/2019122701.png)

> ## LinkMap结构

Path
- 可执行文件路径：
`# Path: /Users/linhuijie/Library/Developer/Xcode/DerivedData/test-gjocatkmtbmvawcegstrzwyevagk/Build/Products/Debug-iphonesimulator/test.app/test`

Arch
- 架构类型
`# Arch: x86_64`

Object files
- 目标文件列表

```
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


