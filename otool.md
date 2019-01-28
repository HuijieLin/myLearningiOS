# otool

---

> ##  参考链接

[https://mp.weixin.qq.com/s?\_\_biz=MzAwNDY1ODY2OQ==&mid=207986417&idx=1&sn=77ea7d8e4f8ab7b59111e78c86ccfe66&3rd=MzA3MDU4NTYzMw==&scene=6\#rd](https://mp.weixin.qq.com/s?__biz=MzAwNDY1ODY2OQ==&mid=207986417&idx=1&sn=77ea7d8e4f8ab7b59111e78c86ccfe66&3rd=MzA3MDU4NTYzMw==&scene=6#rd)

> ## 生成Link Map File

LinkMap文件是Xcode产生可执行文件的同时生成的链接信息，用来描述可执行文件的构造成分，包括代码段（\_\_TEXT）和数据段（\_\_DATA）的分布情况。只要设置`Project -> Build Settings -> Write Link Map File`为`YES`，并设置`Path to Link Map File`，build完后就可以在设置的路径看到LinkMap文件了。![](/assets/2019012801.png)

> ## 查看使用到的方法

```c
// YOUR_APP_PATH: 可以是build出来APP的路径，也可以是build出来Framework的路径
// Link Map生成的所有的方法列表，可以用两个列表的数据相减，可以得出没有使用到的方法
otool -v -s __DATA __objc_selrefs YOUR_APP_PATH
```

> ## 查看所有类

```c
// YOUR_APP_PATH: 可以是build出来APP的路径，也可以是build出来Framework的路径
// 文件里面的 __objc_classlist -> 所有类
// 文件里面的 __objc_classrefs -> 被引用的类
// 两个集合相减就是没有用到的类
otool -o -V YOUR_APP_PATH

// 看指定架构下面的信息
otool -arch x86_64 -V -o YOUR_APP_PATH

// 查看支持什么架构
lipo -info YOUR_APP_PATH
```



