## vue 双向绑定原理/vue 响应式原理

1. 简要描述双向绑定原理
  1.1 Object.defineProperty
  1.2 DocumentFragment
  1.3 发布·订阅模式
2. 简单实现
  2.1 视图层到逻辑层
  2.2 逻辑层到视图层
3. 理解 Observer、Dep、Watcher，为什么会有这几个对象，以及他们之间的关系。
4. 

DocumentFragment（文档片段）可以看作节点容器，它可以包含多个子节点，当我们将它插入到DOM中时，只有它的子节点会插入目标节点，所以把它看作一组节点的容器。使用DocumentFragment处理节点，速度和性能远远优于直接操作DOM。Vue进行编译时，就是将挂载目标的所有子节点劫持（真的是劫持）到DocumentFragment中，经过一番处理后，再将DocumentFragment整体返回插入挂载目标。

回顾一下，每当new一个Vue，主要做了两件事：第一个是监听数据：observe(data)，第二个是编译HTML：nodeToFragement(id)。

在监听数据的过程中，会为data中的每一个属性生成一个主题对象dep。

在编译HTML的过程中，会为每个与数据绑定相关的节点生成一个订阅者watcher，watcher会将自己添加到相应属性的dep中。

```
  <div id="app">
    <input type="text" v-model="text" id="a">
    {{text}}
    <!-- <span id="b">{{text}}</span> -->
  </div>

  <script>
    var list = {}

    // https://zhuanlan.zhihu.com/p/30328650
    function nodeToFragment(node, vm) {
      var flag = document.createDocumentFragment();
      var child;

      while (child = node.firstChild) {
        compile(child, vm);
        flag.append(child);  // 将子节点劫持到文档片段中
      }
      return flag;
    }

    function compile(node, vm) {
      var reg = /\{\{(.*)\}\}/;
      // 节点类型为元素
      if (node.nodeType === 1) {

        var attr = node.attributes;
        // 解析属性
        for (var i = 0; i < attr.length; i++) {
          if (attr[i].nodeName == 'v-model') {
            var name = attr[i].nodeValue;  // 获取v-model绑定的属性名
            node.value = vm[name];  // 将 data 的值赋给该node
            node.removeAttribute('v-model');

            node.addEventListener('input', (e) => {
              vm[name] = e.target.value
            }, false)
          }
        }

        // if (reg.test(node.innerText)) {
        //   var name = RegExp.$1;  // 获取匹配到的字符串
        //   name = name.trim();
        //   node.innerText = vm[name];  // 将data的值赋给该 node 
        //   // node.nodeValue = vm[name];  // 将data的值赋给该 node 
        // }
      }
      if (node.nodeType === 3) {
        if (reg.test(node.nodeValue)) {
          var name = RegExp.$1;  // 获取匹配到的字符串
          name = name.trim();
          notify(name, () => {
            node.nodeValue = vm[name];
          })
          node.nodeValue = vm[name];  // 将data的值赋给该 node 
        }
      }
    }

    function Vue(options) {
      this.data = options.data;
      var data = this.data
      observe(data, this)

      var id = options.el;
      var dom = nodeToFragment(document.getElementById(id), this);
      // 编译完成后，将dom返回到app中
      document.getElementById('app').appendChild(dom);
    }

    var vm = new Vue({
      el: 'app',
      data: {
        text: 'hello world'
      }
    })

    function defineReactive(obj, key, val) {
      list[key] = []
      Object.defineProperty(obj, key, {
        set: function (newVal) {
          if (newVal === val) {
            return;
          }
          val = newVal
          update(key)
        },
        get: function () {
          
          return val
        }
      })
    }

    function observe(obj, vm) {
      Object.keys(obj).forEach((key) => defineReactive(vm, key, obj[key]))
    }



    // 订阅消息处理函数
    function notify(message, func) {
      if (!list[message]) {
        list[message] = [func]
      } else {
        list[message].push(func)
      }
    }

    // 发布操作（更新需要更新内容的地方）
    function update(key) {

      // 需要提前在某个时间点把消息定义（设置）好
      // Uncaught TypeError: Cannot read property 'text' of undefined
      if (!list[key]) return
      list[key].forEach(ele => {
        ele()
      })
    }

    /*
      在逻辑层为data中的属性赋值，触发了该属性的 [SET] 操作，在 [SET] 操作函数内部可以监听到变化。
      侦测到变化之后就要分发变化，通知模板中各处使用到这个属性的地方更新内容。
      那么如何通知呢？用发布订阅模式。
      发布订阅模式中有三个对象，发布者、事件中心、订阅者，发布者将消息发布到事件中心，事件中心再将消息推送给订阅者。
      在set函数中执行的应该是事件中心将消息推送给订阅者，在此之前要先订阅消息。
      消息在哪里订阅呢，当然是在模板处理过程中，譬如说在处理插值表达式的时候，在这个时候就要订阅消息。
      订阅消息有哪些操作呢？
      首先需要一个用来订阅消息的函数，notify，notify 函数会将同一个消息加入对应的消息列表中。

      set 函数执行后，需要将依赖于该属性的值的地方更新一遍，并且

    */

    // 为什么要封装 Dep 和 watcher
```

### 参考资料
- [Vue.js双向绑定的实现原理](https://zhuanlan.zhihu.com/p/30328650) 
- [Vue响应式原理-理解Observer、Dep、Watcher](https://zhuanlan.zhihu.com/p/68096416)
- [Vue 源码研究会](https://zhuanlan.zhihu.com/learn-vue-source-code)

## vue 通过调用方法显示组件。

一般这样的组件通常是一些消息组件或弹框输入组件，像element-ui中的Message组件。这种组件的好处是将组件的显隐交给组件自身控制，不用在父组件内部定义过多的变量和方法，可以使得代码更加简单，也可以带来一定程度上的高复用性。

下面通过定义一个通过调用方法显示的弹出输入组件，简要介绍下如何来做。

首先要定义一个 EditDialog 组件，注意在这个单文件组件中不要有 props 属性，所有组件用到的变量都存放到 data 方法中。另外这个组件是一个弹框输入组件，组件中有“确认”和“取消”按钮，其点击逻辑是无论点击了哪个按钮，都要第一时间隐藏该组件，所以要定义一个将组件本身从DOM树中删除的方法。

EditDialog 组件代码如下:

```vue
<!-- 新增或编辑menu节点 -->
<template>
  <div class="container">
    <!-- :visible.sync="dialogVisible" -->
    <el-dialog class="dialog" :visible.sync="dialogVisible" :title="title" width="40%">
      <el-form label-position="left" :model="node">
        <el-form-item v-if="mode === 'ADD'" label="父节点">
          <el-input :value="parentNode.title" disabled></el-input>
        </el-form-item>
        <el-form-item label="路径：">
          <el-input v-model="node.path"></el-input>
        </el-form-item>
        <el-form-item label="名称：">
          <el-input v-model="node.name"></el-input>
        </el-form-item>
        <el-form-item label="标题：">
          <el-input v-model="node.title"></el-input>
        </el-form-item>
      </el-form>
      <el-row>
        <el-button ref="btn-cancel" @click="onCancel">取消</el-button>
        <el-button type="primary" ref="btn-confirm" @click="onConfirm">确认</el-button>
      </el-row>
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "editNodedialog",
  data() {
    return {
      title: "",
      // nodes: [],
      mode: "ADD",
      node: {},
      parentNode: {},
      dialogVisible: true
    };
  },
  mounted() {
    let mode = this.mode;
    mode === "ADD" ? (this.title = "新增节点") : (this.title = "修改节点");
  },
  methods: {
    // 确认 点击事件处理函数
    onConfirm() {},
    // 取消 点击事件处理函数
    onCancel() {},
    hideComp() {
      this.$destroy(true);
      this.$el.parentNode.removeChild(this.$el); // 从DOM里将这个组件移除
    }
  }
};
</script>

<style lang="scss" scoped>
.dialog ::v-deep .el-input {
  width: 350px;
}
</style>
```

然后同目录下面新建一个js文件，其中的代码大体如下。

```js
const constructor = Vue.extend(require('./EditDialog.vue'))
// 上面那种写法报错用这种
// const constructor = Vue.extend(require('./EditDialog.vue').default)

let nId = 1
const editNodeDialog = (nodes, node, mode) => {
    let id = 'editDialog-' + nId++
    const instance = new constructor({
        data: {
            nodes: nodes, node: node, mode: mode
        }
    })
    instance.id = id
    instance.vm = instance.$mount()    // 挂载但是并未插入dom，是一个完整的Vue实例
    instance.vm.visable = true
    instance.dom = instance.vm.$el
    document.body.appendChild(instance.dom)       // 将 dom 插入body
    return instance.vm
}
```

上面的代码只实现了调用方法显示组件。在我的想法中

```js
const editNodeDialog = (node, mode,parentNode={}) => {
    return new Promise((resolve, reject) => {
        let id = 'editDialog-' + nId++
        const instance = new constructor({
            data: {
                parentNode: parentNode, node: node, mode: mode
            }
        })
        instance.id = id
        instance.vm = instance.$mount()    // 挂载但是并未插入dom，是一个完整的Vue实例
        instance.vm.visable = true
        instance.dom = instance.vm.$el
        document.body.appendChild(instance.dom)       // 将 dom 插入body
        // EditDialoginstance.dom.style.zIndex = nId + 1001        // 后插入的 
        const hideComp = instance.vm.hideComp
        // const onConfirm = instance.vm.onConfirm
        instance.vm.onCancel = () => {
            hideComp()
            reject()
        }
        instance.vm.onConfirm = () => {
            hideComp()
            resolve({node: instance.$data.node})
        }
        return instance.vm
    })
}
```

最后导出上面定义好的方法，并且在main.js中引入。

```js
export default {
    install: Vue => {
        Vue.prototype.$editNodeDialog = editNodeDialog  // 将 EditDialog组件暴露出去，并挂载在Vue的prototype上
    }
}
```

```js
import EditNodeDialog from '@/components/EditDialog/index.js'
Vue.use(EditNodeDialog)
```

参考文章：
- <https://www.cnblogs.com/huancheng/p/9590782.html>