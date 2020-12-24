## WebAPI：setTimeout

> setTimeout是如何实现的？

浏览器页面是由消息队列和事件循环系统来驱动的。

 `setTimeout` 是一个**定时器，用来指定某个函数在多少毫秒之后执行**。它会返回一个整数，表示定时器的编号，同时你还可以通过该编号来取消这个定时器。下面的示例代码就演示了定时器最基础的使用方式：

```js
function showName() {
  console.log("ramona");
}
var timerID = setTimeout(showName, 200);
```

执行上述代码，通过 setTimeout 指定在 200 毫秒之后调用showName 函数，并输出ramona。

