

# 通知Crash防护

- 新建 NSNotificationCenter 分类
    - 在 iOS9 以下 hook `addObserver:selector:name:object:`, iOS9以上（包含）不需要处理，因为系统已经修复了
    - 在被 hook 的方法里给 observer 增加一个标记 `hasRegisterForNotification = YES`，然后继续调用原来的方法
    
- 新建 NSObject 分类
    - 在 iOS9 以下 hook delloc 方法
    - 增加一个属性 hasRegisterForNotification， 用户标记添加过通知的object
    - 在被 hook 的方法里面判断 `self.hasRegisterForNotification` 是否等于 YES ，如果是就调用 `[[NSNotificationCenter defaultCenter] removeObserver:self];`
