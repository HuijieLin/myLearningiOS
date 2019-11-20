在 MRC 中,方法内部的 block 是在栈区的,使用 copy 可以把它放到堆区.在 ARC 中写不写都行

# block

---

> ## block的本质和原理是什么

* block的本质是一个OC对象，它内部也有一个isa指针
* block是封装了函数调用以及函数调用环境的OC对象
* block的底层结构如下：

![](/assets/2019112101.png)

> block 的变量捕获

* auto是默认的变量声明标签，例如：
* ```objectivec
  // 这种就表示auto变量类型，默认值
  int age = 10;

  // 等于
  auto int age = 10;
  ```
* 判断block是否捕获变量，就主要关注变量的类型即可，block只捕获**局部变量**

| 变量类型 | 是否捕获到block内部 | 访问方式 |
| :--- | :--- | :--- |
| 局部变量 - auto | 是 | 值传递 |
| 局部变量 - static | 是 | 指针传递 |
| 全局变量 | 否 | 直接访问 |

* auto变量的捕获

```objectivec
// clang转换为c++代码
xcrun -sdk iphoneos clang -arch arm64 -rewrite-objc -fobjc-arc -fobjc-runtime=ios-8.0.0 main.m

/*****************
 * OC代码
******************/
int age = 10;

void (^block)(void) = ^{
    NSLog(@"age is %d", age);
};

/*****************
 * C++代码
******************/
static struct __main_block_desc_0 {
  size_t reserved;
  size_t Block_size;
};

struct __block_impl {
  void *isa;
  int Flags;
  int Reserved;
  void *FuncPtr; // 指向block的内容，调用block的时候通过这个来找到block的内容
};

// block的结构体入口
struct __main_block_impl_0 {
  struct __block_impl impl;
  struct __main_block_desc_0* Desc;
  int age; // 捕获到的auto变量，存在block的结构体中
};
```

> block的类型

| block类型 | 环境 |
| :--- | :--- |
| NSGlobalBlock | 没有访问auto变量 |
| NSStackBlcok | 访问了auto变量 |
| NSMallocBlock | NSStackBlock调用了copy |

* 一共3种类型，都是继承自NSBlock

  * \_\_NSGlobalBlock\_\_ ：在data区（数据区域）
  * \_\_NSStackBlock\_\_：在栈
  * \_\_NSMallockBlack\_\_：在堆

* 每一种类型的block调用copy的结果如下所示：

![](/assets/2019112103.png)

> \_\_block的作用是什么
>
> block 的属性修饰词为什么是copy
>
> 使用block有哪些注意点
>
> block 在修改NSMutableArray的时候需不需要添加\_\_block



