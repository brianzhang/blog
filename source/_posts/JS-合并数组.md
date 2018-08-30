title: JS 合并数组
author: brian
tags:
  - javascript
categories: []
date: 2012-08-03 22:10:00
---
>
案例说明：

        某充话费充值渠道支持a充值列表，但某充值卡的面额只有b列表面额
        其中other表示其他输入金额  
        var a = [5, 10, 20, 50, 100, 'other'];

        var b = [10,  30, 50];
        理想结果：a渠道能支持b卡充值：[10, 50]
        

```js
function ArrayIntersection( a, b ) {//合并数组  
	var ai=0, bi=0;  
    var result = new Array();  
    while ( ai < a.length && bi < b.length ) {  
    	if ( a[ai] < b[bi] ) {   
        	ai++;   
		} else if ( a[ai] > b[bi] ) {   
			bi++;
		} else {  
			result.push ( a[ai] );  
			ai++;  
			bi++;  
		}  
	}  
	return result;  
}  
```

```bash
# run
> var new_item = ArrayIntersection(a, b);  
> console.log(new_item);
> [10, 50]
```

 