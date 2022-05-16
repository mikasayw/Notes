## :maple_leaf: Vue.extend + $mount 函数式调用

 - ####  Vue.extend返回的是一个扩展实例构造器，是预设了部分选项的Vue实例构造器
 - ####  调用扩展实例构造器来生产组件实例，并挂载到自定义元素上

```javascript
import Vue from 'vue'
import dialog from './index.vue'

const dialogConstructor = Vue.extend(dialog)
let instance = null

// 用完即卸载实例、销毁dom
const destroyEvent = e => {
  // 当有动画时平滑过渡
  // animation监听animationend
  // transition监听transitionend
  if (e.target.className === 'ant-modal') {
    instance.$el.removeEventListener('animationend', destroyEvent)
    instance.$destroy()
    instance = null
  }
}

const initInstance = ({ type, httpOptions } = {}) => {
  // 根据构造器生成一个实例
  instance = new dialogConstructor({
    // props的赋值方法，但此传值在组件内并不会双向绑定以及验证
    propsData: { type, httpOptions },
  })
  instance.destroyFn = () => {
    instance.$el.addEventListener('animationend', destroyEvent)
  }
}

const useDialog = options => {
  if (!instance) {
    initInstance(options)
  }

  // mount
  // 生成Dom并获取
  document.body.appendChild(instance.$mount().$el)
  instance.open()


  // 返回promise以供调用
  return new Promise((resolve, reject) => {
    // 绑定callBack
    // 组件内部只需要在合适的时候进行this.callBack.reject/resolve(res)
    instance.callBack = {
      resolve: res => {
        resolve(res)
      },
      reject: error => {
        reject(error)
      },
    }
  }).finally(() => {
    instance.destroyFn()
  })
}

export { useDialog }

```
