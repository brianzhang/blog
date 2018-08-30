title: javascript正则表达式提取指定字符
author: brian
tags:
  - js
categories: []
date: 2013-08-19 09:36:00
---
```js

var url = "http://v.youku.com/v_show/id_XMjM0OTI4NzMy.html";
function getInfo(source,start,end){
  var oReg=new RegExp(start+".*?"+end,"img");
  var oRegStart=new RegExp(start,"g");
  var oRegEnd=new RegExp(end,"g");
  return source.match(oReg).join("=").replace(oRegStart,"").replace(oRegEnd,"").split("=");
}
getInfo(url, "id_", ".html");
```

```bash
> XMjM0OTI4NzMy
```