---
layout: post
title: 'PowerMockio 使用注意事项小计之一'
categories: 
- programming
- Java
- UnitTest
tags:
- PowerMock
- PowerMockio
- Mockio
- UnitTest

---


PowerMock是Java开发中的一种Mock框架，是基于EasyMock或Mockio的实现，可以完成对private/static/final等方法的Mock。
实在是一个大杀器。更多的使用说明入门，可以见[jonyang](http://km.oa.com/user/jonyang)的
[Powermock和Mockito单元测试常用例子](http://km.oa.com/group/23033/articles/show/206825)。

这里主要记录一下使用中遇到的坑。

## `doReturn().when()`与`when().thenReturn`的区别


使用中，我用的后端是Mockio，它的用法比EasyMock更加简洁。我在使用过程中遇到过一个问题。

关于`doReturn().when()` 和`when().thenReturn()`：官方文档中，是建议使用后者的，因为后者能够保证类型安全。
官方建议在使用后者无效场景下可以使用前者来替代。
但是实际上两者在执行mockedMethod时是有很大区别的。
1. `doReturn(value).when()` 在执行 mockedMethod 时，会匹配方法和参数是否与`when()`内设置的相同，
如果相同，该mock实现将**不执行mockedMethod**，而直接返回预设的返回值`value`。
2. 而`when().thenReturn(value)`则是会先**执行 mockedMethod**，然后不论执行结果是怎样，都会返回`value`。

如果被 mockedMethod 仅仅是简单的逻辑，不涉及数据库、网络等操作的话，通常是不会有问题。
但如果 mockedMethod 实际上是封装了对数据库、RPC等造成，而实际上测试时数据库或者RPC等等不可用时，
就会很头疼了，通常情况下，我们实现这种封装时会抛出相应的exception来让外部逻辑处理。
但是这种抛出异常的情况就不会有返回值了。那么`when().thenReturn()`的方式就不会返回我们预期的`value`，
而是直接向上传递异常，这会导致测试用例不通过。
这通常不是我们期望的。
那么这种情况下，就需要用`doReturn().when()`来替代了，因为调用 mockedMethod 时，这种方式就不会执行 mockedMethod内部的逻辑了。

参数匹配之`Matchers.anyXXX()`
================================

我们mock一些方法时，通常会测试不同输入下的不同表现。但如果不关心mock方法的内部逻辑，例如这个方法实际上是第三方模块的时候。
我们就可以通过`Matchers.anyXXX()`之类的方法来匹配任意输入。常见的使用方式是

```Java
// 通过PowerMock创建测试对象
ClassUnderTest underTest = PowerMockito.spy(new ClassUnderTest());

// 准备测试的相关信息和数据
final String methodNameToMock = "somePrivateMethod";
ClassSomeParameter param = new ClassSomeParameter(...);
ClassReturnValue   value = new ClassReturnValue(...);

// 指定mock方法的行为
// 匹配指定数值参数 param
PowerMockito.when(underTest, methodNameToMock,param).thenReturn(value);
// 匹配任意类型参数
PowerMockito.when(underTest, methodNameToMock,Matchers.any()).thenReturn(value);
```
