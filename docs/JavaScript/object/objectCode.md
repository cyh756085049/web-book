## 深拷贝与浅拷贝的区别及实现

在 javascript 中有不同的方法来复制对象，那么我们怎样才能正确地复制一个对象呢？**对象类型在赋值的过程中其实是复制了地址，从而会导致改变了一方其他也都能被改变的情况**，解决这种问题的方法就是利用深拷贝和浅拷贝。

#### 浅拷贝

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

#### 深拷贝

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

## class的继承
