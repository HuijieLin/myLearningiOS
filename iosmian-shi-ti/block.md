> ```
> block的本质和原理是什么
> ```

* block的本质是一个OC对象，它内部也有一个isa指针
* block是封装了函数调用以及函数调用环境的OC对象
* block的底层结构如下：

![](/assets/2019112101.png)

> ## block的变量捕获

* ```
  auto是默认的变量声明标签，如下：
  ```
* ```objectivec
  // 这种就表示auto变量类型，默认值
  int age = 10;

  // 等于
  auto int age = 10;
  ```
* 判断block是否捕获变量，就主要关注变量的类型即可，**block只捕获局部变量**

| 变量类型 | 是否捕获到block内部 | 访问方式 |
| :--- | :--- | :--- |
| 局部变量 - auto | 是 | 值传递 |
| 局部变量 - static | 是 | 指针传递 |
| 全局变量 | 否 | 直接访问 |

* auto变量的捕获demo

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

* block对auto变量的内存管理
  * 如果block在栈上，将不会对auto变量产生强应用
  * 如果block被copy到堆上：
    * 会调用block内部的copy函数，
    * copy函数内部最终会调用`_Block_object_assign`函数
    * `_Block_object_assign`函数会根据auto变量的修饰符\(strong, weak 等\)形成对变量的强应用还是弱应用
  * 如果block在堆上被移除：
    * 会调用block内部的dispose函数
    * dispose函数内部会调用`_block_object_dispose`函数
    * `_block_object_dispoase`会自动释放引用的auto变量

> ## block的类型

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

* ARC下，以下情况编译器会根据情况自动将栈上的block复制到堆上面（这也说明为什么在ARC下，block用strong和copy声明都可以）

  * block作为函数返回值时

  * 将block赋值给\_\_strong修饰的变量时

  * block作为GCD API中的参数时

  * block作为cocoa API中方法名含有usingBlock的方法参数时

> ## \_\_block的作用是什么

* 可以用于解决block内部修改auto变量值的问题
* 不能修饰全局变量和静态变量
* 编译器会把\_\_block变量包装成一个对象，主要流程如下：
* ```objectivec
  /***************
   * OC代码
   ***************/
  __block int age = 10;

  void (^block)(void) = ^{
      NSLog(@"age is %d", age);
  };

  /***************
  * C++代码
  ***************/
  // __block修饰的age变量包装成下面的结构体
  struct __Block_byref_age_0 {
    void *__isa;
  __Block_byref_age_0 *__forwarding;
   int __flags;
   int __size;
   int age;
  };

  // block结构主入口
  struct __main_block_impl_0 {
    struct __block_impl impl;
    struct __main_block_desc_0* Desc;
    __Block_byref_age_0 *age; // age变量
  };

  // 最终通过这种方式来设置age变量
  age->__forwarding->age
  ```
* block对\_\_block变量的内存管理

  * block对\_\_block修饰变量生成的结构体强引用（如上面的：`__Block_byref_age_0`）

  * \_\_block修饰变量的结构体`__Block_byref_age_0`对变量（如上面的age）的引用类型取决于变量的修饰符（strong， weak等）

  * 注意：在MRC下，`__Block_byref_age_0`对里面的变量不会retain，也就是弱引用

> ## block在修改NSMutableArray的时候需不需要添加\_\_block

不需要，因为本身没有修改到NSMutableArray

> ## 解决循环引用



