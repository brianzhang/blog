title: css修改滚动条默认样式
author: brian
tags:
  - css3
  - ''
  - ''
categories: []
date: 2016-08-10 19:22:00
cover: /img/2.jpg
---
>写之前，先了解一下css3 webkit支持的特性

	::-webkit-scrollbar    //滚动条整体部分    
  	::-webkit-scrollbar-button   //滚动条两端的按钮  
  	::-webkit-scrollbar-track   // 外层轨道  
  	::-webkit-scrollbar-track-piece    //内层轨道，滚动条中间部分（除去）  
  	::-webkit-scrollbar-thumb //滚动条里面可以拖动的那个  
  	::-webkit-scrollbar-corner   //边角  
  	::-webkit-resizer   ///定义右下角拖动块的样式  

```scss
/*scss 代码*/
/*滚动条样式*/  
 .innerbox::-webkit-scrollbar {/*滚动条整体样式*/  
     width: 4px;     /*高宽分别对应横竖滚动条的尺寸*/  
     height: 4px;  
 }  
 .innerbox::-webkit-scrollbar-thumb {/*滚动条里面小方块*/  
     border-radius: 5px;  
     -webkit-box-shadow: inset 0 0 5px rgba(0,0,0,0.2);  
     background: rgba(0,0,0,0.2);  
 }  
 .innerbox::-webkit-scrollbar-track {/*滚动条里面轨道*/  
     -webkit-box-shadow: inset 0 0 5px rgba(0,0,0,0.2);  
     border-radius: 0;  
     background: rgba(0,0,0,0.1);  
 }  
```
>设置scrollbar的为CSS伪元素，对应上图的数字：

```bash
::-webkit-scrollbar              { /* 1 */ }    
::-webkit-scrollbar-button       { /* 2 */ }    
::-webkit-scrollbar-track        { /* 3 */ }    
::-webkit-scrollbar-track-piece  { /* 4 */ }    
::-webkit-scrollbar-thumb        { /* 5 */ }    
::-webkit-scrollbar-corner       { /* 6 */ }    
::-webkit-resizer                { /* 7 */ }  
```