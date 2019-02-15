> ## 导出.o文件

* .a文件

```
lipo YOUR_A_FILE -thin armv7 -output xxxx
ar -x YOUR_NOFLAT_FILE
```

* .framework文件

```
otool -o YOUR_FRAMEWORK_FILE
```





