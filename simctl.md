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

> 在模拟器中安装APP

```objectivec
// booted: 表示正在运行的模拟器，如果有多个模拟器启动，表示正在激活的模拟器
// 注意：安装的APP为app格式，不是ipa格式
xcrun simctl install booted Your_APP_Path.app
```

> 打开指定APP

```
xcrun simctl launch booted Your_APP_Bundle_Identifier
```

> 模拟器打开一个网页

```
// 打开百度
xcrun simctl openurl booted "https://www.baidu.com"
```



