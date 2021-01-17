


## 防抖和节流实现

### 防抖

防抖是指在某个事件期限内，对于短时间内连续触发的事件（比如：滚动事件），事件处理函数只执行一次。

```js
/**
 * 防抖函数
 * 多次触发，只在最后一次触发时，执行目标函数
 * @param fn
 * @param time
 * @returns {function(): void}
 */
function debounce(fn, time) {
    let timer = null;
    return () => {
        if (timer) {
            clearTimeout(timer);
        }
        timer = setTimeout(() => {
            fn.apply(this, arguments);
        }, time);
    }
}

const demo = debounce(() => console.log('fn 防抖函数执行了'), 200);
setInterval(demo, 100);


function debounce(fn, delay) {
  let timer;
  return function() {
    if (timer) {
      clearTimeout(timer);
    }
    timer = setTimeout(fn, delay, ...arguments);
  }
}
```

### 节流

如果短时间内大量触发同一事件，那么在函数执行一次之后，该函数在指定的时间期限内不再工作，直至过了这段时间才重新生效。

1、

```js
function throttle(fn, time) {
    // 上一次执行fn的时间
    let previous = 0;
    // 将throttle处理结果当做函数返回
    return function(...args) {
        // 获取当前时间，转换成时间戳，单位毫秒
        let now = +new Date();
        // 将当前时间和上一次执行函数的时间进行对比，大于time就把previous设置为当前时间并执行fn函数
        if (now - previous > time) {
            previous = now;
            fn.apply(this, args);
        }
    }
}

const betterFn = throttle(() => console.log('fn 节流函数执行了'), 1000);
setInterval(betterFn, 10);
```

##### 1、标志位 + 定时器

```js
function throttle(fn, delay) {
  let flag = false;
  return function() {
    if (flag) {
      return;
    }
    flag = true;
    fn.apply(this, arguments);
    setTimeout(() => {
      flag = false;
    }, delay);
  }
}
```

##### 2、使用时间戳

```js
function throttle(fn, delay) {
  let now = 0;
  return function() {
    if (now + delay > Date.now()) {
      return;
    }
    fn.apply(this, arguments);
    now = Date.now();
  }
}
```


## 手写闭包

## 手写继承

## 奇数次调用打印1，偶数次调用打印2

> es6实现？iterator？generator？

```js
// 1
function countFn() {
    countFn.count = countFn.count ? countFn.count + 1 : 1;
    if (countFn.count % 2 === 0) {
        console.log(2);
    } else {
        console.log(1);
    }
}
countFn(); // 1
countFn(); // 2
countFn(); // 1
countFn(); // 2
// 2
const fn = () => {
    (fn.count & 1) === 0 ? console.log(2) : console.log(1);
    fn.count++;
}
fn.count = 1;
fn();
fn();
fn();
fn();
// 3
const A = (function countFn() {
    let index = 1;
    function f() {
        if (index % 2 === 1) {
            console.log(1);
        } else {
            console.log(2);
        }
        index++;
    }
    return f;
})();
A(); // 1
A(); // 2
```






## 函数式编程`compose`

实现功能：

```js
compose([a, b, c])('参数') => a( b( c('参数') ) 
```

代码：

```js
function compose(fn) {
  var len = fn.length;
  var index = len - 1;
  
  for (let i = 0; i < len; i++) {
    if (typeof fn[i] !== "function") {
      throw new TypeError("Expected function");
    }
  }
  
  return function(...args) {
    let result = fn[index](...args);
    while (--index > 0) {
      result = fun[index](tesult);
    }
    return result;
  }
}
```













## Ajax原生实现

## React高阶组件

## 数组去重

## 几种排序算法的实现及其复杂度比较

## 前序后序遍历二叉树（非递归）

## 二叉树深度遍历（分析时间复杂度）

## 跨域的实现（JSONP、CORS）

