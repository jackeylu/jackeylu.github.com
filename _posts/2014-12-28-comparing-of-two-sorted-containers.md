---
layout: post
title: "Comparing of two sorted containers"
categories: 
- programe
- algorithm

tags:
- algorithm
- sorted
- comparing 


---

How to Find the same and different elements in two sorted containers?
======================================================================

## Input ##
Two sorted containers `A` and `B`, they have the same element type.

## Output ##

1. same elements in `A` and `B`
2. elements only in `A`
3. elements only in `B`

## Requirements ##

1. linear time complexity of length `A` and `B`

## Solution ##

``` C++

Container DA; // elements only in `A`
Container DB; // elements only in `B`
Container Same; // same elements in both

Container::size_type i, j;

for (i =0, j = 0; i < len(A) and j < len(B); )
{
   if (A[i] == B[j])
   {
      add A[i] to Same;
      i++;
      j++;
      continue;
   }   

   if (A[i] < B[j])
   {
      add A[i] to DA;
      i++;
      continue;
   }

   if (A[i] > B[j])
   {
      add B[j] to DB;
      j++;
      continue;
    }
}

for (; i < len(A); i++)
{
    add A[i] to DA;
}


for (; j < len(B); j++)
{
    add B[j] to DB;
}

```

## Analysis ##

well-known, the time complexity is len(A)+len(B).


