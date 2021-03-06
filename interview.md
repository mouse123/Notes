---
description: Interview prepare
---

# Project Problem:

#### Safari阻塞加载：

* 问题：接入google 的 gtag 统计=&gt;加载 google adsence 的统计=&gt;加载 facebook 的统计。 由于资源在国外的限制，导致不翻墙要请求会超时。
* safari 分析：
  1. 资源加载失败，会重试几次，导致如果要彻底失败，要等好几分钟
  2. 如果是资源加载失败导致整个页面的加载状态还没有结束的时候，这时候重复的get请求就会直接取之前的get请求的缓存 \(前提是参数，路径的 url 都一样\)
  3. 资源彻底加载失败之后，这时候页面的加载状态就会进入完成阶段了， get 请求就不会再去取缓存了
* 解决：
  1. 一种是 get 请求的时候，加上随机时间戳，不让其缓存
  2. 不加载 gtag，也就不会加载 facebook 资。改用 google analytics

#### 路由重构

* 问题：项目只有一级路由，导致二级菜单都是模拟二级路由的模式，模块耦合性高，逻辑复杂难以理解以及维护。拓展性差。
* 解决：重构路由，支持嵌套路由，解耦各模块中的组件。

#### websocket Promise封装

* 问题: websocket 全局注册，但是在组件里都是要用 watch 监听返回值，造成代码十分难以理解以及维护。
* 分析：在业务上，websocket请求分为需要等待服务端回值（类似http，有超时）和不需要等待回值两种（只发送数据，无超时）。
* 解决：用Promise封装websoket函数，
  1. 封装成promise函数，并添加 callback和time，分别代表是否需要等待返回值和超时时间
  2. 重点: 每次发送websocket请求都将 该请求参数 添加到全局数组队列\( 项目是放在store \)中，得到在websocket得到返回值之后根据请求id获取到队列中的请求参数，触发callback函数。若超时，则在该队列中删除这条请求即可。

#### websocket 浏览器不同页面通讯

* 问题：用户双屏多开tab页登陆看设备列表，但是其中一个tab页数据无法实时更新。
* 分析：websocket 根据用户id 只能是和服务端连接一次，上一个连接会被踢掉，但是登录使用cookie登录，可以多开几个tab页同时登录。
* 解决：用localstorage 在浏览器不同tabs能共享机制，收到websocket值后写入localstorage，监听localstorage即可获取新数据。

