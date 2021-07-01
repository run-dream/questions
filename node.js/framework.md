### 知识框架

- KOA
  - middleware
    - koa-compose

- Egg.js
  - service
    - ctx 的注入
  - controller
- Middway.js
  - 依赖注入



### koa-compose是怎么实现的

关键词:  ***Promise*** ***回调***

回答:

```js
module.exports = compose

function compose(middleware) {
  // middleware 必须是一个栈数组
  if (!Array.isArray(middleware)) throw new TypeError('Middleware stack must be an array!')
  // middleware 栈数组的每一项必须是函数
  for (const fn of middleware) {
    if (typeof fn !== 'function') throw new TypeError('Middleware must be composed of functions!')
  }
  // 核心代码
  return function (context, next) {
    let index = -1
    return dispatch(0)

    function dispatch(i) {
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      if (i === middleware.length) fn = next
      if (!fn) return Promise.resolve()
      try {
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1)));
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
```



### egg.js 怎么实现的

参考:

https://blog.csdn.net/zdplife/article/details/83147615

https://github.com/YOLO0927/how-dose-egg-work