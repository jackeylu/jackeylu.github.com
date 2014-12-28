---
layout: post
title: "macros in vim"
categories:
- vim
- tips
tags:
- vim
- macro
- recording


---
The macro function in vi/vim is `recording`, see the `:help recording` for more information.

An example of coment all the lines contained `abc1` or `abc2`.

```javascript
1. gg, to the first line
2. q1, recording a new macro named `1`
3. /abc[12], searching it
4. 0, go to the head of found line
5. i, begin insert
6. #, insert the comment character
7. <ESC>j, go to the next line
8. q. stop recording

```

when running the macro, we would like to set no row wrap scan to avoid more comments in one line.

```javascript
gg
:set nowrapscan
100@1, running the macro 1 100 times.
```



