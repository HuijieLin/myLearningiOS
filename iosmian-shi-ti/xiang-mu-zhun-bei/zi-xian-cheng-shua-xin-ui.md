> # 子线程刷新UI检测

hook UIView和CALayer的以下三个方法，然后判断是否在主线程：

* setNeedsLayout
* setNeedsDisplay
* setNeedsDisplayInRect







