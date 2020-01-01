# 切分文件

> ## 导出.o文件

* lipo方法

```text
// YOUR_A_FILE -> 需要拆分.a或者.framework的路径
// YOUR_OUTPUT_FILE_PATH -> 拆分后存放的路径
// 拆分出armv7的库
lipo YOUR_A_FILE -thin armv7 -output YOUR_OUTPUT_FILE_PATH
// 导出.o文件
ar -x YOUR_OUTPUT_FILE_PATH

// 例子.framework
lipo my.framework/my -thin armv7 -output my_armv7.a
// 例子.a
lipo my.a -thin armv7 -output my_armv7.a
// 导出.o文件
ar -x my_armv7.a

// 备注：如果想看.o文件的具体实现，可以讲.o文件拖入Hopper即可
```

* 直接解压

```text
// 安装7zip
brew install p7zip

// 开始解压
7z x my.framework/my

// 会得到几个文件
my~.arm-9
my~.cpu16777228
my~.x64
my~.x86

// 选择其中一个解压，即可得到.o文件
7z x my~.x64
```

* 查看单个.o文件

```text
xcrun nm -nm xx.o文件
otool -o xx.o文件
```

