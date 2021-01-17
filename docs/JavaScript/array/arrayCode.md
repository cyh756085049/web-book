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