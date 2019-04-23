---
title: nextTick
date: 2018-11-20
categories: vue-SourceCode
---

`nextTick`是 Vue 的一个核心实现。上节提到，`watcher.update`会执行`queueWatcher`，而`queueWatcher`会执行`nextTick`方法，下面就仔细看看`nextTick`。

# nextTick

JS的单线程的，对于异步操作，JS中有个叫做事件轮询的东西来控制。事件队列中又有两种任务：macro-task（宏任务）和micro-task（微任务），事件轮询的步骤如下：
- 所有的同步任务都在主线程上执行，形成一个执行栈
- 异步任务会存放在事件队列中。当执行栈中的同步任务执行完毕后，会读取任务队列，对应的任务队列会进入执行栈，开始执行，其中**微任务会先于宏任务执行**
- 主线程重复上述步骤

宏任务有：
- DOM事件
- XHR
- setTimeout、setInterval

微任务有：
- Promise

下面来看一下vue中的实现，`nextTick`函数位于`util/next-tick.js`文件中
```js
// callbacks存放回调函数
const callbacks = []
let pending = false

// flushCallbacks作用是遍历并执行callbacks中的回调函数
function flushCallbacks () {
  pending = false
  const copies = callbacks.slice(0)
  callbacks.length = 0
  for (let i = 0; i < copies.length; i++) {
    copies[i]()
  }
}
let microTimerFunc
let macroTimerFunc
let useMacroTask = false

if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  macroTimerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else if (typeof MessageChannel !== 'undefined' && (
  isNative(MessageChannel) ||
  // PhantomJS
  MessageChannel.toString() === '[object MessageChannelConstructor]'
)) {
  const channel = new MessageChannel()
  const port = channel.port2
  channel.port1.onmessage = flushCallbacks
  macroTimerFunc = () => {
    port.postMessage(1)
  }
} else {
  /* istanbul ignore next */
  macroTimerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  microTimerFunc = () => {
    p.then(flushCallbacks)
    if (isIOS) setTimeout(noop)
  }
} else {
  // fallback to macro
  microTimerFunc = macroTimerFunc
}
```
上面重新定义了微任务和宏任务函数。
对于宏任务函数：
- 优先检查浏览器是否支持`setImmediate`,支持则将宏任务指向`setImmediate`
- 否则检查`MessageChannel`
- 以上全部支持则使用`setTimeout`
  对于微任务函数：
- 检查是否支持`Promise`，不支持则直接使用宏任务函数

以上是对任务队列的处理，下面看一下`nextTick`:
```js
export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  // 把传入的回调函数存入callbacks数组中
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  // 最后由条件决定使用微任务还是宏任务去执行
  if (!pending) {
    pending = true
    if (useMacroTask) {
      macroTimerFunc()
    } else {
      microTimerFunc()
    }
  }
  // 当 nextTick 不传 cb 参数的时候，提供一个 Promise 化的调用
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}
```

使用`callbacks`而不是直接在` nextTick` 中执行回调函数的原因是保证在同一个` tick` 内多次执行 `nextTick`，不会开启多个异步任务。

# 总结

数据的变化到 DOM 的重新渲染是一个异步过程，发生在下一个 tick。 我们平时在开发的过程中，比如从服务端接口去获取数据的时候，数据做了修改，如果我们的某些方法去依赖了数据修改后的 DOM 变化，我们就必须在 nextTick 后执行。比如下面的伪代码：
```js
getData(res).then(()=>{
  this.xxx = res.data
  this.$nextTick(() => {
    // 这里我们可以获取变化后的 DOM
  })
})
```

`Vue.js` 提供了 2 种调用` nextTick `的方式，一种是全局 API `Vue.nextTick`，一种是位于原型上的方法，使用 `vm.$nextTick`，无论我们使用哪一种，最后都是调用` next-tick.js` 中实现的 nextTick 方法。

众所周知，**next**是下一次的意思，在vue中`nextTick`的作用是在**本次事件循环**时将当前的操作（函数）放入到异步队列中，在**下一次事件轮询时执行**，因此才有了`next`。