3、ECharts 使用经验

ECharts 使用时必须指定容器的宽度和高度，否则图表不会被渲染出来。


使用ECharts的步骤：
1. 引入相关的js文件，（引入 echarts.js 文件后就可以使用 echarts 对象了）。
2. 使用 `echarts.init(chartsNode)` 初始化图表节点。`var myChart = echarts.init(document.getElementById('line-chart'));`
3. 编写 echarts 配置。
4. 将配置设置到图表节点上 

```html
<div id="line-chart"></div>

<script src="https://cdn.bootcdn.net/ajax/libs/echarts/4.8.0/echarts-en.common.js"></script>
<script>
var myChart = echarts.init(document.getElementById('line-chart'));
var options = {
    ...
}
myChart.setOption(trafficOption);
</script>
```

配置是重中之重，但是ECharts配置项众多。

①、图表大小和位置

有这么一种情况，图表区域容器比较小，虽然图表可以显示出来，但可能会出现图标被遮掉一部分的情况，在这种情况下，可以修改如下的配置，这样就可以完全显示出来了。

```js
var option = {
    grid: {
        left: '1%',
        top: '3%',
        bottom: '3%',
    },
}
```

对于 **grid** 组件（直角坐标系底板，规定了图表在容器内出现的位置和大小），其定位方式类似于 css 的绝对定位，可以使用的值有 **left/right/width/top/bottom/height** 。其中 left/right/width 是一组，top/bottom/height 是一组，这两组之间没有什么必然的联系，只要给定每一组的其中两个属性就可以算出另一个属性。

![官方的gird配置示例](https://echarts.apache.org/zh/documents/asset/img/basic-concepts-overview/locate.jpg)

②、环形图表

```js
var options = {
    series: [{
        type: 'pie',
        label: {  // 定义与饼图扇形块相关的标签出现的位置
            position: 'center',
            show: false,
        },
        emphasis: {  // 鼠标悬浮到图形元素上时，出现的高亮的样式。
            label: {
                show: true,
                formatter: '{b} \\n {c}笔 \n {d}%',
                fontSize: 20,
                align: 'center',
                color: 'black'
            }
        },
    }] 
}
```
**emphasis** 定义鼠标悬浮到图形元素上时高亮的样式，此时可以定义与此元素相关联的标签的样式。


## vue中使用Echarts

1、 Echarts地图取消鼠标移入高亮

①、最简单的方法是将 geo 的 silent属性设置为false，这样会禁止所有事件的触发，当然也会包括鼠标移入高亮。

②、也可以通过如下的方法，将默认的背景色和鼠标移入时的背景色设置成一样的，实现鼠标移入禁止高亮的效果。

```js
{
  geo: {
    itemStyle: {
      areaColor: "rgba(31,181,252,0.4)",
      emphasis: {
        areaColor: "rgb(31,181,252,0.4)",
      },
    },
  }
}
```

③、调用api
```
myChart.on('mourseover',function(e){
  myChart.dispatchAction({
    type: 'downplay'
  });
})
```

<!-- 在实际使用的时候，上面的 api 对通过 geo 属性配置的地图不起作用。 -->

> downplay官方文档 <https://echarts.apache.org/zh/api.html#action.downplay>

2、 图例图标与文本颜色一致

Echarts 的图例分为图标和文本两部分，图标的颜色默认取与之关联的图表的颜色，如与图例关联的折线图的线是红色的，则该图例图表也是红色的。

图例文本默认是棕色，不会与图表颜色关联，要使得颜色一致需要手动设置文本颜色。值得注意的是图表的颜色默认取自 options 对象下的 color 数组，有可能出现Echarts自动取到的颜色与手动设置的颜色不一样的情况，所以最好同时也手动设置一下图表的颜色。

```js
{
  legend: {
    data: [ 
      { 
        textStyle: { 
          color: 'red'
        }
      },
    ],
  }
}
```

4、 柱状图条竹设置渐变色

- [echarts柱状图渐变色](https://www.cnblogs.com/luckyShuang/p/11927358.html)
- [echarts渐变色实现方法](https://www.cnblogs.com/liuwei54/p/9962182.html)

5、 echarts 配置项复用

Echarts的配置项多且繁杂，一开始的思路是将**本项目中不同类型图表的通用配置项提取出来，放到一个统一的js文件里面，需要的时候直接导入，通过js函数将单个图表独有配置和通用配置合并成一个配置选项**。这时遇到了第一个问题，那时候使用的**对象合并函数**会改变第一个参数对象，其实是把可变配置对象的属性合并到了通用配置对象上去。但是这里的逻辑，要求可变配置和通用配置合并成一个对象，并且两者之间不同的要用后者覆盖前者，且前者不能被改变。

又因为 import 的机制，可能对一个页面全局只执行一次 import ，不同组件相同的 import 导入只执行一次。相当于用import实现了一个可改变的共享变量。

然后就写了一个可以不改变第一个参数的深度对象合并方法，顺便写了一个对象深度复制（深拷贝，生成与源对象尽量少的引用关联的对象）的函数。

接着又遇到了第二个问题，Echarts的有些配置项既可以是数组，也可以是对象，是数组的时候，其内部元素数目也可能不一样，这样就导致。最后想到在通用配置对象中将既可以是数组也可以是对象的属性写成空数组，同时将其中的通用配置抽离出来最为更小粒度的基础配置对象。

```js
// 深度合并两个对象，返回一个合并对象
// 不会修改 source 对象
function deepAssign(base, target) {
    if (!base) return null
    if (!target) return copy(base)

    var source = copy(base)
    mergeObj(source, target)

    return source
}

// 为对象生成一个互不干涉的备份
function copy(source) {
    let temp = {}
    for (let key in source) {
        if (source[key] instanceof Array) {  // 对象属性值是数组
            temp[key] = copyArray(source[key])
        } else if (source[key] instanceof Function) {
            temp[key] = source[key]
        } else if (source[key] instanceof Object) {  // 对象类型不能简单地赋值
            temp[key] = copy(source[key])
        }
        else {
            
            temp[key] = source[key]
        }
    }
    return temp
}

// 为数组生成一个互不干涉的备份
function copyArray(array) {
    let temp = []
    for (let i of array) {
        if (i instanceof Array) {
            temp.push(copyArray(i))
        }
        else if (i instanceof Function) {
            temp.push(i)
        } else if (i instanceof Object) {
            temp.push(copy(i))
        }else {
            temp.push(i)
        }
    }
    return temp
}

// 深入合并两个对象
function mergeObj(source, target) {
    var key;
    for (key in target) {
        if (!(key in source)) {
            source[key] = target[key]
        } else if (typeof source[key] === 'object') {
            mergeObj(source[key], target[key])
        } else {
            source[key] = target[key]
        }
    }
}
```

在生成具体的图表时，用对象合并函数合并基础

对象中同层级的同名属性，后面的会将前面的覆盖掉。

### 参考资料

- [vue中使用echarts来绘制世界地图和中国地图](https://www.cnblogs.com/ldlx-mars/p/9242250.html)
- [ECharts地图配置](https://echarts.apache.org/zh/option.html#geo)
- [在线自定义geojson地图数据](http://geojson.io/#map=2/20.0/0.0)
- [官方在地图中标注数据（描点）实例](https://echarts.apache.org/examples/zh/editor.html?c=map-polygon)

