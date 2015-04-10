---
layout: post
title: Scrapy Doc - Items
category : translation
tagline: 
tags : [python, scrapy]
---

我没有想到翻译这里的`item`的优美姿势, 所以, 干脆不翻译好了, 个别专有名词是不会影响阅读的.

这一章的主要内容是讲从非结构化源的, 典型的的网页中抓取结构化数据. scrapy专门提供了`Item`类来做此事. `Item`有类似于dict数据的API.

## 定义Item

```
import scrapy

class Product(scrapy.Item):
    name = scrapy.Field()
    price = scrapy.Field()
    stock = scrapy.Field()
    last_updated = scrapy.Field(serializer=str)
```

可以发现, `Item`和`Python Django`中的`Model`非常像, 但item简单得多, 它只有一种Field属性类型.


## Item的Field(域)

`Field`对象用来对每一个域进行元数据的特征化. 比如在上面`last_updated`域的序列化(?serializer)函数

你可以对任何域的元数据进行特征化. 对于`Field`的值没有任何限制