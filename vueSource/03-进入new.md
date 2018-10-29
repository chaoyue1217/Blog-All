---
title: 进入new
date: 2018-08-21
categories: vue-SourceCode
---

上一节已经了解了Vue在new之前已经自带的一些属性，接下来，要看看new时做了什么。

# new Vue()

从最简单的开始：

```html
<div id='app'>{{message}}</div>
```

```js
var vm = new Vue({
  el: '#app',
  data: {
    message: 'demo'
  }
});
console.log(vm);//从打印结果可以看到实例化的对象上又增加了一堆属性
```

先找到Vue的构造函数：
```js
function Vue (options) {
  if (process.env.NODE_ENV !== 'production' &&
    !(this instanceof Vue)
  ) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  /* options为传入的对象，此处为
  {
    el: '#app',
    data: {
      message: 'demo'
    }
  }
*/  
  this._init(options)
}
```
可以看出，在`new Vue()`时，只做了一步操作，就是`_init()`。从上一节也可以得知，`_init`方法是一个原型方法，是在initMixin文件中挂载上去的。那，`_init`做了什么呢？

```js
Vue.prototype._init = function (options?: Object) {
	//将实例对象赋值给常量vm
    const vm: Component = this
    // 添加实例属性_uid，唯一标识，初始值为0
    vm._uid = uid++

    let startTag, endTag
    /* istanbul ignore if */
    if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
      startTag = `vue-perf-start:${vm._uid}`
      endTag = `vue-perf-end:${vm._uid}`
      mark(startTag)
    }

    // 添加实例属性_isVue,表示该对象是Vue实例
    vm._isVue = true
    // _isCompoent是创建组件时使用的，由于并没有传入，因此执行else分支
    if (options && options._isComponent) {
      // optimize internal component instantiation
      // since dynamic options merging is pretty slow, and none of the
      // internal component options needs special treatment.
      initInternalComponent(vm, options)
    } else {
    // 执行else
    // 选项合并，将vue构造函数及实例化时传入对象按照一定的策略合并
      vm.$options = mergeOptions(
        resolveConstructorOptions(vm.constructor),
        options || {},
        vm
      )
    }
    /* istanbul ignore else */
    if (process.env.NODE_ENV !== 'production') {
      initProxy(vm)
    } else {
      vm._renderProxy = vm
    }
    // expose real self
   // 添加实例属性_self 指向实例
    vm._self = vm
    // 下面为初始化和执行钩子函数
    initLifecycle(vm)
    initEvents(vm)
    initRender(vm)
    callHook(vm, 'beforeCreate')
    initInjections(vm) // resolve injections before data/props
    initState(vm)
    initProvide(vm) // resolve provide after data/props
    callHook(vm, 'created')

    /* istanbul ignore if */
    if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
      vm._name = formatComponentName(vm, false)
      mark(endTag)
      measure(`vue ${vm._name} init`, startTag, endTag)
    }
	// 如果传入了el参数，则执行$mount方法。
    if (vm.$options.el) {
      vm.$mount(vm.$options.el)
    }
  }
}
```

整个函数执行完，vue实例也创建完成，但是其中的细节还是深之又深，接下来慢慢看。


