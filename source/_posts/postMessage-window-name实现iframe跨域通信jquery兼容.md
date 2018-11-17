title: postMessage+window.name实现iframe跨域通信jquery兼容
author: brian
tags:
  - javascript
categories: []
date: 2017-04-12 19:03:32
---
在标准的浏览器上面可以用 postMessage 进行通信，那么在不支持这种方法的低版本 IE 上面就不行了，低版本的 IE 可以使用 window 对象的 name 属性来进行跨域的数据通信。

window.name 在低版本 IE 下面有跨域可以在 iframe 之间互相读取而不受限，所以在低版本 IE 下就能通过 互相重写，读取而进行数据的相互交换，但有个问题就是只能是字符串类型的，可以通过传输 JSON 对象数据转化来的字符串。

```js
(function() {
    var util = {
        mix: $.extend,
        stringify: JSON.stringify,
        parse: JSON.parse
    }; // 基础的一些方法
    var usePM = ( typeof window.postMessage !== 'undefined'); // 是否支持 postMessage
    var PM = function( win ) {
        this._win = win;
        this._event = function(){};
        this._initialize();
    };
    util.mix( PM.prototype, {
        _initialize: function() { // 初始化
            var me = this;
            if ( usePM ) {
                if( window.addEventListener ) {
                    window.addEventListener('message', function(e){
                    me._event( util.parse(e.data));
                });
                } else {
                    window.attachEvent('onmessage', function(e){
                        me._event( util.parse(e.data));
                    });
                }
            } else {
                var lastName = window.name;
                setInterval( function() {
                    if( window.name != lastName && window.name != '' ) {
                        lastName = window.name;
                        me._event( util.parse(lastName));
                    }
                }, 50);
            }
        },
        onmessage: function(callback) { // 添加 onmessage 方法
            this._event = callback;
        },
        send: function( data, origin ) { // send 方法
            var wl = window.location;
            var sendOrigin = wl.href.substr( 0, wl.href.indexOf( wl.pathname ) + 1 );
            var sendData = {
                data: data,
                ts: (+(new Date)).toString(10),
                origin: sendOrigin
            }

            if( usePM ) {
                this._win.postMessage( util.stringify(sendData), origin || '*' );
            } else {
                this._win.name = util.stringify(sendData);
            }
        }
    });
    window.XPM = PM;
})();
```

#### 使用方法
```js
var f = window.parent;
var PM = new XPM(f);
PM.onmessage(function(e){
    alert(e.data);
    console.log(e);
});
$('button').bind('click',function(){
    var text = "this is a message!";
    PM.send(text);
});
```