
---
layout: post
title: "Contiguous vs. Linked Data Structures in The Algorithm Design Manual"
categories:
- Algorithms
- Data Structures
tags:
- contigous data structures
- linked data structures
- array
- link/list


---

# The Array data structure #

The `array` is the fundamental contiguously-allocated data structure. Arrays are structures of fixed-size data records such that each element can be efficiently located by its `index` or (equivalently) address.

Advantages of contiguously-allocated arrays include:

1. *Constant-time access given the index* -- Because the index of each element maps directly to a particular memory address, we can access arbitrary data items instantly provided we know the index.

1.  *Space efficiency* - Arrays consist purely of data, so no space is wasted with links or other formatting information. Further, end-of-record information is not needed becasuse arrays are built from fixed-size records.
2.  *Memory locality* - A common programming idiom involves iterating through all the elements of a data structure. Arrays are good for this because they exhibit excellent memory locality. Physical continuity between successive data accesses helps exploit the high-speed *cache memory* on modern computer architectures. 连续存储的空间，有利于提高告诉缓存命中率。

The downside of arrays is that we cannot adjust their size in the middle of a program's execution^[array-size].
容易出现内存越界，例如 add the (n+1)st customer, if we only allocate room for n records.

在空间利用效率和数组访问便利性上的均衡，来自于*dynamic arrays*方法。 

[array-size]: in modern data container, like the vector in stl, it provided the `size()` method to get the size. 

# Pointers and Linked Structures

*Pointers* are the connections that hold the pieces of linked structures together. Pointers represent the address of a location in memory. 

All linked data structures share certain properties, as revealed by the following
linked list type declaration: 

    typedef struct list {
    	item_type item; 		/* data item */
		struct list *next; 		/* point to successor */
	} list;

In particular:

1. Each node in our data structure (here **list**) contains one or more data fields
(here **item**) that retain the data that we need to store.
1. Each node contains a pointer field to at least one other node (here **next**).
This means that much of the space used in linked data structures has to be
devoted to pointers, not data.
1. Finally, we need a pointer to the head of the structure, so we know where to
access it.

The list is the simplest linked structure. The three basic operations supported
by lists are *searching*, *insertion*, and *deletion*. In *doubly-linked lists*, each node points
both to its predecessor and its successor element. This simplifies certain operations
at a cost of an extra pointer field per node.

## Searching a List ##

A recursively version of searching a list:

	list *search_list(list *l, item_type x)
	{
		if (l == NULL) return(NULL);
		if (l->item == x)
			return(l);
		else
			return( search_list(l->next, x) );
	}

## Insertion into a List ##

	void insert_list(list **l, item_type x)
	{
		list *p; 		/* temporary pointer */
		p = malloc( sizeof(list) );
		p->item = x;
		p->next = *l;
		*l = p;
	}

## Deletion From a List ##

Find the predecessor of item `x` in list `**l`,

	list *predecessor_list(list *l, item_type x)
	{
		if ((l == NULL) || (l->next == NULL)) {
			printf("Error: predecessor sought on null list.\n");
			return(NULL);
		}
		if ((l->next)->item == x)
			return(l);
		else
			return( predecessor_list(l->next, x) );
	}

Delete item `x` in a list `**l`,

	delete_list(list **l, item_type x)
	{
		list *p = NULL; 			/* item pointer */
		list *pred = NULL ; 		/* predecessor pointer */		
		
		p = search_list(*l,x);
		if (p != NULL) {
			pred = predecessor_list(*l,x);
			if (pred == NULL) /* splice out out list */
				*l = p->next;
			else
				pred->next = p->next;
			free(p); /* free memory used by node */
		}
	}

C language requires explicit deallocation of memory, so we must **free** the
deleted node after we are finished with it to return the memory to the system.

## Comparison ##

The relative advantages of linked lists over static arrays include:

1. Overflow on linked structures can never occur unless the memory is actually
full.
2. Insertions and deletions are simpler than for contiguous (array) lists.
3. With large records, moving pointers is easier and faster than moving the
items themselves.

while the relative advantages of arrays include:

1. Linked structures require extra space for storing pointer fields.
2. Linked lists do not allow efficient random access to items.
3. Arrays allow better memory locality and cache performance than random
pointer jumping.

One final thought about these fundamental structures is that they can be
thought of **as recursive objects**:

- *Lists* – Chopping the first element off a linked list leaves a **smaller linked list**.
This same argument works for strings, since removing characters from string
leaves a string. Lists are recursive objects.
- *Arrays* – Splitting the first k elements off of an n element array gives two
**smaller arrays**, of size k and n−k, respectively. Arrays are recursive objects.

This insight leads to simpler list processing, and efficient **divide-and-conquer**
algorithms such as *quicksort* and *binary search*.
