---
layout: post
title: "Call template function in Java"
category: "Java"
tags: [java,template]
---

### How to call template function in Java ###

Here is a util class:

```
class Util {
    public static <T> boolean compare(T t1, T t2) {
        return t1.equals(t2);
    }
}
```

If you want to call compare with implicit type, you can

```
Util.<String>compare("a", "b")
```
