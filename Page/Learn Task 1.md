# Objective-C Learn 1

## Learn Task

* Foudation
* 属性
  * strong
  * retain
  * copy
  * assign
* Extension
* Protocol
* Delegate
* Block
* 引用计数
  * MRC
  * ARC：自动引用计数（Automatic Reference Counting）
  * 循环引用

## Foundation框架

### 概述

框架(Framwork)是由多种相关的类、函数、数据类型、常量组成的库。
XCode创建项目的时候，会自动引入Foundation框架：`#import <Foundation/Foundation.h>`。
> 注意这里引入和C语言的`#inlcude`有区别，前者相对于后者来说，解决了重复引用的问题，我们不必再像C语言中使用`#ifndef`。


Foundation框架只是Cocoa的一部分，并没有内置于OC语言中（如果开发iOS程序，还需要用到UIKit框架），Foundation框架包含了很多的基础类，下图显示了部分类：
![类图](https://i.loli.net/2019/05/28/5ced23a5081f638142.jpg)

在XCode中，Shift+Command+0键打开API文档可以进行查询具体使用方法。

![帮助](https://i.loli.net/2019/05/28/5ced23a53dfcc95155.jpg)

下面以几个类为例，熟悉语法。

### NSDate

NSDate的实例代表了某个时间点，概念上类似Java中的Date类。

```

#import <Foundation/Foundation.h>
#import "Employee.h"
#import "StockHolding.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool { //内存管理相关
        NSDate *now = [NSDate date];
        //NSDate *now = [[NSDate alloc] init];
        NSLog(@"the date is %@",now);
    }
    return 0;
}

```

在OC里面执行方法代码，需要**发送消息**给包含这个方法的对象和类。在上面的代码中，`NSDate`是接收方，指针指向接收消息的对象的地址；后面的`date`是选择器，要触发的方法名。

### NSString

NNString实例可以保存字符串，`@"....."`是OC中的一种缩写，代表根据给定的字符串创建一个NNString对象，这种缩写称为字面量语法（literal syntax）。
与Java中的String，NSString也是不可变的（immutable），如果想要执行操作，可以使用NSMutableString。
该类提供的类方法和实例方法可以通过查询文档得知。

### NSArray

NSArray可以保存一组指向其他对象的指针，可以通过相应的索引进行访问。需要注意的是，NSArray的实例是无法改变的。一旦NSArray实例被创建后，就无法添加或删除数据里的指针，也无法改变数组的指针顺序。

```
NSString *shop1 = @"Loaf of bread";
NSString *shop2 = @"Container of milk";
NSString *shop3 = @"Stick of butter";  
NSDate *date = [NSDate date];
NSArray *array = @[shop1,shop2,shop3,date];
```
###NSMutableArray

NSMutableArray是NSArray的子类，可以进行添加、删除等操作。
```
NSMutableArray *shopList = [NSMutableArray array];
[shopList addObject:shop1];
[shopList addObject:shop2];
[shopList addObject:shop3];
[shopList insertObject:shop4 atIndex:1];
[shopList removeObject:shop1];
```

### 类前缀

比较好奇为什么每一个类前面都要加一个NS前缀，原来Objective-C与C++不同，本身没有命名空间，所以会出现这种情况：
在自定义类的时候，假如给类命名为Person，当编译器连接到另一个库，其中也包含一个Person类，这时编译器无法识别这两个类，出现错误。
为了避免这样的名字冲突，Apple推荐使用三个或三个以上的字母作为前缀，让类的尽量独一无二。


----


## 属性

属性的声明可以有一个或多个，属性特性会告诉编译关于属性行为的更多信息。
```
@property(nonatomic,readonly) int index; //可以自动生成setter和getter方法
```
在很多老资料中看到需要配合@synthesize使用，在XCode4.5之后，可以不必使用了（当前版本10.2.1）。

### atomic和nonatomic

原子性，如果不在多线程情况下使用，可以使用`nonatomic`有限地提高性能，如果不声明，默认为`atomic`。

### assign

是reference，而不是owner。在非ARC内存管理模式下，assgin是默认属性。assgin声明的属性不会增加引用计数，在调用assign属性的getter属性时候，返回对实际数据的引用。所以如果对对象指针使用这个属性可能会造成野指针，程序崩溃。
一般用来修饰基本数据类型，如`int`、`float`等。

### copy

是owner，而不是reference，与assign相反。当对象可变时，可设置为copy，用于获取此时值的副本。新的对象引用计数为1，与原始对象无关，原始对象的引用计数并不会改变。使用copy创建的新对象也是强引用，使用完成后需要负责释放该对象。
copy特性可以减少对象对上下文的依赖。新对象、原始对象中任一对象的值改变不会影响另一对象的值。要想设置该对象的特性为copy，该对象必须遵守NSCopying协议，Foundation类默认实现了NSCopying协议，所以只需要为自定义的类实现该协议即可。关于这个协议在下面解释一下。

### retain

创建一个强引用的指针，使引用对象的引用计数加1。我们需要在完成对象之后释放它。通过使用retain，它将增加retain count并占用autorelease池中的内存。

### strong

strong是retain属性的替换，作为Objective-C ARC的一部分，。在非arc代码中，它只是retain的同义词。

### weak

weak类似于assign，是reference而不是owner。它不会将引用计数增加1，如果对象的引用计数下降到0，即使仍然在这里指向对象，对象也将从内存中释放。
可将weak对象设为nil，向nil发送消息，什么都不会执行，程序也不会崩溃。


-------


## Block对象

Block对象是一段代码（其实就是匿名的函数代码块）
语法表达式：
> ^ 返回值类型 (参数列表) {表达式}

其中，返回值类型和参数列表都是可以省略的。

例如：
```
^{
    NSLog(@"hello world");
}
```

下面以一段代码为例熟悉基本使用方法，功能是去掉字符串中的元音字母。

```

    NSArray *oldStrings = [NSArray arrayWithObjects:@"dong",@"yu",@"hao", nil];
    NSMutableArray *newStrings = [NSMutableArray array];
    NSArray *vowels = [NSArray arrayWithObjects:@"a",@"e",@"i",@"o",@"u", nil];
        
    //声明Block变量
    void (^devowelizer)(id,NSUInteger,BOOL *);
    //将Block对象赋值
    devowelizer = ^(id string,NSUInteger i,BOOL *stop){
    NSMutableString *newString = [NSMutableString stringWithString:string];
    for(NSString *s in vowels){
            NSRange fullRange = NSMakeRange(0, [newString length]);
            [newString replaceOccurrencesOfString:s withString:@"" options:NSCaseInsensitiveSearch range:fullRange];
        }
        [newStrings addObject:newString];
    };//Block结束
        
    [oldStrings enumerateObjectsUsingBlock:devowelizer];
    NSLog(@"new strings %@",newStrings);

```

`enumerateObjectsUsingBlock`方法具体要求了传入的Block对象参数类型，对于这种比较固定的格式，可以在头文件或者实现文件顶部，用typedef来定义一个新类型。

```

    typedef void(^ArrayEnumerationBlock)(id,NSUInteger,BOOL *);

    ArrayEnumerationBlock devowelizer;
    devowelizer = ^(id string,NSUInteger i,BOOL *stop){
        .......
    };

```




## 内存管理

Java内置的垃圾回收机制，让我们可以比较放心的把内存管理交给JVM，而C语言则需要谨慎处理内存问题。Cocoa采用了一种叫做引用计数（Reference Counting）的技术来确定对象的生命周期是否结束。

### MRC 

手动引用计数（Manual Reference Counting，MRC）。在ARC出现之前，程序员需要手动维护retain计数。
下面是retain计数规则：
* 如果用来创建一个对象的方法名是`alloc`、`new`或包含`copy`，对象的保留计数器值被置为1
* 当某段代码访问一个对象的时候，同时将对象的保留计数器值加一，当不再访问则减一。
* 保留计数器为0的时候，表示不再有代码访问该对象，将被销毁，回收内存

增加计数发送retain消息，减少计数发送release消息，计数为0的时候OC会自动发送delloc消息（不要主动调用delloc），我们可以重写dealloc方法。

Cocoa中有一个自动释放池（Auto Release Pool）的概念，在`main`函数里面见过这个` @autoreleasepool`。NSObject提供了`autorelease`方法，该方法预定了一条会在未来某个时间发送的release消息，返回值是接受这条消息的对象。

```
[obj retain] //引用数加1
[obj release] //引用数减1
[obj autorelease] //放到AutoReleasePool
```

### ARC 

程序员在内存管理上花了大量时间，并且容易出现内存泄漏现象。所以引入了自动引用计数（Automatic Reference Counting，ARC）。ARC会自动追踪对象并决定哪一个会不会使用到的，编译器会自动插入retain和release语句，无需自己动手。从这里也能够看出，ARC与JVM的垃圾回收器不是一回事。

### 循环引用

## Extension 扩展

类扩展

## Protocol 协议

其实就是Java中的interface，定义一个接口，但是不提供具体的实现方法.
定义一个协议如下：
```
@protocol SonProtocol <FatherProtocol> //可以继承父协议

@required //必须实现的
- (void) call
@optional //可选实现
- (void) logF

@end
```

使用的时候，在头文件中引入

```
@interface Person : NSObject<StuProtocols>
......
@end
```



## Delegate 代理

代理设计模式，这是一种消息传递的方式，由代理者、委托者、协议组成。

* 协议：用来指定代理双方可以做什么和必须做什么。
* 代理方：根据指定的协议，完成委托放需要实现的功能（需求）。
* 委托方：根据指定的协议，指定代理方完成什么功能。

下面以老师给学生留作业这样一个例子简单理解Delegate。

step1：


| --- | --- | --- |
| Step1 | 规定 今天的作业是什么  | 声明一个协议并在协议中明确必须做什么和可以做什么 |
| Step1 | 规定 今天的作业是什么  | 声明一个协议并在协议中明确必须做什么和可以做什么 |

step2：委托方添加一个属性deleget，该属性向外界说明我有一个代理职位（需要遵从step1中的协议），类比：主机上的视频接口（VGA、HDMI）；
step3：委托方在需要完成某种功能的（协议中已提前确定）时候，告诉代理帮我完成这个功能，类比：主机需要显示画面；
step4：代理方需要遵从step1中的协议；
step5：代理方需要成为委托方的代理，类比：显示器工作VGA线连接到主机；
step6：代理方在接受到委托方需要完成某种功能的信息后，实现该功能，类比：显示画面

## Category 分类








> 参考资料：
> Objective-C 编程（第二版）
> Objective-C 基础教程
> https://github.com/qinjx/30min_guides/blob/master/ios.md
> https://stackoverflow.com/questions/8927727/objective-c-arc-strong-vs-retain-and-weak-vs-assign
> https://stackoverflow.com/questions/626898/how-do-i-create-delegates-in-objective-c







