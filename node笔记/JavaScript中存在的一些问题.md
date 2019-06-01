### js遇到的问题

* js基本类型中，`null==undefined`为`true`，可以理解为值为0，但是`null==0`为`false`。`null==false`也为false，在`if(null)`中`null`作为`false`处理。
* `toString()`方法的使用：打印字符串，打印对象名称，转化10/2进制