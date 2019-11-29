# 内存管理

---

> ## iOS 程序的内存布局

低地址
｜ 保留
｜ ---------------------------------------------------------------------------------
｜ 代码段（\_\_TEXT）: 编译之后的代码
｜ ---------------------------------------------------------------------------------
｜ 数据段（\_\_DATA）
｜ - 字符串常量 ： 比如`NSString *str = @"123";`
｜ - 已初始化数据 ：已初始化的全局变量，静态变量
｜ - 未初始化数据 ：未初始化的全局变量，静态变量
｜ ---------------------------------------------------------------------------------
｜ 堆⬇️ ：通过alloc，malloc， calloc等动态分配的空间。分配的内存空间地址越来越大
｜ ---------------------------------------------------------------------------------
｜ 栈⬆️ ：函数调用开销，比如局部变量。分配的内存空间地址越来越小
｜ ---------------------------------------------------------------------------------
｜ 内核区
高地址

> ## OC对象的内存管理

- 在iOS中，使用**引用计数**来管理OC对象的内存
- 可以通过以下私有函数来查看自动释放池的情况：
`extern void _objc_autoreleasePoolPrint(void);`

> ## 引用计数的存储

- 在64bit中，引用计数可以直接存储在优化过的isa指针中，当存不下的时候，在存到SideTable类中。
- SideTable结构如下：
```objectivec
struct SideTable {
    spinlock_t slock; // 保证原子操作的自旋锁
    RefcountMap refcnts; // 引用计数的hash表
    weak_table_t weak_table; // weak 引用全局hash表
};
```

> ## weak指针的实现原理

- 当一个对象obj被weak指针指向时，这个weak指针会以obj作为key，被存储到sideTable类的weak_table这个散列表上对应的一个weak指针数组里面
- 当一个对象obj的delloc方法被调用是，runtime会以obj为key，从sideTable类的weak_table这个散列表中，找出对应的weak指针列表，然后将里面的weak指逐个设置为nil

```objectivec
struct weak_table_t {
    weak_entry_t *weak_entries;
    size_t    num_entries;
    uintptr_t mask;
    uintptr_t max_hash_displacement;
};
```

> ## weak和assign的区别

不同点：

* assign 可以用于非OC对象，weak必须用在OC对象上



