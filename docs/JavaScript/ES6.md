## **let**和**const**命令 

### let

* `let` 命令，用来**声明变量**，用法类似于`var`，但所声明的变量**只在` let `命令所在的代码块内有效**。而 `var` 声明变量，在全局范围内都有效。

```js
// var声明的变量
var a = [];
for (var i = 0; i < 10; i++) {
    a[i] = function() {
        console.log(i);
    };
}
a[6](); // 输出：10
// let声明的变量
var a = [];
for (let i = 0; i < 10; i++) {
    a[i] = function() {
        console.log(i);
    };
}
a[6](); // 输出：6
```

* **`let` 不存在变量提升**。所以，变量一定要**先声明后使用**，否则报错。

```js
console.log(foo); // 输出undefined 
console.log(bar); // 报错ReferenceError 
var foo = 2; 
let bar = 2;
```

* **暂时性死区**。只要块级作用域内存在`let` 命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响。 

```js
var tmp = 2;
if (true) {
    tmp = 3; // ReferenceError
    let tmp;
}
```

上面代码中，存在全局变量` tmp` ，但是块级作用域内` let `又声明了一个局部变量 `tmp` ，导致后者绑定这个块级作用域，所以在 `let` 声明变量前，对`tmp` 赋值会报错。

**在代码块内**，**使用`let`命令声明变量之前，该变量都是不可用的**。这在语法上，称为“暂时性死区”（`temporal dead zone`，简称`TDZ`）。

> ES6规定暂时性死区和修订变量提升，主要是为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为。

* let不允许在相同作用域内，**重复声明同一个变量**。 因此，不能在函数内部重新声明参数。

```js
function func(arg) { 
    let arg; // 报错 
}
function func(arg) { 
    { 
        let arg; // 不报错 
    } 
}
```

### const

* const 声明一个只读的常量。一旦声明，常量的值就不能改变。 const一旦声明变量，就必须立即初始化，不能留到以后赋值。 对于 const 来说，只声明不赋值，就会报错。 

```js
const PI = 3.1415; 
PI // 3.1415 
PI = 3; // TypeError: Assignment to constant variable
```

* 只在声明所在的块级作用域内有效。 

* const 命令声明的常量也是不提升，同样存在暂时性死区，只能在声明的位置后面使用。 

* 与 let 一样不可重复声明。 

* 对于复合类型的变量，变量名不指向数据，而是指向数据所在的地址。const 命令只是保证变量名指向的地址不变，并不保证该地址的数据不变。

  ```js
  const a = []; 
  a.push('Hello'); // 可执行 
  a.length = 0; // 可执行 
  a = ['Dave']; // 报错
  ```

  如果真的想**将对象冻结**，应该使用 `Object.freeze` 方法。 

  ```js
  const foo = Object.freeze({});
  foo.prop = 123; // 常规模式时，这一行不起作用；严格模式时，该行会报错 
  ```

  除了将对象本身冻结，对象的属性也应该冻结。下面是一个**将对象彻底冻结的函数**：

  ```js
  var constantize = (obj) => {
      Object.freeze(obj);
      Object.keys(obj).forEach((key, value) => {
          if (typeof obj[key] === 'object') {
              constantize(obj[key]);
          }
      });
  }
  ```

> ES5只有两种声明变量的方法： var 命令和 function 命令。ES6除了添加 let 和 const 命令，另外还有两种声明变量的方法： import 命令和 class 命令。所以，ES6一共有6种声明变量的方法。 

### 全局对象的属性

全局对象是最顶层的对象，在浏览器环境指的是 window 对象，在Node.js指的是 global 对象。ES5之中，全局对象的属性与全局变量是等价的。 而ES6改变一些规定，为了保持兼容性， var 命令和 function 命令声明的全局变量，依旧是全局对象的属性；另一方面规定， let 命令、 const 命令、 class 命令声明的全局变量，不属于全局对象的属性。

```js
var a = 1; // 如果在Node的REPL环境，可以写成global.a，或者采用通用方法，写成this.a 
window.a // 1 
let b = 1; 
window.b // undefined
```
## 模板字符串
* 基本的字符串格式化。将表达式嵌入字符串中进行拼接。用${}来界定。
* 通过反引号(``)做多行字符串或者单行字符串的拼接。


## 变量的解构赋值

## 箭头函数
- 不需要function关键字来创建函数
- 省略return关键字
- 继承当前上下文的 this 关键字
- 普通 function 的声明在变量提升中是最高的，**箭头函数没有函数提升**
- 箭头函数**没有属于自己的`this`，`arguments`**
- 箭头函数**不能作为构造函数，不能被 new，没有 property**
- 不可以使用 yield 命令，因此箭头函数**不能用作 Generator 函数**
- 不可以使用 new 命令，因为：
  - 没有自己的 this，**无法调用 call，apply**
  - 没有 prototype 属性 ，而 new 命令在执行时需要将构造函数的 prototype 赋值给新的对象的 `__proto__`


## 扩展运算符

组装对象或者数组

```js
//数组
const color = ['red', 'yellow']
const colorful = [...color, 'green', 'pink']
console.log(colorful) //[red, yellow, green, pink]
    
//对象
const alp = { fist: 'a', second: 'b'}
const alphabets = { ...alp, third: 'c' }
console.log(alphabets) //{ "fist": "a", "second": "b", "third": "c"}
```

获取数组或者对象除了前几项或者除了某几项的其他项

```js
//数组
const number = [1,2,3,4,5]
const [first, ...rest] = number
console.log(rest) //2,3,4,5
//对象
const user = {
  username: 'lux',
  gender: 'female',
  age: 19,
  address: 'peking'
}
const { username, ...rest } = user
console.log(rest) //{"address": "peking", "age": 19, "gender": "female"}
```

对于 Object 而言，还可以用于组合成新的 Object 。(ES2017 stage-2 proposal) 当然如果有重复的属性名，右边覆盖左边

```js
const first = {
  a: 1,
  b: 2,
  c: 6,
}
const second = {
  c: 3,
  d: 4
}
const total = { ...first, ...second }
console.log(total) // { a: 1, b: 2, c: 3, d: 4 }
```

## Symbol

## Set和Map数据结构

### Set

ES6提供了新的数据结构Set。Set本身是一个构造函数，用来生成Set数据结构。

##### Set特性

* 类似于数组，但**成员的值都是唯一的，没有重复的值**。 

```js
var s = new Set();
[2,3,5,4,2,2,3].map(x => s.add(x));
for (let i of s) {
  console.log(i); // 输出：2 3 5 4
}
```

* 可接受**一个数组或类似数组的对象**作为参数，用来初始化。

```js
// 数组参数
var s = new Set([1,2,3,4,3]);
console.log([...s]); // [1, 2, 3, 4]
console.log(s.size); // 4
// 类似数组的对象参数
function divs() {
  return [...document.querySelectorAll('div')];
}
var set = new Set(divs());
// 类似于
divs().forEach(div => set.add(div));
console.log(set.size);
```

* 向Set加入值的时候，**不会发生类型转换**，所以 5 和 "5" 是两个不同的值。

  > Set内部**判断两个值是否不同**，使用的算法叫做“Same-value equality”，它**类似于精确相等运算符（ === ）**，**主要的区别是 NaN 等于自身，而精确相等运算符认为 NaN 不等于自身**。

  * 在Set内部，两个NaN是相等的。

    ```js
    let set = new Set();
    let a = NaN;
    let b = NaN;
    set.add(a);
    set.add(b);
    console.log(set); // Set(1) {NaN}
    ```

  * 在Set内部，两个对象总是不相等的。

    ```js
    let set = new Set();
    set.add({});
    console.log(set.size); // 1
    set.add({});
    console.log(set.size); // 2
    console.log(set); // Set(2) {{…}, {…}}
    ```

##### **Set**实例的属性和方法

Set结构的实例属性

| 属性                        | 描述                        |
| --------------------------- | --------------------------- |
| `Set.prototype.constructor` | 构造函数，默认就是`Set`函数 |
| `Set.prototype.size`        | 返回`Set`实例的成员总数     |

Set实例的方法

* 操作方法：用于操作数据

| 操作方法        | 描述                                         |
| --------------- | -------------------------------------------- |
| `add(value)`    | 添加某个值，返回Set结构本身                  |
| `delete(value)` | 删除某个值，返回一个布尔值，表示删除是否成功 |
| `has(value)`    | 返回一个布尔值，表示该值是否为 Set 的成员    |
| `clear()`       | 清除所有成员，没有返回值                     |

```js
let s = new Set();
s.add(1).add(2).add(2);
console.log(s); // Set(2) {1, 2}
console.log(s.size); // 2
console.log(s.has(2)); // true
console.log(s.has(3)); // false
console.log(s.delete(2)); // true

// 判断是否包括一个键上面,Object 结构和 Set 结构的写法
// 对象的写法 
var properties = { 'width': 1, 'height': 1 };
if (properties[someName]) { 
  // do something 
}
// Set的写法 
var properties = new Set(); 
properties.add('width'); 
properties.add('height'); 
if (properties.has(someName)) { 
  // do something 
}
```

* 遍历方法：用于遍历成员

| 遍历方法    | 描述                     |
| ----------- | ------------------------ |
| `keys()`    | 返回键名的遍历器         |
| `values()`  | 返回键值的遍历器         |
| `entries()` | 返回键值对的遍历器       |
| `forEach()` | 使用回调函数遍历每个成员 |

> ⚠️：**Set 的遍历顺序就是插入顺序**。这个特性有时非常有用，比如使用Set保存一个回调函数列表，调用时就能保证按照添加顺序调用。 

（**1**） **keys()** ， **values()** ， **entries()** 

```js
let set = new Set(['red', 'green', 'blue']); 
for (let item of set.keys()) { 
  console.log(item); 
}
// red 
// green 
// blue 
for (let item of set.values()) { 
  console.log(item); 
}
// red 
// green 
// blue 
for (let item of set.entries()) { 			
  console.log(item); }
// ["red", "red"] 
// ["green", "green"] 
// ["blue", "blue"]
```

key 方法、 value 方法、 entries 方法返回的都是遍历器对象。由于Set结构没有键名，只有键值（或者说键名和键值是同一个值），所以 **key 方法和 value 方法的行为完全一致。**

**Set结构的实例默认可遍历，它的默认遍历器生成函数就是它的 values 方法。** 

```js
Set.prototype[Symbol.iterator] === Set.protptype.values; // true

// 可以省略 values 方法，直接用 for...of 循环遍历Set
let set = new Set(['red', 'green', 'blue']); 
for (let x of set) { 
  console.log(x); 
}// red 
// green 
// blue
```

（**2**） **forEach()** 

Set结构的实例的 forEach 方法，用于对每个成员执行某种操作，没有返回值。 

```js
let set = new Set([1, 2, 3]); 
set.forEach((value, key) => console.log(value * 2) ) 
// 2 
// 4 
// 6
```

 forEach 方法的参数就是一个处理函数。该函数的参数依次为键值、键名、集合本身。另外， forEach 方法还可以有第二个参数，表示绑定的this对象。

##### Set的应用

###### 1、对数组进行去重

```js
// 方式一 
const array = [1,2,3,4,3];
console.log([...new Set(array)]); // [1,2,3,4]
// 方式二
function dedupe(array) {
  return Array.from(new Set(array));
}
console.log(dedupe([1,1,2,4])); // [1,2,4]
```

###### 2、数组的 map 和 filter 方法也可以用于Set

```js
let set = new Set([1, 2, 3]); 
set = new Set([...set].map(x => x * 2)); 
// 返回Set结构：{2, 4, 6} 
let set = new Set([1, 2, 3, 4, 5]); 
set = new Set([...set].filter(x => (x % 2) == 0)); 
// 返回Set结构：{2, 4}
```

###### 3、使用Set实现并集（Union）、交集（Intersect）和差集 （Difference）

```js
let a = new Set([1, 2, 3]);
let b = new Set([4, 3, 2]);
// 并集
let union = new Set([...a, ...b]);
console.log(union); // Set(4) {1, 2, 3, 4}
// 交集
let intersect = new Set([...a].filter(x => b.has(x)));
console.log(intersect); // Set(2) {2, 3}
// 差集
let difference = new Set([...a].filter(x => !b.has(x)));
console.log(difference); // Set(1) {1}
```

###### 4、在遍历操作中，同步改变原来的Set结构

```js
// 方式1：使用map函数
let set = new Set([1,2,3]);
set = new Set([...set]).map(val => val * 2);
// 方式2：Array.from()
let set = new Set([1,2,3]);
set = new Set(Array.from(set, val => val * 2));
```

##### WeakSet

###### 特点：

1、WeakSet结构与Set类似，也是不重复的值的集合。

2、WeakSet是一个构造函数，可以使用 new 命令，创建WeakSet数据结构。

3、WeakSet没有 size 属性，没有办法遍历它的成员。 

###### 与Set的区别：

* WeakSet的成员只能是对象，而不能是其他类型的值。 
* WeakSet中的对象都是弱引用，即垃圾回收机制不考虑WeakSet对该对象的引用，因此WeakSet是不可遍历的。 

###### WeakSet结构的方法：

**WeakSet.prototype.add(value)**：向WeakSet实例添加一个新成员。 

**WeakSet.prototype.delete(value)**：清除WeakSet实例的指定成员。 

**WeakSet.prototype.has(value)**：返回一个布尔值，表示某个值是否在 

WeakSet实例之中。

###### WeakSet用途：

储存DOM节点，而不用担心这些节点从文档移除时，会引发内存泄漏。 

```js
const foos = new WeakSet();
class Foo {
  constructor() {
    foos.add(this);
  }
  method() {
    if (!foos.has(this)) {
      throw new TypeError('Foo.prototype.method 只能在Foo的实例上调用！');
    }
  }
}
```

上面代码**保证了 Foo 的实例方法，只能在 Foo 的实例上调用**。这里使用WeakSet的好处是， foos 对实例的引用，不会被计入内存回收机制，所以删除实例的时候，不用考虑 foos ，也不会出现内存泄漏。

### Map

##### 出现的原因：

JavaScript的对象（Object），本质上是键值对的集合（Hash结构），但是**传统上只能用字符串当作键**。为了解决这个问题，**ES6提供了Map数据结构**。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，**各种类型的值（包括对象）都可以当作键**。

##### 特点：

1、Map是一种更完善的Hash结构实现。

```js
var map = new Map();
var o = {p: 'hello world'};
map.set(o, 'content');
map.get(o);
map.has(o);
map.delete(o);
```

2、作为构造函数，Map也可以接受一个数组作为参数。该数组的成员是一个个表示键值对的数组。 

```js
var map = new Map([['name', '张三'], ['title', 'Author']]); 
map.size // 2 
map.has('name') // true 
map.get('name') // "张三" 
map.has('title') // true 
map.get('title') // "Author"

// map实际执行的算法
var item =  [ ['name', '张三'], ['title', 'Author'] ];
var map = new Map();
item.forEach(([key, value]) => map.set(key, value));
```

3、map如果对同一个键多次赋值，后面的值将覆盖前面的值。 

```js
// 覆盖的情况
let map = new Map(); 
map.set(1, 'aaa').set(1, 'bbb'); 
map.get(1) // "bbb"

// 不覆盖的情况
var map = new Map(); 
var k1 = ['a']; 
var k2 = ['a']; 
map.set(k1, 111).set(k2, 222); 
map.get(k1) // 111 
map.get(k2) // 222
```

> **Map的键实际上是跟内存地址绑定的，只要内存地址不一样，就视为两个键**。这就解决了同名属性碰撞（clash）的问题，我们扩展别人的库的时候，如果使用对象作为键名，就不用担心自己的属性与原作者的属性同名。

##### Map方法：

###### 实例的属性和操作方法 :

| 属性和操作方法    | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| `size`属性        | 返回Map结构的成员总数                                        |
| `set(key, value)` | 设置 key 所对应的键值，然后返回整个Map结构。如果 key 已经有值，则键值会被更新，否则就新生成该键 |
| `get(key)`        | 读取 key 对应的键值，如果找不到 key ，返回 undefined         |
| `has(key)`        | has 方法返回一个布尔值，表示某个键是否在Map数据结构中        |
| `delete(key)`     | delete 方法删除某个键，返回true。如果删除失败，返回false     |
| `clear()`         | clear 方法清除所有成员，没有返回值                           |

###### 遍历方法：

| 方法        | 描述                 |
| ----------- | -------------------- |
| `keys()`    | 返回键名的遍历器     |
| `values()`  | 返回键值的遍历器     |
| `entries()` | 返回所有成员的遍历器 |
| `forEach()` | 遍历Map的所有成员    |

> Map的遍历顺序就是插入顺序

```js
let map = new Map([ ['F', 'no'], ['T', 'yes'], ]); for (let key of map.keys()) { 
  console.log(key); 
}
// "F" 
// "T" 
for (let value of map.values()) { 		   
  console.log(value); 
}
// "no" 
// "yes" 
for (let item of map.entries()) { 
  console.log(item[0], item[1]); 
}
// "F" "no" 
// "T" "yes" 
// 或者 
for (let [key, value] of map.entries()) { 	     
  console.log(key, value); 
}// 等同于使用map.entries() 
for (let [key, value] of map) { 
  console.log(key, value); 
}
```

## Promise对象
* **Promise是异步编程的一种解决方案**，比传统的解决方案(回调函数和事件)更合理和更强大。Promise里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。
* 有了 Promise 对象，就可以**将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数**。此外， Promise 对象提供统一的接口，使得控制异步操作更加容易。 

### Promise对象的特点
1、对象的状态不受外界影响。

* Promise对象代表一个**异步操作**。包含3种状态： **Pending （进行中）、 Resolved （已完成，又称Fulfilled） 和 Rejected （已失败）**。只有异步操作的结果，可以决定当前是哪一种状态，任 何其他操作都无法改变这个状态。

2、一旦状态改变，就不会再变，任何时候都可以得到这个结果。

*  Promise 对象 的状态改变，只有两种可能：**从 Pending 变为 Resolved 和从 Pending 变 为 Rejected 。**只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果。

#### 缺点

* 无法取消 Promise ，一旦新建它就会立即执行，无法中途取消。
* 如果不设置回调函数， Promise 内部抛出的错误，不会反应到外部。
* 当处于 Pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

### Promise的基本用法

* `Promise`对象是一个构造函数，可以生成Promise实例，其参数是一个函数，该函数有两个参数为`resolve`和`reject`，这两个参数都是函数，由JavaScript引擎提供，不用自己部署。在执行异步操作时调用，并将其结果作为参数传递出去。

* Promise实例生成后，可以使用`then`方法接受`Resolved`和`Reject`状态的回调函数作为参数。其中，`Reject`函数是可选的，两个函数都接受Promise对象传出的值作为参数。（`then`方法指定的回调函数，将在当前脚本所有同步任务执行完才会执行）。
* `resolve` 函数的参数除了正常的值以外，还可能是另一个`Promise`实例，表示异步操作的结果有可能是一个值，也有可能是另一个异步操作。

*  then 方法是定义在原型对象 Promise.prototype上的。它的作用是为Promise实例添加状态改变时的回调函数，可以采用链式写法。
* Promise.prototype.catch 方法是 .then(null, rejection) 的别名，用于指定发生错误时的回调函数。如果 catch 方法返回的还是一个Promise对象，则后面还可以接着调用 then 方法。 如果没有报错，则会跳过 catch 方法。 catch 方法之中，还能再抛出错误，如果catch 方法抛出一个错误，而后面没有别的 catch 方法了，这个错误将不会被捕获，也不会传递到外层。如果其后边再加入一个catch方法，则可以捕获前一个 catch 方法抛出的异常。

```js
var promise = new Promise(function(resolve, reject) { 
  // ... some code 
  if (/* 异步操作成功 */){ 
      resolve(value); 
	} else { 
      reject(error); 
	} 
});
promise.then(function(value) {
  // success
}， function(error) {
  // failure
})
```

##### Promise对象实现的Ajax操作的例子

```js
var getJSON = function(url) {
  var promise = new Promise(function(resolve, reject) {
    var client = new XMLHttpRequest();
    clinet.open("GET", url);
    clinet.onreadystatechange = handler;
    client.responseType = "json";
    client.setRequestHeader("Accept", "application/json");
    client.send();

    function handler() {
      if (this.readyState !== 4) {
        return;
      }
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    }
  });
  return promise;
};
getJSON("/post.json").then(function(json) {
  console.log('Contents:' + json);
}, function(error) {
  console.log('出错了', error);
})
```

#### Promise.resolve()

作用：将现有的对象转为Promise对象。

Promise.resolve 方法的参数分成四种情况。 

（**1**）参数是一个**Promise**实例 

如果参数是Promise实例，那么 Promise.resolve 将不做任何修改、原封不动地 

返回这个实例。 

（**2**）参数是一个 **thenable** 对象 

thenable 对象指的是具有 then 方法的对象，Promise.resolve 方法会将这个对象转为Promise对象，然后就立即执行 thenable 对象的 then 方法。 

（**3**）参数不是具有 **then** 方法的对象，或根本就不是对象 

如果参数是一个原始值，或者是一个不具有 then 方法的对象，则 Promise.resolve 方法返回一个新的Promise对象，状态为 Resolved 。

（**4**）不带有任何参数 

Promise.resolve 方法允许调用时不带参数，直接返回一个 Resolved 状态的Promise对象。 

#### **Promise.reject()** 

Promise.reject(reason) 方法也会返回一个新的Promise实例，该实例的状态为rejected 。它的参数用法与 Promise.resolve 方法完全一致。 

### Promise.all

 `Promise.all(promiseArray) `方法是将多个 Promise 对象实例包装，生成并返回一个新的 Promise 实例。

##### Promise.all特点

* 接收一个Promise对象的实例的数组或具有Iterator接口的对象，且返回的每个成员都是Promise实例。如果元素不是 Promise 对象的实例，则使用 Promise.resolve 方法将参数转成 Promise 实例，再进一步处理。
* 如果全部成功，状态变为 resolved（ fulfilled），返回值将组成一个数组传给回调函数。
* 只要有一个失败，状态就变为 rejected，第一个被 reject 的实例的返回值，会传递给 新的Promise实例的回调函数。

```js
var p1 = Promise.resolve(1),
p2 = Promise.reject(2),
p3 = Promise.resolve(3);
Promise.all([p1, p2, p3]).then(function(results) {
  console.log(results);
}).catch(function(e) {
  console.log(e); 
})
// 打印： 2  // then方法没有执行，catch方法执行
```

promise 数组中所有的 promise 实例都变为 resolve 的时候，then方法才会返回，并将所有结果传递 results 数组中。promise 数组中任何一个 promise 为 reject 的话，则整个 Promise.all 调用会立即终止，执行catch方法，并返回一个 reject 的新的 promise 对象。

##### 实现Promise.all方法

```js
function promiseAll(promiseArray) {
  return new Promise(function(resolve, reject) {
    // 判断传入的是否是数组
    if (isArray(promiseArray)) {
      return reject(new Error("Promise must be an array"));
    }
    var resolveCount = 0;
    var promiseNum = promiseArray.length;
    var resolveValue = [];
    for (let i = 0; i < promiseNum; i++) {
      Promise.resolve(promiseArray[i]).then(
        (value) => {
          resolveValue[i] = value;
          resolveCount++;
          if (resolveCount === promiseNum) {
            return resolveValue;
          }
        },
        (reason) => {
          return reject(reason);
        }
      );
    }
  });
}
```

### Promise.race()

* Promise.race 方法同样是将多个Promise实例，包装成一个新的Promise实例。`var p = Promise.race([p1,p2,p3]); `只要 p1 、 p2 、 p3 之中有一个实例率先改变状态， p 的状态就跟着改变。那个率先改变的Promise实例的返回值，就传递给 p 的回调函数。 

* Promise.race()方法的参数同Promise.all方法一样。

```js
var p = Promise.race([ 
  fetch('/resource-that-may-take-a-while'), 
  new Promise(function (resolve, reject) { 
    setTimeout(() => reject(new Error('request timeout')), 5000) 
  }) 
])
p.then(response => console.log(response)) 
p.catch(error => console.log(error))
// 如果5秒之内 fetch 方法无法返回结果，变量 p 的状态就会变 为 rejected ，从而触发 catch 方法指定的回调函数
```

### 附加方法

**done()** 

Promise对象的回调链，不管以 then 方法或 catch 方法结尾，要是最后一个方法抛出错误，都有可能无法捕捉到（因为Promise内部的错误不会冒泡到全局）。 因此，我们可以提供一个 **done 方法**，**总是处于回调链的尾端，保证抛出任何可能出现的错误。** 

```js
// 示例
asyncFunc() .then(f1) .catch(r1) .then(f2) .done();
// 实现
Promise.prototype.done = function (onFulfilled, onRejected) { 	
  this.then(onFulfilled, onRejected) 
    .catch(function (reason) { 
    // 抛出一个全局错误 
    setTimeout(() => { throw reason }, 0); 
  }); 
};
```

**finally()** 

finally 方法用于指定**不管Promise对象最后状态如何，都会执行的操作**。它与 done 方法的最大区别，**它接受一个普通的回调函数作为参数**，该函数不管怎样都必须执行。 

```js
// 示例：服务器使用Promise处理请求，然后使用 finally 方法关掉服务器
server.listen(0) 
  .then(function () { 
  // run test 
	})
  .finally(server.stop);
// 实现
Promise.prototype.finally = function (callback) { 
  let P = this.constructor; 
  return this.then( 
    value => P.resolve(callback()).then(() => value), 	
    reason => P.resolve(callback()).then(() => { throw reason }) 
  ); 
};
```

### 应用

1、加载图片。将图片的加载写成一个Promise，一旦加载完成，Promise的状态就发生变化。

```js
const preloadImage = function(path) {
  return new Promise(function(resolve, reject) {
    var image = new Image();
    image.onload = resolve;
    image.onerror = reject;
    image.src = path;
  });
};
```

2、**Generator**函数与**Promise**的结合。使用Generator函数管理流程，遇到异步操作的时候，通常返回一个 Promise 对象。

3、**async**函数。async函数与Promise、Generator函数一样，是用来取代回调函数、解决异步操作的一种方法。**它本质上是Generator函数的语法糖**。async函数并不属于ES6，而是被列入了ES7，但是traceur、Babel.js、regenerator等转码器已经支持这个功能，转码后立刻就能使用。

## **Babel**转码器 

Babel是一个广泛使用的**ES6转码器**，**可以将ES6代码转为ES5代码，从而在现有环境执行**。这意味着，你可以用ES6的方式编写程序，又不用担心现有环境是否支持。

```js
// 转码前 
input.map(item => item + 1); 
// 转码后 
input.map(function (item) { return item + 1; });
```

**Babel的配置文件是 .babelrc** ，存放在项目的根目录下。该文件用来设置转码规则和插件，基本格式如下。 

```js
{ 
"presets": [], 
"plugins": [] 
}
```

**presets 字段设定转码规则**，官方提供以下的规则集，你可以根据需要安装。 

```shell
# ES2015转码规则 
$ npm install --save-dev babel-preset-es2015 
# react转码规则 
$ npm install --save-dev babel-preset-react 
# ES7不同阶段语法提案的转码规则（共有4个阶段），选装一个 
$ npm install --save-dev babel-preset-stage-0 
$ npm install --save-dev babel-preset-stage-1 
$ npm install --save-dev babel-preset-stage-2 
$ npm install --save-dev babel-preset-stage-3
```

然后，将这些规则加入 .babelrc 。

```js
{ "presets": [ "es2015", "react", "stage-2" ],"plugins": [] }
```

以下所有Babel工具和模块的使用，都必须先写好 .babelrc 。 

#### 命令行转码 **babel-cli** 

Babel提供 **babel-cli 工具**，用于**命令行转码**。 安装命令及基本用法如下。 

```shell
$ npm install --global babel-cli 
# 转码结果输出到标准输出 
$ babel example.js 
# 转码结果写入一个文件 
# --out-file 或 -o 参数指定输出文件 
$ babel example.js --out-file compiled.js 
# 或者 
$ babel example.js -o compiled.js 
# 整个目录转码 
# --out-dir 或 -d 参数指定输出目录 
$ babel src --out-dir lib 
# 或者 
$ babel src -d lib 
# -s 参数生成source map文件 
$ babel src -d lib -s
```

上面代码是**在全局环境下，进行Babel转码**。这意味着，如果项目要运行，全局环境必须有Babel，也就是说项目产生了对环境的依赖。另一方面，**这样做也无法支持不同项目使用不同版本的Babel**。一个解决办法是**将 babel-cli 安装在项目之中**。 

```shell
# 安装 
$ npm install --save-dev babel-cli 
```

然后，改写 package.json 。

```json
{ 
// ... 
    "devDependencies": { 
    "babel-cli": "^6.0.0" 
    },
    "scripts": { 
    "build": "babel src -d lib" 
    }, 
} 
```

转码的时候，就执行下面的命令。 

```shell
$ npm run build 
```

## 异步操作和**Async**函数 

### 什么是异步

所谓"异步"，简单说就是一个任务分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段。

相应地，连续的执行就叫做同步。由于是连续执行，不能插入其他任务，所以操作系统从硬盘读取文件的这段时间，程序只能干等着。

### 为什么进行异步操作

Javascript语言的执行环境是“单线程”的，如果没有异步编程，根本没法用，非卡死不可。

#### 2、ES6诞生以前，异步编程的方法

* ##### 回调函数 

JavaScript语言对异步编程的实现，就是回调函数。所谓回调函数，就是把任务的第二段单独写在一个函数里面，等到重新执行这个任务的时候，就直接调用这个函数。它的英语名字callback，直译过来就是"重新调用"。 

* ##### Promise 对象 

回调函数本身并没有问题，它的问题出现在多个回调函数嵌套。如果依次读取多个文件，就会出现多重嵌套。

Promise就是为了解决这个问题而提出的。它不是新的语法功能，而是一种新的写法，允许将回调函数的嵌套**，改成链式调用**。采用Promise，连续读取多个文件， Promise提供then方法加载回调函数，catch方法捕捉执行过程中抛出的错误。 

Promise 的最大问题是代码冗余，原来的任务被Promise 包装了一下，不管什么操作，一眼看去都是一堆 then，原来的语义变得很不清楚。 因此引入协程（**Generator**函数 ）。

* 事件监听 

* 发布/订阅 

##### **Generator**函数 

Generator函数是协程在ES6的实现，最大特点就是可以交出函数的执行权（即暂停执行）。 

整个Generator函数就是一个封装的异步任务，或者说是异步任务的容器。异步操作需要暂停的地方，都用 yield 语句注明。Generator函数的执行方法如下。

```js
function* gen(x) {
    var y = yield x + 2;
    return y;
}
var g = gen(1);
g.next();// { value: 3, done: false }
g.next(); // { value: undefined, done: true }
```

上面代码中，调用Generator函数，会返回一个内部指针（即遍历器）g 。这是Generator函数不同于普通函数的另一个地方，即执行它不会返回结果，返回的是指针对象。调用指针g的next方法，会移动内部指针（即执行异步任务的第一段），指向第一个遇到的yield语句，上例是执行到 x + 2 为止。 

换言之，next方法的作用是分阶段执行Generator函数。每次调用next方法，会返回一个对象，表示当前阶段的信息（value属性和done属性）。value属性是yield语句后面表达式的值，表示当前阶段的值；done属性是一个布尔值，表示Generator函数是否执行完毕，即是否还有下一个阶段.

#### **4、async**函数 

ES7提供了 async 函数，使得异步操作变得更加方便。 async 函数就是Generator函数的语法糖。 

```js
var asyncReadFile = async function (){ 
    var f1 = await readFile('/etc/fstab'); 
    var f2 = await readFile('/etc/shells'); 
    console.log(f1.toString()); 
    console.log(f2.toString()); 
};
```

async 函数完全可以看作多个异步操作，包装成的一个Promise对象，而 await 命令就是内部 then 命令的语法糖。 

##### 语法：

* async 函数返回一个Promise对象，async 函数内部 return 语句返回的值，会成为 then 方法回调函数的参数。

  ```js
  async function f() { 
      return 'hello world'; 
  }
  f().then(v => console.log(v)) // "hello world"
  ```

* async 函数返回的Promise对象，必须等到内部所有 await 命令的Promise对象执行完，才会发生状态改变。也就是说，只有 async 函数内部的异步操作执行完，才会执行 then 方法指定的回调函数。 

* 正常情况下， await 命令后面是一个Promise对象。如果不是，会被转成一个立即 resolve 的Promise对象。 

> 只要一个 await 语句后面的Promise变为 reject ，那么整个 async 函数都会中断执行。 

##### **async** 函数的用法 

async 函数返回一个Promise对象，可以使用 then 方法添加回调函数。当函数执行的时候，一旦遇到 await 就会先返回，等到触发的异步操作完成，再接着执行函数体内后面的语句。 

```js
function timeout(ms) {
    return new Promise((resolve) => {
        setTimeout(resolve, ms);
    });
}

async function asyncPrint(value, ms) {
    await timeout(ms);
    console.log(value);
}

asyncPrint('hello world', 50);
```

> 注意点：
>
> 1、await 命令后面的Promise对象，运行结果可能是rejected，所以最好把 await 命令放在 try...catch 代码块中。
>
> 2、多个 await 命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。 
>
> 3、await 命令只能用在 async 函数之中，如果用在普通函数，就会报错。

## Iterator
JavaScript原有的表示“集合”的数据结构，主要是数组（Array）和对象 （Object），ES6又添加了Map和Set。这样就需要一种统一的接口机制，来处理所有不同的数据结构。遍历器（Iterator）就是这样一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。

### Iterator的作用

* 为各种数据结构，提供一个统一的、简便的访问接口； 
* 使得数据结构的成员能够按某种次序排列；
* ES6创造了一种新的遍历命令 for...of 循环，Iterator接口主要供 for...of 消费。 

## Generator函数
### 协程

传统的编程语言，早有异步编程的解决方案（其实是多任务的解决方案）。其中有一种叫做"协程"（coroutine），意思是多个线程互相协作，完成异步任务。 

> 协程有点像函数，又有点像线程。它的运行流程大致如下。 
>
> 第一步，协程A开始执行。 
>
> 第二步，协程A执行到一半，进入暂停，执行权转移到协程B。 
>
> 第三步，（一段时间后）协程B交还执行权。 
>
> 第四步，协程A恢复执行。 
>
> 上面流程的协程A，就是异步任务，因为它分成两段（或多段）执行

示例代码如下：

```js
function *asyncJon() {
    // 其他代码
    var f = yield readFile(fileA);
    // 其他代码
}
```

协程遇到 yield 命令就暂停，等到执行权返回，再从暂停的地方继续往后执行。它的最大优点，就是代码的写法非常像同步操作，如果去除yield命令，简直一模一样。

### 异步编程
异步编程的方法，大概有[下面四种](http://www.ruanyifeng.com/blog/2012/12/asynchronous＿javascript.html)。

> - 回调函数
> - 事件监听
> - 发布/订阅
> - Promise 对象

 **异步编程的语法目标，就是怎样让它更像同步编程。**

**这种不连续的执行，就叫做异步。**连续的执行，就叫做同步。

### 回调函数

JavaScript 语言对异步编程的实现，就是回调函数。**所谓回调函数，就是把任务的第二段单独写在一个函数里面，等到重新执行这个任务的时候，就直接调用这个函数。**它的英语名字 callback，直译过来就是"重新调用"。

### Promise

回调函数本身并没有问题，它的问题**出现在多个回调函数嵌套**。如果依次读取多个文件，就会**出现多重嵌套**。代码不是纵向发展，而是横向发展，很快就会乱成一团，**无法管理**。

Promise就是为了解决这个问题而提出的。它不是新的语法功能，而是一种新的写法，**允许将回调函数的横向加载，改成纵向加载**。采用Promise，连续读取多个文件，写法如下。

Promise 的最大问题是代码冗余，原来的任务被Promise 包装了一下，不管什么操作，一眼看去都是一堆 then，原来的语义变得很不清楚。

## Generator函数的概念

Generator 函数是协程在 ES6 的实现，最大特点就是可以交出函数的执行权（即暂停执行）。

> ```javascript
> function* gen(x){
> var y = yield x + 2;
> return y;
> }
> ```

上面代码就是一个 Generator 函数。它不同于普通函数，是可以暂停执行的，所以函数名之前要加星号，以示区别。

## Proxy和Reflect

## Class

## Module



