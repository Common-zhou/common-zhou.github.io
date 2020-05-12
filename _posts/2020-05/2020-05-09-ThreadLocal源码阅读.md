---
layout: post
title:  "ThreadLocal源码阅读"
categories: 源码
tags: 源码 ThreadLocal
excerpt: ThreadLocal源码阅读
auth: zhou
---
* content
{:toc}


# 1.ThreadLocal源码

## 1.1 简介 

## 1.2 set方法

```java
//thread的一个成员变量
ThreadLocal.ThreadLocalMap threadLocals = null;

public void set(T value) {
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            map.set(this, value);
        } else {
            createMap(t, value);
        }
}

ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
}

void createMap(Thread t, T firstValue) {
        t.threadLocals = new ThreadLocalMap(this, firstValue);
}
```



## 1.3 get方法

