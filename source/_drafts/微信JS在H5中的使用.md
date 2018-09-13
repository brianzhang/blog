title: 微信JS-SDK在H5中的使用
author: brian
date: 2018-09-13 17:30:40
tags:
---
#### 微信JS SDK的功能

> 
	微信JS-SDK是微信公众平台面向网页开发者提供的基于微信内的网页开发工具包。
	通过使用微信JS-SDK，网页开发者可借助微信高效地使用拍照、选图、语音、位置等手机系统的能力，同时可以直接使用微信分享、扫一扫等微信特有的能力，为微信用户提供更优质的网页体验。
    
通过使用微信的JS-SDK，你可以让你网页在微信内调用拍照、语音、支付、位置、扫一扫这些只能在微信内使用的功能。进过下面的步骤，一步一步的配置，就可以让你正确的在项目中引入微信的JS-SDK。

再正式使用微信JS SDK前，请查阅官方文档中5个重要步骤。

[官方文档](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115)


#### 引入微信的JS文件

1. 在需要调用JS接口的页面引入如下JS文件，（支持https)http://res.wx.qq.com/open/js/jweixin-1.4.0.js


  ```html
  <script src="http://res.wx.qq.com/open/js/jweixin-1.4.0.js"></script>
  ```

2. 但是只支持使用 AMD/CMD 标准模块加载方法加载。于是我就在npm的官网上找到了发布后的js-sdk，支持CommonJS的引入方式。npm的地址在[这里](https://www.npmjs.com/package/weixin-js-sdk)。可以在你的项目中使用如下命令安装。

  ```bash
  npm install weixin-js-sdk
  ```

安装好后在js代码中引入

  ```js
    // 使用CommonJS规范
	let wx = require('weixin-js-sdk')
    //  CoffeeScript
    wx = require 'weixin-js-sdk'
    // ES6
    import wx from 'weixin-js-sdk'
  ```

完成js引入后下面我们讲讲如何确实的在前端js中使用

#### wx.config 鉴权签名算法

>	
	生成签名之前必须先了解一下jsapi_ticket，jsapi_ticket是企业号号用于调用微信JS接口的临时票据。正常情况下，jsapi_ticket的有效期为7200秒，通过access_token来获取。由于获取jsapi_ticket的api调用次数非常有限，频繁刷新jsapi_ticket会导致api调用受限，影响自身业务，开发者必须在自己的服务全局缓存jsapi_ticket。

1.参考以下文档获取access_token（有效期7200秒，开发者必须在自己的服务全局缓存access_token）：../15/54ce45d8d30b6bf6758f68d2e95bc627.html

2.用第一步拿到的access_token 采用http GET方式请求获得jsapi_ticket（有效期7200秒，开发者必须在自己的服务全局缓存jsapi_ticket）：https://api.weixin.qq.com/cgi-bin/ticket/getticket?access_token=ACCESS_TOKEN&type=jsapi

```json
{
    'errcode': 0,
    'errmsg': 'ok',
    'ticket': 'bxLdikRXVbTPdHSM05e5u5sUoXNKd841ZO3MhKoyN5OfkWITDGgnr2fwJ0m9E8NYzWKVZvdVtaUgWvsdshFKA',
    'expires_in': 7200,
}

```

##### 签名算法

签名生成规则如下：参与签名的字段包括noncestr（随机字符串）, 有效的jsapi_ticket, timestamp（时间戳）, url（当前网页的URL，不包含#及其后面部分） 。对所有待签名参数按照字段名的ASCII 码从小到大排序（字典序）后，使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串string1。这里需要注意的是所有参数名均为小写字符。对string1作sha1加密，字段名和字段值都采用原始值，不进行URL 转义。

即signature=sha1(string1)。 示例：

```js
noncestr=Wm3WZYTPz0wzccnW
jsapi_ticket=sM4AOVdWfPE4DxkXGEs8VMCPGGVi4C3VM0P37wVUCFvkVAy_90u5h9nbSlYy3-Sl-HhTdfl2fzFy1AOcHKP7qg
timestamp=1414587457
url=http://mp.weixin.qq.com?params=value
```
步骤1. 对所有待签名参数按照字段名的ASCII 码从小到大排序（字典序）后，使用URL键值对的格式（即key1=value1&key2=value2…）拼接成字符串string1：

```js
jsapi_ticket=sM4AOVdWfPE4DxkXGEs8VMCPGGVi4C3VM0P37wVUCFvkVAy_90u5h9nbSlYy3-Sl-HhTdfl2fzFy1AOcHKP7qg&noncestr=Wm3WZYTPz0wzccnW&timestamp=1414587457&url=http://mp.weixin.qq.com?params=value
```

步骤2. 对string1进行sha1签名，得到signature：

```js
0f9de62fce790f9a083d5c99e95740ceb90c27ed
```

注意事项

	1.签名用的noncestr和timestamp必须与wx.config中的nonceStr和timestamp相同。
	2.签名用的url必须是调用JS接口页面的完整URL。
	3.出于安全考虑，开发者必须在服务器端实现签名的逻辑。


[签名校验工具](https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=jsapisign)

完成签名生成后，即可开始配置wxjs-sdk的使用了

```js
wx.config({
    debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
    appId: '', // 必填，企业号的唯一标识，此处填写企业号corpid
    timestamp: , // 必填，生成签名的时间戳
    nonceStr: '', // 必填，生成签名的随机串
    signature: '',// 必填，签名，见附录1
    jsApiList: [] // 必填，需要使用的JS接口列表，所有JS接口列表见附录2
});
```
[weixinJS-SDK接口列表](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115)

写了这么多，我们来一段实际应用的代码

```js
import wx from 'weixin-js-sdk'
ajax.get({url: <getSignature>}).then((resp) => {
	if (resp.ok) {
      let signature = resp.data
      signature.debug = false
      signature.signature = signature.sign
      signature.nonceStr = signature.noncestr
      signature.appId = signature.appid
      signature.jsApiList = ['onMenuShareTimeline', 'checkJsApi',
        'hideOptionMenu', 'showOptionMenu', 'onMenuShareAppMessage'
      ]
      wx.config(signature)
      wx.ready(()=> {
      	let callback = {
        	title: 'test',
            desc: 'test',
            imgUrl: 'http://yourdomain.com',
            link: 'http://yourdomain.com',
            success: () => {
            	console.log('分享成功')
            },
            cancel: () => {
            	console.log('分享失败')
            },
            trigger: () => {
            	console.log('')
            },
            complete: () => {
            	console.log('接口调用完成')
            },
            fail () {
            	console.log('调用失败')
            }
        }
        wx.onMenuShareAppMessage(callBack)
        wx.onMenuShareTimeline(callBack)
        wx.onMenuShareQQ(callBack)
        wx.onMenuShareWeibo(callBack)
        wx.onMenuShareQZone(callBack)
      })
    }
})
```
ajax 调用就是获取签名数据后实现签名注册

	注意：不要尝试在trigger中使用ajax异步请求修改本次分享的内容，因为客户端分享操作是一个同步操作，这时候使用ajax的回包会还没有返回。


##### 最后
---

	如果页面的url发生了变化，在新的url下调用js-sdk一定要再调用一次签名接口，用新的url再进行一次签名，否则会调用不成功。
    
[官方文档](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421141115)

[在线调试工具](https://mp.weixin.qq.com/debug)

[签名校验工具](https://mp.weixin.qq.com/debug/cgi-bin/sandbox?t=jsapisign)
