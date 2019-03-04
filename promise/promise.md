简单实现：
```js
/**
 * 1.内置resolve
 * 2.直接执行fn(resolve) 
 * 3.resolve中执行callback
 */
 
function MyPromise(fn) {
  var _this = this
  this.callback = []
  this.isResolved = false
  function resolve(val) {
    if (this.isResolved) {
      return
    }
    this.isResolved = true
    if (this.callback.length > 0) {
      this.callback.forEach(item => {
        var res
        var cb = item.cb
        var resolve = item.resolve
        cb && (res = cb(val))
        if (typeof res === 'object' && res.then) {
          res.then(resolve)
        } else {
          resolve && resolve(res)
        }
      })
    }
  }
  fn(resolve)
}
MyPromise.prototype.then = function(cb) {
  var _this = this
  return new MyPromise(function(resolve) {
    _this.callback.push({
      cb: cb,
      resolve: resolve
    })
  })
}

```