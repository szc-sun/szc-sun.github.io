---
layout: post
title: 'vue路由跳转 params与query'
subtitle: 'vue路由跳转 params与query  路由传参,vue-router打开新页面'
date: 2021-04-06
categories: 前端
cover: ''
tags: vue vue-router
---

### params与query

router文件下index.js里面，是这么定义路由的：
```
{
    path: '/about',
    name: 'About',
    component: About
}

```
### 一、用query传参可以直接写在path路由地址里，也可写在json对象中

##### 1、写在json对象中 ，name和path都可以
```
//query,用路径跳转

this.$router.push({
  	path:'/about',
  	query:{
  		name:'about',
  		code:111
  	}
})

```

##### 2、写在path中
```
//query,用路径跳转

this.$router.push({
  	path:'/about?name=about&code=111'
})

```
##### 接收参数 都是下面内容
```
this.$route.query

是{name: "about", code: "111"}
```
### 二、用params时 用query传参可以直接写在path路由地址里，或者写在json对象中用路由的name属性传参
路由配置要在path写:id，否则的话就会出现网上说的**刷新后获取不到params参数**的情况,下面的跳转页面url为 ip +'/about/about/111'，参数的path里面了

##### 路由表的配置
```
{
    path: '/about/:name/:code',
    name: 'About',
    component: About
}

```
##### 1、用params传参需要直接写在json对象中，跳转地址为router中配置的name（此处是About），params中是传的数据
```
//params,用name跳转传参
this.$router.push({
	name:'About',
	params:{
		name:'about',
		code:111
	}
})
```
##### 2、用params传参需要直接写在path，params中不用写数据
```
//params,用name跳转传参
this.$router.push({
        path: '/about/about/111',
})
```
##### 接收参数都是下面的内容

```
this.$route.params.code;
是
{name: "about", code: "111"}
```

### 总结：
> query要用path来引入，params最好用name来引入，接收参数都是类似的，分别是this.$route.query.name和this.$route.params.name。
注意接收参数的时候，已经是$route而不是$router；params和query可以混合传参

query更加类似于我们ajax中get传参，params则类似于post，说的再简单一点，前者在浏览器地址栏中显示参数，后者则不显示

### vue-router打开新页面
```
      const { href } = this.$router.resolve({
        path: '/about',
        query: {
          id: item.id
        }
      })
      window.open(href, '_blank')

```