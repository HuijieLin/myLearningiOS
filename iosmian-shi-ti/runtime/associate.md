> ## assocate声明weak属性

[http://mrpeak.cn/blog/ios-weak/](http://mrpeak.cn/blog/ios-weak/)

```
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



