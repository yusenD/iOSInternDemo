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

框架(Framwork)是由多种相关的类、函数、数据类型、常量组成的库。XCode创建项目的时候，会自动引入Foundation框架：`#import <Foundation/Foundation.h>`。
> 注意这里引入和C语言的`#inlcude`有区别，前者相对于后者来说，解决了重复引用的问题，我们不必再像C语言中使用`#ifndef`。

Foundation框架包含了很多的基础类，下图显示了部分类：
![](https://i.loli.net/2019/05/28/5ced23a5081f638142.jpg)

在XCode中，Shift+Command+0键打开API文档可以进行查询具体使用方法。

![](https://i.loli.net/2019/05/28/5ced23a53dfcc95155.jpg)


## Objective-C 面向对象特性

## 内存管理

### ARC 

### MRC

