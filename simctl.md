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
// 通过UUID方式启动
// UUID 可以使用 xcrun simctl list 进行查看
// 在创建模拟器的时候，如果创建成功也会返回对应的UUID
xcrun simctl boot "C6BEEF0D-94EC-4C52-B0C9-8C6F5B0AA542"

// 通过模拟器名字启动
xcrun instruments -w 'Your_Simulator_Name'
```



