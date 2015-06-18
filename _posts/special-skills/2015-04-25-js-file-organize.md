---
layout: post
title: JavaScript Skill - 组织js文件
category : note
tagline: 
tags : [JavaScirpt]
---

## 在一个js文件里include其它js文件

JavaScript没有import, include, require等方法来引入或包含一个别的脚本的函数. 但是我们仍然有其他的方法来达到这个目的.

### 方法一: eval

### 方法二: JQuery

```
$.getScript('script.js', function(){...});
```

### 方法三

```
function loadScript(url, callback) {
	// http://stackoverflow.com/questions/950087/include-a-javascript-file-in-another-javascript-file
    // Adding the script tag to the head as suggested before
    var head = document.getElementsByTagName('head')[0];
    var script = document.createElement('script');
    script.type = 'text/javascript';
    script.src = url;

    // Then bind the event to the callback function.
    // There are several events for cross browser compatibility.
    script.onreadystatechange = callback;
    script.onload = callback;

    // Fire the loading
    head.appendChild(script);
}
```