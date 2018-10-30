---
title: web Storage
date: 2016-5-15
categories: HTML5
---
# web Storage

- html5中的Web Storage包括了两种存储方式：sessionStorage和localStorage。sessionStorage用于本地存储一个会话（session）中的数据，这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。
- 因此sessionStorage不是一种持久化的本地存储，仅仅是会话级别的存储。而localStorage用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。  
- 浏览器的支持除了IE７及以下不支持外，其他标准浏览器都完全支持

## web storage和cookie的区别
- Web Storage的概念和cookie相似，区别是它是为了更大容量存储设计的。Cookie的大小是受限的，并且每次你请求一个新的页面的时候Cookie都会被发送过去，这样无形中浪费了带宽，另外cookie还需要指定作用域，不可以跨域调用。
- 除此之外，Web Storage拥有setItem,getItem,removeItem,lear等方法，不像cookie需要前端开发者自己封装setCookie，getCookie。
- 但是Cookie也是不可以或缺的：Cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web Storage仅仅是为了在本地“存储”数据而生。

## localStorage和sessionStorage操作
- localStorage和sessionStorage都具有相同的操作方法，例如setItem、getItem和removeItem等
- setItem
    + 将value存储到key字段 
    + .setItem( key, value)
```js
sessionStorage.setItem("key", "value");
localStorage.setItem("site", "js8.in");
```
- getItem
    + 获取指定key本地存储的值
    + .getItem(key)
```js
var value = sessionStorage.getItem("key"); 
var site = localStorage.getItem("site");
```
- removeItem 
    + 删除指定可以本地存储的值
    + .removeItem(key)    
```js
sessionStorage.removeItem("key"); 
localStorage.removeItem("site");
```
- clear
    + 清除所有的key/value
    + .clear()
```js
sessionStorage.clear(); 
localStorage.clear();
```
## localStorage和sessionStorage的key和length属性实现遍历
- sessionStorage和localStorage提供的key()和length可以方便的实现存储的数据遍历
```js
var storage = window.localStorage; 
for (var i=0, len = storage.length; i < len; i++){
    var key = storage.key(i); 
    var value = storage.getItem(key); 
    console.log(key + "=" + value); 
}
```

## storage 事件
- torage还提供了storage事件，当键值改变或者clear的时候，就可以触发storage事件
```js
if(window.addEventListener){ 
    window.addEventListener("storage",handle_storage,false); 
}
else if(window.attachEvent){ 
    window.attachEvent("onstorage",handle_storage); 
} 
function handle_storage(e){
    if(!e){e=window.event;} 
}
```

- storage事件对象的具体属性如下表：
| Property |  Type  |                              Description |
| -------- | :----: | ---------------------------------------: |
| key      | String | The named key that was added, removed, or moddified |
| oldValue |  Any   | The named key that was added, removed, or moddified |
| newValue |  Any   | The new value, or null if an item was added |
| url/uri  | String | The page that called the method that triggered this change |

## localStorage和sessionStroage

* 区别：localStorage：关闭浏览器信息依旧存在; sessionStroage：关闭浏览器信息消失
* 两者的调用方法完全一致
```html
<h1>请输入你的名字</h1>
<input type="text">
<input type="button" value="保存">
<script>
    // 点击保存按钮 保存输入的名字
    document.querySelector("input[type=button]").onclick = function(){
        // 获取输入的值
        var text = document.querySelector("input[type=text]").value;
        // 将输入的值保存
        window.localStorage.setItem("userName",text);
    };
    // 页面加载完毕后获取保存的值        
    window.onload = function(){
        var username = window.localStorage.getItem("userName");
        // 将输入的值设置给h1
        if(username){
            document.querySelector("h1").innerHTML = username;
        }
    };
</script>
```