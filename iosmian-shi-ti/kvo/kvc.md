# KVC

---

> ## KVC赋值流程 - setValueForKey

1. 调用`setValueForKey`
2. 按照`setKey`，`_setKey：`顺序查找方法
3. 如果找到调用方法
4. 如果没有找到方法检查`accessInstanceVariablesDirectly`方法的返回值（默认值 = YES）
5. 如果是YES
   1. 按照_`key`, _`isKey`, `key`, `isKey`的顺序查找成员变量，找到了成员变量直接赋值
   2. 如果没有找到直接调用，`setValueForUndefinedKey`
6. 如果是NO
   1. 调用`setValueForUndefineKey`

> ## KVC取值流程
>
> ## _KVC修改属性会触发KVO吗_

会触发KVO



