---
layout: post
title: "An interesting macro"
categories:
- Programming
tags: 
- macro leveldb

---

LevelDB[#leveldb]的源码中有一个辅助测试的宏，非常有趣。摘录如下。

这个宏的关键代码如下![]({{ site.img_url }}leveldb-Test-Macro.png)

我们分析一下这个宏的用法。

这里给出一个使用这个宏的示例代码，
![]({{ site.img_url }}An-example-of-Test-Macro.png)。

我们可以让编译器告诉我们，一个复杂的宏在编译预处理时是如何展开的。
![]({{ site.img_url }}real-macro-code1.png)，
忽略其中的细节，可以从简化的图中看到
![]({{ site.img_url }}real-macro-code-2.png)。

