### iOS面试题

---

> 《招聘一个靠谱的iOS》面试题参考答案

[https://github.com/ChenYilong/iOSInterviewQuestions/](https://github.com/ChenYilong/iOSInterviewQuestions/)

### KVO

---

> KVO的实现原理



> addObserver:forKeyPath:options:context:各个参数的作用分别是什么，observer中需要实现哪个方法才能获得KVO回调？

```objectivec
// 添加键值观察
/**
 * 1 观察者，负责处理监听事件的对象
 * 2 观察的属性
 * 3 观察的选项
 * 4 上下文
 **/
[self.person addObserver:self 
              forKeyPath:@"name" 
                 options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld 
                 context:@"Person Name"];
```

observer中需要实现以下方法获取KVO回调：

```objectivec
// 所有的 kvo 监听到事件，都会调用此方法
/**
 * 1. 观察的属性
 * 2. 观察的对象
 * 3. change 属性变化字典（新／旧）
 * 4. 上下文，与监听的时候传递的一致
 **/
- (void)observeValueForKeyPath:(NSString *)keyPath 
                      ofObject:(id)object 
                        change:(NSDictionary *)change 
                       context:(void *)context;
```

### 算法

---

> 算法奇数排在前面，偶数排在后面



