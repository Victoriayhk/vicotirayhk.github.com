---
layout: post
title: Scrapy Doc - 命令行工具
category : translation
tagline: 
tags : [python, scrapy]
---

## 命令行工具

当前的版本是0.10, 这里我们也称它为`Scrapy Tool`. Scrapy tool提供了有不同目的几种命令, 每一个命令的参数都有不同.


## 默认scrapy项目结构

scrapy项目的结构可以修改, 但所有的scrapy项目开始结构都大致如此:

```
scrapy.cfg
myproject/
    __init__.py
    items.py
    pipelines.py
    settings.py
    spiders/
        __init__.py
        spider1.py
        spider2.py
        ...
```
