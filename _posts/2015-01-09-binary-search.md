---
layout: post
title: "Talk on Binary Search"
categories:
- programming
- algorithm
tags:
- algorithm
- binary search


---

```java
/*
* A binary search implementation from Gaston Gonnet.
* http://www.tbray.org/
*/
package binary;

public class Finder {
	public static int find(String[] keys, String target) {
		int high = keys.length;
		int low = -1;
		// high and low in the first step, are all out of the range of keys,
		// this help to avoid the comparing of range values.
		while (high - low > 1) { // DO NOT TRY TO COMPARE THE VALUE OF PROBE FOR LARGE LENGTH.
			int probe = low+(high-low) >> 1; // /2 may be integer overflow in same case
			if	(keys[probe].compareTo(target) > 0 )
				high = probe;
			else
				low = probe;
		}
		
		if (low == -1 || keys[low].compareTo(target) != 0)
			return -1;
		else
			return low;
	}
}

```

For any large n of len(keys), we would better not to check the target in the condition of `while`,
as in the most scene, the probability of get the target in the first step are too small.
