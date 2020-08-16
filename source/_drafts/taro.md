4、解决在taro项目中使用taro-echarts 组件，编译后微信开发者工具预览时，小程序报错

```
Component is not found in path "npm/taro-echarts/components/ec-canvas/ec-canvas" (using by "npm/taro-echarts/components/Chart/index");onAppRoute
Error: Component is not found in path "npm/taro-echarts/components/ec-canvas/ec-canvas" (using by "npm/taro-echarts/components/Chart/index")
```

这是因为 `npm/taro-echarts/components/ec-canvas/ec-canvas` 目录下没有 ec-canvas.wxml 文件导致的，在该目录内添加该文件可以成功显示。

可以在 `config/index.js` 内添加如下代码解决，重新执行编译命令后生效。
```
const config = {
  //...
  copy: {
    patterns: [
      // 需添加如下配置
      {
        from: "node_modules/taro-echarts/components/ec-canvas",
        // from: "src/components/ec-canvas",
        to: "dist/npm/taro-echarts/components/ec-canvas",
        ignore: ["ec-canvas.js", "wx-canvas.js", "echarts.js"]
      }
    ],
    options: {}
  },
}
```