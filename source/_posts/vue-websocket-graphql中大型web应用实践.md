title: vue+websocket+graphql中大型web应用实践
author: brian
date: 2018-08-29 16:39:17
tags:
---
> 随着前端框架的不断成熟，MVVM已成为主流的WEB前端开发框架，今天我给大家带来之前项目中的vue的实践

--------------
> 项目介绍，这个项目主要是app内嵌h5，前后端分离都是共识了，前后端分离不知大家有没有遇到这样一个问题（通病），后端提供接口开发的人员往往写好接口给前端开发对接后基本没文档，如接口发生改变，传参，返回数据有变动，前端异常情况，背锅的总是前端出问题。在这样的前提下，我们找到了一个比较的解决方案Graphql的应用，下面我就一一给大家讲解一下项目中如何使用


如果对graphql还不大了解的可以去官网了解下 https://graphql.cn/
>GraphQL 既是一种用于 API 的查询语言也是一个满足你数据查询的运行时。 GraphQL 对你的 API 中的数据提供了一套易于理解的完整描述，使得客户端能够准确地获得它需要的数据，而且没有任何冗余，也让 API 更容易地随着时间推移而演进，还能用于构建强大的开发者工具。

他的好处就是，后端提供多少可用使用的数据字段，前端开发可根据需求传递定制。


项目结构：

```bash
> build/
  config/
  src/
	assets/
    	# css, scss, img
    components/
    	# 组建
    controller/
    	index.js # 入口文件
    	graphql.js # graphql 封装
        websocket.js # 长链接封装
    router/
    	router.js # vue-router 路由
    utils/
    	api.js # axios ajax 封装
        utils.js # 通用库
    views/
    	# page
    vuex/
    	# vuex store module
  static/
  	# 公共图片，js
  test/
  	# test.js
  index.html
```

> 整个项目使用vue构建，vue-router做页面路由，vuex 状态树管理

关键流程实现

1. ajax的封装

```js
// api.js 代码片段

let ajax = (opt) => {
	let options = {
    	method: opt.type,
        url: opt.url,
        headers: opt.headers,
        timeout: 1000
    }
    return axios(options).catch((e) => {
    	if (e) {
        	console.log('ajax error', e.toString())
        }
    })
}
```

2. graphql 接口实现

```js
// graphql.js 代码片段
const postGraphql = (query, data) => {
	return Prom((resolve, reject) => {
    	ajax.post({
        	url: <url>,
            data: {
            	query: query,
                ...data
            }
        }).then(({ data }) => {
        	resolve(data)
        }).catch(e => {
        	reject(e)
        })
    })
}
export {
	postGraphql
}
```

3. 实现调用

```js
// index.js 调度模块
let getData = (data)=> {
	let queryString = `
	query {
	rankList(slug: "${slug}", page: ${page}, pageSize: ${page_size}) {
        userId
        rank
        score
        user {
          ${DATA_MODEL.USER}
          glamour
          level{
            level
            exp
            levelName
          }
        }
      }
    }
    `
    return postGraphql(queryString)
}
```

4. 页面调用

```js
# vue 页面调用
import getData from index
let data = getData({page: 1, slug: 'a', page_size: 10})
```