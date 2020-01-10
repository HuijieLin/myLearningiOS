

# Notification 防护

- 新建 NSObject 分类
    - 在 iOS9 以下 hook delloc 方法
    - 增加一个属性 hasRegisterForNotification， 用户标记添加过通知的object
    - 在被 hook 的方法里面判断 `self.hasRegisterForNotification` 是否等于 YES ，如果是就调用 `[[NSNotificationCenter defaultCenter] removeObserver:self];`

- 新建 NSNotificationCenter 分类
    - 在 iOS9 以下 hook `addObserver:selector:name:object:`, iOS9以上（包含）不需要处理，因为系统已经修复了
    - 在被 hook 的方法里给 observer 增加一个标记 `hasRegisterForNotification = YES`，然后继续调用原来的方法
    
# Timber 防护

- 新建 NSTimer 分类
    - hook 方法 `scheduledTimerWithTimeInterval:target:selector:userInfo:repeats:` ，同时只对方法参数 repeats = YES 的情况做处理，repeats = NO 不要处理
    - 因为 repeats = NO 在执行完毕之后系统会自动调用 invalidated
    - 当 repeats = YES
        - 新建一个中间类，这个类弱引用timer中的target， 同时把相关的参数保存在这个类对应的属性里面（包括：NSTimer，target， selector）
        - 这时候把原方法的target和selector，换成新建类对象和里面一个写死的方法
        - 这时候timer的回调就会到那个写死的方法里面
        - 然后在那个方法里面判断target是否为nil：
            - 如果 `target = nil` 把 timer = invalidate，
            - 如果 `target != nil`，先用`respondsToSelector`判断select是否存在，然后在通过`[_target performSelector:_selector withObject:timer];`给真实的target进行回调