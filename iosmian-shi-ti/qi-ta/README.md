# 其他

## TableView优化，怎么减少卡顿？

1. 提前计算并缓存好高度
2. 滑动的时候按需加载图片，用SDWebImage就可以实现异步加载
3. 正确使用reusedIdentifier来重用cell
4. 尽量少用或者不用透明图层
5. 减少subView的层级和数量
6. 尽量少用addView的方式给Cell动态添加View，可以先初始化好，通过控制hidden的方式是否展示
7. 尽量少用conerRadius和maskToBounds来设置圆角（因为会导致离屏渲染）

## 怎么实现LRU

通过双向l

## 





