title: 'javascript获取Flash下载进度兼容IE, FireFox,Chrome浏览器'
author: brian
tags:
  - html
  - javascript
categories: []
date: 2011-08-09 23:25:00
---
```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type" content="text/html; charset=gb2312" />
<title>flash load progress</title>
<script>
</script>
</head>
<body>
<p><span class="intro">下载进度：</span>
  <p class="barborder">
    <p class="bar" id="loadpercent"></p>
  </p>
</p>
<object classid="clsid:D27CDB6E-AE6D-11cf-96B8-444553540000" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,29,0" width="700px" height="350" id="ieGame">
    <param name="movie" value="http://360.kuaiwan.com/games/6/6C36DF75DE92FF1148592FF0F093E5C00DA17A91.swf">
    <param name="quality" value="high"><param name="wmode" value="transparent">
    <embed 
        wmode="transparent"         src="http://360.kuaiwan.com/games/6/6C36DF75DE92FF1148592FF0F093E5C00DA17A91.swf" 
        quality="high" 
        type="application/x-shockwave-flash"         pluginspage="http://www.macromedia.com/go/getflashplayer" 
        width="700px" height="350px" id="fireFoxGame" name="fireFoxGame">
    </embed>
</object>
</body>
</html>
```

```js
if(window.navigator.userAgent.indexOf("Firefox")>=1 || window.navigator.userAgent.indexOf("AppleWebKit")>=1) {
  /*is fireFox*/
  var progress = self.setInterval(function() { 
  var progress = document.fireFoxGame;
  document.getElementById("loadpercent").innerHTML=""+progress.PercentLoaded();
 }, 5);
  //document.getElementById("loadpercent").innerHTML=""+Math.abs(progress)+"%";
  } else { /*is IE*/
	refreshProgress();
  }
  function show(){//隐藏AD层、显示FLASH层
    document.getElementById("showAD").style.display='none';
    //document.getElementById("showSWF").style.display='block';
  }
 
 function refreshProgress() { //刷新进度条函数
   var bar = document.getElementById("loadpercent");
   var movie = document.getElementById("ieGame");
   var nPercentLoaded = Math.abs(movie.PercentLoaded());
   bar.innerHTML= nPercentLoaded+"%";
   if(nPercentLoaded==100) {
     bar.innerHTML= "Game download is complete";
   } else {
    setTimeout('refreshProgress()',0);
   }
 }
```