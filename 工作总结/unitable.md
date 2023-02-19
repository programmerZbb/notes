# 1. 前端

## 内核

* 首先是一个一对多的关系，采用了发布订阅模式

  发布订阅使用的是rxjs实现。



## frame work

* No frame work 采用了无框架设计，兼容react 或vue，不限制使用框架。
* react 使用 reactDom.render 方法，通信采用传入一个方法，高阶函数的方式，触发回调函数通信的方案。



# 2. 后端

## 协同

* 采用了shareDB实现

  shareDB实现了OT算法



## 数据库

* mongodb



## 检索+聚合

* elasticsearch dsl方案



## 接口通信

* 接口采用了 express + ws