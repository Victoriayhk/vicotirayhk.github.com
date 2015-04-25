---
layout: post
title: Python Skill - 关于**kwargs的使用
category : note
tagline: 
tags : [python]
---

python的函数有两种接受不定长参数的方法, 一种是`*args`, 一种是`**kwargs`, 前者是一种list, 后者是一种dict.

我们知道参数有两个属性, 一个是参数的名称, 一个是参数的值, 比如`func(foo = bar)`, 中`foo`是参数名, `bar`是参数值. 但`*args`就像list一样, 只有值, 没有用于索引的名称; `**kwargs`作为dict是有参数名(key)和参数值(value)的.

既然`**kwargs`是一种dict, 意味着应该有直接将dict类型数据结构传递给有`**kwargs`类型参数的函数, 用法是这样的:

```
func(**{ key1: value1, key2: value2 })
func(**some_dict)
```

是不是很方便?