# 桥接 - Toll-Free Bridge

> 桥接 - Toll-Free Bridge

[https://developer.apple.com/library/archive/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html](https://developer.apple.com/library/archive/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html)

> ## \_\_bridge

用于OC和CF之间的转换，但是不会改变对应的所有权。

> ## \_\_bridge\_\_\_retained

用于OC转CF，同时还会把所有权交给CF，也就是需要手动调用CFRelese来释放。

> ## \_\_bridge\_transfer

用于CF转OC，同时还会把所有权交给OC，如果是ARC情况下，就会自动管理内存释放。

