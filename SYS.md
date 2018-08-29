### 服务器
> 阿里云(1550/年)
  1. DDR 2GB
  2. CPU 2CORE
  3. HD 20GB
  4. 网络 2MB
  5. OS  ubuntu 16.04

### 产品前端

  ##  主页
  |   名称   |  所属模块  |  描述  |
  | -------- | --------- | ------ |
  | 图片轮播  | 主页  | 2-4张图片  |
  | 导航栏 | 主页  | 景区概况，动态，游览，玩转平南，特产商店  |
  | 景点简介 | 主页  | 文字，图片介绍  |
  | 景区游览  | 主页推荐  | 3-6张图集展示  |
  | 景点动态  | 主页  | 最新景点动态展示  |
  | 特产   | 主页  | 特产商品推荐，3-5个 图片 + 描述  |

  ## 景点动态
  |   名称   |  所属模块  |  描述  |
  | -------- | --------- | ------ |
  | 资讯列表  | 资讯  |  图文标题，列表展示 ，支持分页  |
  | 资讯详情  | 资讯  | 标题，发布时间，正文内容 |

  ## 景区游览
  |   名称   |  所属模块  |  描述  |
  | -------- | --------- | ------ |
  | 相册列表  | 相册  | 景点名称，相册数量，查阅次数  |
  | 相册查阅  | 相册  | 图片左右滑动查阅，底部图片文字描述 |

  ## 景点概况
  |   名称   |  所属模块  |  描述  |
  | -------- | --------- | ------ |
  | Tab列表  | 概况  | Tab列表展示简介，文化  |
  | 景点简介  | 概况  | 图文描述   |
  | 景区文化  |  概况 | 图文描述  |

  ## 特产商城
  |   名称   |  所属模块  |  描述  |
  | -------- | --------- | ------ |
  | 商品列表  | 特产  | 特产商品展示，缩略图，价格，文字简介  |
  | 商品详情  | 特产  | 图片轮播（3-5张）文字描述，详情，图片展示，电话订购  |

  ## 玩转平南
  |   名称   |  所属模块  |  描述  |
  | -------- | --------- | ------ |
  | Tab列表  | 玩转平南  | Tab列表展示景点，人文，特产  |
  | 平南旅游景点  | 玩转平南  | 图文描述介绍 |
  | 人文历史  |  玩转平南 | 图文描述 |
  | 特产介绍  |  玩转平南 | 图文描述 |

  ## 联系我们
    1.联系我们，电话，地址信息

  ## 服务条款
    1.服务内容说明

  ## 帮助中心
    1.常见问题咨询

### 管理后台

```code
  1. 登陆
  2. 资讯管理
    1. 录入，修改
    2. 支持图片，视频地址
  3. 问答管理
    1. 回复，修改
  4. 相册管理
    1. 上传，修改
  5. 系统设置
    1. 景点名字设置
    2. 客服电话管理
    3. 登陆密码修改
  6. 主页轮播图片设置
  7. 特产商品管理
    1. 特产列表数据
    2. 信息录入，修改
```

### 接口文档
  >主页轮播接口

  ```coffee
  @URL: '/get/banner'
  @method: GET
  @param: null
  @return: json
  {
    data: [
      {
        img: '<url>',
        desc: '<desc>'
      }
      ...
    ]
  }
  ```

  >景区游览列表(最多6张图)

  ```coffee
  @URL: '/get/album'
  @method: GET
  @param: null
  @return: json
  {
    data: [
      {
        img: '<url/>',
        id: <ID>,
        desc: '<desc>'
      }
    ]
  }
  ```

  >玩转平南

  ```coffee
  @URL: '/get/pingnan'
  @method: GET
  @param: null
  @return: json
  {
    data: {
      attractions:'<markdown>',
      history:'<markdown>',
      specialty: '<markdown>'
    }
  }
  ```

  >景点简介

  ```coffee

  #景点简介
  @URL: '/get/attractions/desc'
  @method: GET
  @param: null
  @return: json
  {
    data: {
      desc: '<markdown>'
    }
  }

  #景区文化
  @URL: '/get/attractions/cultural'
  @method: GET
  @param: null
  @return: json
  {
    data: {
      desc: '<markdown>'
    }
  }
  ```

  >景点动态

  ```coffee
  #动态列表
  @URL: '/get/dyn/news'
  @method: GET
  @param: null
  @return: json
  {
    data: [
      {
        id: 10001,
        title: '标题',
        tumblr_img: '缩略图',
        text: '....',
        create_time: '2018-03-28 15:20'
      }
      ...
    ]
  }
  # 动态详情
  @URL: '/get/dyn/news/info'
  @method: GET
  @param: {id: 10001} #新闻ID
  @return: json
  {
    data: {
      id: 10001,
      title: '标题',
      tumblr_img: '缩略图',
      text: '....<markdown>',
      create_time: '2018-03-28 15:20'
    }
  }
  ```

  >特产

  ```coffee
  #特产列表
  @URL: '/get/specialty/list'
  @method: GET
  @param: null
  @return: json
  {
    data: [
      {
        id: 10001,
        title: '标题',
        desc: '简介',
        tumblr_img: '缩略图',
        amount: 10.3,
        create_time: '2018-03-28 15:20'
      }
      ...
    ]
  }

  #特产详情
  @URL: '/get/specialty/info'
  @method: GET
  @param: {id: 10001} #特产ID
  @return: json
  {
    data: {
      id: 10001,
      title: '标题',
      desc: '简介',
      text: '详细介绍',
      tumblr_img: '缩略图',
      imgs: ['<url>', '<url>', '<url>'],
      amount: 10.3,
      tel: '0755-57868788', 订购电话
      create_time: '2018-03-28 15:20'
    }
  }
  ```

  >景区游览

  ```coffee

  #相册列表
  @URL: '/get/album/list'
  @method: GET
  @param: null
  @return: json
  {
    data: [
      {
        id: 10001,
        tumblr_img: '<url>',
        title: '相册名',
        count: 100,
        read_count: 1200
      }
      ...
    ]
  }

  #查看某个相册图集
  @URL: '/get/album/info'
  @method: GET
  @param: null
  @return:json
  {
    data: {
      id: 10001,
      tumblr_img: '<url>',
      title: '相册名',
      count: 100,
      read_count: 1200
      imgs: ['<url>', '<url>', '<url>', '<url>',...]
    }
  }
  ```

  >系统信息查询

  ```coffee
  @URL: '/get/sys'
  @method: GET
  @param: null
  @return: json
  {
    data: {
      sys_name: 'xx景点',
      sys_phone: '0755-88943292',
    }
  }
  ```
