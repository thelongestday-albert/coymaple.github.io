5、vue 通过调用方法显示组件。

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