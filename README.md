# study-vue

## 实现 @click

```
思路：通过目标元素的onclick事件覆盖，实现@click
```

构造函数接收并处理 methods

```js
class KVue {
  constructor(options) {
    // 1.响应式
    // ...
    this.$methods = options.methods;

    // 1.1代理
    proxy(this);

    // 2.编译
    new Compile(options.el, this);
  }
}
```

```js
// 将$xx的key代理到vm上去，用户就可以直接使用
function proxy(vm) {
  // $data
  // ...
  // $methods
  Object.keys(vm.$methods).forEach(key => {
    Object.defineProperty(vm, key, {
      get() {
        return vm.$methods[key];
      },
      set(v) {
        vm.$methods[key] = v;
      }
    });
  });
}
```

编译元素函数 compileElement 处理 `@xxx` 情况

```js
  compileElement(node) {
    // 获取节点特性
    const nodeAttrs = node.attributes
    Array.from(nodeAttrs).forEach(attr => {
      // ...
      if (this.isEvent(attrName)) {
        // 事件
        // 获取事件执行函数并调用
        const dir = attrName.substring(1)
        this[dir] && this[dir](node, exp)
      }
    })
  }
```

```js
  isEvent(attrName) {
    return attrName.startsWith('@')
  }
```

实现 `@click` 处理函数 `click()`

```js
  click(node, exp) {
    // 绑定元素的onclick点击事件
    // 箭头函数 绑定上下文this
    node.onclick = (e) => {
      this.$vm[exp] && this.$vm[exp](e)
    }
  }
```

完成 `@click` 处理逻辑, 同理 `@input` 实现 `input()` 函数即可

```js
  input(node, exp) {
    // 元素绑定input事件
    // 箭头函数 绑定上下文this
    node.addEventListener('input', (e) => {
      this.$vm[exp] = e.target.value
    })
  }
```

## 实现 v-model

```
思路：1.初始化input控件的value 2.通过控件input事件实现数据绑定
```

```js
  model(node, exp) {
    // 初始化
    node.value = this.$vm[exp]
    // 绑定input事件
    this.input(node, exp)
  }
```
