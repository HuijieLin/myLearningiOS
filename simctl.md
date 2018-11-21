## 简介

---

是一个apple提供的命令行，用来与iOS模拟器进行交互的。与安卓的`adb`命令有点类似。

> 地址

Applications/Xcode.app/Contents/Developer/usr/bin/simctl

> 模拟器地址

/Applications/Xcode.app/Contents/Developer/Applications/Simulator.app

## 常用命令

---

> 帮助

```objectivec
xcrun simctl help
```

> 查看已经安装的模拟器列表

```
xcrun instruments -s devices
```

> 查看所有支持的模拟器列表

```objectivec
xcrun simctl list

// 可以添加参数 -j ，以json的形式展示
xcrun simctl list -j
```

> 创建模拟器

```objectivec
// Device Types 和 Runtimes，可以使用 xcrun simctl list 进行查看
// Device Types = 设备类型
// Runtimes = 系统版本号
xcrun simctl create "Your_Simulator_Name" "Device_Types" "Runtimes"

// 例子：
// 创建一个名字为Your_Simulator_Name的模拟器，iPhone Xs iOS 12.1
xcrun simctl create "Your_Simulator_Name" "com.apple.CoreSimulator.SimDeviceType.iPhone-XS" "com.apple.CoreSimulator.SimRuntime.iOS-12-1"
```

> 启动模拟器

```objectivec
// 通过UDID方式启动
// UDID 可以使用 xcrun simctl list 进行查看
// 在创建模拟器的时候，如果创建成功也会返回对应的UDID
xcrun simctl boot "C6BEEF0D-94EC-4C52-B0C9-8C6F5B0AA542"

// 通过模拟器名字启动
xcrun instruments -w 'Your_Simulator_Name'

// 通过open+UDID的方式启动模拟器
open -n /Applications/Xcode.app/Contents/Developer/Applications/Simulator.app --args -currentDeviceUDID C6BEEF0D-94EC-4C52-B0C9-8C6F5B0AA542
```

> 安装APP

```objectivec
// booted: 表示正在运行的模拟器，如果有多个模拟器启动，表示正在激活的模拟器
// 注意：安装的APP为app格式，不是ipa格式
xcrun simctl install booted Your_APP_Path.app
```

> 卸载APP

```
xcrun simctl uninstall booted Your_APP_Bundle_Identifier
```

> 启动APP

```objectivec
// 通过Bundle ID启动APP
xcrun simctl launch booted Your_APP_Bundle_Identifier

// 通过URL Schema启动APP
xcrun simctl openurl booted "Your_APP_URL_Schema"

// 启动APP带启动参数
// 这种方式传进来的参数，是保存在UserDefault里
xcrun simctl launch booted Your_APP_Bundle_Identifier -key1 value1 -key2 value2 -key3 value3
```

> 关闭APP

```
xcrun simctl terminate booted "Your_APP_Bundle_Identifier"
```

> 打开网页

```objectivec
// 打开百度
xcrun simctl openurl booted "https://www.baidu.com"

// 延伸：
// 如果APP支持Universal Link，也可以使用URL的方式打开APP甚至指定APP的指定页面
```

> 添加图片或者视频到模拟器

```
// 也可以把资源文件直接拽入模拟器，更方便
xcrun simctl addmedia booted Your_file_Path
```

> 打印模拟器日志

```
xcrun simctl spawn booted log stream — level=debug

// 添加过滤条件
xcrun simctl spawn booted log stream — predicate ‘processImagePath endswith “Your_KeyWord”’
xcrun simctl spawn booted log stream — predicate ‘eventMessage contains “error” and messageType == info’

// 收集日志
xcrun simctl spawn booted log collect
```

> 截图

```
xcrun simctl io booted screenshot screen.png
```

> 录屏

```
// 可以使用Ctrl+C来提前结束录制
xcrun simctl io booted recordVideo news.mov
```

> 显示APP的沙盒地址

```
xcrun simctl get_app_container booted Your_APP_Bundle_Identifier
```

> 显示APP的完整信息

```
xcrun simctl appinfo booted Your_APP_Bundle_Identifier
```

## 日常应用

---

* 在日常开发中，每次编译只能打开一个模拟器，针对UI适配调试的话，需要多次编译到不同模拟器上会比较浪费时间，所以可以在Build Phases -&gt; New Run Script Phases添加以下脚本来实现一次编译，在多个模拟器同时启动APP

```objectivec
// 首先打开模拟器应用
open "/Applications/Xcode.app/Contents/Developer/Applications/Simulator.app/"

// 启动需要测试的模拟器
xcrun simctl boot "C6BEEF0D-94EC-4C52-B0C9-8C6F5B0AA542"

// 在模拟器安装编译完的app
xcrun simctl install "C6BEEF0D-94EC-4C52-B0C9-8C6F5B0AA542" "${BUILT_PRODUCTS_DIR}/${TARGET_NAME}.app"

// 打开应用
xcrun simctl launch "C6BEEF0D-94EC-4C52-B0C9-8C6F5B0AA542" "${PRODUCT_BUNDLE_IDENTIFIER}
```



