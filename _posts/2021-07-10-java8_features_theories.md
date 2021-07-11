---
layout: post
title: "java8特性实现原理"
date:   2021-07-10
tags: [java]
comments: true
toc: true
author: OJumpyo
---

# 1、lambda表达式
[参考链接](https://www.cnblogs.com/fanguangdexiaoyuer/p/7729235.html)  

java8采用内部类实现lambda表达式:  
- 先生成一个私有的静态函数，这个私有的静态函数实现lambda表达式中的内容
- 然后在该函数中生成一个final类，该类就是调用刚才这个私有的静态函数。

# 2、方法引用
对于有的已经实现了的方法，使用方法引用能避免用lambda再重新实现。

方法引用使用：：关键字，调用并执行时，会在内部生成一个final类，这个类就是lambda创建的那个final类。

# 3、默认方法
使用default关键字提供默认实现

# 4、stream
[参考链接](https://blog.csdn.net/lcgoing/article/details/87918010)

![](https://gitee.com/jumyoright/blog-images/raw/master/images/youdaoNote/java/20210711215839.png)  

这种方式直观，但需要多次迭代。每次迭代需要存储中间结果，难以接受。

![](https://gitee.com/jumyoright/blog-images/raw/master/images/youdaoNote/java/20210711220102.png)  

用循环链表，双端队列实现，每个stage记录前面stage，本次的操作，回调函数。节省资源，并且减少迭代。  

![](https://gitee.com/jumyoright/blog-images/raw/master/images/youdaoNote/java/20210711220344.png)  
只需要实现begin()和end()函数即可。  

# 5、自定义注解
通过@Interface标注该类为注解，使用四个元注解表明该注解的信息。