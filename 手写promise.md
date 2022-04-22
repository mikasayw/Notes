## :black_nib: 手写 Promise experience

- #### :maple_leaf: 为了在 promise 状态发生变化时（resolve / reject 被调用时）再执行 then 里的函数，我们使用一个 callbacks 数组先把传给 then 的函数暂存起来，等状态改变时再调用。

  ```javascript
  this.callBackList.push({
    resolveCb: () => {
      try {
        const x = resolveCb(this.promiseResult)
      } catch (e) {
        reject(e)
      }
    },
    rejectCb: () => {
      try {
        const x = rejectCb(this.promiseResult)
      } catch (e) {
        reject(e)
      }
    }
  })
  ```

- #### :maple_leaf: 怎么保证后一个 then 里的方法在前一个 then（可能是异步）结束之后再执行呢？

  - ##### 将传给 then 的函数和新 promise 的 resolve / reject 一起 push 到前一个 promise 的 callbacks 数组中
  - ##### 当前一个 promise 完成后，调用其 resolve / reject 变更状态，在这个 resolve / reject 里会依次调用 callbacks 里的回调，这样就执行了 then 里的方法了
  - ##### 当 then 里的方法执行完成后，返回一个结果，如果这个结果是个简单的值，就直接调用新 promise 的 resolve，让其状态变更，这又会依次调用新 promise 的 callbacks 数组里的方法，循环往复。。。如果返回的结果是个 promise，则需要等它完成之后再触发新 promise 的 resolve，所以可以在其结果的 then 里调用新 promise 的 resolve(实际每次返回的都是上一个 promise 实例)

```javascript
/**
 * @description:
 * @param {*} promise2
 * @param {*} x
 * @param {*} resolve
 * @param {*} reject
 * @return {*}
 */
function resolvePromise(promise2, x, resolve, reject) {
  resolve(x)
}

class NewPromise {
  static PENDING = 'pending'
  static FULLFILLED = 'fullfilled'
  static REJECTED = 'rejected'
  static resolve(val) {
    return new NewPromise(resolve => {
      resolve(val)
    })
  }
  static reject(val) {
    return new NewPromise((resolve, reject) => {
      reject(val)
    })
  }

  constructor(executor) {
    try {
      this.status = NewPromise.PENDING
      this.promiseResult = null
      this.callBackList = []
      executor(this.resolve.bind(this), this.reject.bind(this))
    } catch (error) {
      this.reject(error)
    }
  }

  resolve(value) {
    if (this.status === 'pending') {
      setTimeout(() => {
        this.status = NewPromise.FULLFILLED
        this.promiseResult = value

        this.callBackList.forEach(({ resolveCb, rejectCb }) => {
          resolveCb()
        })
      })
    }
  }

  reject(value) {
    if (this.status === 'pending') {
      setTimeout(() => {
        this.status = NewPromise.REJECTED
        this.promiseResult = value

        this.callBackList.forEach(({ resolveCb, rejectCb }) => {
          rejectCb()
        })
      })
    }
  }

  then(resolveCb, rejectCb) {
    resolveCb = typeof resolveCb === 'function' ? resolveCb : value => value
    rejectCb =
      typeof rejectCb === 'function'
        ? rejectCb
        : reason => {
            throw reason
          }

    // 链式调用
    switch (this.status) {
      case 'fullfilled':
        break
      case 'rejected':
        setTimeout(() => {
          rejectCb(this.PromiseResult)
        })
        break
      default:
        return new NewPromise((resolve, reject) => {
          // this.onRejectedCallbacks.push(() => {
          //   try {
          //     let x = onRejected(this.PromiseResult)
          //     resolvePromise(promise2, x, resolve, reject)
          //   } catch (e) {
          //     reject(e)
          //   }
          // })
          this.callBackList.push({
            resolveCb: () => {
              const x = resolveCb(this.promiseResult)
              resolvePromise('自身', x, resolve, reject)
            },
            rejectCb: () => {
              try {
                console.log(1)
                const x = rejectCb(this.promiseResult)
                console.log(2)
                resolvePromise('自身', x, resolve, reject)
              } catch (e) {
                reject(e)
              }
            }
          })
        })
        break
    }
  }

  catch(rejectCb) {
    return this.then(undefined, rejectCb)
  }

  finally(cb) {
    // console.log(this)
    // console.log(p1)
    return this.then(cb, cb)
  }
}

const p1 = new NewPromise((resolve, reject) => {
  resolve(9)
  // reject(9)
})

p1.then(res => {
  console.log('success', res)
  return 2 * res
})
  // .then(res => {
  //   console.log(res)
  //   return 2 * res
  // })
  // .then(res => {
  //   console.log(res)
  // })
  // .catch(err => {
  //   console.log(err)
  // })
  .finally(() => {
    console.log('finally')
  })

console.log('end')
```
