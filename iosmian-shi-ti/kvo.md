# KVO

> ## KVO的实现原理

原理如下：

* 当观察一个对象的时候，苹果会动态创建一个类。这个新建的类继承自观察对象所属的类。并且在新建的子类里面重写观察对象的`setter`方法，重写的`setter`方法会在调用原`setter`方法之前插入`willChangeValueForKey`和`didChangeValueForKey`来通知观察对象的值的更改。最后通过 isa-swizzling 把观察对象的isa指针指向新建子类，这样观察对象就变成了新建子类的实例。
* isa 指针 - 告诉 runtime 这个对象的类是什么
* 新建的子类的前缀是`NSKVONotifying_` ，如果手动创建一个同名的类，在注册的时候就会报错
* 重写`setter`方法是在运行时而不是编译时

流程图如下：![](../.gitbook/assets/2019011001.png)

> ## addObserver:forKeyPath:options:context:各个参数的作用分别是什么，observer中需要实现哪个方法才能获得KVO回调？

```objectivec
// 添加键值观察
/**
 * 1 观察者，负责处理监听事件的对象
 * 2 观察的属性
 * 3 观察的选项
 * 4 上下文
 **/
[self.person addObserver:self 
              forKeyPath:@"name" 
                 options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld 
                 context:@"Person Name"];
```

observer中需要实现以下方法获取KVO回调：

```objectivec
// 所有的 kvo 监听到事件，都会调用此方法
/**
 * 1. 观察的属性
 * 2. 观察的对象
 * 3. change 属性变化字典（新／旧）
 * 4. 上下文，与监听的时候传递的一致
 **/
- (void)observeValueForKeyPath:(NSString *)keyPath 
                      ofObject:(id)object 
                        change:(NSDictionary *)change 
                       context:(void *)context;
```

> ## 如何手动触发KVO

[Apple官网文档 - Manual Change Notification](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/KeyValueObserving/Articles/KVOCompliance.html#//apple_ref/doc/uid/20002178-SW3)

给属性添加完观察者之后，手动调用`willChangeValueForKey`和`didChangeValueForKey`即可。

代码如下：

```objectivec
@property (nonatomic, strong) NSDate *now;

- (void)viewDidLoad {
   [super viewDidLoad];

   _now = [NSDate date];

   // 添加属性观察
   [self addObserver:self forKeyPath:@"now" options:NSKeyValueObservingOptionNew context:nil];
   NSLog(@"1");

   // 手动调用以下方法
   [self willChangeValueForKey:@"now"]; // “手动触发self.now的KVO”，必写。
   NSLog(@"2");
   [self didChangeValueForKey:@"now"]; // “手动触发self.now的KVO”，必写。
   NSLog(@"4");
}
```

> ## KVC和KVO的keyPath一定是属性么？

KVC支持实例变量，KVO只支持属性。如果KVO需要支持实例变量的监听，需要手动实现。

[参考链接](https://yq.aliyun.com/articles/30483)

方法如下：

* 重写实例变量的`get`和`set`方法，在`set`方法的前后分别插入`willChangeValueForKey`和`didChangeValueForKey`
* 重写`automaticallyNotifiesObserversForKey`，然后判断传入的key为实例变量就`return NO`

代码如下：

```objectivec
//
//  Student.h

#import <Foundation/Foundation.h>

@interface Student : NSObject {
    NSString  *_age;
}
- (void)setAge:(NSString *)age;
- (NSString *)age;

@property (nonatomic, strong) NSString  *name;

@end
```

```objectivec
//
//  Student.m

#import "Student.h"

@implementation Student

@synthesize name = _name;
- (void)setName:(NSString *)name
{
    _name = name;
}
- (NSString *)name
{
    return _name;
}


// 手动设定KVO
- (void)setAge:(NSString *)age
{
    [self willChangeValueForKey:@"age"];
    _age = age;
    [self didChangeValueForKey:@"age"];
}
- (NSString *)age
{
    return _age;
}
+ (BOOL)automaticallyNotifiesObserversForKey:(NSString *)key
{
    // 如果监测到键值为age,则指定为非自动监听对象
    if ([key isEqualToString:@"age"])
    {
        return NO;
    }

    return [super automaticallyNotifiesObserversForKey:key];
}

@end
```

```objectivec
//
//  RootViewController.m

#import "RootViewController.h"
#import "Student.h"

@interface RootViewController ()

@property (nonatomic, strong) Student *student;

@end

@implementation RootViewController

- (void)viewDidLoad
{
    [super viewDidLoad];

    // 创建学生对象
    _student = [Student new];

    // 监听属性name
    [_student addObserver:self
               forKeyPath:@"name"  // 属性
                  options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld
                  context:nil];

    // 监听实例变量age
    [_student addObserver:self
               forKeyPath:@"age"   // 实例变量
                  options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld
                  context:nil];

    _student.name = @"YouXianMing"; // 改变名字
    _student.age  = @"18";          // 改变年龄
}

- (void)observeValueForKeyPath:(NSString *)keyPath
                      ofObject:(id)object
                        change:(NSDictionary *)change
                       context:(void *)context
{
    NSLog(@"%@", change);
}

@end
```

> ## 如何手动实现KVO

[参考链接](https://tech.glowing.com/cn/implement-kvo/)

[Demo链接](https://github.com/okcomp/ImplementKVO)

思路如下：

* 创建一个监听方法和移除的方法
* 在监听方法里面大概包含以下步骤：
  1. 检查观察对象的属性有没有`setter`方法，没有的话抛出异常-
     * 可以通过`class_getInstanceMethod`来获取`setter`方法的`method`，如果没有代表没有`setter`方法
  2. 判断观察对象的`isa`指向的类是不是一个KVO类，如果不是需要新建一个继承自原来类的子类，然后把观察对象的isa指向新建的KVO类。（这个KVO类的前缀自己定义，避免和其他类或者系统的KVO类冲突）
     * 使用`objc_setClass`来修改`isa`的指向
     * 使用`objc_allocateClassPair`来注册一个类，入参父类、类名、额外的空间（一般传0）。然后可以给这个类添加方法、变量等
     * 使用`objc_registerClassPair`来使类生效
  3. 检查KVO类有没有重写过观察对象的`setter`方法，如果没有，添加重写的`setter`方法
     * 在重写的`setter`方法里，记录新旧值然后调用原来的`setter`方法，通知给观察者回调
  4. 添加观察者

> ## 直接修改成员变量会触发KVO吗

不会触发

> ## Mutable 属性如何触发KVO

```objectivec
// NSMutableArray
// 自动触发KVO主要是看有没有经过 setter 方法，
// 由于对 NSMutableArray 对象进行增、删操作不会触发到 setter 方法
// 所以可以使用 mutableArrayValueForKey 
@property (nonatomic, strong) NSMutableArray *kvoMutableArray;
[[self mutableArrayValueForKey:@"kvoMutableArray"] addObject:@"1"];

// NSMutableSet
// 和上面同理，使用这个方法mutableSetValueForKey

// NSMutableDictionary
// 目前只能手动触发，在改变值的前后手动调用KVO对应的方法
@property (nonatomic, strong) NSMutableDictionary *kvoMutableDic;
[self willChangeValueForKey:self.kvoMutableDic];
[self.kvoMutableDic setObject:object forKey:key];
[self didChangeValueForKey:self.kvoMutableDic];
```

> ## KVO是否线程同步

是的。在哪个线程监听就在哪个线程回调。

> ## 调试KVO

```objectivec
po [observedObject observationInfo]
// 这会打印出有关谁观察谁之类的很多信息。
// 这个信息的格式不是公开的，我们不能让任何东西依赖它，因为苹果随时都可以改变它。不过这是一个很强大的排错工具
```

> ## 如何禁止KVO回调

```objectivec
// 方法1
// 如果两个都写，方法1的优先级 > 方法二
+ (BOOL)automaticallyNotifiesObserversForKey:(NSString *)key
{
    // 把需要禁止回调的属性，返回NO
    if ([key isEqualToString:@"kvoString"])
    {
        return NO;
    }

    return [super automaticallyNotifiesObserversForKey:key];
}

// 方法2
// 方法名 = automaticallyNotifiesObserversOf + 监听的属性（首字母大写）
// 例如：监听的属性（kvoString）
// 所以方法名 = automaticallyNotifiesObserversOfKvoString
+ (BOOL)automaticallyNotifiesObserversOfKvoString {
    return NO;
}
```

