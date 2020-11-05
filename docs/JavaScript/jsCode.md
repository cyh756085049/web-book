## 深拷贝与浅拷贝的区别及实现

在 javascript 中有不同的方法来复制对象，那么我们怎样才能正确地复制一个对象呢？**对象类型在赋值的过程中其实是复制了地址，从而会导致改变了一方其他也都能被改变的情况**，解决这种问题的方法就是利用深拷贝和浅拷贝。

### 浅拷贝

浅拷贝是创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值，如果属性是引用类型，拷贝的就是内存地址 ，所以**如果其中一个对象改变了这个地址，就会影响到另一个对象**。

##### 实现方式

1、通过`Object.assign()`实现

```js
let a = {
  name: 'raymond'
};
let b = Object.assign({}, a);
a.name = 'ramona';
console.log(b.name);  // raymond
```

2、通过扩展运算符`...`实现

```js
let a = { 
  name: 'raymond'
};
let b = {...a};
a.name = 'ramona';
console.log(b.name);  // raymond
```

3、针对数组

* `Array.prototype.slice`
* `Array.prototype.concat`

```js
let arr = [1, 2, {name: 'ramona'}];
console.log(Array.prototype.slice.call(arr));
console.log(Array.prototype.concat.call(arr));
// 输出：[1, 2, {name: 'ramona'}]
//      [1, 2, {name: 'ramona'}]
```

> 浅拷贝只解决了第一层的问题，当接下去的值中还有对象的话，就需要使用深拷贝

### 深拷贝

深拷贝是将一个对象从内存中完整的拷贝一份出来,从堆内存中开辟一个新的区域存放新对象,且**修改新对象不会影响原对象**。

##### 实现方式

1、`JSON.parse(JSON.stringify(object))`

- 忽略undefined
- 忽略symbol
- 原型链上的属性无法拷贝
- 不能处理RegExp
- 不能正确处理Date
- 不能序列化函数
- 不能处理循环引用的对象

2、递归

遍历对象、数组知道里边都是基本数据类型，然后再去复制

```js
function deepClone(obj, target) {
  if (!obj) return;
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      if (Array.isArray(key) || typeof obj[key] === 'object' && obj[key] !== null) {
        target[key] = [];
        deepClone(obj[key], target[key]);
      } else {
        target[key] = obj[key];
      }
    }
  }
  return target;
}
```


## new的实现
> 步骤：<br/>
 1、创建一个空的简单的js对象<br/>
 2、链接该对象（即该对象的构造函数）到另一个对象<br/>
 3、将步骤1新创建的对象作为this的上下文<br/>
 4、如果该函数没有返回对象，则返回this<br/>
```js
/**
 * 实现 new 运算符
 * 作用：创建一个用户定义的对象类型的实例或者具有构造函数的内置对象的实例
 */
function newObject() {
    // 创建一个空的对象
    let obj = new Object();
    // 获取构造函数
    let Con = [].shift.call(arguments);
    // 链接到原型
    obj.__proto__ = Con.prototype;
    // 绑定this执行构造函数
    let result = Con.apply(obj, arguments);
    // 确保new出来的是一个对象
    return typeof result === 'object' ? result : obj;
}

/**
 * 优化的 new 实现
 * @returns {*}
 */
function newObject1() {
    // 获取构造函数，同时删除arguments 中的第一个参数
    Con = [].shift.call(arguments);
    // 创建一个空的对象并链接到原型，obj可以访问构造函数原型中的属性
    let obj = Object.create(Con.prototype);
    // 绑定this实现继承，obj可以访问到构造函数中的属性
    let ret = Con.apply(obj, arguments);
    // 优化返回构造函数返回的对象
    return ret instanceof Object ? ret : obj;
}

/**
 * 带参数的 new 实现
 * @param fn
 * @param args
 * @returns {*}
 */
function myNew(fn, ...args) {
    const obj = {};
    obj.__proto__ = fn.prototype;
    const res = fn.call(obj, ...args);
    return typeof res === 'object' ? res : obj;
}
```

## 手写一个结合all和race的方法，所有都resolved/reject时才返回，并返回所有的结果

```js
async function  allResolve(promises){
   const success = Array.from(promises).map(promise=>{
 			Promise.resolve(promise).then(val=>val).catch(err=>err);
      })
   return await Promise.all(success);
}
```

## 直接查找字符串

- 不存在返回 false
- 存在，截取 `/` 后面的数字。

```javascript
function find(userAgent) {
  const idx = userAgent.indexOf("tigerbroker");
  if (idx === -1) {
    return "false";
  } else {
    let idxOfApp = userAgent.indexOf(".app");
    let res = userAgent.substring(idx + "tigerbroker".length() + 1, idx2);
    return res;
  }   
}
```





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

## 数组扁平化

数组扁平化就是指将一个多维数组变为一维数组。

```js
[1,[4,6],[8,3,[5,14]]] --->  [1, 4, 6, 8, 3, 5, 14]
```

思路：

遍历数组arr，若arr[i]为数组则**递归遍历**，直至arr[i]不为数组然后与之前的结果concat。

#### 1、reduce方法

reduce本身就是一个迭代循环器，通常用于累加，此处可以通过reduce迭代数组中的元素，然后判断元素是否为数组，如果是，则递归flatten函数，如果不是，则直接返回该元素，并通过concat进行拼接，实现数组扁平化。

```js
const arr = [1,[4,6],[8,3,[5,14]]]
function flatten(arr) {
  return arr.reduce((result, item) => {
    return result.concat(Array.isArray(item) ? flatten(item) : item);
  }, []);
}
console.log(flatten(arr));
// 输出：[1, 4, 6, 8, 3, 5, 14]
```

#### 2、递归方法

递归遍历遍历数组元素，如果元素仍然为数组则继续递归，否则通过concat进行拼接。

```js
const arr = [1,[4,6],[8,3,[5,14]]]
function flatten(arr) {
  var res = [];
  arr.map(item => {
    if (Array.isArray(item)) {
      res = res.concat(flatten(arr));
    } else {
      res.push(item);
    }
  });
  return res;
}
console.log(flatten(arr));
// 输出：[1, 4, 6, 8, 3, 5, 14]
```

> 缺点：数组嵌套非常深时，函数递归调用层数太深会造成栈溢出。

#### 3、`toString`或者`join`或者`String(arr)`实现

将数组转化成字符串的方式：

```js
var str = arr.toString();
var str = String(arr);
var str = arr.join(',');
```

代码实现：

```js
// toString()实现
const arr = [1,[4,6],[8,3,[5,14]]]
function flatten(arr) {
  return arr.toString().split(',').map(item => {
    return Number(item);
  })
} 
console.log(flatten(arr));
// 输出：[1, 4, 6, 8, 3, 5, 14]

// join()实现
const arr = [1,[4,6],[8,3,[5,14]]]
function flatten(arr) {
  return arr.join(',').split(',').map(item => {
    return Number(item);
  })
} 
console.log(flatten(arr));
// 输出：[1, 4, 6, 8, 3, 5, 14]

// String(arr)实现
const arr = [1,[4,6],[8,3,[5,14]]]
function flatten(arr) {
  return String(arr).split(',').map(item => {
    return Number(item);
  })
} 
console.log(flatten(arr));
// 输出：[1, 4, 6, 8, 3, 5, 14]
```

> 缺点：需要进行类型转换

#### 4、ES6扩展运算符

`[].concat(...arr)`每次只会扁平化一层，因此需要判断该数组中有几层数组，然后一层一层的进行扁平化。

```js
const arr = [1,[4,6],[8,3,[5,14]]]
console.log([].concat(...arr));
// 输出：(6) [1, 4, 6, 8, 3, Array(2)]
function flatten(arr) {
  while (arr.some(item => Array.isArray(item))) {
    arr = [].concat(...arr);
  }
  return arr;
}
console.log(flatten(arr));
// 输出：[1, 4, 6, 8, 3, 5, 14]
```

> `arr.some()`方法：数组中只要有一个元素满足「指定条件返回 true」，则停止遍历，此方法就返回 true

#### 5、ES6扩展运算符 + shift + unshift

每次取数组的第一个元素，当判断第一个元素是数组的时候，使用`arrs.unshift(...item)`在数组前边插入。

```JS
const arr = [1,[4,6],[8,3,[5,14]]]
function flatten(arr) {
  let arrs = [...arr];
  let res = [];
  while (arrs.length) {
    // shift()删除数组中的第一个元素，返回结果为被删除的元素
    let item = arrs.shift();
    if (Array.isArray(item)) {
      // unshift()在数组最前面插入一个或多个元素
      arrs.unshift(...item);
    }else {
      res.push(item);
    }
  }
  return res;
}
console.log(flatten(arr));
// 输出：[1, 4, 6, 8, 3, 5, 14]
```






## 解析url
> 编写函数parse，解析url 的参数成一个数组。

> var a = '?token=abc&id=123&id=456'; 执行parse(a) 返回 {'token':'abc', 'id':[123, 456]}
>
> var b = '?name=john&age'; 执行parse(b) 返回 {'name':'john', 'age':null} 

```js
function parseParams(url) {
    if (!url) return null;
    const paramObj = {};
    // 解构赋值
    const [, queryString] = url.split('?');
    const queryStringArr = queryString.split('&');
    for (const queryObj of queryStringArr) {
        let [key, value] = queryObj.split('=');
        // 如果值存在
        if (value) {
            // 将已编码 URI 中所有能识别的转义序列转换成原字符
            value = decodeURIComponent(value);
        } else {
            // 将值置为null
            value = null;
        }
        // 如果参数对象中存在该key对于的value
        if (paramObj[key]) {
            paramObj[key] = Array.isArray(paramObj[key]) ? paramObj[key] : [paramObj[key]];
            paramObj[key].push(value);
        } else {
            // 否则，直接添加到对象中
            paramObj[key] = value;
        }
    }
    return paramObj;
}

const url = '?token=abc&id=123&id=456';
const url1 = '?name=john&age';
const paramObj = parseParams(url1);
const paramObj1 = parseParams(url);
console.log(paramObj);
console.log(paramObj1);

```

## 数组原型去重方法
> 给数组原型增加一个方法strip，用于去掉数组中的重复元素

> var a = ['a', 'b', 'a', 8, 3, 5, 8]; a.strip(); => a为['a', 'b', 8, 3, 5];      
>
> var b = ['apple', 'banana', 'apple', 'grape']; b.strip(); => b为['apple', 'banana', 'grape'];

```js
/**
 * 数组去重方法
 * @param arr
 * @returns {any[]}
 */
// 1 ES6 Set 去重
// 缺点：无法去掉空对象 {}
function uniqueArray1 (arr) {
    // return Array.from(new Set(arr));
    return [...new Set(arr)];
}

// 2 双层嵌套for循环 splice 去重
// 缺点：NaN 和 {} 没有去重
function uniqueArray2 (arr) {
    for (let i = 0; i < arr.length; i++) {
        for (let j = i + 1; j < arr.length; j++) {
            if (arr[i] === arr[j]) {
                arr.splice(j, 1);
                j--;
            }
        }
    }
    return arr;
}

// 3 indexOf 去重
// 缺点：NaN 和 {} 没有去重
function uniqueArray3 (arr) {
    if (!Array.isArray(arr)) {
        console.log('type error');
        return ;
    }
    let array = [];
    for (let i = 0; i < arr.length; i++) {
        if (arr.indexOf(arr[i] === -1)) {
            array.push(arr[i]);
        }
    }
    return array;
}

const arr = [1,1,'true','true',true,true,14,13,false,false,undefined,undefined,null,null,NaN,NaN,'NaN','NaN','a','a',{},{}];
console.log(uniqueArray1(arr));
console.log(uniqueArray2(arr));
console.log(uniqueArray3(arr));
```

> https://segmentfault.com/a/1190000016418021

## 手写promiseAll

```js
function promiseAll1(promiseArray) {
    return new Promise(function (resolve, reject) {
        if (Array.isArray(promiseArray)) {
            return reject(new Error("Promise must be an array"));
        }
        let resolveCount = 0;
        let promiseNum = promiseArray.length;
        let resolveValue = [];
        for (let i = 0; i < promiseNum; i++) {
            Promise.resolve(promiseArray[i]).then(
                (value)  => {
                    resolveValue[i] = value;
                    resolveCount++;
                    if (resolveCount === promiseNum) {
                        return resolveValue;
                    }
                },
                (reason) => {
                    return reject(reason);
                }
            )
        }
    })
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

## 翻转一个字符串
>（美团、猫眼）

```js
// 思路： 双指针 + 解构赋值   从队头和队尾向中间逼近，用解构赋值交换值
// 输入：["h","e","l","l","o"]
// 输出：["o","l","l","e","h"]
var reverseString = function (s) {
    var i = 0, j = s.length - 1;
    while (i < j) {
        [s[i], s[j]] = [s[j], s[i]];
        i++;
        j--;
    }
    return s;
}


// 翻转字符串
function reverse(str) {
    return str.split('').reverse().join("");
}

function reverse1(str) {
    let res = '';
    for (let i = str.length - 1; i >= 0; i--) {
        res += str[i];
    }
    return res;
}
```

## 最长公共子序列

```js
var a = [2,3,4,1,2]
var b = [4,1,2,5,7]
// 动态规划
function longestCommonSubsequence(a, b) {
    let n = a.length;
    let m = b.length;
    let dp = Array.from(new Array(n + 1), () => new Array(m + 1).fill(0));
    for (let i = 1; i <= n; i++) {
        for (let j = 1; j <= m; j++) {
            if (a[i - 1] === b[j - 1]) {
                dp[i][j] = dp[i - 1][j - 1] + 1;
            } else {
                dp[i][j] = Math.max(dp[i][j - 1], dp[i - 1][j]);
            }
        }
    }
    return dp[n][m];
}
// 暴力法递归
function findLonGetSubSequenceArray(a, b) {
    this.dp = function (i, j) {
        // 有一个字符串为空字符串
        if (i === -1 || j === -1) {
            return 0;
        }
        // 相等即找到一个公共字符，加1后继续向前查找
        if (a[i] === b[j]) {
            return this.dp(i - 1, j - 1) + 1;
        } else {
            // 没有找到公共字符，则判断是移动哪个指针能使lcs更长
            return Math.max(this.dp(i - 1, j), this.dp(i, j - 1), this.dp(i - 1, j - 1));
        }
    }
    // 从后往前遍历
    return this.dp(a.length - 1, b.length - 1);
}
```

## 对象key值转数组的方法(Object.keys)

```js
var obj = {
   foo: 'bar',
   baz: 42
 }
 // ES5
 var arr = [];
 for (var i in obj) {
   arr.push(i);
 }
 console.log(arr); // [ 'foo', 'baz' ]
 // ES6
const array = Object.keys(obj);
console.log(array); // [ 'foo', 'baz' ]
```

## 连续子序列的和

```js
function maxSubArray(nums) {
    let res = nums[0];
    for (let i = 1; i < nums.length; i++) {
        nums[i] += Math.max(nums[i - 1], 0);
        res = Math.max(res, nums[i]);
    }
    return res;
}
```

## 实现一个等差数列

> function range(start, end, step) {
>
> //  TODO
>
> }
>
> range(1, 10, 3) // [1, 4, 7, 10]

```js
function range(start, end, step) {
    let res = [];
    for (let i = start; i <= end; i += step)
        res.push(i);
    return res;
}
```

## 原生的 JS 对象扩展方法
> 将原生的 JS 对象扩展一个方法，让方法绑定在 a 变量上(原型链)

```js
Object.protptype.duplicate = function() {
  a.repeat(2);
}
a="123"
a.duplicate(); // '123123'
```

## 封装一个原生的继承方法

```js
/**
 * 继承
 * @param Parent
 * @param Child
 */
function extendsClass(Parent, Child) {
  function F() {}
  F.prototype = Parent.prototype;
  Child.prototype = new F();
  Child.prototype.constrctor = Child;
  return Child;
}
```
## 用reduce实现map功能

```js
Array.protptype.map = function(callback) {
  const array = this;
  return array.reduce((arr, cur. index) => {
    arr.push(callback(cur, index, array));
    return arr;
  }, []);
};
```

测试：

```js
var m = [1, 2, 3, 4, 5].map(function(v, i, arr) {
  return v + v;
});
console.log(m);
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
##  输出数组中出现次数最多的字母前数字的和
```js
function sum(arr) {
    let res = {};
    arr.forEach(item => {
      res[item[1]] ? res[item[1]] += Number(item[0]) : res[item[1]] = Number(item[0]);
    })
    let sum = 0;
    for (let i in res) {
      if (res[i] > sum) {
        sum = res[i]
      }
    }
    return sum
  }

```

## 让a == 1 且 a == 2结果为true

思路：可通过重新定义对象的隐式转换行为实现

* `valueOf`
* `toString`
* `[Symbol.toPrimitive]`

```js
// 方法一
let a = {
  i: 1,
  valueOf() {
    return this.i++
  }
}
console.log(a == 1 && a == 2); // true

// 方法二
let b = {
  i: 1,
  toString() {
    return this.i++
  }
}
console.log(b == 1 && b == 2); // true

// 方法三
let c = {
  i: 1,
  [Symbol.toPrimitive]() {
    return this.i++
  }
}
console.log(c == 1 && c == 2); // true

// 方法四
var a = 1;
var a = 2;
console.log(a == 1 && a== 2); // true
```

## 判断一个字符串是否为回文
> 忽略大小写

##### 思路：

解法1、将字符串转化为数组，翻转后再拼接成字符串，然后同原字符串比较。

```js
function palindrome(str) {
  str = str.toLowerCase();
  return str.split("").reverse().join("") === str;
}
```

解法2：`for`循环，遍历字符串长度的一半，头尾进行比较。

```js
function palindrome(str) {
  for (let i = 0; i < str.length/2; i++) {
    const left = str[i];
    const right = str[str.length - 1 - i];
    if (left.toLowerCase() !== right.toLowerCase()) {
      return false;
    }
  }
  return true;
}
```

## 判断一个数字是否为回文

解法1、将数字先转化为字符串，然后分割翻转后拼接成新的字符串，再与原字符串比较。

```js
function palindrome(number) {
  number = number.toString();
	return number.split("").reverse().join("") === number;
}
```

解法2、通过取余的方法先取到每一位数字，然后构造成数字与原数进行比较。

```js
function palindrome(number) {
  let newNum = number;
  let remainder = 0;
  let reversedNum = 0;
  while (newNum !== 0) {
    remainder = newNum % 10;
    reversedNum = reversedNum * 10 + remainder;
    newNum = parseInt(newNum / 10);
  }
  return reversedNum === number;
}
```


## Promise（A+规范）、then、all方法

## Iterator遍历器实现

## Thunk函数实现（结合Generator实现异步）

## async实现原理（spawn函数）

## class的继承

## Ajax原生实现

## React高阶组件

## 数组去重

## 几种排序算法的实现及其复杂度比较

## 前序后序遍历二叉树（非递归）

## 二叉树深度遍历（分析时间复杂度）

## 跨域的实现（JSONP、CORS）

