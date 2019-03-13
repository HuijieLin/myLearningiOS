> # dealloc

---

> ## 什么时候被调用

[https://stackoverflow.com/questions/13853438/when-does-dealloc-method-call](https://stackoverflow.com/questions/13853438/when-does-dealloc-method-call)

1. 没有被持有
2. 对象的内存被回收

> ## delloc的底层实现

* delloc的调用链路
  * dealloc -&gt; \_objc\_rootDealloc -&gt; rootDealloc -&gt; object\_dispose -&gt; objc\_destructInstance

```
// 最后一步的代码
void *objc_destructInstance(id obj) 
{
    if (obj) {
        // Read all of the flags at once for performance.
        bool cxx = obj->hasCxxDtor();
        bool assoc = obj->hasAssociatedObjects();

        // This order is important.
        if (cxx) object_cxxDestruct(obj);
        if (assoc) _object_remove_assocations(obj);
        obj->clearDeallocating();
    }

    return obj;
}
```

* 处理步骤：
  * 如果有hasCxxDtor，调用object\_cxxDestruct，C++的析构函数，清理ARC的成员变量
  * 如果有assoc，调用\_object\_remove\_assocations，清理associated对象
  * 最后调用clearDeallocating，清空引用计数和weak引用表，并且将weak对象设置为nil

> ## object\_cxxDestruct

* 调用链路
  * object\_cxxDestruct -&gt; object\_cxxDestructFromClass -&gt; .cxx\_destruct

```
void object_cxxDestruct(id obj)
{
    if (!obj) return;
    if (obj->isTaggedPointer()) return;
    object_cxxDestructFromClass(obj, obj->ISA());
}

static void object_cxxDestructFromClass(id obj, Class cls)
{
    void (*dtor)(id);

    // Call cls's dtor first, then superclasses's dtors.

    for ( ; cls; cls = cls->superclass) {
        if (!cls->hasCxxDtor()) return; 
        dtor = (void(*)(id))
            lookupMethodInClassAndLoadCache(cls, SEL_cxx_destruct);
        if (dtor != (void(*)(id))_objc_msgForward_impcache) {
            if (PrintCxxCtors) {
                _objc_inform("CXX: calling C++ destructors for class %s", 
                             cls->nameForLogging());
            }
            (*dtor)(obj);
        }
    }
}
```

* 处理步骤
  * 递归寻找父类中名为`SEL_cxx_destruct`的方法然后调用，最后调用`.cxx_destruct`，用于释放成员变量
  * 在编译的时候看到一个对象里面含有c++对象，就会自动生成这个`.cxx_destruct`方法来用于析构, ARC借助这个方法插入了清理成员变量的逻辑
  * 可以给属性加上`watchpoint`然后断来观察`.cxx_destruct`这个函数

> ## .cxx\_destruct

* 调用链路
  * `.cxx_destruct`的具体实现由`GenerateObjCCtorDtorMethod`这个方法来生成
  * 接着调用`emitCXXDestructMethod`
  * 遍历当前对象的所有实例变量，然后调用`objc_storeStrong`去释放

> ## \_object\_remove\_assocations

找到对象里的关联对象，然后放在数组里，接着遍历释放

> ## clearDeallocating

* 调用链路：
  * clearDeallocating -&gt; （最后调到）weak\_clear\_no\_lock
* 在weaktable中找到对应的引用置为nil，然后在weaktable中移除



