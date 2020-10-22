## JavaScript发展史及实现


## 在HTML中使用JavaScript


## JavaScript基本概念


## JavaScript数据类型

### 基本数据类型
### 引用数据类型

## JavaScript作用域

## JavaScript垃圾收集及内存泄漏

## 面向对象的程序设计

## BOM

## DOM





## 闭包及其作用

**闭包**（closure）：指有权**访问**另一个函数作用域中**变量**的**函数**。闭包可以让你从内部函数访问外部函数作用域。

示例代码：

```js
function fn() {
  let a = 20;
  function foo() {
    console.log(a);
  }
  foo();
}
fn(); // 打印：20
```

在浏览器控制台中可以查看如下，函数foo的作用域访问了fn中的局部变量，此时在fn中就产生了闭包，fn称为闭包函数。

<img src="https://tva1.sinaimg.cn/large/007S8ZIlly1gi5hxhlpuoj30zq0n0q6l.jpg" alt="image-20200827172646335" style="zoom:50%;" />

### 闭包的作用

**延伸变量的作用范围。**

示例代码：

```js
function fn() {
  var a = 20;
  return function foo() {
    console.log(a);
  }
}
const res = fn();
res(); // 打印：20
```

当内部函数被保存到外部时，将会生成闭包。生成闭包后，内部函数依旧可以访问其所在的外部函数的变量。

> 详细解释：
>
> 当函数执行时，会创建一个称为**执行期上下文的内部对象（AO）**，执行期上下文定义了一个函数执行时的环境。
>
> 函数还会获得它所在作用域的**作用域链**，是存储函数能够访问的所有执行期上下文对象的集合，即这个函数中能够访问到的东西都是沿着作用域链向上查找直到全局作用域。
>
> 函数每次执行时对应的执行期上下文都是独一无二的，当函数执行完毕，函数都会失去对这个作用域链的引用，JS 的垃圾回收机制是采用引用计数策略，如果一块内存不再被引用了那么这块内存就会被释放。
>
> 但是，当闭包存在时，即内部函数保留了对外部变量的引用时，这个作用域链就不会被销毁，此时内部函数依旧可以访问其所在的外部函数的变量，这就是闭包，也将会导致内存泄漏。
>

### 经典示例

1、循环中的var变量和setTimeout函数打印结果

```js
for (var i = 0; i < 5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i * 100);
}
function test() {
  var a = [];
  for (var i = 0; i < 5; i++) {
    a[i] = function () {
      console.log(i);
    };
  }
  return a;
}

var myArr = test();
for (var j = 0; j < 5; j++) {
  myArr[j]();
}
// 输出： 5 5 5 5 5
```

当使用立即执行函数进行操作时，代码如下：

```js
for (var i = 0; i < 5; i++) {
  (function (i) {
    setTimeout(function timer() {
      console.log(i);
    }, i * 100);
  })(i);
}
function test() {
  var arr = [];
  for (i = 0; i < 5; i++) {
    (function (j) {
      arr[j] = function () {
        console.log(j);
      };
    })(i);
  }
  return arr;
}

var myArr = test();
for (j = 0; j < 5; j++) {
  myArr[j]();
}
// 输出： 1 2 3 4 5
```

> 闭包问题的解决方法：立即执行函数、let

### 闭包-封装私有变量

编程语言中，比如 Java，是支持将方法声明为私有的，即它们只能被同一个类中的其它方法所调用。而 JavaScript 没有这种原生支持，但我们可以使用闭包来模拟私有方法。私有方法不仅仅有利于限制对代码的访问：还提供了管理全局命名空间的强大能力，避免非核心的方法弄乱了代码的公共接口部分。

```js
<script>
  // 简易计数器
  function Counter() {
  let count = 0;
  this.plus = function() {
    return ++count;
  }

  this.cut = function() {
    return --count;
  }

  this.getCount = function() {
    return count;
  }
}
const counter = new Counter();
counter.plus();
counter.plus();
counter.cut();
counter.plus();
console.log(counter.getCount());
</script>
```

