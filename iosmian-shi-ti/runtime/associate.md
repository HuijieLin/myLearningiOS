> ## assocate声明weak属性

[http://mrpeak.cn/blog/ios-weak/](http://mrpeak.cn/blog/ios-weak/)

```objectivec
// 通过一个中间层block来解决
- (void)setContext:(CDDContext*)object {
    id __weak weakObject = object;
    id (^block)() = ^{ return weakObject; };
    objc_setAssociatedObject(self, @selector(context), block, OBJC_ASSOCIATION_COPY);
}

- (CDDContext*)context {
    id (^block)() = objc_getAssociatedObject(self, @selector(context));
    id curContext = (block ? block() : nil);
    return curContext;
}
```

> ## 关联对象什么时候被销毁

[https://stackoverflow.com/questions/10842829/will-an-associated-object-be-released-automatically/10843510\#10843510](https://stackoverflow.com/questions/10842829/will-an-associated-object-be-released-automatically/10843510#10843510)

```
 1. 调用 -release ：引用计数变为零
     * 对象正在被销毁，生命周期即将结束.
     * 不能再有新的 __weak 弱引用， 否则将指向 nil.
     * 调用 [self dealloc] 
 2. 子类 调用 -dealloc
     * 继承关系中最底层的子类 在调用 -dealloc
     * 如果是 MRC 代码 则会手动释放实例变量们（iVars）
     * 继承关系中每一层的父类 都在调用 -dealloc
 3. NSObject 调 -dealloc
     * 只做一件事：调用 Objective-C runtime 中的 object_dispose() 方法
 4. 调用 object_dispose()
     * 为 C++ 的实例变量们（iVars）调用 destructors 
     * 为 ARC 状态下的 实例变量们（iVars） 调用 -release 
     * 解除所有使用 runtime Associate方法关联的对象
     * 解除所有 __weak 引用
     * 调用 free()
```

> ## 给关联对象的属性值设置nil，等于remove掉关联对象存储表中的值

## -

> ## 关联对象policy和属性修饰符的对应关系

| objc\_AssociationPolicy | 对应的修饰符 |
| :--- | :--- |
| OBJC\_ASSOCIATION\_ASSIGN | assign |
| OBJC\_ASSOCIATION\_RETAIN\_NONATOMIC | strong，nonatomic |
| OBJC\_ASSOCIATION\_COPY\_NONATOMIC | copy，nonatomic |
| OBJC\_ASSOCIATION\_RETAIN | strong，atomic |
| OBJC\_ASSOCIATION\_COPY | copy，atomic |



