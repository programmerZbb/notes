# 0. 前言

> react 代码仅仅 1000 多行，而react-dom 源码将近 2w 行

# 1. 重点目录

* react 通过npm 安装 react 包源码

* events

* react-dom 

* react-reconciler

  reconcile 调和

  reconciler 调节器

* scheduler (调度程序)

* shred

注意：

react 使用 flowtype 来做类型检查

# 2. 准备

## jsx 编译

* jsx 编译之后。babel react ，调用 react createElement api

```jsx
<div></div>
// -----
<div key="111">
  111
	<div>222</div>
</div>

// 自定义组件
function Test() {
	return <div>111</div>
};
Test.defaultProps = {
	name: '11'
};

<Test name={1}></Test>
```

```jsx
React.createElement("div", null);

React.createElement("div", {
  key: "111"
}, "111", /*#__PURE__*/React.createElement("div", null, "222"));

// 自定义组件
function Test() {
  return /*#__PURE__*/React.createElement("div", null, "111");
}

;
Test.defaultProps = {
  name: '11'
};

/*#__PURE__*/
React.createElement(Test, {
  name: 1
});
```

> 组件内的标签，会转换成 React.createElement 生产的元素。传递参数，第一项元素名称，第二项 props 对象的形式，第三项... 子元素。
>
> 页面上打印的虚拟 DOM 是长得什么样？
>
> ==babel 会根据元素名称首字母的大小写，判定是组件还是标签元素==
>
> 自定义组件传递的是组件本身，普通元素传递的是字符串，这就是为什么 defaultProps 要挂载到组件本身上。

# react 源码

* 在 react 下的 src 目录下

## 1. createElement

1. 剔除掉 config 中的特殊属性，key 和 ref，\__self， __source，不会出现在 this.props 中

```js
function createElement(type, config, children) {
  
}
```

2. 检测 defaultProps 是否挂载

3. 处理完成，return ReactElement

   key: key,
   ref: ref,

   单独俩属性

   ```js
   {
       // This tag allows us to uniquely identify this as a React Element
       $$typeof: REACT_ELEMENT_TYPE,
   
       // Built-in properties that belong on the element
       type: type,
       // type 记录是什么类型，原生的component 还是 function 还是普通元素
       key: key,
       ref: ref,
       props: props,
   
       // Record the component responsible for creating this element.
       _owner: owner,
    }
   ```

   * 大部分的$$typeof都是 REACT_ELEMENT_TYPE，在 createPortal 创建的元素不是这个类型，是 REACT_PROTAL_TYPE。

## 2.  Component & PureComponent

* 在 reactBaseClasses.js 文件中

详解：

​	接受三个参数 `props, context, updater`

