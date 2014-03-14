---
layout: post
title: "如何在stl::sort中传递复杂的函数对象？"
categories:
- C++
- Programming
tags:
- c++
- std
- boost
- bind
- c++11

---

先看StackOverflow上的一个问题和答案[Use class method as a comparator in std::sort](http://stackoverflow.com/questions/7930403/use-class-method-comparator-on-stdsort)。


	#include <iostream>
  	#include <algorithm>
	#include <iterator>
	#include <boost/bind.hpp>
  
  	struct S {
    		bool ascending;
    		bool Compare(int lhs, int rhs) {
      			return ascending ? (lhs < rhs) : (rhs < lhs);
    		}
  	};
  	
  	int main () {
  	
    		int i[] = { 1, 3, 5, 7, 8, 6, 4, 2 };
    		S s;
    		s.ascending = true;
    		std::sort(i, i+8, boost::bind(&S::Compare, &s, _1, _2));
    		std::copy(i, i+8, std::ostream_iterator<int>(std::cout, " "));
    		std::cout << "\n";
  		
    		s.ascending = false;
    		std::sort(i, i+8, boost::bind(&S::Compare, &s, _1, _2));
    		std::copy(i, i+8, std::ostream_iterator<int>(std::cout, " "));
    		std::cout << "\n";
  	}
  
上面的例子是构建了一个复杂Compare的最"小麻雀"的实现，因为S有个默认构造函数是不需要传入任何参数的。所以我们通常会实现一个operator() 方法，直接‘’‘std::sort(i, i+8, S());’‘’   这样调用是可以的。

但是若这个S中需要借助其他成员变量时，这样使用会导致编译错误，这时候，我们的一个解决方法就是使用bind方法。若是c++03标准，则只能用boost::bind来实现。若是c++11则可以更好支持这种绑定。

	#include <iostream>
	#include <algorithm>
	#include <iterator>
	#include <boost/bind.hpp>
	
	struct S {
  		bool ascending;
  		S(bool _ascending):ascending(_ascending){}
		
  		bool Compare(int lhs, int rhs) {
    			return ascending ? (lhs < rhs) : (rhs < lhs);
  		}

  		bool operator()(int lhs, int rhs)
  		{
	  		return Compare(lhs, rhs);
  		}
	};
	
	int main () {
	
  		int i[] = { 1, 3, 5, 7, 8, 6, 4, 2 };
		
  		std::sort(i, i+8, S()); //  error: no matching function for call to 'S::S()'
		
  		S s(false);
  		s.ascending = true;
  		std::sort(i, i+8, boost::bind(&S::Compare, &s, _1, _2));
  		std::copy(i, i+8, std::ostream_iterator<int>(std::cout, " "));
  		std::cout << "\n";

  		s.ascending = false;
  		std::sort(i, i+8, boost::bind(&S::Compare, &s, _1, _2));
  		std::copy(i, i+8, std::ostream_iterator<int>(std::cout, " "));
  		std::cout << "\n";
	}

在构造函数更复杂，需要初始化等场景，例如operator() 中的判断方法会需要调用一些成员变量/方法时，常见的operator()就不能直接传递给sort方法，而需要用bind方法来实现。

同样的，如果是C++11的话，还有其他实现方法，即[lambda表达式](http://stackoverflow.com/questions/18273997/passing-a-private-method-of-the-class-as-the-compare-operator-for-stdsort)。

In C++11 there are two different solutions that you can take. The most generic is creating a lambda that captures the this argument:

	std::sort(std::begin(i),std::end(i),
        	  [](int x, int y) { return compare(x,y); }); // or maybe even implement here

A different approach would be binding the object and the member function into a functor:

	std::sort(std::begin(i),std::end(i),
        	  std::bind(&MyClass::compare,this,_1,_2));
        	  
        	  
