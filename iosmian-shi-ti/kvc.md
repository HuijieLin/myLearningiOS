# KVC

> ## KVC赋值流程 `- setValueForKey`

1. 调用`setValueForKey`
2. 按照`setKey`，`_setKey：`的顺序查找方法
3. 如果找到方法直接调用
4. 如果没有找到方法，检查`accessInstanceVariablesDirectly`方法的返回值（默认值 = YES）
5. 如果返回值是YES 1. 按照`_key`, `-isKey`, `key`, `isKey`的顺序查找成员变量，找到了成员变量直接赋值
   1. 如果没有找到直接调用`setValueForUndefinedKey`
6. 如果返回值是NO
   1. 调用`setValueForUndefineKey`

> ## KVC取值流程 `- valueForKey：`

1. 调用`valueForKey：`
2. 按照`getKey`，`key`，`_isKey`，`_key`的顺序查找方法
3. 如果找到方法直接调用
4. 如果没有找到方法，检查`accessInstanceVariablesDirectly`方法的返回值（默认值 = YES）
5. 如果返回值是YES
   1. 按照`-key`, `-isKey`, `key`, `isKey`的顺序查找成员变量，找到了成员变量直接赋值
   2. 如果没有找到直接调用`setValueForUndefinedKey`
6. 如果返回值是NO
   1. 调用`setValueForUndefineKey`

> ## KVC修改属性会触发KVO吗

会触发KVO

> ## KVC的应用场景

1. 动态取值和赋值

