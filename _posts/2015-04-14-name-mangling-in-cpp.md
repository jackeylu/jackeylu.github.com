---
layout: post
title: "Notes on Name Mangling in C++"
categories:
- programming
- comipler and linker
tags:
- name mangling
- demangle
- demangling


---


# Name Mangling #

这是一篇网络文章的汇总，大部分都是来自网络上的，
主要借鉴了Chaos Lee的[Name Mangling in C++ ](http://hipercomer.blog.51cto.com/4415661/855223)
以及avabodh的[Name Mangling and Function Overloading](http://www.avabodh.com/cxxin/namemangling.html)

以下是摘自Chaos Lee的概述
---------------------------------------------------------------------------------

Name Mangling概述
大型程序是通过多个模块构建而成，模块之间的关系由makefile来描述。对于由C++语言编制的大型程序而言，也是符合这个规则。
程序的构建过程一般为：各个源文件分别编译，形成目标文件。多个目标文件通过链接器形成最终的可执行程序。
显然，从某种程度上说，**编译器的输出是链接器的输入，链接器要对编译器的输出做二次加工**。
从通信的角度看，这两个程序需要一定的协议来规范符号的组织格式。这就是**Name Mangling产生的根本原因**。

C++的语言特性比C丰富的多，C++支持的函数重载功能是需要Name Mangling技术的最直接的例子。对于重载的函数，不能仅依靠函数名称来区分不同的函数，因为C++中重载函数的区分是建立在以下规则上的：

`函数名字不同 || 参数数量不同||某个参数的类型不同`

那么区分函数的时候，应该充分考虑参数数量和参数类型这两种语义信息，这样才能为却分不同的函数保证充分性。

当然，C++还有很多其他的地方需要Name Mangling，如namespace, class, template等等。
总的来说，Name Mangling就是一种规范编译器和链接器之间用于通信的符号表表示方法的协议，其目的在于按照程序的语言规范，使符号具备足够多的语义信息以保证链接过程准确无误的进行。

------------------------------------------------------------------------------------------------
我记录这份摘要，觉得最有用的信息是：

1. 两篇文章对于 name mangling 的来龙去脉的介绍
2. Name Mangling的规则以及各个编译器为什么不统一 Name Mangling 的原因 
3. abi中的hack方法，以及c++filt和nm两个工具的demangle功能

继续摘抄

>>> 标准

不同编译器使用不同的方式进行name mangling, 你可能会问为什么不将C++的 name mangling标准化，这样就能实现各个编译器之间的互操作了。事实上，在C++的FAQ列表上有对此问题的回答：

"Compilers differ as to how objects are laid out, how multiple inheritance is implemented, how virtual function calls are handled, and so on, so if the name mangling were made the same, your programs would link against libraries provided from other compilers but then crash when run. For this reason, the ARM (Annotated C++ Reference Manual) encourages compiler writers to make their name mangling different from that of other compilers for the same platform. Incompatible libraries are then detected at link time, rather than at run time."

“编译器由于内部实现的不同而不同，内部实现包括对象在内存中的布局，继承的实现，虚函数调用处理等等。所以如果将name mangling标准化了，不错，你的程序确实能够链接成功，但是运行肯定要崩的。恰恰是因为这个原因，ARM鼓励为同一平台提供的不同编译器应该使用不同的name mangling方式。这样在编译的时候，不兼容的库就会被检测到，而不至于链接时虽然通过了，但是运行时崩溃了。”
显然，这是基于“运行时崩溃比链接时失败的代价更大”这个原则而考虑的。


## name demangling ##

### 解析规则 ###

Here are few examples of mangled name for g++ compiler:-
Original name:   void myFun();      Mangled name:  \_Z5myFunv
Original name:  void myFun(int a, char c);      Mangled name:  \_Z5myFunic

looking at the mangled name we can observe the pattern. The pattern looks like: -
\_Z numberOfCharsInOriginalFunctionName OriginalFunctionName parameter_names_encoded


### C++ ABI __cxa_demangle ###

```c
    namespace abi { 
		extern "C" char* __cxa_demangle (const char* mangled_name,      
		char* buf, 
		size_t* n, 
		int* status); 
    } 
```

用于将mangled_name所指向的mangled进行demangle并将结果存放在buf中，n为buf的大小。status存放函数执行的结果，返回值为0表示执行成功。

```c
    /* 
    * Author: Chaos Lee 
    * Description: Employ __cxa_demangle to demangle a mangling function name. 
    * Date:2012/05/06 
    * 
    */ 
    #include<iostream> 
    #include<cxxabi.h> 
    using namespace std; 
     
    using namespace abi; 
     
    int main(int argc,char *argv[]) 
     
    { 
            const char * mangled_string = "_Z9test_funcRiPKcdSsf"; 
     
            char buffer[100]; 
            int status; 
            size_t n=100; 
            __cxa_demangle(mangled_string,buffer,&n,&status); 
     
            cout<<buffer<<endl; 
            cout<<status<<endl; 
            return 0; 
    } 
```

运行结果如下

```shell
$ g++ demangling.cpp -o cxa_demangling
$ ./cxa_demangling 
test_func(int&, char const*, double, std::string, float)
0
```
-------------------------------------------------------------------------------------------------

## the extern "C" ## 

`extern`是用来解决mixed-compiler的场景的，除了我们常用的

```c
#ifdef __cplusplus
extern "C" int libFunction(int x);
#else
int libFunction(int x);
#endif
```

来申明C/C++兼容的编译链接方式外，世界上还是有`extern "c++"`这种东东的，见IBM的资料文档[Name mangling (C++ only)](http://www-01.ibm.com/support/knowledgecenter/SSLTBW_2.1.0/com.ibm.zos.v2r1.cbclx01/cplr020.htm)

The extern "C" linkage specifier can also be used to prevent mangling of functions that are defined in C++ so that they can be called from C. For example,

```c
extern "C" {
   void p(int){
      /* not mangled */
   }
};
```

In multiple levels of nested extern declarations, the innermost extern specification prevails.

```c
extern "C" {
      extern "C++" {
            void func();
      }
}
```

In this example, func has C++ linkage.
