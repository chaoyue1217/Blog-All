---
title: vue之公众号开发总结
date:  2018-01-23
categories:
---

# 总结

这个项目，对我来说一切都是新的，vue、移动端、公众号。但是学习新东西的心情还是很激动的。
首先技术选型：在得知不能前后分离的情况下，抛弃了使用框架的想法，ui方面使用的是微信出版的weui。后经历种种波折，静态页面完成后又得知前后可以分离（PS：当时我的小心脏就熄火了...），所以果断转向了vue，原因嘛，是因为听说学会了vue能挣更多的￥￥￥。然而ui框架的选择还是首要的问题，因为之前用了weui，所以最后选择了VUX。官网上是这样说的：VUX是基于WeUI和Vue(2.x)开发的移动端UI组件库，主要服务于微信页面。So，很符合我的要求。接下来就是坎坷的开发之路了。

# vux
在项目创建时使用的是官方推荐的快速初始化的方法：

```bash
npm install vue-cli -g // 如果还没安装
vue init airyland/vux2 projectPath
```

**特别注意**

非常值得一提的是，在使用该方法创建项目后，`npm install`第三方插件的时候会报一堆关于`node-gyp`错误，并且不能成功安装，最后通过各种搜索，找到一种解决办法

```bash
npm install -g node-gyp
```
经过该操作，虽然还是会报错，但是终于能正常的安装第三方插件了。

# 权限管理和登录保持
状态管理本来想用vuex来做，但是这个项目一共才七八个页面，总觉得杀鸡焉用宰牛刀，所以选择了最简单的方式——cookie + vue-router路由守卫。
登录状态的代码很easy

```js
//在main.js中。因为做的是全局的
router.beforeEach(function(to, from, next){
    const routerPath = "/healthme"
    //获取cookie中的token值
    const roles = ["ROLE_USER","ROLE_REVIEWER","ROLE_ASSIGNEE"]
    let roleName = cookie.get("token")
    if(to.path.indexOf(routerPath) != -1){
        //判断token是否过期，不存在  则过期 跳转至登录页面
        if(roles.indexOf(roleName) == -1){
            next({path:"/login"})
        }
    }
    if(to.path == "/login"){
        //去往登录页面的时候  判断token是否过期  没有过期则去往首页
        if(roles.indexOf(roleName) != -1){
            next({path: "/healthme"})
        }
    }
    next()
});
```

# 移动端分页
说起移动端分页，也是费了很大的劲，主要的问题不是代码上，而是在插件的选择上，又要配合vue，又要在适用移动端。尝试过`better-scroll`等几个，但是效果都不太理想，但是皇天不负苦心人呐~
**vue-infinite-scroll**真的有必要收藏一下。
分页的要求：后台每次传回25条数据，当用户屏幕滚动到最底端时，再请求数据填充到之前的数据后面。

使用：
1、在所需页面引入vue-infinite-scroll，并且use一下
2、在需要遍历的盒子的父盒子上添加相关属性
- `v-infinite-scroll`：值为加载数据的函数，即每次页面滚动到底部时的处理函数
- `infinite-scroll-disabled`: 是否禁用无限滚动。如果该属性的值为true，则将禁用无限滚动。
- `infinite-scroll-distance`: 数字(默认值= 0)——在执行`v-infinite-scroll`方法之前，元素底部和viewport底部之间的最小距离。

```html
<template>
    <div class="group-container"  v-infinite-scroll="getLists" infinite-scroll-disabled="busy" infinite-scroll-distance="0">
        <!-- ...此处为使用v-for遍历出来的元素 -->
        <load-more :tip="loadTip" :show-loading="showLoading"></load-more>
    </div>
</template>
```
```js
import infiniteScroll from 'vue-infinite-scroll'
//因为在全局使用不成功，因为在vue文件中单独引用的
import Vue from 'vue'
Vue.use(infiniteScroll)
export default{
    data():{
        return {
            showLoading: false,
            busy: false,
            page: 1,
            maxPage: 1,
            loadTip: "正在加载",
        }
    },
    methods:{
        getLists(){
            //此处为v-infinite-scroll的函数，用于处理返回的结果
            if(this.page > this.maxPage){
                this.busy = false;
                this.showLoading = false;
                this.loadTip = "没有更多了"
                return;
            }
            this.showLoading = true
            /*
            获取病人信息
             */
           this.$http.get(url+"?page="+this.page)
            .then((response) => {
                if(response.data.success){
                    //将返回的数据进行拼接
                    this.patients = this.patients.concat(response.data.list)
                    this.maxPage = response.data.totalPage
                    //数据为零时的显示
                    if(response.data.totalPage == 0){
                        this.busy = false;
                        this.showLoading = false;
                        this.loadTip = "暂无数据"
                        return
                    } 
                    //数据全部加载完成的显示
                    if(response.data.totalPage == 1 ){
                        this.busy = false;
                        this.showLoading = false;
                        this.loadTip = "没有更多了"
                        return
                    }
                }else{
                    this.showLoading = false;
                    this.loadTip = "获取数据失败，请重新获取"
                }
            }).catch(()=>{
                this.showLoading = false;
                this.loadTip = "获取数据失败，请重新获取"
            })
            this.page++ 
        }
    }
}
```
# pdf查看
网上好多人推荐PDF.js，去github中看了一下，使用很简单，而且功能齐全。
下载github里的pdfjs，根据文档进行构建，最后只需要build和web两个文件夹即可。
使用：
```js
window.location.href = "http://www.XXX.com/pdfjs/web/viewer.html?file=/report/1.pdf"
// pdf文件的地址必须和pdfjs是同源的，不支持跨域。
// file后面的参数即要展示的pdf文件
```

- 出现的问题
  不兼容IOS，在IOS上不显示pdf。调查发现，IOS中可以直接使用window.open('pdf的src')来直接打开一个pdf文件。
- 解决：
  根据navigator.userAgent判断当前的微信系统是IOS还是android，从而使用两种不同的方式展示pdf。


# 技术栈
js框架：vue
UI框架：vux
登陆状态保持：vue-router路由守卫
权限管理：cookie
移动端分页：vue-infinite-scroll
查看pdf：pdfjs




