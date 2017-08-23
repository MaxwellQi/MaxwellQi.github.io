---
layout: post
title: KVC与KVO的用法以及其原理解析
description: 介绍KVC与KVO的用法以及内部实现原理
category: blog
tag: iOS,Ojbective-C,KVO,KVC
---

## KVO

KVO(Key-Value-Observing),用于观察键值。任何对象都允许观察其它对象的属性，并且可以接收其它对象的属性发生变化时的通知。它是一个观察者模式。

`NSKeyValueObserving `是`NSObject`的一个非正式协议。从协议的角度看，是定义了一套让开发者遵守的规范和使用的方法。在 Cocoa 的 MVC 框架中，架起 ViewController 和 Model 沟通的桥梁。

### KVO的使用

#### 注册于移除注册

比如我们现在有一个`Person`类，我们要观察`Person`(被观察者)类对象的变化，那么就可以在该类的对象上调用名为`NSKeyValueObserverRegistration`的category方法将观察者对象与被观察者对象注册与移除注册：

```
@interface NSObject(NSKeyValueObserverRegistration)

- (void)addObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath options:(NSKeyValueObservingOptions)options context:(nullable void *)context;
- (void)removeObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath context:(nullable void *)context NS_AVAILABLE(10_7, 5_0);
- (void)removeObserver:(NSObject *)observer forKeyPath:(NSString *)keyPath;

@end
```

这两个方法的定义在 `Foundation/NSKeyValueObserving.h `中，`NSObject`，`NSArray`，`NSSet`均实现了以上方法，因此我们不仅可以观察普通对象，还可以观察数组或结合类对象。在该头文件中，我们还可以看到 NSObject 还实现了 `NSKeyValueObserverNotification` 的 category 方法。

#### 为属性赋值

将观察者与被观察者注册好之后，就可以对被观察者对象的属性进行操作，这些变更操作就会被通知给观察者对象。注意，只有遵循 KVO 方式来设置属性，观察者对象才会获取通知，也就是说遵循使用属性的 setter 方法，或通过 key-path 来设置：

```
    [person setAge:10];
    [person setValue:[NSNumber numberWithInt:10] forKey:@"age"];
```

#### 处理变更通知

观察者需要实现名为 `NSKeyValueObserving` 的 category 方法来处理收到的变更通知：

```
@interface NSObject(NSKeyValueObserving)
- (void)observeValueForKeyPath:(nullable NSString *)keyPath ofObject:(nullable id)object change:(nullable NSDictionary<NSKeyValueChangeKey, id> *)change context:(nullable void *)context;

@end

```

### 示例

#### 一般用法

##### 实现观察者类

首先定义一个观察者类：

```
/* PersonObserver.h */
#import <Foundation/Foundation.h>

@interface PersonObserver : NSObject

@end


/* PersonObserver.m */
#import "PersonObserver.h"

@implementation PersonObserver


- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context
{
    if ([keyPath isEqualToString:@"age"]) {
        Class classInfo = (__bridge Class)context;
        NSString *className = [NSString stringWithCString:object_getClassName(classInfo) encoding:NSUTF8StringEncoding];
        
        NSLog(@" >> class: %@, Age changed", className);
        NSLog(@" old age is %@", [change objectForKey:@"old"]);
        NSLog(@" new age is %@", [change objectForKey:@"new"]);
    }else if([keyPath isEqualToString:@"info"])
    {
        Class classInfo = (__bridge Class)context;
        NSString *className = [NSString stringWithCString:object_getClassName(classInfo) encoding:NSUTF8StringEncoding];
        NSLog(@" >> class: %@, Info changed", className);
        NSLog(@" old info is %@", [change objectForKey:@"old"]);
        NSLog(@" new info is %@", [change objectForKey:@"new"]);
        
    }else{
        [super observeValueForKeyPath:keyPath
                             ofObject:object
                               change:change
                              context:context];
    }
}
@end

```

一定要注意：在实现处理变更通知方法 observeValueForKeyPath 时，要将不能处理的 key 转发给 super 的 observeValueForKeyPath 来处理。

使用示例：

```
#import "ViewController.h"
#import "Person.h"
#import "PersonObserver.h"

@interface ViewController ()

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    PersonObserver *observer = [[PersonObserver alloc] init];
    
    Person *person = [[Person alloc] init];
    [person addObserver:observer forKeyPath:@"age" options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld context:(__bridge void * _Nullable)([Person class])];
    [person setAge:10];
//    [person setValue:[NSNumber numberWithInt:10] forKey:@"age"];
    [person removeObserver:observer forKeyPath:@"age"];

}
```
运行结果如下：

```
2017-08-23 16:15:53.420238+0800 DemoKVO[4320:1052355]  >> class: Person, Age changed
2017-08-23 16:15:53.420343+0800 DemoKVO[4320:1052355]  old age is 5
2017-08-23 16:15:53.420450+0800 DemoKVO[4320:1052355]  new age is 10
```

##### 实现被观察者类

上面的被观察者类(Person)如何实现呢？其实现方式有两种，一种是手动实现，另一种是自动实现。


1）手动实现

```
/* Person.h */
#import <Foundation/Foundation.h>

@interface Person : NSObject
{
    int age;
}

- (void)setAge:(int)tage;
@end


/* Person.m */
#import "Person.h"
@implementation Person

- (id)init
{
    self = [super init];
    if (self) {
        age = 5;
    }
    return self;
}

- (int)age
{
    return age;
}

- (void)setAge:(int)tage
{
    [self willChangeValueForKey:@"age"];
    age = tage;
    [self didChangeValueForKey:@"age"];
}

+ (BOOL)automaticallyNotifiesObserversForKey:(NSString *)key
{
    if ([key isEqualToString:@"age"]) {
        return NO;
    }
    return [super automaticallyNotifiesObserversForKey:key];
}

@end

```

首先，需要手动实现属性的 `setter `方法，并在设置操作的前后分别调用 `willChangeValueForKey:` 和 `didChangeValueForKey`方法，这两个方法用于通知系统该 key 的属性值即将和已经变更了；
其次，要实现类方法 `automaticallyNotifiesObserversForKey`，并在其中设置对该 key 不自动发送通知（返回 NO 即可）。这里要注意，对其它非手动实现的 key，要转交给 super 来处理。

2）自动实现

自动实现键值观察就非常简单了，只要使用了自动属性即可。(在这里我用Student类替代Person类)

```
/*  Student.h */
#import <Foundation/Foundation.h>

@interface Student : NSObject
@property (nonatomic,assign) int age;
@end

/*  Student.m */
#import "Student.h"

@implementation Student
@synthesize age;

- (id)init
{
    self = [super init];
    if (self) {
        age = 15;
    }
    return self;
}
@end
```

运行结果：

```
2017-08-23 16:15:53.420731+0800 DemoKVO[4320:1052355]  >> class: Student, Age changed
2017-08-23 16:15:53.420751+0800 DemoKVO[4320:1052355]  old age is 15
2017-08-23 16:15:53.420763+0800 DemoKVO[4320:1052355]  new age is 18
```

#### 键值观察依赖键

有时候一个属性的值依赖于另一对象中的一个或多个属性，如果这些属性中任一属性的值发生变更，被依赖的属性值也应当为其变更进行标记。因此，object 引入了依赖键。

##### 观察依赖键

观察依赖键的方式与前面描述的一样，下面先在 Observer 的 `observeValueForKeyPath:ofObject:change:context:` 中添加处理变更通知的代码：

```
#import "PersonObserver.h"

@implementation PersonObserver


- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary<NSKeyValueChangeKey,id> *)change context:(void *)context
{
    if ([keyPath isEqualToString:@"age"]) {
        Class classInfo = (__bridge Class)context;
        NSString *className = [NSString stringWithCString:object_getClassName(classInfo) encoding:NSUTF8StringEncoding];
        
        NSLog(@" >> class: %@, Age changed", className);
        NSLog(@" old age is %@", [change objectForKey:@"old"]);
        NSLog(@" new age is %@", [change objectForKey:@"new"]);
    }else if([keyPath isEqualToString:@"info"])
    {
        Class classInfo = (__bridge Class)context;
        NSString *className = [NSString stringWithCString:object_getClassName(classInfo) encoding:NSUTF8StringEncoding];
        NSLog(@" >> class: %@, Info changed", className);
        NSLog(@" old info is %@", [change objectForKey:@"old"]);
        NSLog(@" new info is %@", [change objectForKey:@"new"]);
        
    }else{
        [super observeValueForKeyPath:keyPath
                             ofObject:object
                               change:change
                              context:context];
    }
}
@end
```

##### 实现依赖键

在这里，观察的是 `TeacherWrapper` 类的 `info` 属性，该属性是依赖于 `Teacher` 类的 `age` 和 `grade` 属性。为此，我在 `Teacher` 中添加了 `grade` 属性：

```
/* Teacher.h */
#import <Foundation/Foundation.h>

@interface Teacher : NSObject
@property (nonatomic,readwrite) int grade;
@property (nonatomic,readwrite) int age;
@end

/* Teacher.m */
#import "Teacher.h"

@implementation Teacher
@synthesize age;
@synthesize grade;
@end
```

下面来看看 `TeacherWrapper` 中的依赖键属性是如何实现的。

```
/* TeacherWrapper.h */
#import <Foundation/Foundation.h>
@class Teacher;
@interface TeacherWrapper : NSObject
{
    @private
    Teacher * _teacher;
}
@property (nonatomic,assign) NSString* info;
@property (nonatomic,retain) Teacher *teacher;

- (id)init:(Teacher *)aTeacher;
@end


/* TeacherWrapper.m */
#import "TeacherWrapper.h"
#import "Teacher.h"

@implementation TeacherWrapper

- (id)init:(Teacher *)aTeacher
{
    self = [super init];
    if (self) {
        _teacher = aTeacher;
    }
    return self;
}

- (NSString *)info
{
    return [[NSString alloc] initWithFormat:@"%d#%d", [_teacher grade], [_teacher age]] ;
}

- (void)setInfo:(NSString *)theInfo
{
    NSArray *array = [theInfo componentsSeparatedByString:@"#"];
    [_teacher setGrade:[[array objectAtIndex:0] intValue]];
    [_teacher setAge:[[array objectAtIndex:1] intValue]];
}

+ (NSSet *)keyPathsForValuesAffectingInfo
{
    NSSet * keyPaths = [NSSet setWithObjects:@"teacher.age", @"teacher.grade", nil];
    return keyPaths;
}

@end

```
首先，要手动实现属性 `info` 的 `setter/getter` 方法，在其中使用 `Teacher` 的属性来完成其 `setter` 和 `getter`。

其次，要实现 `keyPathsForValuesAffectingInformation` 或 `keyPathsForValuesAffectingValueForKey:` 方法来告诉系统 `info` 属性依赖于哪些其他属性，这两个方法都返回一个key-path 的集合。在这里要多说几句，如果选择实现 `keyPathsForValuesAffectingValueForKey`，要先获取 super 返回的结果 set，然后判断 key 是不是目标 key，如果是就将依赖属性的 key-path 结合追加到 super 返回的结果 set 中，否则直接返回 super的结果。
在这里，`info` 属性依赖于 `teacher` 的 `age` 和 `grade` 属性，`teacher` 的 `age/grade` 属性任一发生变化，`info` 的观察者都会得到通知。

使用实例：

```
- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    Teacher *teacher = [[Teacher alloc] init];
    TeacherWrapper *wrapper = [[TeacherWrapper alloc] init:teacher];
    [wrapper addObserver:observer forKeyPath:@"info" options:NSKeyValueObservingOptionNew | NSKeyValueObservingOptionOld context:(__bridge void* _Nullable)([TeacherWrapper class])];
    [teacher setAge:30];
    [teacher setGrade:1];
    [wrapper removeObserver:observer forKeyPath:@"info"];
    
}

```

输出结果：

```
2017-08-23 16:15:53.421590+0800 DemoKVO[4320:1052355]  >> class: TeacherWrapper, Info changed
2017-08-23 16:15:53.421626+0800 DemoKVO[4320:1052355]  old info is 0#0
2017-08-23 16:15:53.421653+0800 DemoKVO[4320:1052355]  new info is 0#30
2017-08-23 16:15:53.421690+0800 DemoKVO[4320:1052355]  >> class: TeacherWrapper, Info changed
2017-08-23 16:15:53.421714+0800 DemoKVO[4320:1052355]  old info is 0#30
2017-08-23 16:15:53.421736+0800 DemoKVO[4320:1052355]  new info is 1#30
```

### KVO实现原理

当某个类的对象第一次被观察时，系统就会在运行期动态地创建该类的一个派生类，在这个派生类中重写基类中任何被观察属性的 setter 方法。 派生类在被重写的 setter 方法实现真正的通知机制，就如前面手动实现键值观察那样。这么做是基于设置属性会调用 setter 方法，而通过重写就获得了 KVO 需要的通知机制。当然前提是要通过遵循 KVO 的属性设置方式来变更属性值，如果仅是直接修改属性对应的成员变量，是无法实现 KVO 的。 同时派生类还重写了 class 方法以“欺骗”外部调用者它就是起初的那个类。然后系统将这个对象的 isa 指针指向这个新诞生的派生类，因此这个对象就成为该派生类的对象了，因而在该对象上对 setter 的调用就会调用重写的 setter，从而激活键值通知机制。此外，派生类还重写了 dealloc 方法来释放资源。

#### 派生类 NSKVONotifying_Person 剖析

在这个过程，被观察对象的 isa 指针从指向原来的 Person 类，被 KVO 机制修改为指向系统新创建的子类 NSKVONotifying_Person 类，来实现当前类属性值改变的监听。

所以当我们从应用层面上看来，完全没有意识到有新的类出现，这是系统“隐瞒”了对 KVO 的底层实现过程，让我们误以为还是原来的类。但是此时如果我们创建一个新的名为 NSKVONotifying_Person 的类()，就会发现系统运行到注册 KVO 的那段代码时程序就崩溃，因为系统在注册监听的时候动态创建了名为 NSKVONotifying_Person 的中间类，并指向这个中间类了。

因而在该对象上对 setter 的调用就会调用已重写的 setter，从而激活键值通知机制。这也是 KVO 回调机制，为什么都俗称 KVO 技术为黑魔法的原因之一吧：内部神秘、外观简洁。

#### 子类 setter 方法剖析

`KVO` 在调用存取方法之前总是调用 `willChangeValueForKey:`，通知系统该 keyPath 的属性值即将变更。 当改变发生后，`didChangeValueForKey:` 被调用，通知系统该 keyPath 的属性值已经变更。 之后，`observeValueForKey:ofObject:change:context:` 也会被调用。

重写观察属性的 `setter` 方法这种方式是在运行时而不是编译时实现的。 `KVO` 为子类的观察者属性重写调用存取方法的工作原理在代码中相当于：

```
- (void)setName:(NSString *)newName
{
    [self willChangeValueForKey:@"name"];    // KVO在调用存取方法之前总调用
    [super setValue:newName forKey:@"name"]; // 调用父类的存取方法
    [self didChangeValueForKey:@"name"];     // KVO在调用存取方法之后总调用
}
```

#### 总结

KVO 的本质就是监听对象的属性进行赋值的时候有没有调用 setter 方法

1. 系统会动态创建一个继承于 `Person` 的 `NSKVONotifying_Person`
2. `person` 的 `isa` 指针指向的类 `Person` 变成 `NSKVONotifying_Person`，所以接下来的 `person.age` = `newAge` 的时候，他调用的不是 `Person` 的 `setter` 方法，而是 `NSKVONotifying_Person`（子类）的 `setter` 方法
3. 重写`NSKVONotifying_Person`的`setter`方法：`[super setName:newName]`
4. 通知观察者告诉属性改变。

