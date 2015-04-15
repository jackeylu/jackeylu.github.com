---
layout: post
title: "The Art of Readable Code"
categories:
- programming
- social code
tags:
- programming
- readable code


---

```c++

struct Recorder {
	vector<float> data;
	...
	
	void Clear(){
		// Force vector to relinquish its memory (look up "STL swap trick")
		vector<float>().swap(data);
	}
}

```
