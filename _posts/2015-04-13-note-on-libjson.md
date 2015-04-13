---
layout: post
title: "LibJson 的使用方法总结"
categories:
- programming
- json
tags:
- json
- programming


---


网络上有不少开源的libjson，我这里目前要用于C++中，最后选取的sf上的[libjson](http://sourceforge.net/projects/libjson/)。
因为这个lib最简单，对于C++，可以直接编译用于自己的工程中，也可以编译成lib或so来使用。

这里介绍一下简单的使用注意事项或技巧。

libjson是以类似Tree的方式来存储json数据的，并且提供一些遍历和find的方法来访问数据。

```json
{
    "RootA" : "Value in parent node",
    "ChildNode" : {
        "ChildA" : "String Value",
        "ChildB" : 42
    }
}
```

以上面的数据为例，最顶端是一个unnamed 的节点，相当于树的根节点。在libjson中用 `JSONNode` 来描述这样一个结构。
我们可以类似于容器的iterator方式来遍历，或者用下标\[i\]的方式来访问当前节点的下一层子节点。

通过节点或叶子的name()方法来检查是否是自建需要处理的那个数据。常用的方法还包括type(), as_int(), as_bool(), as_string()等方法。

上例中，假设我们获得一个`JSONNode` `n`来表示根节点，那么 n.name()是空的，n.empty()是false，表示其具有子节点。
我们可以用n[0],n[1],...的方式来获取n.size()以内的所有子节点。

最后需要注意的就是解析之前，最好用`libjson::is_valid(json)`方法来检查输入的是否是合法的json数据，否则使用过程中可能会出现异常，
这一点libjson是不保证的。
