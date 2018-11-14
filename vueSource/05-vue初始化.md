---
title: vue初始化
date: 2018-10-30
categories: vue-SourceCode
---

# vue初始化
上一节，我们在`_init`函数中看到了选项合并，经过一系列的处理，每个选项都被处理成了特定的形式，且都挂载到了`vm.$options`对象上。
经过深入了解，知道了每个选项都经过了什么样的处理。继续跟着`_init`往下走，我们会看到好多的`init***`，选项合并后得`vm.$options`就是用于下面各个初始化的：

```js
// _self属性指向实例对象
vm._self = vm
initLifecycle(vm)
initEvents(vm)
initRender(vm)
callHook(vm, 'beforeCreate')
initInjections(vm) // resolve injections before data/props
initState(vm)
initProvide(vm) // resolve provide after data/props
callHook(vm, 'created')
```

## 1. initLifecycle

```js
export function initLifecycle (vm: Component) {
  // options和vm.$options共用一个引用
  const options = vm.$options
  // locate first non-abstract parent
  let parent = options.parent
  // 如果当前实例有父组件，且当前实例不是抽象的
  if (parent && !options.abstract) {
  	// 循环查找第一个非抽象的父组件
    while (parent.$options.abstract && parent.$parent) {
      parent = parent.$parent
    }
    // 将当前实例添加到非抽象父组件的$children属性中
    parent.$children.push(vm)
  }

 // 添加实例属性，并初始化
  vm.$parent = parent
  vm.$root = parent ? parent.$root : vm

  vm.$children = []
  vm.$refs = {}

  vm._watcher = null
  vm._inactive = null
  vm._directInactive = false
  vm._isMounted = false
  vm._isDestroyed = false
  vm._isBeingDestroyed = false
}
```

## 2. initEvents

```js
function initEvents (vm: Component) {
// 实例上添加_events属性，初始化为空对象
  vm._events = Object.create(null)
  // 添加_hasHookEvent属性
  vm._hasHookEvent = false
  // init parent attached events
  // 获取父组件的事件监听
  const listeners = vm.$options._parentListeners
  if (listeners) {
    updateComponentListeners(vm, listeners)
  }
}
```

## 3. initRender

```js
function initRender (vm: Component) {
  vm._vnode = null // the root of the child tree
  vm._staticTrees = null // v-once cached trees
  const options = vm.$options
  const parentVnode = vm.$vnode = options._parentVnode // the placeholder node in parent tree
  const renderContext = parentVnode && parentVnode.context
  vm.$slots = resolveSlots(options._renderChildren, renderContext)
  vm.$scopedSlots = emptyObject
  vm._c = (a, b, c, d) => createElement(vm, a, b, c, d, false)
  // normalization is always applied for the public version, used in
  // user-written render functions.
  vm.$createElement = (a, b, c, d) => createElement(vm, a, b, c, d, true)

  // $attrs & $listeners are exposed for easier HOC creation.
  // they need to be reactive so that HOCs using them are always updated
  const parentData = parentVnode && parentVnode.data

  //......未写出来的是处理组件用的
  }
}
```

`initRender`虽然比较复杂，但是简单来说也是在实例上添加属性的：
```js
vm._vnode
vm._staticTrees
vm.$vnode
vm.$slots
vm.$scopedSlots
vm._c
vm.$createElement
```

## 4. 执行生命周期钩子

```js
callHook(vm, 'beforeCreate')
...
callHook(vm, 'created')
```

```js
function callHook (vm: Component, hook: string) {
  // #7573 disable dep collection when invoking lifecycle hooks
  pushTarget()
  // 获取生命周期函数
  const handlers = vm.$options[hook]
  // 从选项合并中我们知道，所有的生命周期选项都被处理成了数组
  // 如果存在生命周期函数，则对数组进行遍历，并对每一项进行调用
  if (handlers) {
    for (let i = 0, j = handlers.length; i < j; i++) {
      try {
        handlers[i].call(vm)
      } catch (e) {
        handleError(e, vm, `${hook} hook`)
      }
    }
  }
  if (vm._hasHookEvent) {
    vm.$emit('hook:' + hook)
  }
  popTarget()
}
```

## 5. initState
在initState前后，有initInjections和initProvide，这两个比较复杂，以后再细看，先看简单的：
```js
export function initState (vm: Component) {
// 添加_watchers属性
  vm._watchers = []
  // 判断props,methods,data,computed,watch属性是否存在，存在则进行相应的初始化
  const opts = vm.$options
  if (opts.props) initProps(vm, opts.props)
  if (opts.methods) initMethods(vm, opts.methods)
  // data存在则直接初始化
  if (opts.data) {
    initData(vm)
  } else {
  	// 不存在则监测一个空对象
    observe(vm._data = {}, true /* asRootData */)
  }
  if (opts.computed) initComputed(vm, opts.computed)
  // 判断wacth是否存在，并且是否是原生的，处理与火狐的冲突
  if (opts.watch && opts.watch !== nativeWatch) {
    initWatch(vm, opts.watch)
  }
}
```

props、data等都是响应式数据，因此它们的初始化都与数据相应有关，接下来再深入研究。

**总结**
从上面的init顺序来看，在执行beforCreate钩子之前，并没有初始化props、data，因此在beforeCreate中是拿不到这些数据的。






















