# 内存管理

---

> ## iOS 程序的内存布局

**低地址**  
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
**高地址**

> ## OC对象的内存管理

* 在iOS中，使用**引用计数**来管理OC对象的内存
* 可以通过以下私有函数来查看自动释放池的情况：
  `extern void _objc_autoreleasePoolPrint(void);`

> ## 引用计数的存储

* 在64bit中，引用计数可以直接存储在优化过的isa指针中，当存不下的时候，在存到SideTable类中。
* SideTable结构如下：
  ```objectivec
  struct SideTable {
    spinlock_t slock; // 保证原子操作的自旋锁
    RefcountMap refcnts; // 引用计数的hash表
    weak_table_t weak_table; // weak 引用全局hash表
  };
  ```

> ## weak指针的实现原理

* 当一个对象obj被weak指针指向时，这个weak指针会以obj作为key，被存储到sideTable类的weak\_table这个散列表上对应的一个weak指针数组里面
* 当一个对象obj的delloc方法被调用是，runtime会以obj为key，从sideTable类的weak\_table这个散列表中，找出对应的weak指针列表，然后将里面的weak指逐个设置为nil
* 在散列表里面的查找过程：

```objectivec
// weak指针表结构
struct weak_table_t {
    weak_entry_t *weak_entries; // 保存了所有指向指定对象的weak指针
    size_t    num_entries; // 散列表最大可以存放的容量
    uintptr_t mask; // 通过和这个值计算，可以算出对应的index，然后在散列表中查找元素
    uintptr_t max_hash_displacement; // hash key的最大偏移值
};

// 查找逻辑
// 1. 用对象内存地址referent转成hash值 然后 & weak_table->mask 获取一个开始索引
// 2. 然后根据这个索引在数组中查找元素
// 3. 如果查找出来的元素跟入餐referent不一样，在通过 (index+1) & weak_table->mask 重新计算index，重新查找，直到查到对应的对象
static weak_entry_t *
weak_entry_for_referent(weak_table_t *weak_table, objc_object *referent)
{
    assert(referent);

    weak_entry_t *weak_entries = weak_table->weak_entries;

    if (!weak_entries) return nil;

    size_t begin = hash_pointer(referent) & weak_table->mask;
    size_t index = begin;
    size_t hash_displacement = 0;
    while (weak_table->weak_entries[index].referent != referent) {
        index = (index+1) & weak_table->mask;
        if (index == begin) bad_weak_table(weak_table->weak_entries);
        hash_displacement++;
        if (hash_displacement > weak_table->max_hash_displacement) {
            return nil;
        }
    }

    return &weak_table->weak_entries[index];
}
```

> ## weak和assign的区别

不同点：

* assign 可以用于非OC对象，weak必须用在OC对象上



