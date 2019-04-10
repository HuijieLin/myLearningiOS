> # FPS检测

[https://wereadteam.github.io/2016/12/12/Monitor/](https://wereadteam.github.io/2016/12/12/Monitor/)

Demo：[https://github.com/featuretower/GYMonitor](https://github.com/featuretower/GYMonitor)

> ### 计算FPS思路

* 在主线程runloop设置`CADisplayLink`的方式来统计一秒内刷新多少次，从而得出具体的FPS数值
* 使用`frameInterval`来控制刷新屏幕，默认是1，表示1帧刷新1次，我们这次设置是2，表示2帧刷新1次，也就是理论情况下一秒刷新30次

> ### 上报思路

* 利用NSthread+Runloop开启一个常驻子线程，每隔0.5秒计算最近两次CADisplayLink刷新的时间，如果超过0.5秒（这个值可以自动设置）就代表有卡顿，然后捞去对应的堆栈。为了避免防止频繁捞去堆栈，间隔12秒才在次捞取一次，并且使用最后一次的堆栈信息上报。





