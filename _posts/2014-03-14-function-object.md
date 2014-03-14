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
  
上面的例子是构建了一个复杂Compare的最"小麻雀"的实现，因为S有个默认构造函数是不需要传入任何参数的，所以

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

  std::sort(i, i+8, S());

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

