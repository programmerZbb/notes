# echarts 学习

## 1.浏览器画图原理

### canvas

* 基于像素
* 单个html，类似于画笔在画布上画画（canvas标签为画布，js为画笔）
* Echarts 基于 canvas 画图

### svg

* 基于对象模型
* 多个图形元素
* 高保真
* （使用的是标签来实现的）

## 2. 实例代码介绍

* html 结构

  bar.html

* Echarts.init()

  初始化 Echarts 实例

  setOption 用指定数据绘图

* Option对象（配置项 在文档中的配置项中查找）

  * 标题： title

  * 图轴： legend

  * X轴： xAxis
  * Y轴： yAxis （横纵坐标必须得有，饼图没有 X 轴和 Y 轴）

  * 数据： series：
    * name，type，data
    * 根据 type 的值来判断什么图形（bar、line）

## 3. 工具栏组件

* 保存着可视化的附加功能，也属于 option 中的一项。（右上角的工具图标）
* toolbox.html 组件的工具栏
  * show: 是否显示
  * Feature: 具体显示的功能
  * saveAslmage: 保存图片
  * Restore: 还原
  * dataView: 数据视图
  * dataZoom: 缩放视图
  * magicType: 动态类型切换

## 4. tooltip 组件

* tooltip.html，组件的工具栏（就是一些显示的小贴士）
  * show: 是否显示
  * Trigger: 触发方式，axis 就是 x 轴出发

## 5. 标记线和标记点

* 作为图上的额外值（最大值和最小值、平均值）

* 标记线：markline
  * 标记线的添加
  * 最大值、平均值，最小值的标记线

* 标记点：markpoint

  * 标记点的添加
  * 最大值、平均值，最小值的标记点
  * 任意位置的标记点（用特殊的符号来标记）

  ![QQ截图20181024103157](C:\workspace\note\echarts-pic\QQ截图20181024103157.png)

* data 属性中能够配置数据的格式（点还是箭头）、动画

## 饼图

* 饼图展示数据的特点（主要是series中的改变）
  * 展示百分比
  * Type是pie
* center 圆心半径
* Radius 半径
* Name 图例名称
* selectedMode: 是否支持多选
* 能够使用 startAngle 和 endAngle 来绘制扇形图

## 地图

* 主要使用的是geo 属性

## 多个坐标轴（两个y轴）

* 如果数据想指定右边的Y轴，需要在 series 中配置 yAxisIndex: 的值（就是 yAxis 这个数组中的位置）

## 大数据显示的处理

* 需要配置 dataZoom 来实现数据的缩放(在option 中设置)
  * type: "slider" 一个拖拽栏来控制数据的缩放
  * start: 0   开始缩放位置
  * end: 10   结束缩放的位置

# echart 在项目中使用

