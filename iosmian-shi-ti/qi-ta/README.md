# 其他

---

## TableView优化，怎么减少卡顿？

1. 提前计算并缓存好高度
2. 滑动的时候按需加载图片，用SDWebImage就可以实现异步加载
3. 正确使用reusedIdentifier来重用cell
4. 尽量少用或者不用透明图层
5. 减少subView的层级和数量
6. 尽量少用addView的方式给Cell动态添加View，可以先初始化好，通过控制hidden的方式是否展示
7. 尽量少用conerRadius和maskToBounds来设置圆角（因为会导致离屏渲染）

---

## 怎么实现LRU

整体设计思路是，使用hashMap存储key，这样可以做到save和get key 的事件都是O（1），而HashMap的Value指向双向链表实现的LRU的Node节点，如下图所示：

![](../../.gitbook/assets/image %282%29.png)

LRU 存储是基于双向链表，head表示表头，tail表示表尾。如果存储满了，通过O（1）的时间淘汰掉链表的尾部，每次新增和访问数据，都可以通过O（1）的时间吧新的节点增加到表头，或者把已经存在的节点移动到表头。

操作：

* save（key1，7）
* save（key2，0）
* save（key3，1）
* save（key4，2）
* get（key2）
* save（key5，3）
* get（key2）
* save（key6，4）

对应的 LRU 双向链表部分的变化如下：

![](../../.gitbook/assets/image %283%29.png)

核心操作步骤：

1. save（key，value）首先在HashMap中找到Key对应的节点，如果节点存在，更新节点的值，并且吧这个节点移动到表头。如果不存在，就构建新节点，并且把节点放在表头，如果空间不足，就通过tail把尾部的节点给淘汰掉，同时删除HaskMap中对应的Key
2. get（key）通过 HashMap 找到 LRU 链表节点，同时把这个节点移动到表头

---

# setNeedsLayout与layoutIfNeeded的区别

* setNeedsLayout：
  * 添加刷新标志位，下一次RunLoop的时候进行刷新
* layoutIfNeeded：
  * 判断如果有刷新标识位，就立马刷新

---

# hash和isEqual的关系

- 对象的判断是相互的，即：`[a isEqual:b]` == `[b ieEqual:b]`
- 如果两个对象相等，那么他们的hash值是一样的，即：`[a isEqual:b]` => `[a hash] == [b hash]`
- 如果hash值一样，两个对象不一定相等，即：`[a hash] == [b hash]` != `[a isEqual:b]`

为了优化判等的效率，基于hash的 NSSet 和 NSDictionary 在判断成员是否相等时，会：
  1. 先判断 hash 值是否一样，如果不一样，进入第2步
  2. 在进行对象判等，作为最终结果
  
---

# NSDictionary 和 NSMutableArray 底层结构

NSDictionary
- 哈希表

NSMutableArray
- 环形缓冲区

# SDWebImage 下载了图片后为什么要解码