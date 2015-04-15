---
layout: post
title: "Alternative operator representations"
categories:
- programming
- c++
tags:
- c++
- Alternative operator


---


Can this code be compiled?

```c++
%:include <iostream>
 
int main(int argc, char *argv<::>) 
<%
    if (argc > 1 and argv<:1:> not_eq NULL) <%
        std::cout << "Hello, " << argv<:1:> << '\n';
    %>
%>

```

yes! this is alternative operator representations, see <http://en.cppreference.com/w/cpp/language/operator_alternative> for more details.

It is same with
```c++

#include <iostream>
 
int main(int argc, char *argv[]) 
{
    if (argc > 1 and argv[1] != NULL) {
        std::cout << "Hello, " << argv[1] << '\n';
    }
}

```
