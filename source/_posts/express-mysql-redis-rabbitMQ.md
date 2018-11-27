title: express+mysql+redis+rabbitMQ
author: brian
tags: []
categories: []
date: 2018-11-27 15:33:00
---
做为一名前端开发，最基本的程序原理，数据结构，web框架什么的都得略知一二，如今前端全栈形开发越来越流行，nodejs 已经成为前端工程师的必要武器了。本文分享在NODEJS中使用 EXPRESS + MYSQL + REDIS + RABBITMQ 实现web应用开发。

首先准备环境:

```shell
npm install express -g  # 全局安装
```

使用express 命令创建express 工程

```shell
express prjName
```
>工程目录

![upload successful](/images/pasted-0.png)

好了一个基于express 的工程就创建好了，通过编辑工具打开项目，进行后面的编码操作吧！

如题，我们把express 工程创建好了，其他的好像还没加到工程中来，那我们继续添加以来库。

```bash
npm install mysql --save # 安装mysql
npm install redis --save # 安装redis
npm install amqp --save  # 安装rabbitMQ node库
```

依赖库安装完毕，我们就可以继续代码业务的实现了。
> 
	首先我们创建一个config.js, utils.js
	config.js 中存放 mysql, redis, amqp 的配置信息
    utils.js 实现连接实例的方法

```js
// util.js
var amqp  = require('amqp');
var mysql = require('mysql');
var redis = require('redis');
var {REDIS_CONFIG, MYSQL_DB_CONFIG, RABBIT_MQ_CONFIG} = require('./config');
```
	

1. 创建数据库连接对象
```js
var MYSQL_DB_CONFIG = { // mysql config
    host: '127.0.0.1',
    port: 3306,
    password: '******',
    user: 'root',
    database: 'db_name',
    multipleStatements: true,
    timeout: 60 * 60 * 1000,
    connectionLimit: 100,
    connectTimout: 60 * 60 * 1000,
    acquireTimeout: 60 * 60 * 1000
 }
 
 var create_mysql = function() {
  var conn = mysql.createPool(MYSQL_DB_CONFIG).on('connection', function(conn) {
    console.log('connection ok');
  });

  conn.queryData = function(sql, parameter, callback) {
    conn.getConnection(function(err, _conn) {
      _conn.query(sql, parameter, function(error, results, fields) {
        if(error) {
          throw error;
        } else {
          typeof callback === 'function' ? callback(results, error) : void(0);
        }
        _conn.release();
      });
    });
  };
  return conn
};
```

2. 创建redis对象
```js
var REDIS_CONFIG = { // redis config
    port: '127.0.01',
    host: '6379',
    db: 0,
    password: '',
    connect_timeout: 0
}
var create_redis = function() {
  var r = redis(REDIS_CONFIG);
  r.on('error', function(err) {
    console.log('error:', err);
  });
  return r;
};
```

3. 创建rabbitMQ对象
```js
var = RABBIT_MQ_CONFIG { // rabbitmq config
    host: '127.0.01',
    port: 5672,
    login: 'server',
    password: '******',
    connectionTimeout: 1000
}
var create_mq = function(queue_name, callback) {
  var mq = amqp.createConnection(RABBIT_MQ_CONFIG);
  var _exchange, _queue, _consumerTag = null;
  mq.on('error', function(e){
    console.log(e);
  });

  mq.on('tag.change', function(event) {
    console.log(event);
    if (_consumerTag === event.oldConsumerTag) {
      _consumerTag = event.consumerTag;
      _queue.unsubscribe(event.oldConsumerTag);
    }
  });

  mq.on('ready', function() {
    console.log('ready');
    mq.exchange(queue_name, {type: 'direct', passive: true, durable: true, autoDelete: true}, function(exchange) {
      _exchange = exchange;
      mq.queue(queue_name, {durable: true, autoDelete: false}, function(queue){
        queue.bind(queue_name, queue_name);
        _queue = queue;
        typeof callback === 'function' ? callback(exchange, queue) : void(0);
      });
    });    
  });
  mq.push_data = function(data, fn) {
    console.log(data)
    _exchange.publish(queue_name, data, {
      contentType: 'application/json'
    }, fn);
  };
  return mq;
};
```


> 在app.js 中加入实例调用
```js
{create_mysql} = require('./config/utils')
app.locals.conn = create_mysql()
```

> router调用方法，查询数据

```js
var sql = `select * from yoex_member_login_log LIMIT ${page}, ${page_size}; select count(log_id) as count from yoex_member_login_log`,
app.locals.conn.query(sql,function(results, error){
	res.render('index', {data: results[0], count: results[1][0]})
})
```

> 模版渲染数据
```html
ul
    li
      span 日志ID
      span 用户ID
      span 登陆IP地址
      span 登陆地区
      span 登陆时间
    each item in data
      li
        span=item.log_id
        span=item.member_id
        span=item.ip
        span=item.address
        span=item.add_time
```
##### 查询结果

![upload successful](/images/pasted-2.png)


> rebbitMQ 的使用
```js
// routers/index.js
var { create_mq } = require('../config/utils');
var data = {};
var mq = create_mq('test', function(ex, qu) {
  qu.subscribe(function(msg) {
    console.log('subscribe msg', msg);
    data = msg['d'];
  }).addCallback(function(ok) {
    // qu.unsubscribe(ok.consumerTag);
    console.log('callback', ok);
  });
});
router.get('/', function(req, res, next) {
	var _d = (new Date());
	mq.push_data({'d': _d});
    res.render('index', { title: '上次访问时间:' + data});
})
```

> 页面渲染
```html
h1= title
```
![upload successful](/images/pasted-3.png)

小结：
一个基于express+mysql+redis+rabbitMQ的web应用就基本搭建出来了
相关知识点：

[redis](https://github.com/NodeRedis/node_redis) 

[amqp](https://github.com/postwait/node-amqp) 

[mysql](https://github.com/mysqljs/mysql)