> ## 导出.o文件

```
// YOUR_A_FILE -> 需要拆分.a或者.framework的路径
// YOUR_OUTPUT_FILE_PATH -> 拆分后存放的路径
// 拆分出armv7的库
lipo YOUR_A_FILE -thin armv7 -output YOUR_OUTPUT_FILE_PATH
// 导出.o文件
ar -x YOUR_OUTPUT_FILE_PATH
```



