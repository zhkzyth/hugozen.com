---
layout: post
title: "the little scheme"
date: 2012-12-02 15:24
comments: true
published: true
tags:
- reading
- notes
- lisp
- scheme
categories: tech
---

Introdcution
------------
scheme, a dialect of lisp.

Notes
------
- The law of Car
    The primitive `car` is defined only for non-empty lists.The `cdr` of any nonempty list is always another list.

- The law of Cons
    The primitive `cons` takes two arguments.The second arugument to `cons` must be a list.The result is a list.

- The law of Null?
    The primitive `null?` is defined only for lists.
<!--more-->

-  The law of eq?
    The primitive `eq?` takes two arguments.Each must be a nonnumeric atom.

- The First Commandment
    Always ask `null?` as the first question in expressing any function.

- The Second Commandment
    Use `cons` to build lists.

- The Third Commandment
    When building a list,describe the first typical element,and then cons it onto the natural recursion.

- The Fourth Commandment
    Always change at least one argument while recurring.It must be changed to be closer to termination.The changing argument must be tested in the termination condition:when using cdr,test termination with `null?`.

- The Fifth Commandment
    When building a value with +,always use 0 for the value of the terminating line,for adding 0 does not change the value of an addition.
    When building a value with x,always use 1 for the value of the terminating line,for multiplying by 1 doese not change the value of a multiplication.
    When building a value with cons,always consider () for the value of the terminating line.
