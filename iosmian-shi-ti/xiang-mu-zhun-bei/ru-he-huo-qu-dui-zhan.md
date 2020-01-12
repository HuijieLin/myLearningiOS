# 如何获取堆栈

[https://bestswifter.com/callstack/](https://bestswifter.com/callstack/)

> ##大概步骤：

调用栈获取：
- task_thread 获取所有线程
- thread_get_state 获取线程信息，信息填充在 _STRUCT_MCONTEXT 结构体中，存储了当前线程的Stack pointer 和最顶部的 Frame Pointer，可以获取到所有调用栈

符号解析：
- 根据 Frame Pointer 找到函数调用地址
- 找到 Frame Pointer 属于哪个Image
- 找到Image里面的符号表，在 LC_SYMTAB 的 symbal table offset找到符号表的位置
- 在符号表中找到函数调用地址对应的符号表

