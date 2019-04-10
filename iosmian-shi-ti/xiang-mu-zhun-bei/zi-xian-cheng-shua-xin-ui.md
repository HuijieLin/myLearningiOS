> # 子线程刷新UI检测

[https://wereadteam.github.io/2016/12/12/Monitor/](#)

hook UIView和CALayer的以下三个方法，然后判断是否在主线程，如果不是就可以记录堆栈：

* setNeedsLayout
* setNeedsDisplay
* setNeedsDisplayInRect



