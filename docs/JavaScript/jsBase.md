## JavaScript发展史及实现

## D3.js与echart.js的区别

共同点：它们都是用于数据可视化中，实现图表的绘制。

| 特点/JS  | D3.js                            | echart.js                                                    |
| -------- | -------------------------------- | ------------------------------------------------------------ |
| 绘制     | 通过Svg来绘制图形                | 使用canvas绘制图形                                           |
| 兼容性   | IE9以上以及所有的主流浏览器      | IE6以及以上的所有主流浏览器                                  |
| 优缺点   | 使用灵活，太底层，但学习成本大   | 封装好的方法可以直接调用；使用简单，但不够灵活               |
| 适用场景 | 在项目开发中有较多用户交互的场景 | 项目中一般没有用户交互的场景，前端将数据通过图表的形式展示给用户 |

?> **Svg**：不依赖分辨率；可以操作dom、支持事件处理器；复杂度高，会减慢页面的渲染速度<br/> **Canvas：**：依赖分辨率；基于js绘制图形、不支持事件处理器；能以png或者jpg的格式保存图片。

### 适用场景

1）**Echarts**：如果项目中一般没有用户交互的场景，**前端将数据通过图表的形式展示给用户**，一般使用echarts，Echarts里面的方法封装比较好，用的时候直接调用就能实现效果。

对于echarts的使用比较简单，**引入echarts.js**，然后就可以通过 [echarts.init](https://blog.csdn.net/ysj1218/article/details/80523474#echarts.init) 方法**初始化一个 echarts 实例**并通过 [setOption](https://blog.csdn.net/ysj1218/article/details/80523474#echartsInstance.setOption) 方法**设置图表实例的配置项以及数据**，万能接口，所有参数和数据的修改都可以通过setOption完成，ECharts 会合并新的参数和数据，然后刷新图表。如果开启[动画](https://blog.csdn.net/ysj1218/article/details/80523474#option.html%23option.animation)的话，ECharts 找到两组数据之间的差异然后通过合适的动画去表现数据的变化。

2）**D3**：因为D3支持事件处理器可以操作dom，所以如果在项目开发中如果有较多用户交互的场景，可以使用D3。**对事件监听的话**，一般使用D3，使用svg绘制图形，支持事件处理器。需要自己添加画布，绘制图形，绘制的每一个图形都为一个对象，可以添加相应的事件操作，操作dom。


## 在HTML中使用JavaScript


## JavaScript基本概念

## 函数、变量的内存什么时候创建

JavaScript是在**创建变量（对象，字符串等）时自动进行了分配内存**，并且在不使用它们时“自动”释放。 释放的过程称为垃圾回收。

?> **内存生命周期：**  <br>1. 分配你所需要的内存<br/>2. 使用分配到的内存（读、写）<br>3. 不需要时将其释放\归还

* 值的初始化 `var n = 123;`
* 通过函数调用分配内存 `var d = new Date(); `

## 数组的常见API

![截屏2020-09-0318.23.27](https://tva1.sinaimg.cn/large/007S8ZIlly1gidmwpfwpsj30rj0r7grj.jpg)

## JavaScript数据类型（3）

- **基本数据类型（值类型）**：String 字符串、Number 数值、Boolean 布尔值、Null 空值、Undefined 未定义、Symbol (ES6)。
- **引用数据类型（引用类型）**：Object 对象。

!> 内置对象 Function、Array、Date、RegExp、Error等都是属于 Object 类型。也就是说，除了那五种基本数据类型之外，其他的，都称之为 Object类型。

## 栈内存和堆内存

* 栈内存：所有的变量（基本数据类型的值）。
* 堆内存：对象（引用数据类型）。

## 基本和引用数据类型的区别（2）

- 基本数据类型：
  - 参数赋值的时候，**传数值**。
  - 基本数据类型的值，直接保存在**栈内存**中。值与值之间是独立存在，修改一个变量不会影响其他的变量
- 引用数据类型：
  - 参数赋值的时候，**传地址**（修改的同一片内存空间）。
  - 对象是保存到**堆内存**中的。每创建一个新的对象，就会在堆内存中开辟出一个新的空间；而**变量保存了对象的内存地址**（对象的引用），保存在栈内存当中。**如果两个变量保存了同一个对象的引用，当一个通过一个变量修改属性时，另一个也会受到影响。**

> 基本类型值在内存中占据固定大小的空间，因此被保存在栈内存中；从一个变量向另一个变量复制基本类型的值，会创建这个值的一个副本；
>
> 引用类型的值是对象，保存在堆内存中；包含引用类型值的变量实际上包含的并不是对象本身，而是一个指向该对象的指针；从一个变量向另一个变量复制引用类型的值，复制的其实是指针，因此两个变量最终都指向同一个对象；
>

#### 示例

**基本数据类型举例**：（传值）

```javascript
var a = 23;
var b = a;
a++;
console.log(a); // 打印结果：24
console.log(b); // 打印结果：23
```

上面的代码中：a 和 b 都是基本数据类型，让 b 等于 a，然后**改变 a 的值之后，发现 b 的值并没有被改变**。

**引用数据类型举例**：（传址）

```javascript
var obj1 = new Object();
obj1.name = 'cyh';
// 将 obj1 的地址赋值给 obj2。从此， obj1 和 obj2 指向了同一个堆内存空间
var obj2 = zl;
// 修改 obj1 的 name 属性
obj1.name = 'vae';
console.log(obj1.name); // 打印结果：zl
console.log(obj2.name); // 打印结果：zl
```

上面的代码中：obj1 和 obj2 都是引用数据类型，让 obj2 等于 obj1，然后**修改 obj1.name 的值之后，发现 obj2.name 的值也发生了改变**。

对于引用类型的数据，赋值相当于地址拷贝，a、b指向了同一个堆内存地址。所以改了b，a也会变；本质上a、b就是一个东西。

如果你打算把引用类型 A 的值赋值给 B，让A和B相互不受影响的话，**可以通过 Object.assign() (浅拷贝)来复制对象**。效果如下：

```js
var obj1 = {name: 'cyh'};
// 复制对象：把 obj1 赋值给 obj3。两者之间互不影响
var obj3 = Object.assign({}, obj1);
```

## JavaScript数据类型判断

> 确定一个值是哪种基本类型可以使用` typeof`操作符，而确定一个值是哪种引用类型可以使用`instanceof `操作符。

### 1、typeof

| typeof 的代码写法            |   返回结果    |
| ---------------------------- | :-----------: |
| typeof 数字 2                |  `"number"`   |
| typeof 字符串 ”“             |  `"string"`   |
| typeof 布尔型 true           |  `"boolean"`  |
| typeof 对象 {}               |  `"object"`   |
| **typeof 方法** function(){} | `"function"`  |
| **typeof null**              |  `"object"`   |
| typeof undefined             | `"undefined"` |
| **typeof NaN**               |  `"number"`   |

!> ⚠️ 1：为什么 `typeof null`的返回值也是 `"object"`呢？因为 null 代表的是**空对象**。<br>⚠️ 2：`typeof NaN`的返回值是 `"number"`。**NaN**是一个特殊的数字，表示Not a Number，非数值。NaN 是一个特殊的数字。Undefined和任何数值计算的结果为 NaN。NaN 与任何值都不相等，包括 NaN 本身。<br>⚠️3： `typeof []`、 `typeof {}` ，返回值也是 `object` 。因为这里的返回结果`object`指的是**引用数据类型**。空数组、空对象都是**引用数据类型 Object**。

因此，**在使用`typeof`运算符时采用引用类型存储值会出现无论引用的是什么类型的对象，都会返回`"object"`，**要想区别内置对象如Array、Date等，单纯使用 typeof 是不行的，**JavaScript通过`Object.prototype.toString`方法，可以检测对象类型。**

### 2、Object.prototype.toString.call()

每个对象都有一个 `toString()` 方法，当该对象被表示为一个文本值时，或者一个对象以预期的字符串方式引用时自动调用。默认情况下，`toString()` 方法被每个 `Object` 对象继承。如果此方法在自定义对象中未被覆盖，`toString()` 返回 "[object *type*]"，其中 `type` 是对象的类型。

可以通过 `toString()` 来获取每个对象的类型。为了每个对象都能通过 `Object.prototype.toString()` 来检测，需要以 `Function.prototype.call()` 或者 `Function.prototype.apply()` 的形式来调用，传递要检查的对象作为第一个参数，称为 `thisArg`。

##### 原型链的概念

我们都知道js中的对象都继承自`Object`，所以当我们在某个对象上调用一个方法时，会先在该对象上进行查找，如果没找到则会进入对象的原型（也就是`.prototype`）进行查找，如果没找到，同样的也会进入对象原型的原型进行查找，直到找到或者进入原型链的顶端`Object.prototype`才会停止。

所以，当我们使用`arr.toString()`时，不能进行复杂数据类型的判断，因为它调用的是`Array.prototype.toString`，虽然`Array`也继承自`Object`，但js在`Array.prototype`上重写了`toString`，而我们通过`toString.call(arr)`实际上是通过原型链调用了`Object.prototype.toString`。

判断对象的类型示例：

```js
console.log(Object.prototype.toString.call("tom"));//[object String]
console.log(Object.prototype.toString.call(17));//[object Number]
console.log(Object.prototype.toString.call(true));//[object Boolean]
console.log(Object.prototype.toString.call(undefined));//[object Undefined]
console.log(Object.prototype.toString.call(null));//[object Null]
console.log(Object.prototype.toString.call({name: "tom"}));//[object Object]
console.log(Object.prototype.toString.call(function(){}));//[object Function]
console.log(Object.prototype.toString.call([]));//[object Array]
console.log(Object.prototype.toString.call(new Date));//[object Date]
console.log(Object.prototype.toString.call(/\d/));//[object RegExp]
function Person(){};
console.log(Object.prototype.toString.call(new Person));//[object Object]
// 判断原生JSON对象
var isNativeJSON = window.JSON && Object.prototype.toString.call(JSON);
console.log(isNativeJSON);//输出结果为：[object JSON] ，说明JSON是原生的，否则不是；
```

对于自定义类型，``Object.prototype.toString``方法不能准确判断一个实例是否属于某种类型，因此引入**`instanceof`** **运算符**进行判断。

```js
//自定义类型
  function Person(name, age) {
      this.name = name;
      this.age = age;
  }
  var person = new Person("Rose", 18);
  Object.prototype.toString.call(person); // 打印 "[object Object]"
	console.log(person instanceof Person); // 打印 true
```

> 参考引用：
>
> [Object.prototype.toString.call(obj)精确判断对象的类型](https://blog.csdn.net/qq_39408204/article/details/91492061)
>
> [MDN | Object.prototype.toString()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/toString)

### 3、instanceof

**`instanceof`** **运算符**用于检测构造函数的 `prototype` 属性是否出现在某个实例对象的原型链。

*原理*：判断实例对象的**proto**属性与构造函数的 prototype 是不是用一个引用。如果不是，他会沿着对象的**proto**向上查找的，直到顶端 Object。

通常来讲，使用 instanceof 就是判断一个实例是否属于某种类型。例如：

```js
// 判断 foo 是否是 Foo 类的实例
 function Foo(){};
 var foo = new Foo();
 console.log(foo instanceof Foo) // 输出 true
```

更重要的一点是 **instanceof 可以在继承关系中用来判断一个实例是否属于它的父类型**。例如：

```js
// 判断 foo 是否是 Foo 类的实例 , 并且是否是其父类型的实例
function Aoo(){}
 function Foo(){}
 Foo.prototype = new Aoo(); // JavaScript 原型继承
 var foo = new Foo();
 console.log(foo instanceof Foo) // 输出 true
 console.log(foo instanceof Aoo) // 输出 true
```

在多层继承关系中，instanceof 运算符同样适用。

##### instanceof 复杂用法（JavaScript 原型继承机制）

instanceof检测的是原型，用一段代码模拟一下内部执行过程：

```JS
instanceof (A,B) = {
    var L = A.__proto__;
    var R = B.prototype;
    if(L === R) {
        //A的内部属性__proto__指向B的原型对象
        return true;
    }
    return false;
}
```

以下为几个示例：

```js
console.log([] instanceof Array); // true
console.log([] instanceof Object) // true
console.log(Object instanceof Object);//true
 console.log(Function instanceof Function);//true
 console.log(Function instanceof Object);//true
 console.log(Number instanceof Number);//false
 console.log(String instanceof String);//false
 console.log(Foo instanceof Function);//true
```

从上边的示例可以发现，虽然 instanceof 能够判断出 [ ] 是Array的实例，但它认为 [ ] 也是Object的实例，从 instanceof 能够判断出 `[].__proto__ `指向 `Array.prototype`，而 `Array.prototype.__proto__` 又指向了`Object.prototype`，最终 `Object.prototype.__proto__` 指向了null，标志着原型链的结束。因此，[]、Array、Object 就在内部形成了一条原型链。

##### JavaScript 原型链

![JavaScript 原型链](https://tva1.sinaimg.cn/large/007S8ZIlly1ghky0p5tmuj30fu0jpaem.jpg)

因此，==instanceof 只能用来判断两个对象是否属于实例关系， 而不能判断一个对象实例具体属于哪种类型。==

在浏览器中，我们的脚本可能需要在多个窗口之间进行交互。多个窗口意味着多个全局环境，不同的全局环境拥有不同的全局对象，从而拥有不同的内置类型构造函数。这可能会引发一些问题。比如，表达式 `[] instanceof window.frames[0].Array` 会返回 `false`，因为 `Array.prototype !== window.frames[0].Array.prototype`，并且数组从前者继承。

当你在你的脚本中开始处理多个 frame 或多个 window 以及通过函数将对象从一个窗口传到另一个窗口时，比如，你可以通过使用`Array.isArray(myObj)` 或者`Object.prototype.toString.call(myObj) === "[object Array]"` 来安全的检测传过来的对象是否是一个数组。

支持 Array.isArray()方法的浏览器有 IE9+、 Firefox 4+、 Safari 5+、 Opera 10.5+和 Chrome。

当检测Array实例时, `Array.isArray` 优于 `instanceof`,因为Array.isArray能检测`iframes`。

> 参考引用：
>
> [JavaScript instanceof 运算符深入剖析](https://developer.ibm.com/zh/articles/1306-jiangjj-jsinstanceof/)
>
> [instanceof详解](https://www.jianshu.com/p/3956a2204644)
>
> [MDN | instanceof、**Array.isArray()** ](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof)

### 4、constructor

constructor 属性返回对创建此对象的数组函数的引用。

##### 语法：

```js
object.constructor
```

示例：

```js
function Person(){
  this.name = name;
}
var p = new Person('Joe');
console.log(p.constructor === Person); //true
console.log(p.__proto__ === Person.prototype); //true
```

> https://www.cnblogs.com/ihardcoder/p/3667739.html  有待验证

## 判断数组的方法

1、`instanceof`

```js
	console.log([] instanceof Array); //  true
```

2、`Array.isArray()`

```js
	console.log(Array.isArray([])); // true	
```

3、`Object.prototype.toString()`

```js
	function isArray(arr) {
    return Object.prototype.toString.call(arr) === '[object Array]';
  }
	console.log(isArray([])); // true
```

4、`constructor`

```js
	function isArray(arr) {
    if (!(arr instanceof Object)) {
      return false;
    }
    return arr.constructor === Array;
  }
	console.log([]); // true
```

5、正则表达式

```js
	function isArray(arr) {
    try{
      return /^\[.*\]$/.test(JSON.stringify(arr));
    }catch(err) {
      return false;
    }
  }
	console.log(isArray([])); // true
```

## valueOf()与toString()方法及其作用

各种引用对象都继承或最终继承于Object，使用着Object的原型，所以它们不管何时都有 toString() 和 valueOf() 方法，valueOf和toString是Object.prototype的方法。一般很少直接调用，但是在使用对象参与运算的时候就会调用这两个方法了。只不过有些类型的原型重写了这两个方法，比如 Function 实例的原型就重写了 toString() 方法，按照原型链的规则，如果方法和属性在原型链的各原型中有重名，则优先使用最近的方法和属性。

- valueOf: 返回对象的原始值表示
- toString: 返回对象的字符串表示

###### 常见的引用类型：

- Function 重写了 toString()
- Date 重写了 toString() 也重写了 valueOf()
- Array 重写了 toString()

> https://blog.csdn.net/weixin_42476799/article/details/89330017

## == 和 ===的区别

===要求数据类型相同， ==会进行隐式类型转换

##  作用域和作用域链、执行期上下文

#### 作用域（Scope）

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gi5rpetqidj315c0smn2s.jpg)

**词法作用域**，也叫**静态作用域**，它的作用域是指在词法分析阶段就确定了，不会改变。**动态作用域**是在运行时根据程序的流程信息来动态确定的，而不是在写代码时进行静态确定的。**词法作用域关注函数在何处声明，而动态作用域关注函数从何处调用**。

##### 概念

- **概念**：通俗来讲，作用域是一个变量或函数的作用范围。作用域在**函数定义**时，就已经确定了。

- **目的**：为了提高程序的可靠性，同时减少命名冲突。

##### 作用域的分类

在 JS 中，一共有两种作用域：（ES6 之前）

###### 全局作用域

作用于整个 script 标签内部，或者作用于一个独立的 JS 文件。

- 全局作用域在页面打开时创建，在页面关闭时销毁。

- 在全局作用域中有一个全局对象window，它代表的是一个浏览器的窗口，由浏览器创建，我们可以直接使用。

在全局作用域中：

- 创建的**变量**都会作为window对象的属性保存。比如在全局作用域内写 `var a = 100`，这里的 `a` 等价于 `window.a`。

- 创建的**函数**都会作为`window`对象的方法保存。

###### **==变量的声明提前==（变量提升）**

**使用var关键字声明的变量**（ 比如 `var a = 1`），**会在所有的代码执行之前被声明，**但是不会赋值；**如果声明变量时不是用var关键字**（比如直接写`a = 1`），**则变量不会被声明提前**。

示例代码：

```js
console.log(a); // undefined  说明变量a被提前声明了，只是尚未被赋值
var a = 'a';
--------------------------------------------------------
console.log(a); // 报错：Uncaught ReferenceError: a is not defined
a = 'a';
--------------------------------------------------------
foo(); // undefined  变量i在函数执行前，就被提前声明了，只是尚未被赋值。
function foo() {
    if (false) {
        var i = 123;
    }
    console.log(i);
}
```

==函数的声明提前==

**函数声明**：

使用`函数声明`的形式创建的函数`function foo(){}`，**会被声明提前**。也就是说，整个函数会在所有的代码执行之前就被**创建完成**。所以，在代码顺序里，我们可以先调用函数，再定义函数。

示例代码：

```js
foo(); // 输出：hello
function foo() {
	console.log('hello');
}
```

**函数表达式**：

使用`函数表达式`创建的函数`var foo = function(){}`，**不会被声明提前**，所以不能在声明前调用。因为此时foo被声明了（这里只是变量声明），且为undefined，并没有把 `function(){}` 赋值给 foo。

示例代码：

```js
foo(); // 报错：Uncaught TypeError: foo is not a function
var foo = function() {
	console.log('hello');
}
```

###### 函数作用域（局部作用域）：

作用于函数内的代码环境。

- 函数中，使用var关键字声明的变量，会在函数中所有的代码执行之前被声明。

- 函数中，没有var声明的变量都是**全局变量**，而且并不会提前声明。

```js
var a = 1;
function fn() {
  console.log(a);
  a = 2;
}
fn();  // 打印：1
console.log(a);  // 打印：2
console.log(window.a);  // 打印：2
--------------------------------------------------------
function fn() {
  console.log(a);
  a = 2;
}
console.log(a);
fn();  // 报错：Uncaught ReferenceError: a is not defined   说明不会被提前声明
```

* 定义形参就相当于在函数作用域中声明了变量。

```js
function fn(e) { 
  console.log(e);
}
fn();  //打印：undefined
fn(10);  //打印：10
```

##### 作用域的访问关系

在内部作用域中可以访问到外部作用域的变量，在外部作用域中无法访问到内部作用域的变量。

代码举例：

```javascript
var a = 'a';
function foo() {
    var b = 'b';
    console.log(a); // 打印：a  内层作用域可以访问外层作用域里的变量
}

foo();
console.log(b); // 报错：Uncaught ReferenceError: b is not defined   外层作用域无法访问内层作用域里的变量
```

##### 变量的作用域

根据作用域的不同，变量可以分为两类：全局变量、局部变量。

**全局变量**：

- 在全局作用域下声明的变量，叫全局变量。在全局作用域的任一地方，都可以访问这个变量。

- 在全局作用域下，使用 var 声明的变量是全局变量。

- *特殊情况：在函数内不使用 var 声明的变量也是全局变量*（不建议这么用）。

**局部变量**：

- 定义在函数作用域的变量，叫局部变量。

- 在函数内部，使用 var 声明的变量是局部变量。

- 函数的**形参**也是属于局部变量。

从执行效率来看全局变量和局部变量：

- 全局变量：只有浏览器关闭时才会被销毁，比较占内存。

- 局部变量：当其所在的代码块运行结束后，就会被销毁，比较节约内存空间。

##### 作用域的上下级关系

当在函数作用域操作一个变量时，它会先在自身作用域中寻找，如果有就直接使用（**就近原则**）。如果没有则向上一级作用域中寻找，直到找到全局作用域；如果全局作用域中依然没有找到，则会报错 ReferenceError。

在函数中要访问全局变量可以使用window对象。（比如说，全局作用域和函数作用域都定义了变量a，如果想访问全局变量，可以使用`window.a`）

#### 作用域链

- 只要是代码，就至少有一个作用域

- 写在函数内部的局部作用域

- 如果函数中还有函数，那么在这个作用域中就又可以诞生一个作用域

**作用域链**：内部函数访问外部函数的变量，采用的是链式查找的方式来决定取哪个值，这种结构称之为作用域链。查找时，采用的是**就近原则**。

示例代码：

```js
var a = 10;
function fn() {
  var a = 20;
  function foo() {
    console.log(a);
  }
  foo();
}
fn(); // 打印：20
```

## JavaScript的内置对象

| 内置对象  | 对象说明       |
| :-------- | :------------- |
| Arguments | 函数参数集合   |
| Array     | 数组           |
| Boolean   | 布尔对象       |
| Math      | 数学对象       |
| Date      | 日期时间       |
| Error     | 异常对象       |
| Function  | 函数构造器     |
| Number    | 数值对象       |
| Object    | 基础对象       |
| RegExp    | 正则表达式对象 |
| String    | 字符串对象     |

## 内置对象 Math 的常见方法

Math属于一个工具类，里面封装了数学运算相关的属性和方法。如下：

| 方法              | 描述                                       | 备注              |
| :---------------- | :----------------------------------------- | :---------------- |
| Math.PI           | 圆周率                                     | Math对象的属性    |
| Math.abs()        | **返回绝对值**                             |                   |
| Math.random()     | 生成0-1之间的**随机浮点数**                | 取值范围是 [0，1) |
| Math.floor()      | **向下取整**（往小取值）                   |                   |
| Math.ceil()       | **向上取整**（往大取值）                   |                   |
| Math.round()      | 四舍五入取整（正数四舍五入，负数五舍六入） |                   |
| Math.max(x, y, z) | 返回多个数中的最大值                       |                   |
| Math.min(x, y, z) | 返回多个数中的最小值                       |                   |
| Math.pow(x,y)     | 乘方：返回 x 的 y 次幂                     |                   |
| Math.sqrt()       | 开方：对一个数进行开方运算                 |                   |

## 日期Date

Date对象 有如下方法，可以获取日期和时间的**指定部分**：

| 方法名            | 含义              | 备注                 |
| ----------------- | ----------------- | -------------------- |
| getFullYear()     | 获取年份          |                      |
| getMonth()        | **获取月： 0-11** | 0代表一月            |
| getDate()         | **获取日：1-31**  | 获取的是几号         |
| getDay()          | **获取星期：0-6** | 0代表周日，1代表周一 |
| getHours()        | 获取小时：0-23    |                      |
| getMinutes()      | 获取分钟：0-59    |                      |
| getSeconds()      | 获取秒：0-59      |                      |
| getMilliseconds() | 获取毫秒          | 1s = 1000ms          |

## undefined与null的区别

在Java中也有null,而JS中有undefined`和`null两个代表无，如果将一个变量赋值为undefined或null，几乎没区别。

在if语句中，都会被转为`false`，如果是判断`undefined == null` 的话，输出为`true`

#### 区别：

**null表示"没有对象"，即该处不应该有值。**典型用法是：

> （1） 作为函数的参数，表示该函数的参数不是对象。
>
> （2） 作为对象原型链的终点。
>
> ```js
> Object.getPrototypeOf(Object.prototype) // null
> ```

**undefined表示"缺少值"，就是此处应该有一个值，但是还没有定义。**典型用法是：

> （1）变量被声明了，但没有赋值时，就等于undefined。
>
> （2) 调用函数时，应该提供的参数没有提供，该参数等于undefined。
>
> （3）对象没有赋值的属性，该属性的值为undefined。
>
> （4）函数没有返回值时，默认返回undefined。

```js
var i;
i // undefined
function f(x){console.log(x)}
f() // undefined
var o = new Object();
o.p // undefined
var x = f();
x // undefined
```

### js区分null和undefined

`typeof`区分

```js
typeof null==	"object"
typeof undefined ==	"undefined"
```


## call、apply、bind的区别

> - 都能改变this的指向
> - call()/apply()是**立即调用函数**
> - bind()是将函数返回，因此后面还需要加`()`才能调用。
> - `call()和apply()`可以实现继承

每个函数都包含两个非继承而来的方法：`apply()`和`call()`。函数还包括一个`bind()`方法，该方法会创建一个函数的实例，其`this`值会被绑定到传给`bind()`函数的值。

##### 共同点：

* 都可以改变`this`的指向。

`apply()`和`call()`在特定的作用域中调用函数，相当于设置函数体内this对象的值，改变this的指向，扩充函数赖以运行的作用域（对象不需要与方法有任何的耦合关系）。都可以实现继承。

bind() 方法**不会调用函数**，但是可以改变函数内部的 this 指向。

##### 区别：

###### appy()和call()接受参数的方式不同：

`apply()`方法接受两个参数，一个是在其中运行函数的作用域（即this的指向），一个是参数数组（可以是`Array`实例，也可以是`arguments`对象）。

`call()`方法第一个参数是`this`值没有变化，变化的是其余参数都直接传递给函数。`call()`还可以实现继承。

> ###### 第一个参数的传递（传啥指向啥，不传指向window）
>
> 1、thisObj不传或者为null、undefined时，函数中的this会指向window对象（非严格模式）。
>
> 2、传递一个别的函数名时，函数中的this将指向这个**函数的引用**。
>
> 3、传递的值为数字、布尔值、字符串时，this会指向这些基本类型的包装对象Number、Boolean、String。
>
> 4、传递一个对象时，函数中的this则指向传递的这个对象

* `bind()`方法不会调用函数，是将函数返回，因此后面还需要加`()`才能调用。

* `call()/apply()`是**立即调用函数**

```js
function sum(num1, num2) {
  return num1 + num2;
}
// apply 实现
function callSum1(num1, num2) {
  return sum.apply(this, [num1, num2]);
}
function callSum2(num1, num2) {
  return sum.apply(this, arguments);
}
// call 实现
function callSum2(num1, num2) {
  return sum.call(this, num1, num2);
}
console.log(callSum1(10,20)); // 30
console.log(callSum2(10,20)); // 30
console.log(callSum3(10,20)); // 30
```

##### `call()`实现继承

```js
function Parent(name, age) {
  this.name = name;
  this.age = age;
}
function Child(myName, myAge){
  Parent.call(this, myName, myAge);
}
const c = new Child("ramona", 24);
console.log(JSON.stringify(c)); // {"myName":"ramona","myAge":24}
```

##### `apply()`实现求数组中多个元素的最大最小值

```js
const arr = [3, 5, 2, 7, 9];
const maxValue = Math.max.apply(Math, arr);
console.log(maxValue); // 9
const minValue = Math.min.apply(Math, arr);
console.log(minValue);
```

## EventLoop事件循环

> 1. 事件循环，可以理解为实现异步的一种方式。它是通过[任务队列](https://www.w3.org/TR/html5/webappapis.html#task-queues)的机制来进行协调的。主线程从"任务队列"中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为Event Loop（事件循环）。一个 Event Loop 中，可以有一个或者多个任务队列，**每个任务都有一个任务源(task source)，来自同一个任务源的 task 必须放到同一个任务队列，从不同源来的则被添加到不同队列。**
>
>    所有任务可以分成两种，一种是**同步任务，另一种是异步任务**。同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务。异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。
>
>    2、任务可以分为宏任务和微任务，
>
>    **宏任务是每次执行栈执行的代码就是一个宏任务**。宏任务源通常分为四种：DOM操作任务源、用户交互任务源、网络任务源、history traversal任务源，基本上我们绑定的各种事件都是task任务源。宏任务主要包含：**script(整体代码)、setTimeout、setInterval、I/O、UI 交互事件**、postMessage、MessageChannel、setImmediate(Node.js 环境)
>
>    microtask（又称为微任务），**可以理解是在当前 task 执行结束后立即执行的任务**，**每一个event loop都只有一个micro task队列**。
>
>    microtask 主要包含：**Promise.then**、MutaionObserver、process.nextTick(Node.js 环境)
>
>    ###### 运行机制步骤：
>
>    - **执行一个宏任务（栈中没有就从事件队列中获取）**
>    - **执行过程中如果遇到微任务，就将它添加到微任务的任务队列中**
>    - **宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）**
>    - **当前宏任务执行完毕，开始检查渲染，然后 GUI 线程接管渲染**

### async和await

#### async

异步的简写，用来声明一个function是异步的，**async函数返回是一个promise对象**，如果async函数没有返回值，则它会返回promise.resolve(undefined)

async函数不会造成阻塞，它内部所有的阻塞都被封装在一个promise对象中异步执行。

优点：相比于Promise，它能够写出更加清晰的调用链，并且也能优雅的解决回调地狱的问题。

#### await

await是等待的意思，用于等待一个异步方法执行完成。**await只能出现在async函数**，await等待的是一个表达式，实际等待的是一个返回值，等待这个promise完成，并将resolve的结果返回。

> 如果它等到的不是一个 Promise 对象，那 await 表达式的运算结果就是它等到的东西。
> 如果它等到的是一个 Promise 对象，await 就忙起来了，它会阻塞后面的代码，等着 Promise 对象 resolve，然后得到 resolve 的值，作为 await 表达式的运算结果。

缺点：因为await将异步代码变成了同步代码,如果多个异步之间没有关系,会导致性能降低。

原理：await 就是 generator 加上 Promise 的语法糖，且内部实现了自动执行 generator。

### 任务队列

所有任务可以分成两种，一种是同步任务（synchronous），另一种是异步任务（asynchronous）。同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务。异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。


总结：**只要主线程空了，就会去读取"任务队列"，这就是JavaScript的运行机制**。【重要】

### Event Loop（事件循环）

事件循环，可以理解为实现异步的一种方式。

栈、堆、队列的存在和调用方式：

- 栈（Stack）中存储的是同步任务，同步任务是指在主线程上排队执行的任务，如变量和函数的初始化、事件的绑定等可以立即执行、不耗时的任务；
- 堆（Heap）用来存储对象、函数等；
- 队列（Queue），即任务队列，用来存储异步任务

主线程从"任务队列"中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为Event Loop（事件循环）。

事件循环是通过[任务队列](https://www.w3.org/TR/html5/webappapis.html#task-queues)的机制来进行协调的。一个 Event Loop 中，可以有一个或者多个任务队列(task queue)，一个任务队列便是一系列有序任务(task)的集合；**每个任务都有一个任务源(task source)，源自同一个任务源的 task 必须放到同一个任务队列，从不同源来的则被添加到不同队列。** setTimeout/Promise 等 API 便是任务源，而进入任务队列的是他们指定的具体执行任务。

![](http://img.smyhvae.com/20180310_1840.png)


在理解Event Loop时，要理解两句话：

- 理解哪些语句会放入异步任务队列

- 理解语句放入异步任务队列的**时机**

#### 宏任务

(macro)task（又称之为宏任务），可以理解是每次执行栈执行的代码就是一个宏任务（包括每次从事件队列中获取一个事件回调并放到执行栈中执行）。

浏览器为了能够使得 JS 内部(macro)task 与 DOM 任务能够有序的执行，**会在一个(macro)task 执行结束后，在下一个(macro)task 执行开始前，对页面进行重新渲染**，流程如下：

```js
(macro)task->渲染->(macro)task->...
```

task任务源通常分为四种：DOM操作任务源、用户交互任务源、网络任务源、history traversal任务源。task任务源非常宽泛，比如ajax的onload、click事件，基本上我们绑定的各种事件都是task任务源，另外还有setTimeout、setInterval、setImmediate也是task任务源。

(macro)task 主要包含：**script(整体代码)、setTimeout、setInterval、I/O、UI 交互事件**、postMessage、MessageChannel、setImmediate(Node.js 环境)

#### 微任务

microtask（又称为微任务），**可以理解是在当前 task 执行结束后立即执行的任务**。也就是说，在当前 task 任务后，下一个 task 之前，在渲染之前。**每一个event loop都只有一个micro task队列**，

所以它的响应速度相比 setTimeout（setTimeout 是 task）会更快，因为无需等渲染。也就是说，在某一个 macrotask 执行完后，就会将在它执行期间产生的所有 microtask 都执行完毕（在渲染前）。

microtask 主要包含：**Promise.then**、MutaionObserver、process.nextTick(Node.js 环境)

#### 运行机制

在事件循环中，每进行一次循环操作称为 tick，每一次 tick 的任务[处理模型](https://www.w3.org/TR/html5/webappapis.html#event-loops-processing-model)是比较复杂的，但关键步骤如下：

- **执行一个宏任务（栈中没有就从事件队列中获取）**
- **执行过程中如果遇到微任务，就将它添加到微任务的任务队列中**
- **宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）**
- **当前宏任务执行完毕，开始检查渲染，然后 GUI 线程接管渲染**

#### Promise 和 async 中的立即执行

我们知道 Promise 中的异步体现在`then`和`catch`中，所以写在 Promise 中的代码是被当做同步任务立即执行的。而在 async/await 中，在出现 await 出现之前，其中的代码也是立即执行的。那么出现了 await 时候发生了什么呢？

##### await 做了什么

从字面意思上看 await 就是等待，await 等待的是一个表达式，这个表达式的返回值可以是一个 promise 对象也可以是其他值。

很多人以为 await 会一直等待之后的表达式执行完之后才会继续执行后面的代码，**实际上 await 是一个让出线程的标志。await 后面的表达式会先执行一遍，将 await 后面的代码加入到 microtask 中，然后就会跳出整个 async 函数来执行后面的代码。**



### 容易答错的题目

```javascript
    for (var i = 0; i < 3; i++) {
        setTimeout(function () {
            console.log(i);
        }, 1000);
    }
```

很多人以为上面的题目，答案是`0,1,2,3`。其实，正确的答案是：`3,3,3,3`。

分析：for 循环是同步任务，setTimeout是异步任务。for循环每次遍历的时候，遇到settimeout，就先暂留着，等同步任务全部执行完毕（此时，i已经等于3了），再执行异步任务。


我们把上面的题目再加一行代码。最终代码如下：

```javascript
    for (var i = 0; i < 3; i++) {
        setTimeout(function () {
            console.log(i);
        }, 1000);
    }
    console.log(i);
```

如果我们约定，用箭头表示其前后的两次输出之间有 1 秒的时间间隔，而逗号表示其前后的两次输出之间的时间间隔可以忽略，代码实际运行的结果该如何描述？可能会有两种答案：

- A. 60% 的人会描述为：`3 -> 3 -> 3 -> 3`，即每个 3 之间都有 1 秒的时间间隔；

- B. 40% 的人会描述为：`3 -> 3,3,3`，即第 1 个 3 直接输出，1 秒之后，连续输出 3 个 3。

循环执行过程中，几乎同时设置了 3 个定时器，这些定时器都会在 1 秒之后触发，而循环完的输出是立即执行的，显而易见，正确的描述是 B。

上面这个题目的参考链接：

- [80% 应聘者都不及格的 JS 面试题](https://juejin.im/post/58cf180b0ce4630057d6727c)

- [深入浅出Javascript事件循环机制(上)](https://zhuanlan.zhihu.com/p/26229293)

## JavaScript作用域

### JS 变量的作用域，作用域与作用域链（3）

所有变量（包括基本类型和引用类型）都存在于一个执行环境（也称为作用域）当中，这个执行环境决定了变量的生命周期，以及哪一部分代码可以访问其中的变量。以下是关于执行环境的几点总结：

执行环境有全局执行环境（也称为全局环境）和函数执行环境之分；

每次进入一个新执行环境，都会创建一个用于搜索变量和函数的作用域链；

函数的局部环境不仅有权访问函数作用域中的变量，而且有权访问其包含（父）环境，乃至全局环境；

全局环境只能访问在全局环境中定义的变量和函数，而不能直接访问局部环境中的任何数据；

变量的执行环境有助于确定应该何时释放内存。

### 编译原理

程序中的一段源代码在执行之前会经历三个步骤，统称为“编译”。 

* 分词/词性分析

  此过程将由字符组成的字符串分解成有意义的代码块，这些代码块被称为词法单元。例如：`var a = 2;`会被分解为`var`、`a`、`=`、`2`、`;`这些词法单元。

* 解析/语法分析（Parsing） 

  这个过程是将词法单元流（数组）转换成一个由元素逐级嵌套所组成的代表了程序语法结构的树。这个树被称为“抽象语法树”（Abstract Syntax Tree，AST）。

* 代码生成 

  将 AST 转换为可执行代码的过程称被称为代码生成。这个过程与语言、目标平台等息息相关。

> 任何 JavaScript 代码片段在执行前都要进行编译（通常就在执行前）。大部分情况下编译发生在代码执行前的几微秒（甚至更短！）的时间内。

### 理解作用域

变量的赋值操作会执行两个动作，首先编译器会在当前作用域中声明一个变量（如果之前没有声明过），然后在运行时引擎会在作用域中查找该变量，如果能够找到就会对它赋值。 

作用域是根据名称查找变量的一套规则。实际情况中，通常需要同时顾及几个作用域。 

##### 作用域嵌套

当一个块或函数嵌套在另一个块或函数中时，就发生了作用域的嵌套。因此，在当前作用域中无法找到某个变量时，引擎就会在外层嵌套的作用域中继续查找，直到找到该变量， 或抵达最外层的作用域（也就是全局作用域）为止。 

遍历嵌套作用域链的规则很简单：引擎从当前的执行作用域开始查找变量，如果找不到， 就向上一级继续查找。当抵达最外层的全局作用域时，无论找到还是没找到，查找过程都会停止。 

## 块级作用域 

ES5只有全局作用域和函数作用域，没有块级作用域。ES6新增了块级作用域。

#### 存在的原因

* 内层变量可能会覆盖外层变量

```js
var tmp = new Date(); 
function f() { 
    console.log(tmp); 
    if (false) { 
        var tmp = "hello world"; 
    } 
}
f(); // undefined,原因在于变量提升，导致内层的tmp变量覆盖了外层的tmp变量
```

* 用来计数的循环变量泄露为全局变量

```js
var s = "hello";
for (var i = 0; i < s.length; i++) {
    console.log(s[i]);
}
console.log(i); // 输出：5,变量i只用来控制循环，但是循环结束后，它并没有消失，泄露成了全局变量
```

#### 块级作用域作用

* **外层作用域无法读取内层作用域的变量**。 

  ```js
  {{{{{
      let insane = 'Hello World'} 
      console.log(insane); // 报错 
     }}}};
  ```

* **内层作用域可以定义外层作用域的同名变量。**

  ```js
  {{{{
      let insane = 'Hello World'; 
      {let insane = 'Hello World'} 
  }}}};
  ```

* 块级作用域的出现，实际上使得获得广泛应用的**立即执行匿名函数（IIFE）不再必要了**。

  ```js
  // IIFE写法 
  (function () { 
      var tmp = ...; 
      ... 
  }()); 
  // 块级作用域写法 
      { 
          let tmp = ...; 
          ... 
      }
  ```

#### 块级作用域与函数声明 

**ES5规定，函数只能在顶层作用域和函数作用域之中声明，不能在块级作用域声明**。但是，浏览器没有遵守这个规定，还是支持在块级作用域之中声明函数，不过，“严格模式”下还是会报错。

**ES6引入了块级作用域，明确允许在块级作用域之中声明函数**。 并且ES6规定，块级作用域之中，**函数声明语句的行为类似于 let ，在块级作用域之外不可引用**。 但应该避免在块级作用域内声明函数。如果确实需要，也应该写成函数表达式，而不是函数声明语句。

ES6的块级作用域允许声明函数的规则，只在使用大括号的情况下成立，如果没有使用大括号，就会报错。 

```js
function f() { console.log('I am outside!'); } 
(function () { 
    if (false) { 
        // 重复声明一次函数f
        function f() { console.log('I am inside!'); } 
    }
    f(); 
}());
```

## JavaScript事件



## JavaScript垃圾收集

#### Javascript 垃圾回收方法

##### 标记清除（mark and sweep）

- 这是 JavaScript 最常见的垃圾回收方式，当变量进入执行环境的时候，比如函数中声明一个变量，垃圾回收器将其标记为“进入环境”，当变量离开环境的时候（函数执行结束）将其标记为“离开环境”
- **垃圾回收器会在运行的时候给存储在内存中的所有变量加上标记，然后去掉环境中的变量以及被环境中变量所引用的变量（闭包）**，在这些完成之后仍存在标记的就是要删除的变量了

##### 引用计数(reference counting)

- 在低版本 IE 中经常会出现内存泄露，很多时候就是因为其采用引用计数方式进行垃圾回收。**引用计数的策略是跟踪记录每个值被使用的次数，当声明了一个变量并将一个引用类型赋值给该变量的时候这个值的引用次数就加 1，如果该变量的值变成了另外一个，则这个值得引用次数减 1**，当这个值的引用次数变为 0 的时 候，说明没有变量在使用，这个值没法被访问了，因此可以将其占用的空间回收，这样垃圾回收器会在运行的时候清理掉引用次数为 0 的值占用的空间。

#### 如何判断回收内容

如何确定哪些内存需要回收，哪些内存不需要回收，这是垃圾回收期需要解决的最基本问题。我们可以这样假定，**一个对象为活对象当且仅当它被一个根对象 或另一个活对象指向**。根对象永远是活对象，它是被浏览器或 V8 所引用的对象。被局部变量所指向的对象也属于根对象，因为它们所在的作用域对象被视为根对 象。全局对象（Node 中为 global，浏览器中为 window）自然是根对象。浏览器中的 DOM 元素也属于根对象。

#### V8 回收策略

新生代的对象为存活时间较短的对象，老生代中的对象为存活时间较长或常驻内存的对象。分别对新生代和老生代使用 不同的垃圾回收算法来提升垃圾回收的效率。对象起初都会被分配到新生代，当新生代中的对象满足某些条件（后面会有介绍）时，会被移动到老生代（晋升）。

#### 新生代算法

在新生代空间中，内存空间分为两部分，分别为 From 空间和 To 空间。在这两个空间中，必定有一个空间是使用的，另一个空间是空闲的。新分配的对象会被放入 From 空间中，当 From 空间被占满时，新生代 GC 就会启动了。算法会检查 From 空间中存活的对象并复制到 To 空间中，如果有失活的对象就会销毁。当复制完成后将 From 空间和 To 空间互换，这样 GC 就结束了。 [![img](https://github.com/huyaocode/webKnowledge/raw/master/img/gc-new.png)](https://github.com/huyaocode/webKnowledge/blob/master/img/gc-new.png)

#### 老生代算法

老生代中的对象一般存活时间较长且数量也多，使用了两个算法，分别是标记清除算法和标记压缩算法。

在讲算法前，先来说下什么情况下对象会出现在老生代空间中：

1. 新生代中的对象是否已经经历过一次 Scavenge 算法，如果经历过的话，会将对象从新生代空间移到老生代空间中。
2. To 空间的对象占比大小超过 25 %。在这种情况下，为了不影响到内存分配，会将对象从新生代空间移到老生代空间中。

## 内存泄漏及优化

#### 什么是内存泄露

存泄露是指程序中已分配的堆内存由于某种原因未释放或者无法释放，造成系统内存的浪费，导致程序运行速度减慢甚至系统奔溃等后果。

#### 常见的内存泄露的场景

- 缓存
- 作用域未释放（闭包）
- 没有必要的全局变量
- 无效的 DOM 引用
- 定时器未清除
- 事件监听为空白

#### 内存泄露优化

1. 在业务不需要的用到的内部函数，可以重构到函数外，实现解除闭包。
2. 避免创建过多的生命周期较长的对象，或者将对象分解成多个子对象。
3. 避免过多使用闭包。
4. 注意清除定时器和事件监听器。
5. nodejs 中使用 stream 或 buffer 来操作大文件，不会受 nodejs 内存限制。

## this及其指向

#### this 的指向

this 代表函数调用相关联的对象，通常也称为**执行上下文**。

1. 作为**函数直接调用**，非严格模式下，**this 指向 window**，严格模式下，this 指向 undefined；
2. 作为**某对象的方法调用**，this 通常**指向调用的对象**。
3. 使用 **apply、call、bind 可以绑定 this 的指向**。
4. 在**构造函数**中，this 指向**新创建的对象**
5. 箭头函数没有单独的 this 值，this 在箭头函数创建时确定，它与声明所在的上下文相同。

#### 多个 this 规则出现时，this 最终指向

首先，new 的方式优先级最高，接下来是 bind 这些函数，然后是 obj.foo() 这种调用方式，最后是 foo 这种调用方式，同时，箭头函数的 this 一旦被绑定，就不会再被任何方式所改变。

![this](https://tva1.sinaimg.cn/large/007S8ZIlly1gi3zdwjugtj30ko0erdfu.jpg)

## 原型对象和原型链 
### 原型对象

- JavaScript 的**所有对象**中都包含了一个 `__proto__` 内部属性，这个属性所对应的就是该对象的原型
- JavaScript 的**函数对象**，除了原型 `__proto__` 之外，还预置了 prototype 属性
- 当函数对象作为**构造函数创建实例**时，该 prototype 属性值将被作为实例对象的原型 `__proto__`。

```js
function Person() {}
var person = new Person();
Person.prototype = person.__proto__;
```

![原型](https://tva1.sinaimg.cn/large/007S8ZIlly1gi6an8k50pj30ki07xjso.jpg)

### 原型链

**原型链的基本原理**：任何一个**实例**，通过原型链，找到它上面的**原型**，该原型对象中的方法和属性，可以被所有的原型实例共享。

Object是原型链的顶端。

原型可以起到继承的作用。原型里的方法都可以被不同的实例共享：

```js
//给Foo的原型添加 say 函数
    Foo.prototype.say = function () {
        console.log('');
    }
```

**原型链的关键**：在访问一个实例的时候，如果实例本身没找到此方法或属性，就往原型上找。如果还是找不到，继续往上一级的原型上找。

> 函数才有 prototype，实例对象只有**`__proto__`**， 而函数有的**`__proto__`**是因为函数是 Function 的实例对象

### 原型、构造函数、实例的关系

> 构造函数：任何一个函数，如果前面加了new，则是构造函数

1、**构造函数通过new生成实例**。

2、构造函数也是函数，**构造函数的prototype指向原型**。所有的函数都有prototype属性，但是实例没有prototype属性。

3、**原型对象中有constructor，指向该原型的构造函数**

```js
var Foo = function(name) {
  this.name = name;
}
var foo = new Foo('ramona');
Foo.prototype.constructor === Foo;  // true
```

4、实例的`__proto__`指向原型。也就是，`foo.__proto__ === Foo.prototype`。

声明：所有的**引用类型**（数组、对象、函数）都有`__proto__`这个属性。

`Foo.__proto__ === Function.prototype`的结果为true，说明Foo这个普通的函数，是Function构造函数的一个实例。

## prototype与__proto__的关系与区别

函数才有 prototype，实例对象只有**proto**， 而函数有的**proto**是因为函数是 Function 的实例对象

## 面向对象的程序设计

> 1、创建对象的方法
>
> 2、类的定义、声明
>
> 3、实现继承的方式
>
> 4、原型和原型链
>
> 5、new 运算符

![截屏2020-09-0310.21.18](https://tva1.sinaimg.cn/large/007S8ZIlly1gid8zswdcrj30u00uhn5o.jpg)

## 创建对象的方法

#### 1、字面量

```js
// 字面量创建
var obj = {name: 'ramona'};
// 内置函数创建
var obj = new Object('ramona');
```

#### 2、通过构造函数

```js
var Obj = function(name) {
  this.name = name;
}
var obj = new Obj('ramona');
```

#### 3、Object.create

```js
var obj = {name: 'ramona'};
var p = Object.create(obj);
// 通过原型链创建，p是实例，obj是p的原型。
```

##  类的定义、声明

```js
// 构造函数模拟类
function Person() {
  this.name = "ramona";
}
// 用class声明
class  Person() {
  constructor() { // 可以在构造函数里写属性
    this.name = name;
  }
}
```

类的实例化：

```js
new Person();
```

## 继承的方式（本质是原型链）

#### 1、借助构造函数

```js
function Parent() {
  this.name = 'parent 属性';
}

function Child() {
  Parent.call(this);
  this.type = 'child 属性';
}

console.log(new Child());
// Child {name: "parent 属性", type: "child 属性"}
```

`Parent.call(this)`函数， **让Parent的构造函数在child的构造函数中执行**。发生的变化是：**改变this的指向**，parent的实例 --> 改为指向child的实例。导致 parent的实例的属性挂在到了child的实例上，这就实现了继承。

存在的问题：子类不能继承父类的原型。也就是：

```js
Parent.prototype.say = function() {}
console.log(new Child().say());  // 无法获取到Parent原型的内容
```

#### 2、通过原型实现继承

```js
function Parent() {
  this.name = 'Parent 属性';
}

function Child() {
  this.name = 'Child 属性';
}

Child.prototype = new Parent();
console.log(new Child());
```

把parent的实例赋值给child的prototype，实现继承。

```js
new Child().__proto__ === new Parent(); // true
```

存在的问题：child可以继承parent的原型，但如如果创建两个实例，会导致修改一个实例的属性，另一个实例也跟随改变。

#### 3、组合：构造函数 + 原型链

```js
function Parent() {
  this.name = 'Parent 属性';
}

function Child() {
  Parent.call(this);
  this.name = 'Child 属性';
}

Child.prototype = new Parent();
cosole.log(new Child());
```

存在的问题：虽然解决了上述遇到的问题，但是会让Parent的构造方法执行两次。

#### 4、组合2：构造函数 + 原型链 + Object.create()

```js
function Parent() {
  this.name = 'Parent 属性';
}

function Child() {
  Parent.call(this);
  this.name = 'Child 属性';
}
Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;
```

Object.create()方法创建一个新对象，使用现有的对象来提供新创建的对象的**proto**


## ES5/ES6 的继承区别

- class 声明会提升，但不会初始化赋值。Foo 进入暂时性死区，类似于 let、const 声明变量。

  ```js
  const bar = new Bar(); // it's ok
  function Bar() {
    this.bar = 42;
  }
  
  const foo = new Foo(); // ReferenceError: Foo is not defined
  class Foo {
    constructor() {
      this.foo = 42;
    }
  }
  ```

- class 声明内部会启用严格模式。

  ```js
  // 引用一个未声明的变量
  function Bar() {
    baz = 42; // it's ok
  }
  const bar = new Bar();
  
  class Foo {
    constructor() {
      fol = 42; // ReferenceError: fol is not defined
    }
  }
  const foo = new Foo();
  ```

- class 的所有方法（包括静态方法和实例方法）都是不可枚举的。

  ```js
  // 引用一个未声明的变量
  function Bar() {
    this.bar = 42;
  }
  Bar.answer = function() {
    return 42;
  };
  Bar.prototype.print = function() {
    console.log(this.bar);
  };
  const barKeys = Object.keys(Bar); // ['answer']
  const barProtoKeys = Object.keys(Bar.prototype); // ['print']
  
  class Foo {
    constructor() {
      this.foo = 42;
    }
    static answer() {
      return 42;
    }
    print() {
      console.log(this.foo);
    }
  }
  const fooKeys = Object.keys(Foo); // []
  const fooProtoKeys = Object.keys(Foo.prototype); // []
  ```

- class 的所有方法（包括静态方法和实例方法）都没有原型对象 prototype，所以也没有[[construct]]，不能使用 new 来调用。

  ```js
  function Bar() {
    this.bar = 42;
  }
  Bar.prototype.print = function() {
    console.log(this.bar);
  };
  
  const bar = new Bar();
  const barPrint = new bar.print(); // it's ok
  
  class Foo {
    constructor() {
      this.foo = 42;
    }
    print() {
      console.log(this.foo);
    }
  }
  const foo = new Foo();
  const fooPrint = new foo.print(); // TypeError: foo.print is not a constructor
  ```

- 必须使用 new 调用 class。

  ```js
  function Bar() {
    this.bar = 42;
  }
  const bar = Bar(); // it's ok
  
  class Foo {
    constructor() {
      this.foo = 42;
    }
  }
  const foo = Foo(); // TypeError: Class constructor Foo cannot be invoked without 'new'
  ```

- class 内部无法重写类名。

  ```js
  function Bar() {
    Bar = 'Baz'; // it's ok
    this.bar = 42;
  }
  const bar = new Bar();
  // Bar: 'Baz'
  // bar: Bar {bar: 42}  
  
  class Foo {
    constructor() {
      this.foo = 42;
      Foo = 'Fol'; // TypeError: Assignment to constant variable
    }
  }
  const foo = new Foo();
  Foo = 'Fol'; // it's ok
  ```

###  面向对象的程序设计详解

每个对象都是基于一个引用类型创建的，这个引用类型可以是原生类型，也可以是开发人员定义的类型。

#### 属性类型

ECMAScript 中有两种属性：数据属性和访问器属性。

##### 数据属性

**数据属性包含一个数据值的位置。在这个位置可以读取和写入值。**数据属性有 4 个描述其行为的特性。

| 属性名             | 描述                                                         | 属性默认值                                         |
| ------------------ | ------------------------------------------------------------ | -------------------------------------------------- |
| `[[Configurable]]` | 表示能否通过 delete 删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为访问器属性 | 对于直接在对象上定义的属性，这个特性的默认值为true |
| `[[Enumerable]]`   | 表示能否通过 for-in 循环返回属性                             | 对于直接在对象上定义的属性，这个特性的默认值为true |
| `[[Writable]]`     | 表示能否修改属性的值                                         | 对于直接在对象上定义的属性，这个特性的默认值为true |
| `[[Value]]`        | 包含这个属性的数据值。读取属性值的时候，从这个位置读；写入属性值的时候，把新值保存在这个位置 | undefined                                          |

要修改属性默认的特性，必须使用 ECMAScript 5 的` Object.defineProperty()`方法。这个方法接收三个参数：属性所在的对象、属性的名字和一个描述符对象。其中，描述符（descriptor）对象的属性必须是：`configurable、enumerable、writable 和 value`。设置其中的一或多个值，可以修改对应的特性值。例如：

```js
var person = {};
Object.defineProperty(person, "name", {
    wirtable: false,
    value: "ramona"
});
alert(person.name); // "ramona"
person.name = "raymond";
alert(person.name); // "ramona"
```

可以多次调用 `Object.defineProperty()`方法修改同一个属性，但在把 `configurable`特性设置为` false `之后就会有限制了。

在调用` Object.defineProperty()`方法时，如果不指定，`configurable、enumerable 和writable `特性的默认值都是 `false`。多数情况下，可能都没有必要利用 `Object.defineProperty()`方法提供的这些高级功能。

##### 访问器属性

访问器属性不包含数据值；它们包含一对儿 getter 和 setter 函数（不过，这两个函数都不是必需的）。**在读取访问器属性时，会调用 getter 函数，这个函数负责返回有效的值；在写入访问器属性时，会调用setter 函数并传入新值，这个函数负责决定如何处理数据**。访问器属性有如下 4 个特性。

| 属性名             | 描述                                                         | 默认值                                             |
| ------------------ | ------------------------------------------------------------ | -------------------------------------------------- |
| `[[Configurable]]` | 表示能否通过 delete 删除属性从而重新定义属性，能否修改属性的特性，或者能否把属性修改为数据属性 | 对于直接在对象上定义的属性，这个特性的默认值为true |
| `[[Enumerable]]`   | 表示能否通过 for-in 循环返回属性                             | 对于直接在对象上定义的属性，这个特性的默认值为true |
| `[[Get]]`          | 在读取属性时调用的函数                                       | undefined                                          |
| `[[Set]]`          | 在写入属性时调用的函数                                       | undefined                                          |

访问器属性不能直接定义，必须使用` Object.defineProperty()`来定义。请看下面的例子。

```js
var book = { 
 _year: 2004, 
 edition: 1 
}; 
Object.defineProperty(book, "year", { 
 get: function(){ 
 return this._year; 
 }, 
 set: function(newValue){ 
 if (newValue > 2004) { 
 this._year = newValue; 
 this.edition += newValue - 2004; 
 } 
 } 
}); 
book.year = 2005; 
alert(book.edition); //2
```

> _year 前面的下划线是一种常用的记号，用于表示只能通过对象方法访问的属性

#### 定义多个属性

由于为对象定义多个属性的可能性很大，ECMAScript 5 又定义了一个 `Object.defineProperties()`方法。利用这个方法可以通过描述符一次定义多个属性。这个方法接收两个对象参数**：第一个对象是要添加和修改其属性的对象，第二个对象的属性与第一个对象中要添加或修改的属性一一对应**。例如：

```js
var book = {}; 
Object.defineProperties(book, { 
 _year: { 
 value: 2004 
 }, 
 edition: { 
 value: 1 
 }, 
 year: { 
 get: function(){
     return this._year; 
 	}, 
 set: function(newValue){ 
     if (newValue > 2004) { 
     this._year = newValue; 
     this.edition += newValue - 2004; 
     } 
 	} 
 } 
});
```

> 支持 Object.defineProperties()方法的浏览器有 IE9+、Firefox 4+、Safari 5+、Opera 12+和Chrome。

##### 读取属性的特性

使用 ECMAScript 5 的 `Object.getOwnPropertyDescriptor()`方法，可以取得给定属性的描述符。**这个方法接收两个参数：属性所在的对象和要读取其描述符的属性名称**。返回值是一个对象，如果是访问器属性，这个对象的属性有 configurable、enumerable、get 和 set；如果是数据属性，这个对象的属性有 configurable、enumerable、writable 和 value。

> 在 JavaScript 中，可以针对任何对象——包括 DOM 和 BOM 对象，使用 Object.getOwnPropertyDescriptor()方法。支持这个方法的浏览器有 IE9+、Firefox 4+、Safari 5+、Opera 12+和 Chrome。

### 创建对象

虽然 **Object 构造函数或对象字面量**都可以用来创建单个对象，但这些方式有个明显的缺点：使用同一个接口创建很多对象，会产生大量的重复代码。为解决这个问题，人们开始使用工厂模式的一种变体。

#### 工厂模式

工厂模式是软件工程领域一种广为人知的设计模式，这种模式抽象了创建具体对象的过程。

```js
function createPerson(name, age, job) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function() {
        alert(this.name);
    }
    return o;
}

var person1 = createPerson("ramona", 20, "Doctor");
var person2 = createPerson("raymond", 24, "Teacher");
```

缺点：**工厂模式虽然解决了创建多个相似对象的问题，但却没有解决对象识别的问题**（即怎样知道一个对象的类型）。

#### 构造函数模式

ECMAScript 中的构造函数可用来创建特定类型的对象。像 Object 和 Array 这样的原生构造函数，在运行时会自动出现在执行环境中。此外，也可以创建自定义的构造函数，从而定义自定义对象类型的属性和方法。例如，可以使用构造函数模式将前面的例子重写如下。

```js
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function() {
        alert(this.name);
    }
}

var person1 = new Person("ramona", 20, "Doctor");
var person2 = new Person("raymond", 24, "Teacher");

// person1 和 person2 分别保存着 Person 的一个不同的实例。这两个对象都有一个 constructor（构造函数）属性，该属性指向 Person
console.log(person1.constructor == Person); // true
console.log(person2.constructor == Person); // true
```

> `new`操作符的实现原理：
>
> 1. 创建一个新对象；
> 2. 将构造函数的作用域赋给新对象（this就指向这个新对象）；
> 3. 执行构造函数中的代码（为这个新对象添加属性）；
> 4. 返回新对象。

任何函数，只要通过 new 操作符来调用，那它就可以作为构造函数；而任何函数，如果不通过 new 操作符来调用，那它跟普通函数也不会有什么两样。

缺点：**使用构造函数的主要问题，就是每个方法都要在每个实例上重新创建一遍**。然而，创建两个完成同样任务的 Function 实例的确没有必要；况且有 this 对象在，根本不用在执行代码前就把函数绑定到特定对象上面。因此，大可像下面这样，**通过把函数定义转移到构造函数外部来解决这个问题。**

```js
// 存在的问题
function Person(name, age, job){ 
 this.name = name; 
 this.age = age; 
 this.job = job; 
 this.sayName = new Function("alert(this.name)"); // 与声明函数在逻辑上是等价的
}

// 解决方法
function Person(name, age, job){
 this.name = name; 
 this.age = age; 
 this.job = job; 
 this.sayName = sayName; 
} 
function sayName(){ 
 alert(this.name); 
} 
var person1 = new Person("Nicholas", 29, "Software Engineer"); 
var person2 = new Person("Greg", 27, "Doctor");
```

但是，在全局作用域中定义的函数实际上只能被某个对象调用，这让全局作用域有点名不副实。而更让人无法接受的是：**如果对象需要定义很多方法，那么就要定义很多个全局函数，于是我们这个自定义的引用类型就丝毫没有封装性可言了**。这些问题可以通过使用原型模式来解决。

#### 原型模式

我们创建的每个函数都有一个 **prototype（原型）属性**，这个属性是一个指针，指向一个对象，而这个对象的用途是**包含可以由特定类型的所有实例共享的属性和方法**。如果按照字面意思来理解，那么 prototype 就是通过调用构造函数而创建的那个对象实例的原型对象。**使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法**。换句话说，不必在构造函数中定义对象实例的信息，而是可以将这些信息直接添加到原型对象中。

```js
function Person() {
    Person.prototype.name = "ramona";
    Person.prototype.age = 24;
    Person.prototype.job = "Dector";
    Person.prototype.sayName = function() {
        alert(this.name);
    }
}

var person1 = new Person();
person1.sayName(); // "ramona"
var person2 = new Person();
person2.sayName(); // "ramona"
alert(person1.sayName == person2.sayName); // true
```

##### 原型对象

无论什么时候，只要**创建了一个新函数**，就会根据一组特定的规则**为该函数创建一个 prototype属性，这个属性指向函数的原型对象**。在默认情况下，**所有原型对象都会自动获得一个constructor（构造函数）属性，这个属性包含一个指向 prototype 属性所在函数的指针**。

```js
console.log(Person.prototype); 
// 输出:
{name: "ramona", age: 24, job: "Dector", sayName: ƒ, constructor: ƒ}
console.log(Person.prototype.constructor);
// 输出：
ƒ Person() {
    Person.prototype.name = "ramona";
    Person.prototype.age = 24;
    Person.prototype.job = "Dector";
    Person.prototype.sayName = function() {
        alert(this.name);
    }
}
console.log(Person.prototype.constructor == Person); // true
```

**创建了自定义的构造函数之后，其原型对象默认只会取得 constructor 属性**；至于其他方法，则都是从 Object 继承而来的。**当调用构造函数创建一个新实例后，该实例的内部将包含一个指针（内部属性），指向构造函数的原型对象**。ECMA-262 第 5 版中管这个指针叫[[Prototype]]。虽然在脚本中没有标准的方式访问**[[Prototype]]**，但 Firefox、Safari 和 Chrome 在每个对象上都**支持一个属性`__proto__`**；而在其他实现中，这个属性对脚本则是完全不可见的。

```js
console.log(person1.__proto__); 
// 输出：
{name: "ramona", age: 24, job: "Dector", sayName: ƒ, constructor: ƒ}
console.log(person1.__proto__.constructor);
// 输出：
ƒ Person() {
    Person.prototype.name = "ramona";
    Person.prototype.age = 24;
    Person.prototype.job = "Dector";
    Person.prototype.sayName = function() {
        alert(this.name);
    }
}
console.log(person1.__proto__ == Person.prototype); // true
```

以前面使用 Person 构造函数和 Person.prototype 创建实例的代码为例，下图展示了各个对象之间的关系。

![image-20201003150535938](https://i.loli.net/2020/10/27/kF8UAwW9zXlt3If.png)

```js
Person.prototype
Person.prototype.constructor == Person;
person1.__proto__ == Person.prototype;
```

确定对象之间存在的关系及取得一个对象的原型的方法：

```js
console.log(Person.prototype.isPrototypeOf(person1)); // true
console.log(Person.prototype.isPrototypeOf(person2)); // true
// 使用 Object.getPrototypeOf()可以方便地取得一个对象的原型
console.log(Object.getPrototypeOf(person1) == Person.prototype); // true
```

使用 hasOwnProperty()方法可以检测一个属性是存在于实例中，还是存在于原型中。

```js
console.log(person1.hasOwnProperty("name")); // false (来自原型)
console.log(Person.prototype.hasOwnProperty("name")); // true (来自原型)
person1.name = "raymond";
console.log(person1.hasOwnProperty("name")); // true (来自实例)
delete person1.name; 
console.log(person1.hasOwnProperty("name")); // false (来自原型)
```

in 操作符会在通过对象能够访问给定属性时返回 true，无论该属性存在于实例中还是原型中。

```js
console.log("name" in person1); // true (来自原型)
person1.name = "raymond";
console.log("name" in person1); // true (来自实例)
delete person1.name; 
console.log("name" in person1); // true (来自原型)
```

> name 属性要么是直接在对象上访问到的，要么是通过原型访问到的。因此，调用"name" in person1 始终都返回 true，无论该属性存在于实例中还是存在于原型中。

同时使用 hasOwnProperty()方法和 in 操作符，就可以确定该属性到底是存在于对象中，还是存在于原型中。

```js
function hasPrototypeProperty(object, name) {
    return !object.hasOwnProperty(name) && (name in object);
}
```

由于 in 操作符只要通过对象能够访问到属性就返回 true，hasOwnProperty()只在属性存在于实例中时才返回 true，因此只要 in 操作符返回 true 而 hasOwnProperty()返回 false，就可以确定属性是原型中的属性。

原型模式的缺点：

* 它省略了为构造函数传递初始化参数这一环节，结果所有实例在默认情况下都将取得相同的属性值。
* **最大问题是由其共享的本性所导致的**。

## ES6 对象的扩展

### 属性及方法的简洁表示法

当属性名/方法名和变量名一致时，ES6允许在大括号里直接写入变量和函数，作为对象的属性和方法。

#### 属性简写

属性名就是变量名, 属性值就是变量值

```js
// 示例1
const name = 'ramona';
const foo = { name };  
// 等同于
const foo = { name: name };

// 示例2
function f(x, y) {
  return {x, y};
}
// 等同于
function f(x, y) {
  return {x: x, y: y};
}
f(1, 2); // Object {x: 1, y: 2}
```

#### 方法简写

```js
// 示例1
const fn = {
  method() {
    return 'hello world';
  }
}
// 等同于
const fn = {
  method: function() {
     return 'hello world';
  }
}

// 示例2
let birth = '2000/01/01';
const Person = {
  name: '张三', 
  birth, //等同于birth: birth
  hello() { console.log('我的名字是', this.name); }  // 等同于hello: function ()...
};
```

#### 应用场景

1、函数的返回值

```js
function getPoint() {
  const x = 1;
  const y = 10;
  return {x, y};
}
getPoint(); // {x:1, y:10}
```

2、CommonJS 模块输出一组变量

```js
let ms = {};
function getItem(key)  {
  return key in ms ? ms[key] : nulll;
}
function setItem(key, value)  {
  ms[key] = value;
}
function clear() {
  ms = {};
}
module.exports = { getItem. setItem, clear };
// 等同于
module.exports = {
  getItem: getItem,
  setItem: setItem,
  clear: clear
};
```

3、属性的赋值器（setter）和取值器（getter）

```js
const cart = {
  _wheels: 4,

  get wheels () {
    return this._wheels;
  },

  set wheels (value) {
    if (value < this._wheels) {
      throw new Error('数值太小了！');
    }
    this._wheels = value;
  }
}
```

4、打印对象

```js
let user = { name: 'lily' };
let foo = { bar: 'bar' };
console.log(user, foo); // {name: "test"} {bar: "baz"}
console.log({user, bar}); // {user: {name: "test"}, foo: {bar: "baz"}}
```

> 注意📢：简写的对象方法不能用作构造函数。
>
> ```js
> const obj = {
>   f() {
>     this.name = 'jack';
>   }
> };
> new obj.f();  //Uncaught TypeError: obj.f is not a constructor
> ```

### 属性名表达式

JavaScript 定义对象的属性，有两种方法。

* 方法一：直接用标识符作为属性名。

  ```js
  obj.foo = true;
  ```

* 方法二：用表达式作为属性名，这时要将表达式放在方括号之内。

  ```js
  obj['a' + 'bc'] = 123;
  ```

使用字面量方式定义对象，在ES5中只能使用方式一。ES6 允许字面量定义对象时，用方法二（表达式）作为对象的属性名，即把表达式放在方括号内。

```js
let lastWord = 'last word';

const a = {
  'first word': 'hello',
  [lastWord]: 'world'
};

a['first word'] // "hello"
a[lastWord] // "world"
a['last word'] // "world"
```

表达式还可以用来定义方法名。（了解）

```js
let obj = {
  ['h' + 'ello']() {
    return 'hi';
  }
};
obj.hello() // hi
```

>注意📢：属性名表达式与简洁表示法，不能同时使用。
>
>```js
>// 报错
>const foo = 'bar';
>const bar = 'abc';
>const baz = { [foo] };
>
>// 正确
>const foo = 'bar';
>const baz = { [foo]: 'abc'};
>```
>
>属性名表达式如果是一个对象，默认情况下会自动将对象转为字符串`[object Object]`，这一点要特别小心。
>
>```js
>const keyA = {a: 1};
>const keyB = {b: 2};
>
>const myObject = {
>  [keyA]: 'valueA',
>  [keyB]: 'valueB'
>};
>
>myObject; // Object {[object Object]: "valueB"}
>```
>[keyA]和[keyB]得到的都是[object Object]，所以[keyB]会把[keyA]覆盖掉，而myObject最后只有一个[object Object]属性。
>

### 方法的name属性

函数的`name`属性，返回函数名。对象方法也是函数，因此也有`name`属性。

```js
const person = {
  sayName() {
    console.log('hello!');
  },
};

person.sayName.name;   // "sayName"
```

<<<<<<< HEAD
## 对象的新增方法

### Object.is() 比较两个值是否相等



ES5 中比较两个值是否相等，只有两个运算符。

* 相等运算符（`==`），但会自动转换数据类型。
* 严格相等运算符（`===`），但`NaN`不等于自身，以及`+0`等于`-0`。

ES6 提出“Same-value equality”（同值相等）算法，即`Object.is`方法，用来比较两个值是否严格想等，与（`===`）行为基本一致。但是`+0`不等于`-0`，`NaN`等于自身。

```js
Object.is('foo', 'foo'); // true
Object.is({}, {}); // false  ? 

+0 === -0 //true
NaN === NaN // false

Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```

ES5可通过以下代码，部署`Object.is`。

```js
Object.defineProperty(Object, 'is', {
  value: function(x, y) {
    if (x === y) {
      // 针对 +0 不等于 -0 的情况,返回 false
      return x !== 0 || 1 / x === 1 / y;
    }
    // 针对 NaN 的情况
    return x !== x && y !== y;
  },
  configurable: true,
  enumerable: false;
  writable: true
});
```

### Object.assign() 对象的合并

**作用**：将源对象（source）的所有可枚举属性，复制到目标对象（target）。

**语法**：`Object.assign(target, source, ...)`方法的第一个参数是目标对象，后面的参数都是源对象。

```js
const target = { a: 1 };

const source1 = { b: 2 };
const source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```

#### 基本用法

1.如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性。

```js
const target = { a: 1, b: 1 };

const source1 = { b: 2, c: 2 };
const source2 = { c: 3 };

Object.assign(target, source1, source2);
target // {a:1, b:2, c:3}
```

2.如果只有一个参数，`Object.assign()`会直接返回该参数。

```js
const obj = {a: 1};
Object.assign(obj); // {a: 1}
Object.assign(obj) === obj // true
```

3.如果该参数不是对象，则会先转成对象，然后返回。

```js
Object.assign(2); // Number {2}
typeof Object.assign(2); // "object"
```

4.由于`undefined`和`null`无法转成对象，若它们作为首参数，就会报错。

```js
Object.assign(undefined);
Object.assign(null);
// Uncaught TypeError: Cannot convert undefined or null to object
```

5.若非对象参数出现在源对象的位置（即非首参数），首先，这些参数都会转成对象，如果无法转成对象，就会跳过。

* 如果`undefined`和`null`不在首参数，就不会报错。
* 其他类型的值（即数值、字符串和布尔值）不在首参数，也不会报错。但字符串会以数组形式，拷贝入目标对象，其他值都不会产生效果。

```js
const targetObj = {a: 1};

// undefined、null
Object.assign(targetObj, undefined) === targetObj; // true
Object.assign(targetObj, null) === targetObj; // true

// 数值、布尔值等
Object.assign(targetObj, 10); // {a: 1}
Object.assign(targetObj, true); // {a: 1}

// 字符串
Object.assign(targetObj, 'foo'); // {0: "f", 1: "o", 2: "o", a: 1}
```

如下代码所示，布尔值、数值、字符串会分别转成对应的包装对象，它们的原始值都在包装对象的内部属性`[[PrimitiveValue]]`上面，这个属性是不会被`Object.assign()`拷贝的。只有字符串的包装对象，会产生可枚举的实义属性，那些属性则会被拷贝。

```js
Object(true) // {[[PrimitiveValue]]: true}
Object(10)  //  {[[PrimitiveValue]]: 10}
Object('abc') // {0: "a", 1: "b", 2: "c", length: 3, [[PrimitiveValue]]: "abc"}
```

6.`Object.assign()`拷贝的属性是有限制的，只拷贝源对象的自身属性（不拷贝继承属性），也不拷贝不可枚举的属性（`enumerable: false`）。

```js
Object.assign({b: 'c'},
  Object.defineProperty({}, 'invisible', {
    enumerable: false,
    value: 'hello'
  })
);
// { b: 'c' }
```

上述代码中，`Object.assign()`要拷贝的对象只有一个不可枚举属性`invisible`，这个属性并没有被拷贝进去。

7.属性名为 Symbol 值的属性，也会被`Object.assign()`拷贝。

```js
Object.assign({ a: 'b' }, { [Symbol('c')]: 'd' }); // { a: 'b', Symbol(c): 'd' }
=======
如果对象的方法使用了取值函数（`getter`）和存值函数（`setter`），则`name`属性不是在该方法上面，而是该方法的属性的描述对象的`get`和`set`属性上面，返回值是方法名前加上`get`和`set`。

```js
const obj = {
  get foo() {},
  set foo(x) {}
};

obj.foo.name; // TypeError: Cannot read property 'name' of undefined

const descriptor = Object.getOwnPropertyDescriptor(obj, 'foo');
descriptor;
/**
configurable: true
enumerable: true
get: ƒ foo()
set: ƒ foo(x)
**/

descriptor.get.name; // "get foo"
descriptor.set.name; // "set foo"
```

> 对象的每个属性都有一个描述对象（Descriptor），用来控制该属性的行为。`Object.getOwnPropertyDescriptor`方法可以获取该属性的描述对象。

特殊情况:

* `bind`方法创造的函数，`name`属性返回`bound`加上原函数的名字；

* `Function`构造函数创造的函数，`name`属性返回`anonymous`。

```js
// bind方法示例
var doSomething = function() {
  // doSomething ...
};
doSomething.bind().name; // "bound doSomething"

// Function示例
(new Function()).name // "anonymous"
```

如果对象的方法是一个 Symbol 值，那么`name`属性返回的是这个 Symbol 值的描述。

```js
const key1 = Symbol('description');
const key2 = Symbol();
let obj = {
  [key1]() {},
  [key2]() {},
};
obj[key1].name // "[description]"
obj[key2].name // ""
```

### 属性的可枚举性和遍历

#### 属性可枚举性

```js
let obj = { foo: 123 };
Object.getOwnPropertyDescriptor(obj, 'foo');
//  {
//    value: 123,
//    writable: true,
//    enumerable: true,
//    configurable: true
//  }
```

描述对象的`enumerable`属性，表示能否通过 for-in 循环返回属性,称为“可枚举性”，如果该属性为`false`，就表示某些操作会忽略当前属性。

目前，有四个操作会忽略`enumerable`为`false`的属性。

- `for...in`循环：只遍历对象自身的和**继承的**可枚举的属性。
- `Object.keys()`：返回对象自身的所有可枚举的属性的键名。
- `JSON.stringify()`：只串行化对象自身的可枚举的属性。
- `Object.assign()`： 只拷贝对象自身的可枚举的属性。(ES6 新增)

> 引入“可枚举”（`enumerable`）的目的：让某些属性可以规避掉`for...in`操作，不然所有内部属性和方法都会被遍历到。比如，对象原型的`toString`方法，以及数组的`length`属性，就通过“可枚举性”，从而避免被`for...in`遍历到。
>
> ```js
> Object.getOwnPropertyDescriptor(Object.prototype, 'toString').enumerable; // false
> 
> Object.getOwnPropertyDescriptor([], 'length').enumerable; // false
> ```
>
> `toString`和`length`属性的`enumerable`都是`false`，因此`for...in`不会遍历到这两个继承自原型的属性。
>
> ![image-20210822201852538](https://tva1.sinaimg.cn/large/008i3skNly1gtptzkz2dyj61880e0wgo02.jpg)

ES6 规定，所有 Class 的原型的方法都是不可枚举的。

```js
Object.getOwnPropertyDescriptor(class {foo() {}}.prototype, 'foo').enumerable; // false
```

> 总的来说，操作中引入继承的属性会让问题复杂化，大多数时候，我们只关心对象自身的属性。所以，尽量不要用`for...in`循环，而用`Object.keys()`代替。

#### 属性的遍历

ES6 一共有 5 种方法可以遍历对象的属性。

| 属性的遍历方法                        | 描述                                                         |
| ------------------------------------- | ------------------------------------------------------------ |
| **for...in**                          | 遍历对象自身的和继承的可枚举属性（不含 Symbol 属性）。       |
| **Object.keys(obj)**                  | 返回一个数组，包括对象自身的所有可枚举属性（不含 Symbol 属性）的键名。 |
| **Object.getOwnPropertyNames(obj)**   | 返回一个数组，包含对象自身的所有属性（不含 Symbol 属性，但是包括不可枚举属性）的键名。 |
| **Object.getOwnPropertySymbols(obj)** | 返回一个数组，包含对象自身的所有 Symbol 属性的键名。         |
| **Reflect.ownKeys(obj)**              | 返回一个数组，包含对象自身的（不含继承的）所有键名，不管键名是 Symbol 或字符串，也不管是否可枚举。 |

以上的 5 种方法遍历对象的键名，都遵守同样的属性遍历的次序规则。

- 首先遍历所有**数值键**，按照数值升序排列。
- 其次遍历所有**字符串键**，按照加入时间升序排列。
- 最后遍历所有 **Symbol 键**，按照加入时间升序排列。

```js
Reflect.ownKeys({ [Symbol()]:0, b:0, 10:0, 2:0, a:0 });
// ['2', '10', 'b', 'a', Symbol()]
```

### super 关键字

`this`关键字总是指向函数所在的当前对象，ES6 又新增了另一个类似的关键字`super`，**指向当前对象的原型对象**。

JavaScript 引擎内部，`super.foo`等同于`Object.getPrototypeOf(this).foo`（属性）或`Object.getPrototypeOf(this).foo.call(this)`（方法）。

```js
const proto = {
  x: 'hello',
  foo() {
    console.log(this.x);
  },
};

const obj = {
  x: 'world',
  foo() {
    super.foo();
  },
  find() {
    return super.x;
  }
}

Object.setPrototypeOf(obj, proto);
// 当做问题
obj.find(); // ? "hello"
obj.foo() // ? "world"
```

（折叠）上面代码中，对象`obj.find()`方法之中，通过`super.x`引用了原型对象`proto`的`x`属性。`super.foo()`指向原型对象`proto`的`foo`方法，但是绑定的`this`却还是当前对象`obj`，因此输出的就是`world`。

> 注意：`super`关键字表示原型对象时，只能用在对象的方法之中，用在其他地方都会报错。
>
> ```js
> /**
> 报错 Uncaught SyntaxError: 'super' keyword unexpected here
> */
> // 用在属性里面
> const obj = {
>   foo: super.foo
> }
> 
> // 用在一个函数里面，然后赋值给foo属性
> const obj = {
>   foo: () => super.foo
> }
> 
> // 用在一个函数里面，然后赋值给foo属性
> const obj = {
>   foo: function () {
>     return super.foo
>   }
> }
> 
> // 正确✅ 对象方法的简写法
> const obj = {
>   find() {
>     return super.foo;
>   }
> }
> ```
>
> 

### 对象的扩展运算符

#### 解构赋值

对象的解构赋值用于从一个对象取值，相当于将目标对象自身的所有可遍历的（enumerable）、但尚未被读取的属性，分配到指定的对象上面。所有的键和它们的值，都会拷贝到新对象上面。

```js
let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
x // 1
y // 2
z // { a: 3, b: 4 }
>>>>>>> 10d184d5453f6075f3ffc1c49b19a7be4265e6a3
```

> **注意点**
>
<<<<<<< HEAD
> (1)浅拷贝
>
> `Object.assign()`方法实行的是浅拷贝，而不是深拷贝。如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。
>
> ```js
> const obj1 = {a: {b: 1}};
> const obj2 = Object.assign({}, obj1);
> 
> obj1.a.b = 2;
> obj2.a.b // 2
> ```
>
> (2)同名属性的替换 ⭐️
>
> 对于嵌套的对象，一旦遇到同名属性，`Object.assign()`将会替换，而不是添加。
>
> ```js
> const target = { a: { b: 'c', d: 'e' } }
> const source = { a: { b: 'hello' } }
> Object.assign(target, source); // { a: { b: 'hello' } }
> ```
>
> (3)数组的处理
>
> `Object.assign()`可以用来处理数组，但是会把数组视为对象。最终得到的结果话是数组。
>
> ```js
> Object.assign([1, 2, 3], [4, 5]); // [4, 5, 3]
> ```
>
> `Object.assign()`把数组视为属性名为 0、1、2 的对象，因此源数组的 0 号属性`4`覆盖了目标数组的 0 号属性`1`。
>
> (4)取值函数的处理 ⭐️ ？
>
> `Object.assign()`只能进行值的复制，如果要复制的值是一个取值函数，那么将求值后再复制。
>
> ```js
> const source = {
>   get foo() { return 1 }
> };
> const target = {};
> 
> Object.assign(target, source); // { foo: 1 }
=======
> 1.由于解构赋值要求等号右边是一个对象，所以如果等号右边是`undefined`或`null`，就会报错，因为它们无法转为对象。
>
> ```js
> let { ...z } = null; // Uncaught TypeError: Cannot destructure 'null' as it is null.
> let { ...z } = undefined; // Uncaught TypeError: Cannot destructure 'undefined' as it is undefined.
> ```
>
> 2.解构赋值必须是最后一个参数，否则会报错。
>
> ```js
> let { ...x, y, z } = someObject; // Uncaught SyntaxError: Rest element must be last element
> ```
>
> 3.解构赋值的拷贝是浅拷贝，即如果一个键的值是复合类型的值（数组、对象、函数）、那么解构赋值拷贝的是这个值的引用，而不是这个值的副本。(之前讲过)。
>
> ```js
> let obj = { a: { b: 1 } };
> let { ...x } = obj;
> obj.a.b = 2;
> x.a.b // 2
> ```
>
> `x`是解构赋值所在的对象，拷贝了对象`obj`的`a`属性。`a`属性引用了一个对象，修改这个对象的值，会影响到解构赋值对它的引用。
>
> 4.扩展运算符的解构赋值，不能复制继承自原型对象的属性。
>
> ```js
> let o1 = { a: 1 };
> let o2 = { b: 2 };
> o2.__proto__ = o1;
> let { ...o3 } = o2;
> o3 // { b: 2 }
> o3.a // undefined
> ```
>
> 对象`o3`复制了`o2`，但是只复制了`o2`自身的属性，没有复制它的原型对象`o1`的属性。
>
> ```js
> const o = Object.create({ x: 1, y: 2 });
> o.z = 3;
> 
> let { x, ...newObj } = o;
> let { y, z } = newObj;
> x // 1
> y // undefined
> z // 3
> ```
>
> 变量`x`是单纯的解构赋值，所以可以读取对象`o`继承的属性；变量`y`和`z`是扩展运算符的解构赋值，只能读取对象`o`自身的属性，所以变量`z`可以赋值成功，变量`y`取不到值。ES6 规定，变量声明语句之中，如果使用解构赋值，扩展运算符后面必须是一个变量名，而不能是一个解构赋值表达式，所以上面代码引入了中间变量`newObj`，如果写成下面这样会报错。
>
> ```js
> let { x, ...{ y, z } } = o;
> // Uncaught SyntaxError: `...` must be followed by an identifier in declaration contexts
>>>>>>> 10d184d5453f6075f3ffc1c49b19a7be4265e6a3
> ```
>
> 

<<<<<<< HEAD
#### 常见用途

1.为对象添加属性

```js
class Point {
  constructor(x, y) {
    // 将x属性和y属性添加到Point类的对象实例
    Object.assign(this, {x, y}); 
  }
}
```

2.为对象添加方法

```js
Object.assign(SomeClass.prototype, {
  someMethod(arg1, arg2) {
    ···
  },
  anotherMethod() {
    ···
  }
});

// 等同于
SomeClass.prototype.someMethod = function (arg1, arg2) {
  ···
};
SomeClass.prototype.anotherMethod = function () {
  ···
};
```

3.克隆对象

```js
function clone(origin) {
  return Object.assign({}, origin);
}
```

上述方法只能克隆原始对象自身的值，不能克隆它继承的值。如果想要保持继承链，可以采用下面的代码。

```js
function clone(origin) {
  let originProto = Object.getPrototypeOf(origin);
  return Object.assign(Object.create(originProto), origin);
}
```

4.合并多个对象

将多个对象合并到某个对象。

```js
const merge =
  (target, ...sources) => Object.assign(target, ...sources);
```

合并后返回一个新对象。

```js
const merge =
  (...sources) => Object.assign({}, ...sources);
```

5.为属性指定默认值

```js
const DEFAULTS = {
  logLevel: 0,
  outputFormat: 'html'
};

function processContent(options) {
  options = Object.assign({}, DEFAULTS, options);
  console.log(options);
  // ...
}

processContent({name: 'ramona'});
```

注意点：浅拷贝的问题，对象中不要嵌套对象。

## Object.getOwnPropertyDescriptors()

**作用**

* `Object.getOwnPropertyDescriptor()`方法：会返回某个对象属性的描述对象（descriptor）。（ES5）
* `Object.getOwnPropertyDescriptors()`方法：返回指定对象所有自身属性（非继承属性）的描述对象。（ES2017）

```js
const obj = {
  foo: 123,
  get bar() { return 'abc' }
};

Object.getOwnPropertyDescriptor(obj11, 'foo');
// { value: 123, writable: true, enumerable: true,  configurable: true }

Object.getOwnPropertyDescriptors(obj);
/**
{
  bar: {
    configurable: true,
    enumerable: true,
    get: [Function: get bar],
    set: undefined
  },
  foo: {
  	configurable: true,
    enumerable: true,
    value: 123,
    writable: true
  }
}
*/
```

`Object.getOwnPropertyDescriptors()`方法的实现。

```js
function getOwnPropertyDescriptors(obj) {
  const result = {};
  for (let key of Reflect.ownKeys(obj)) {
    result[key] = Object.getOwnPropertyDescriptor(obj, key);
  }
  return result;
}
```

**用途**

1.该方法为了解决`Object.assign()`无法正确拷贝`get`属性和`set`属性的问题。

```js
const source1 = {
  set foo(value) {
    console.log(value);
  }
};

const target1 = {};
Object.assign(target1, source1); // {foo: undefined}
Object.getOwnPropertyDescriptor(target1, 'foo');
// { value: undefined, writable: true, enumerable: true, configurable: true }
```

`source1`对象的`foo`属性的值是一个赋值函数,`Object.assign`方法总是拷贝一个属性的值，而不会拷贝它背后的赋值方法或取值方法,导致该属性的值变成了`undefined`。

当使用`Object.getOwnPropertyDescriptors()`方法配合`Object.defineProperties()`方法，就可以实现正确拷贝。

```js
const source2 = {
  set foo(value) {
    console.log(value);
  }
};
const target2 = {};

Object.defineProperties(target2, Object.getOwnPropertyDescriptors(source2));
Object.getOwnPropertyDescriptor(target2, 'foo');
// { get: undefined, set: [Function: set foo], enumerable: true, configurable: true }

// 等同于
const shallowMerge = (target, source) => Object.defineProperties(
  target,
  Object.getOwnPropertyDescriptors(source)
);
Object.getOwnPropertyDescriptor(shallowMerge(target2, source2), 'foo');
```

2.配合`Object.create()`方法，将对象属性克隆到一个新对象。这属于浅拷贝。

```js
const clone = Object.create(Object.getPrototypeOf(obj),
  Object.getOwnPropertyDescriptors(obj));

// 或者

const shallowClone = (obj) => Object.create(
  Object.getPrototypeOf(obj),
  Object.getOwnPropertyDescriptors(obj)
);
```

3.实现一个对象继承另一个对象。

```js
// ES6 之前继承对象写法
const obj = {
  __proto__: prot,
  foo: 123,
};

// ES6 规定__proto__只有浏览器要部署，其他环境不用部署
const obj = Object.create(prot);
obj.foo = 123;

// 或者
const obj = Object.assign(Object.create(prot),{foo: 123});

// 使用Object.getOwnPropertyDescriptors()
const obj = Object.create(
  prot,
  Object.getOwnPropertyDescriptors({
    foo: 123,
  })
);
```

4.实现 Mixin（混入）模式。

```js
let mix = (object) => ({
  with: (...mixins) => mixins.reduce(
    (c, mixin) => Object.create(
      c, Object.getOwnPropertyDescriptors(mixin)
    ), object)
});

// multiple mixins example
let a = {a: 'a'};
let b = {b: 'b'};
let c = {c: 'c'};
let d = mix(c).with(a, b);

d.c // "c"
d.b // "b"
d.a // "a"
```
### `__proto__	`属性，Object.setPrototypeOf()，Object.getPrototypeOf() 

#### `__proto__`属性

用来读取或设置当前对象的原型对象（prototype）。目前，所有浏览器（包括 IE11）都部署了这个属性。该属性本质上是一个内部属性，最好不要使用。

```js
// es5 的写法
const obj = {
  method: function() { ... }
};
obj.__proto__ = someOtherObj;

// es6 的写法
var obj = Object.create(someOtherObj);
obj.method = function() { ... };
```

`__proto__`调用的是`Object.prototype.__proto__`，具体实现如下。

```js
Object.defineProperty(Object.prototype, '__proto__', {
  get() {
    let _thisObj = Object(this);
    return Object.getPrototypeOf(_thisObj);
  },
  set(proto) {
    if (this === undefined || this === null) {
      throw new TypeError();
    }
    if (!isObject(this)) {
      return undefined;
    }
    if (!isObject(proto)) {
      return undefined;
    }
    let status = Reflect.setPrototypeOf(this, proto);
    if (!status) {
      throw new TypeError();
    }
  },
});

function isObject(value) {
  return Object(value) === value;
}
```

如果一个对象本身部署了`__proto__`属性，该属性的值就是对象的原型。

```js
Object.getPrototypeOf({ __proto__: null }); // null
```

#### Object.setPrototypeOf() 

**作用**：与`__proto__`相同，用来设置一个对象的原型对象（prototype），返回参数对象本身。它是 ES6 正式推荐的设置原型对象的方法。

**语法**：`Object.setPrototypeOf(object, prototype);`

```js
// 示例1
const o = Object.setPrototypeOf({}, null);
// 方法等同于
function setPrototypeOf(obj, proto) {
  obj.__proto__ = proto;
  return obj;
}

// 示例2
let proto = {};
let obj = { x: 10 };
Object.setPrototypeOf(obj, proto);

proto.y = 20;
proto.z = 40;

obj.x // 10
obj.y // 20
obj.z // 40
// 将proto对象设为obj对象的原型，所以从obj对象可以读取proto对象的属性
```

> 注意点
>
> 1.如果第一个参数不是对象，会自动转为对象。但是由于返回的还是第一个参数，所以这个操作不会产生任何效果。
>
> ```js
> Object.setPrototypeOf(1, {}) === 1 // true
> Object.setPrototypeOf('foo', {}) === 'foo' // true
> Object.setPrototypeOf(true, {}) === true // true
> ```
>
> 2.由于`undefined`和`null`无法转为对象，所以如果第一个参数是`undefined`或`null`，就会报错。
>
> ```js
> Object.setPrototypeOf(undefined, {})
> // TypeError: Object.setPrototypeOf called on null or undefined
> 
> Object.setPrototypeOf(null, {})
> // TypeError: Object.setPrototypeOf called on null or undefined
> ```

#### Object.getPrototypeOf()

**作用**：该方法与`Object.setPrototypeOf`方法配套，用于读取一个对象的原型对象。

**语法**：`Object.getPrototypeOf(obj);`

```js
function Rectangle() {
  // ...
}

const rec = new Rectangle();

Object.getPrototypeOf(rec) === Rectangle.prototype; // true

Object.setPrototypeOf(rec, Object.prototype);
Object.getPrototypeOf(rec) === Rectangle.prototype; // false
```

> 注意点
>
> 1.如果参数不是对象，会被自动转为对象。
>
> ```js
> // 等同于 Object.getPrototypeOf(Number(1))
> Object.getPrototypeOf(1);
> // Number {[[PrimitiveValue]]: 0}
> 
> // 等同于 Object.getPrototypeOf(String('foo'))
> Object.getPrototypeOf('foo');
> // String {length: 0, [[PrimitiveValue]]: ""}
> 
> // 等同于 Object.getPrototypeOf(Boolean(true))
> Object.getPrototypeOf(true);
> // Boolean {[[PrimitiveValue]]: false}
> 
> Object.getPrototypeOf(1) === Number.prototype; // true
> Object.getPrototypeOf('foo') === String.prototype; // true
> Object.getPrototypeOf(true) === Boolean.prototype; // true
> ```
>
> 2.如果参数是`undefined`或`null`，它们无法转为对象，所以会报错。
>
> ```js
> Object.getPrototypeOf(null);
> // TypeError: Cannot convert undefined or null to object
> 
> Object.getPrototypeOf(undefined);
> // TypeError: Cannot convert undefined or null to object
> ```
>
> 
=======
##### 应用

扩展某个函数的参数，引入其他操作。

```js
function baseFunction({ a, b }) {
  // ...
}
function wrapperFunction({ x, y, ...restConfig }) {
  // 使用 x 和 y 参数进行操作
  // 其余参数传给原始函数
  return baseFunction(restConfig);
}
```

原始函数`baseFunction`接受`a`和`b`作为参数，函数`wrapperFunction`在`baseFunction`的基础上进行了扩展，能够接受多余的参数，并且保留原始函数的行为。(有点像Java中面向对象中的代理？)

#### 扩展运算符

1.对象的扩展运算符（`...`）用于取出参数对象的所有可遍历属性，拷贝到当前对象之中。

```js
let z = { a: 3, b: 4 };
let n = { ...z };
n // { a: 3, b: 4 }
```

> **注意点**
>
> (1).扩展运算符后面是数组，可获得对象（数组是特殊的对象）。
>
> ```js
> let foo = { ...['a', 'b', 'c'] };
> foo // {0: "a", 1: "b", 2: "c"}
> ```
>
> (2)扩展运算符后面是一个空对象，则没有任何效果。
>
> ```js
> {...{}, a: 1} // { a: 1 }
> ```
>
> (3)扩展运算符后面不是对象，则会自动将其转为对象。
>
> ```JS
> // 运算符后面的 value 是数字、布尔值、undefined、null，等同于 {...Object(value)}，返回空对象
> {...1} // {} 
> {...true} // {}
> {...undefined} // {}
> {...null} // {}
> 
> 
> // 运算符后面的 value 是字符串，会自动转成一个类似数组的对象
> {...'hello'} // {0: "h", 1: "e", 2: "l", 3: "l", 4: "o"}
> ```
>
> 

2.对象的扩展运算符等同于使用`Object.assign()`方法。

```js
const a = {x: 1, y: 2};
let aClone = { ...a };
// 等同于
let aClone = Object.assign({}, a);
```

上例只是拷贝了对象实例的属性，如果想完整克隆一个对象，还拷贝对象原型的属性，可以采用下面的写法。

```js
let testObj = {x: 1, y: 2};
const p = {
  foo: 'foo', 
  find() { 
    return this.foo; 
  }
};
Object.setPrototypeOf(testObj, p);
testObj.__proto__; // {foo: "foo", find: ƒ}

// 写法一
const clone1 = {
  __proto__: Object.getPrototypeOf(testObj),
  ...testObj
};
clone1 // {_proto_: {…}, x: 1, y: 2}

// 写法二
const clone2 = Object.assign(
  Object.create(Object.getPrototypeOf(testObj)),
  testObj
);

// 写法三
const clone3 = Object.create(
  Object.getPrototypeOf(testObj),
  Object.getOwnPropertyDescriptors(testObj)
)
```

写法一的`__proto__`属性在非浏览器的环境不一定部署，因此推荐使用写法二和写法三。

3.扩展运算符可以用于合并两个对象。

```javascript
const aObj = {x: 1};
const bObj = {y: 2};
let ab = { ...aObj, ...bObj };
// 等同于
let ab = Object.assign({}, aObj, bObj);
```

4.如果用户自定义的属性，放在扩展运算符后面，则扩展运算符内部的同名属性会被覆盖掉。

```js
const a = {x: 3};
let aWithOverrides = { ...a, x: 1, y: 2 }; // {x: 1, y: 2} 
// 等同于
let aWithOverrides = { ...a, ...{ x: 1, y: 2 } };
// 等同于
let x = 1, y = 2, aWithOverrides = { ...a, x, y };
// 等同于
let aWithOverrides = Object.assign({}, a, { x: 1, y: 2 });
```

可应用于修改现有对象部分的属性。

```js
const previousVersion = {
  name: 'old name',
  age: 18
}
let newVersion = {
  ...previousVersion,
  name: 'new name' // Override the name property
};
// {name: "New Name", age: 18}
```

5.如果把自定义属性放在扩展运算符前面，就变成了设置新对象的默认属性值。

```js
const a = {x: 3};
let aWithDefaults = { x: 1, y: 2, ...a }; // {x: 3, y: 2}
// 等同于
let aWithDefaults = Object.assign({}, { x: 1, y: 2 }, a);
// 等同于
let aWithDefaults = Object.assign({ x: 1, y: 2 }, a);

```

6.与数组的扩展运算符一样，对象的扩展运算符后面可以跟表达式。

```js
const d = 2;
const obj = {
  ...(d > 1 ? {a: 1} : {}),
  b: 2,
};
// {a: 1, b: 2}
```

7.扩展运算符的参数对象之中，如果有取值函数`get`，这个函数是会执行的。

```js
let paramObj = {
  get x() {
    throw new Error('not throw yet');
  }
}

let paramObj1 = {
  get x() {
    return 'hello';
  }
}

let aWithXGetter = { ...paramObj }; // Uncaught Error: not throw yet at Object.get x [as x]
let aWithXGetter1 = { ...paramObj1 }; // {x: 'hello'}
```

取值函数`get`在扩展`a`对象时会自动执行，导致报错。


>>>>>>> 10d184d5453f6075f3ffc1c49b19a7be4265e6a3

### Object.keys()，Object.values()，Object.entries()

#### Object.keys() (ES5)

返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键名。

```js
var obj = { foo: 'bar', baz: 42 };
Object.keys(obj); // ["foo", "baz"]
```

ES2017 [引入](https://github.com/tc39/proposal-object-values-entries)了跟`Object.keys`配套的`Object.values`和`Object.entries`，作为遍历一个对象的补充手段，供`for...of`循环使用。

```js
let {keys, values, entries} = Object;
let obj = { a: 1, b: 2, c: 3 };

for (let key of keys(obj)) {
  console.log(key); // 'a', 'b', 'c'
}

for (let value of values(obj)) {
  console.log(value); // 1, 2, 3
}

for (let [key, value] of entries(obj)) {
  console.log([key, value]); // ['a', 1], ['b', 2], ['c', 3]
}
```

#### Object.values()

返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值。

```js
const obj = { 100: 'a', 2: 'b', 7: 'c' };
Object.values(obj); // ["b", "c", "a"]
```

`Object.values`只返回对象自身的可遍历属性。

```js
const obj = Object.create({}, {p: {value: 42}});
Object.values(obj); // []
```

上面代码中，`Object.create`方法的第二个参数添加的对象属性（属性`p`），如果不显式声明，默认是不可遍历的，因为`p`的属性描述对象的`enumerable`默认是`false`，`Object.values`不会返回这个属性。只要把`enumerable`改成`true`，`Object.values`就会返回属性`p`的值。

```js
const obj = Object.create({}, {p:
  {
    value: 42,
    enumerable: true
  }
});
Object.values(obj) // [42]
```

`Object.values`会过滤属性名为 Symbol 值的属性。

```js
Object.values({ [Symbol()]: 123, foo: 'abc' }); // ['abc']
```

如果参数不是对象，`Object.values`会先将其转为对象。如果`Object.values`方法的参数是一个字符串，会返回各个字符组成的一个数组。由于数值和布尔值的包装对象，都不会为实例添加非继承的属性。所以，`Object.values`会返回空数组。

```js
Object.values('foo'); // ['f', 'o', 'o']
Object.values(42) // []
Object.values(true) // []
```

#### Object.entries()

返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值对数组。

```js
const obj = { foo: 'bar', baz: 42 };
Object.entries(obj); // [ ["foo", "bar"], ["baz", 42] ]
```

如果原对象的属性名是一个 Symbol 值，该属性会被忽略。

```js
Object.entries({ [Symbol()]: 123, foo: 'abc' }); // [ [ 'foo', 'abc' ] ]
```

**用途**

1.遍历对象的属性。

```js
let obj = { one: 1, two: 2 };
for (let [k, v] of Object.entries(obj)) {
  console.log(
    `${JSON.stringify(k)}: ${JSON.stringify(v)}`
  );
}
// "one": 1
// "two": 2
```

2.将对象转为真正的`Map`结构。

```js
const obj = { foo: 'bar', baz: 42 };
const map = new Map(Object.entries(obj));
map // Map { foo: "bar", baz: 42 }
```

**实现**

```js
// Generator函数的版本
function* entries(obj) {
  for (let key of Object.keys(obj)) {
    yield [key, obj[key]];
  }
}

// 非Generator函数的版本
function entries(obj) {
  let arr = [];
  for (let key of Object.keys(obj)) {
    arr.push([key, obj[key]]);
  }
  return arr;
}
```

#### Object.fromEntries()

该方法是`Object.entries()`的逆操作，用于将一个键值对数组转为对象。

```js
Object.fromEntries([
  ['foo', 'bar'],
  ['baz', 42]
])
// { foo: "bar", baz: 42 }
```

该方法可将键值对的数据结构还原为对象，因此特别适合将 Map 结构转为对象。

```js
// 示例1
const entries = new Map([
  ['foo', 'bar'],
  ['baz', 42]
]);

Object.fromEntries(entries); // { foo: "bar", baz: 42 }

// 示例2
const map = new Map().set('foo', true).set('bar', false);
Object.fromEntries(map); // { foo: true, bar: false }
```

该方法还可以配合`URLSearchParams`对象，将查询字符串转为对象。

```js
Object.fromEntries(new URLSearchParams('foo=bar&baz=qux')); // { foo: "bar", baz: "qux" }
```

## `for-of`和`for-in`的区别

#### 1、`for-of`

遍历可迭代的对象（部署了`Symbol.iterator`），包括 `Array`，`Map`，`Set`，`String`，`arguments`等，遍历的是每个数组的值。

示例代码：

```js
  const arr = [1, 2, 'a'];
	for (let value of arr) {
    console.log(value);
  }
	// 输出： 1 2 a
```

#### 2、`for-in`

遍历一个对象的可枚举属性

> - 不可遍历Symbol属性
> - 可以遍历到原型上的属性
> - 顺序是任意的,不固定的

示例代码：

```js
    const data = {
        [Symbol.for('a')]: 'a',
        name: 'name',
        '2': '2',
        '1': '1',
        b: 'b',
        a: 'a'
    }
    data.__proto__.sex = 'sex'
    for (const k in data) {
        console.log(k) // 1 2 name b a sex
    }
```

#### 3、总结

##### for-of

- 通常用于遍历`数组对象`,不可遍历`普通对象`
- 只能遍历可迭代的对象（部署了`Symbol.iterator`

##### for-in

- 不可遍历Symbol属性
- 为普通对象设计的，遍历顺序是任意的,数组索引顺序很重要,不适用于数组遍历
- 遍历出来的key是string类型
- 可以遍历到原型上的属性



## `instanceof`的原理

[![img](https://camo.githubusercontent.com/9996f26d7a4e9df5ab87a962689d42de99943855/687474703a2f2f696d672e736d79687661652e636f6d2f32303138303330365f323230392e706e67)](https://camo.githubusercontent.com/9996f26d7a4e9df5ab87a962689d42de99943855/687474703a2f2f696d672e736d79687661652e636f6d2f32303138303330365f323230392e706e67)

`instanceof`的**作用**：用于判断**实例**属于哪个**构造函数**。

`instanceof`的**原理**：判断实例对象的`__proto__`属性，和构造函数的`prototype`属性，是否为同一个引用（是否指向同一个地址）。

**注意1**：虽然说，实例是由构造函数 new 出来的，但是实例的`__proto__`属性引用的是构造函数的`prototype`。也就是说，实例的`__proto__`属性与构造函数本身无关。

**注意2**：在原型链上，原型的上面可能还会有原型，以此类推往上走，继续找`__proto__`属性。这条链上如果能找到， instanceof 的返回结果也是 true。

比如说：

- `foo instance of Foo`的结果为true，因为`foo.__proto__ === Foo.prototype`为true。
- **`foo instance of Objecet`的结果也为true**，因为`Foo.prototype.__proto__ === Object.prototype`为true。

但我们不能轻易的说：`foo 一定是 由Object创建的实例`。这句话是错误的。我们来看下一个问题就明白了。

## 判断对象是哪个类的直接实例

> 使用`对象.construcor`直接可判断

**问题：已知A继承了B，B继承了C。怎么判断 a 是由A直接生成**的实例，还是B直接生成的实例呢？还是C直接生成的实例呢？

分析：这就要用到原型的`constructor`属性了。

- `foo.__proto__.constructor === Foo`的结果为true，但是 `foo.__proto__.constructor === Object`的结果为false。

所以，**用 consturctor判断就比用 instanceof判断，更为严谨**。

## BOM

*概念*：`BOM（Brower Object Model）`是浏览器对象模型。主要处理浏览器窗口和框架，描述与浏览器进行交互的方法和接口，可以对浏览器窗口进行访问和操作，比如弹出新的窗口、回退历史记录等。

## DOM
*概念*：通俗的说DOM就是浏览器为JavaScript提供的一系列接口（通过window,document提供的），通过这些接口我们可以操作web页面。比如让盒子移动、变色、轮播图等。DOM不是编程语言，是文档对象模型，该模型独立于编程语言。

> API (web 或 XML 页面) = DOM + JS (脚本语言)

#### 节点

**节点**（Node）：构成 HTML 网页的最基本单元。网页中的每一个部分都可以称为是一个节点，比如：html标签、属性、文本、注释、整个文档等都是一个节点。

虽然都是节点，但是实际上他们的具体类型是不同的。常见节点分为四类：

- 文档节点（文档）：整个 HTML 文档。整个 HTML 文档就是一个文档节点。

- 元素节点（标签）：HTML标签。

- 属性节点（属性）：元素的属性。

- 文本节点（文本）：HTML标签中的文本内容（包括标签之间的空格、换行）。

节点的类型不同，属性和方法也都不尽相同。**所有的节点都是Object**。

#### DOM解析过程

HTML加载完毕，渲染引擎会在内存中把HTML文档，生成一个DOM树，getElementById是获取内中DOM上的元素节点。然后操作的时候修改的是该元素的**属性**。**在HTML当中，一切都是节点**。

#### DOM操作

##### 1、创建新节点

| 方法                     | 描述               |
| ------------------------ | ------------------ |
| createDocumentFragment() | 创建一个 DOM 片段  |
| createElement()          | 创建一个具体的元素 |
| createTextNode()         | 创建一个文本节点   |

##### 2、修改节点

| 方法                  | 描述                                                         |
| --------------------- | ------------------------------------------------------------ |
| appendChild()         | 添加子元素                                                   |
| removeChild()         | 删除子元素                                                   |
| replaceChild()        | 替换子元素                                                   |
| insertBefore()        | 插入子元素                                                   |
| cloneNode(true/false) | 复制/克隆节点（不带参数/带参数false：只复制节点本身，不复制子节点；带参数true：既复制节点本身，也复制其所有的子节点） |

##### 3、DOM查找

###### 元素节点的获取

| 方法                     | 描述                                                         |
| ------------------------ | ------------------------------------------------------------ |
| getElementsByTagName()   | 通过标签名获取元素节点数组                                   |
| getElementsByName()      | 通过元素的 Name 属性的值(IE 容错能力较强，会得到一个数组，其中包括 id 等于 name 值的) |
| getElementById()         | 通过元素 Id获取一个元素节点，唯一性                          |
| querySelector()          | 通过类名或元素Id (JQuery方法)                                |
| getElementsByClassName() | 通过类名获取元素节点数组                                     |

###### 访问关系的获取

JS中的**父子兄**访问关系：

![](http://img.smyhvae.com/20180126_2145.png)

| 属性                                                  | 描述                                                         |
| ----------------------------------------------------- | ------------------------------------------------------------ |
| 节点.parentNode                                       | 获取父节点                                                   |
| 节点.nextElementSibling \|\| 节点.nextSibling         | 获取下一个兄弟节点                                           |
| 节点.previousElementSibling \|\| 节点.previousSibling | 获取前一个兄弟节点                                           |
| 节点自己.parentNode.children[index]                   | 获得任意一个兄弟节点                                         |
| 节点.firstElementChild \|\| 节点.firstChild           | 第一个子元素节点                                             |
| 节点.lastElementChild \|\| 节点.lastChild             | 最后一个子元素节点                                           |
| 父节点.childNodes                                     | 获取所有子节点（标准属性。返回的是指定元素的**子节点**的集合） |
| 父节点.children                                       | 获取所有子节点（非标准属性。返回的是指定元素的**子元素节点**的集合） |

##### 4、属性操作

| 属性/方法                            | 描述             |
| ------------------------------------ | ---------------- |
| 元素节点.属性名  /  元素节点[属性名] | 获取节点的属性值 |
| getAttribute()                       | 获取属性         |
| setAttribute()                       | 设置属性         |
| hasAttribute()                       | 判断属性         |
| removeAttribute()                    | 移除属性         |
| hasAttributes()                      | 是否有属性设置   |

###### nodeType属性

- **nodeType == 1  表示的是元素节点**（标签）。

- nodeType == 2  表示是属性节点。

- nodeType == 3  是文本节点。

###### nodeType、nodeName、nodeValue

我们那下面这个标签来举例：

```html
<div id="box" value="111">hello</div>
```

上面这个标签就包含了三种节点：

- 元素节点（标签）

- 属性节点

- 文本节点

获取这三个节点的方式如下：

```javascript
var element = document.getElementById("box"); // 获取元素节点（标签）
var attributeNode = element.getAttributeNode("id"); //获取box的属性节点
var textNode = element.firstChild; // 获取box的文本节点
console.log(element); // <div id="box" value="111">hello</div>
console.log(attributeNode); // id="box"
console.log(textNode); // "hello"
```

既然这三个都是节点，要想获取它们的nodeType、nodeName、nodeValue，代码如下：

```javascript
var element = document.getElementById("box"); // 获取元素节点（标签）
var attributeNode = element.getAttributeNode("id"); //获取box的属性节点
var textNode = element.firstChild; // 获取box的文本节点
console.log(element); // <div id="box" value="111">hello</div>
console.log(attributeNode); // id="box"
console.log(textNode); // "hello"
// 获取nodeType
console.log(element.nodeType); //1
console.log(attributeNode.nodeType); //2
console.log(textNode.nodeType); //3
// 获取nodeName
console.log(element.nodeName); //DIV
console.log(attributeNode.nodeName); //id
console.log(textNode.nodeName); // #text
// 获取nodeValue
console.log(element.nodeValue); //null
console.log(attributeNode.nodeValue); //box
console.log(textNode.nodeValue); // hello
```


### BOM对象

* `Window JavaScript`层级中的顶层对象，表示浏览器窗口。同时 window 也是网页中的全局对象。
* `Navigator`代表当前浏览器的信息，通过该对象可以识别不同的浏览器。
* `History`包含了浏览器窗口访问过的URL。
* `Location`包含了当前URL的信息，通过 Location 可以获取地址栏信息，或者操作浏览器跳转页面。
* `Screen`包含客户端显示屏的信息。

> *注意*：这些 BOM 对象都是作为 window 对象的属性保存的，可以通过window对象来使用，也可以直接使用。比如，可以使用 `window.location.href`，也可以直接使用 `location.href`，二者是等价的。`document`也是在`window`中保存的

### 常用的对象

#### 1、navigator

**一般我们只会使用`navigator.userAgent`来获取浏览器的信息**。

代码示例如下：

```js
<script>
        // 获取当前浏览器的userAgent
        var UA = navigator.userAgent;
        console.log('ramona当前浏览器的userAgent为：' + UA);

        if (/firefox/i.test(UA)) {
            alert('火狐浏览器');
        } else if (/chrome/i.test(UA)) {
            alert('Chrome浏览器');
        } else if (/msie/i.test(UA)) {
            alert('IE浏览器');
        } else if ('ActiveXObject' in window) {
            alert('IE 11 浏览器');
        }
    </script>
```

我们也可以直接在浏览器控制台上输入：`navigator.userAgent`，查看到如下结果：

![](https://tva1.sinaimg.cn/large/007S8ZIlly1gi31zliw3mj31fy01yt9f.jpg)

另外我们还可以在电脑上模拟移动端浏览器的场景。如下图方式操作：

![image-20200825145607744](https://tva1.sinaimg.cn/large/007S8ZIlly1gi32c3j4r2j327y0lgdu6.jpg)

![image-20200825150103566](https://tva1.sinaimg.cn/large/007S8ZIlly1gi32h8fbzuj327o0fwdjt.jpg)

> ##### 不同浏览器的userAgent
>
> *iOS 版微信浏览器：*
>
> Mozilla/5.0 (iPhone; CPU iPhone OS 9_3 like Mac OS X) AppleWebKit/601.1.46 (KHTML, like Gecko) Mobile/13E233 MicroMessenger/6.3.15 NetType/WIFI Language/zh_CN
>
> *Android 版微信浏览器：*
>
> Mozilla/5.0 (Linux; Android 5.0.1; GT-I9502 Build/LRX22C; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/43.0.2357.121 Mobile Safari/537.36 MicroMessenger/6.1.0.78_r1129455.543 NetType/WIFI
>
> *iOS 版本QQ浏览器：*
>
> Mozilla/5.0 (iPhone; CPU iPhone OS 11_2_2 like Mac OS X) AppleWebKit/604.4.7 (KHTML, like Gecko) Mobile/15C202 QQ/7.3.5.473 V1_IPH_SQ_7.3.5_1_APP_A Pixel/1125 Core/UIWebView Device/Apple(iPhone X) NetType/WIFI QBWebViewType/1
>
> *Android 版 QQ浏览器：*
>
> Mozilla/5.0 (Linux; Android 4.4.2; PE-TL20 Build/HuaweiPE-TL20; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/57.0.2987.132 MQQBrowser/6.2 TBS/043807 Mobile Safari/537.36 V1_AND_SQ_7.3.2_762_YYB_D QQ/7.3.2.3350 NetType/WIFI WebP/0.3.0 Pixel/1080

#### 2、History

History对象可以用来操作浏览器的向前或向后翻页。

常见的属性和方法代码示例如下：

```js
history.length  // 获取浏览器历史列表中的 url 数量,只是统计当次的数量，如果浏览器关了，数量会重置为1
history.back();  // 用来回退到上一个页面，作用和浏览器的「回退按钮」一样
history.forward();  // 用来跳转下一个页面，作用和浏览器的「前进按钮」一样
history.go( 1 ); // 向前跳转一个页面，相当于 history.forward()
history.go( 0 ); // 刷新当前页面
history.go( -1 ); // 向后跳转一个页面，相当于 history.back()
```

#### 3、Location

Location 对象封装了浏览器地址栏的 URL 信息。

常见的属性和方法代码示例如下：

```js
console.log(location.href); // 获取当前页面的url 路径
location.href = 'www.baidu.com'; // 跳转到指定的页面链接。通俗理解就是：跳转到其他的页面
location.assign(str);
location.reload(); // 重新加载当前页面
location.reload(true); // 在方法的参数中传递一个true，则会强制清空缓存刷新页面
 location.replace(); // 使用一个新的页面替换当前页面，调用完毕也会跳转页面。但不会生成历史记录，不能使用「后退按钮」后退
```

> 详情参考：[https://github.com/huyaocode/webKnowledge/blob/master/%E5%89%8D%E7%AB%AF%E5%9F%BA%E7%A1%80/JS/BOM.md](https://github.com/huyaocode/webKnowledge/blob/master/前端基础/JS/BOM.md)


## DOM事件

> DOM事件的级别
>
> DOM事件模型
>
> DOM事件流
>
> DOM事件捕获的具体流程
>
> Event对象的常见应用（Event的常用api方法）
>
> 自定义事件
>
> 事件委托

![截屏2020-09-0201.21.05](https://tva1.sinaimg.cn/large/007S8ZIlly1gibnr17bgkj30u10u0ag9.jpg)

### DOM事件的级别

DOM事件的级别，准确来说，是**DOM标准**定义的级别。包括：

#### DOM0

```js
element.onclick = function () { 
  // TODO  
}
```

#### DOM2

##### addEventListener（高版本浏览器）

```js
element.addEventListener('click', function () { 
	// TODO
}, false);
```

参数解释：

- 参数1：事件名的字符串(注意，没有on)
- 参数2：回调函数：当事件触发时，该函数会被执行
- 参数3：**true表示捕获阶段触发，false表示冒泡阶段触发（默认）**。如果不写，则默认为false。

###### 示例：

```js
<body>
<button>按钮</button>
<script>
    var btn = document.getElementsByTagName("button")[0];
    btn.addEventListener("click", fn1);
    btn.addEventListener("click", fn2);
    function fn1() {
        console.log("事件1");
    }
    function fn2() {
        console.log("事件2");
    }
</script>
</body>
// 输出： 事件1
//       事件2
```

`addEventListener()`这种绑定事件的方式：

- 一个元素的一个事件，可以绑定多个响应函数。**不存在响应函数被覆盖的情况**。执行顺序是：**事件被触发时，响应函数会按照函数的绑定顺序执行。**

- **addEventListener()中的this，是绑定事件的对象**。
- `addEventListener()`不支持 IE8 及以下的浏览器。在IE8中可以使用`attachEvent`来绑定事件。

##### attachEvent（IE8及以下版本浏览器）

```javascript
element.attachEvent('onclick', function () {
	// TODO
});
```

参数解释：

- 参数1：事件名的字符串(注意，有on)

- 参数2：回调函数：当事件触发时，该函数会被执行

###### 示例：

```js
<body>
  <button>按钮</button>
<script>
  var btn = document.getElementsByTagName('button')[0];
  btn.attachEvent('onclick', function() {
    console.log('事件1');
  });

  btn.attachEvent('onclick', function() {
    console.log('事件2');
  });
</script>
</body>
// 输出：事件2
//      事件1
```

`attachEvent()`这种绑定事件的方式：

- 一个元素的一个事件，可以绑定多个响应函数。**不存在响应函数被覆盖的情况**。**注意**：执行顺序是，后绑定的先执行。

- **attachEvent()中的this，是window**。

##### 兼容代码

```js
<body>
  <button>按钮</button>
<script>
  var btn = document.getElementsByTagName('button')[0];
  myBind(btn , "click" , function(){
    alert(this);
  });

  /*
  * 参数：
  *  element 要绑定事件的对象
  *  eventStr 事件的字符串(不要on)
  *  callback 回调函数
  */
  function myBind(element , eventStr , callback){
    if(element.addEventListener){
      //大部分浏览器兼容的方式
      element.addEventListener(eventStr , callback , false);
    }else{
      //IE8及以下
      element.attachEvent("on"+eventStr , function(){
        //在匿名函数 function 中调用回调函数callback
        callback.call(element);
      });
    }
  }
</script>
</body>
```

#### DOM3

```js
element.addEventListener('keyup', function () {
	// TODO
}, false);
```

DOM3中，增加了很多事件类型，比如鼠标事件、键盘事件等。

*PS：*为何事件没有DOM1的写法呢？因为，DOM1标准制定的时候，没有涉及与事件相关的内容。

### DOM事件模型

DOM事件模型讲的就是**捕获和冒泡**。先捕获，再到目标，再冒泡 。捕获是事件会从最外层开始发生，直到最具体的元素。冒泡是事件会从最内层的元素开始发生，一直向上传播，直到window对象。

- 捕获：从上往下（目标元素）。
- 冒泡：从下（目标元素）往上。

![事件模型](https://tva1.sinaimg.cn/large/007S8ZIlly1giblwt1g4oj30ay09x74g.jpg)

### DOM事件流

> 浏览器在与当前页面做交互时，这个事件是怎么传递到页面上的。

DOM 标准采用捕获+冒泡。两种事件流都会触发 DOM 的所有对象，从 window 对象开始，也在 window 对象结束。

完整的事件流，分三个阶段：

- 捕获：从 window 对象传到目标元素。
- 目标阶段：事件通过捕获，到达目标元素，这个阶段就是目标阶段。
- 冒泡：从**目标元素**传到 Window 对象。

![事件捕获和冒泡](https://tva1.sinaimg.cn/large/007S8ZIlly1gibluj36zuj30tg0q4ae9.jpg)

### DOM事件捕获的具体流程

捕获阶段：事件依次传递的顺序是从 window -> document -> html -> body -> ...(父元素 -> 子元素)-> 目标元素

```js
window.addEventListener("click", function () {
  alert("捕获 window");
}, true);

document.addEventListener("click", function () {
  alert("捕获 document");
}, true);

document.documentElement.addEventListener("click", function () {
  alert("捕获 html");
}, true);

document.body.addEventListener("click", function () {
  alert("捕获 body");
}, true);

fatherBox.addEventListener("click", function () {
  alert("捕获 father");
}, true);

childBox.addEventListener("click", function () {
  alert("捕获 child");
}, true);
```

> 冒泡的流程与捕获相反

### Event 对象常见应用

用户做的是什么操作（比如，是敲键盘了，还是点击鼠标了），这些事件基本都是通过Event对象拿到的。

#### 阻止默认事件

```js
 event.preventDefault();
```

比如，已知`<a>`标签绑定了click事件，此时，如果给`<a>`设置了这个方法，就阻止了链接的默认跳转。

#### 阻止冒泡

w3c的方法：（火狐、谷歌、IE11）

```js
event.stopPropagation();
```

IE10以下则是：

```js
event.cancelBubble = true;
```

兼容代码如下：

```js
// 对box进行了阻止冒泡，事件不会向上冒泡传递到父元素、body、html、document、window
box.onclick = function (event) {
  alert("child");
  //阻止冒泡
  event = event || window.event;
  if (event && event.stopPropagation) {
  	event.stopPropagation();
  } else {
    event.cancelBubble = true;
    }
}
```

经常在业务场景中遇到。

#### 设置事件优先级

```js
event.stopImmediatePropagation();
```

场景：我用addEventListener给某按钮同时注册了事件A、事件B。此时，如果我单击按钮，就会依次执行事件A和事件B。现在要求：单击按钮时，只执行事件A，不执行事件B。该怎么做呢？这是时候，就可以用到`stopImmediatePropagation`方法了。做法是：在事件A的响应函数中加入该语句。

#### event 属性

event 有很多属性，常见属性有如下：

```js
 event.currentTarget   //当前所绑定的事件对象。在事件委托中，指的是【父元素】。
event.target  //当前被点击的元素。在事件委托中，指的是【子元素】。
```

![](http://img.smyhvae.com/20180203_1739.png)

由于pageX 和 pageY的兼容性不好，我们可以这样做：

- 鼠标在页面的位置 = 滚动条滚动的距离 + 可视区域的坐标。

### 自定义事件

自定义事件的代码如下：

```js
var myEvent = new Event('clickTest');
element.addEventListener('clickTest', function () {
console.log('hello');
});

//元素注册事件
element.dispatchEvent(myEvent); //注意，参数是写事件对象 myEvent，不是写 事件名 clickTest
```

上面这个事件是定义完了之后，就直接自动触发了。在正常的业务中，这个事件一般是和别的事件结合用的。比如**延时器设置按钮**的动作：

```js
var myEvent = new Event('clickTest');
element.addEventListener('clickTest', function () {
  console.log('hello');
});
setTimeout(function () {
  element.dispatchEvent(myEvent); //注意，参数是写事件对象 myEvent，不是写 事件名 clickTest
}, 1000);
```

### 事件委托

通俗地来讲，就是把一个元素响应事件（click、keydown......）的函数委托到另一个元素。

比如说有一个列表 ul，列表之中有大量的列表项 `<a>`标签：

```html
<ul id="parent-list">
    <li><a href="javascript:;" class="my_link">超链接一</a></li>
    <li><a href="javascript:;" class="my_link">超链接二</a></li>
    <li><a href="javascript:;" class="my_link">超链接三</a></li>
</ul>
```

当我们的鼠标移到`<a>`标签上的时候，需要获取此`<a>`的相关信息并飘出悬浮窗以显示详细信息，或者当某个`<a>`被点击的时候需要触发相应的处理事件。我们通常的写法，是为每个`<a>`都绑定类似onMouseOver或者onClick之类的事件监听：

```javascript
    window.onload = function(){
        var parentNode = document.getElementById("parent-list");
        var aNodes = parentNode.getElementByTagName("a");
        for(var i=0, l = aNodes.length; i < l; i++){

            aNodes[i].onclick = function() {
                console.log('我是超链接 a 的单击相应函数');
            }
        }
    }
```

但是，上面的做法过于消耗内存和性能。**我们希望，只绑定一次事件，即可应用到多个元素上**，即使元素是后来添加的。

因此，比较好的方法就是把这个点击事件绑定到他的父层，也就是 `ul` 上，然后在执行事件函数的时候再去匹配判断目标元素。如下：

```html
    <!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8" />
        <title></title>
        <script type="text/javascript">
            window.onload = function() {

                // 获取父节点，并为它绑定click单击事件。 false 表示事件在冒泡阶段触发（默认）
                document.getElementById('parent-list').addEventListener('click', function(event) {
                    event = event || window.event;

                    // e.target 表示：触发事件的对象
                    //如果触发事件的对象是我们期望的元素，则执行否则不执行
                    if (event.target && event.target.className == 'link') {
                    // 或者写成 if (event.target && event.target.nodeName.toUpperCase() == 'A') {
                        console.log('我是ul的单击响应函数');
                    }
                }, false);
            };
        </script>
    </head>
    <body>
        <ul id="parent-list" style="background-color: #bfa;">
            <li>
                <p>我是p元素</p>
            </li>
            <li><a href="javascript:;" class="link">超链接一</a></li>
            <li><a href="javascript:;" class="link">超链接二</a></li>
            <li><a href="javascript:;" class="link">超链接三</a></li>
        </ul>
    </body>
```

上方代码，为父节点注册 click 事件，当子节点被点击的时候，click事件会从子节点开始**向父节点冒泡**。**父节点捕获到事件**之后，开始执行方法体里的内容：通过判断 event.target 拿到了被点击的子节点`<a>`。从而可以获取到相应的信息，并作处理。

换而言之，参数为false，说明事件是在冒泡阶段触发（子元素向父元素传递事件）。而父节点注册了事件函数，子节点没有注册事件函数，此时，会在父节点中执行函数体里的代码。

> **总结**：事件委托是利用了**冒泡机制**，减少了事件绑定的次数，减少内存消耗，提高性能。

## script放在 head种和body种的区别

##### 1、加载的顺序不一样

浏览器是从上到下解析HTML的。放在head里的js代码，会在body解析之前被解析；放在body里的js代码，会在整个页面加载完成之后解析。

放在body中：在页面加载的时候被执行

放在head中：在被调用时被执行

总结：**放入html的head,是页面加载前就运行，放入body中，则加载后才运行javascript的代码**

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
原型链和继承

## 函数

### 描述

函数就是将一些功能或语句进行**封装**，在需要的时候进行调用。在 JavaScript中，每个函数其实都是一个`Function`对象。因为它们可以像任何其他**对象**一样具有属性和方法。它们与其他对象的区别在于函数可以被调用。

> 在使用`typeof`检查一个函数时，会返回`function`

##### 作用

简化编程，高内聚、低耦合

##### 返回值

如果一个函数中没有使用return语句，则它默认返回`undefined`。要想返回一个特定的值，则函数必须使用 `return` 语句来指定一个要返回的值。

> 使用[new](https://developer.mozilla.org/zh-cn/JavaScript/Reference/Operators/new)关键字调用一个[构造函数](https://developer.mozilla.org/zh-cn/JavaScript/Reference/Global_Objects/Object/constructor)除外

##### 实参和形参（值传递和引用传递）

调用函数时，传递给函数的值被称为函数的实参（值传递），对应位置的函数参数名叫作形参。**如果实参是一个包含原始值(数字，字符串，布尔值)的变量，则就算函数在内部改变了对应形参的值，返回后，该实参变量的值也不会改变。如果实参是一个对象引用，则对应形参会和该实参指向同一个对象。**假如函数在内部改变了对应形参的值，返回后，实参指向的对象的值也会改变：

```js
/* 定义函数 myFunc */
 function myFunc(theObject)
 {
   //实参 mycar 和形参 theObject 指向同一个对象.
   theObject.brand = "Toyota";
 }
 
 /*
  * 定义变量 mycar;
  * 创建并初始化一个对象;
  * 将对象的引用赋值给变量 mycar
  */
 var mycar = {
   brand: "Honda",
   model: "Accord",
   year: 1998
 };

 /* 弹出 'Honda' */
 window.alert(mycar.brand);

 /* 将对象引用传给函数 */
 myFunc(mycar);

 /*
  * 弹出 'Toyota',对象的属性已被修改.
  */
 console.log(mycar.brand);
```

### 函数定义

#### 1、函数声明（命名函数）

利用函数关键字`function`自定义函数，`name`为函数名，`param`为传递给函数的参数名称。语法如下：

```js
// 语法
function name([param[, param[, ... param]]]) { statements }

// 示例
function add(a, b) {
  return a + b;
}
```

#### 2、函数表达式

同函数声明类似，可以定义函数“名字”（例如可以在调用堆栈时使用）或者使用“匿名”函数。函数表达式不会提升，所以不能在定义之前调用。语法如下：

```js
// 语法 name为函数名可以省略，如果函数名省略，该函数就变为匿名函数
var myFunction = function name([param[, param[, ... param]]]) { statements }
var myFunction = function([param[, param[, ... param]]]) { statements }

// 示例
var myFunction = function() {
    console.log('hello');
}
var myFunction = function namedFunction() {
    console.log('hello');
}
```

当函数只使用一次时，通常使用**IIFE (\Immediately Invokable Function Expressions\)。**

```js
(function() {
    // statements
})();
```

**IIFE**是在函数声明后立即调用的函数表达式。

#### 3、构造函数

所有其他对象, [`Function`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Function) 对象可以用new操作符创建:

```js
// 语法 arg1,arg2...表示参数名称，functionBody是一个构成的函数定义的，包含JavaScript声明语句的字符串。
new Function (arg1, arg2, ... argN, functionBody)

// 示例
var fn = new Function('a', 'b', 'console.log("hello");  console.log(a + b);');

fn(1, 2); // 调用函数
```

> **注意:** 不推荐使用 `Function` 构造函数创建函数,因为它需要的函数体作为字符串可能会阻止一些JS引擎优化,也会引起其他问题。

#### 4、箭头函数表达式（=>）

箭头函数表达式有着更短的语法，`param`是参数名称，零参数需要用()表示，只有一个参数时不需要括号(例如 `foo => 1`)

```js
// 语法 
([param] [, param]) => { statements } param => expression

// 示例
(a, b) => {return a + b};
const materials = [
  'Hydrogen',
  'Helium',
  'Lithium',
  'Beryllium'
];
console.log(materials.map(material => material.length));
// output: Array [8, 6, 7, 9]
```

## JS获取元素属性和自定义属性

> `<div id="id" data-first-name="李四" data-name="ramona">`如何获取name

##### 获取元素的属性分为两种类型：

1、获取元素常见的属性（class,id,type,value…）

2、获取自定义的元素的属性（data-value,data-message…）

> 原生js获取元素
>
> var ele = document.getElementById("id");
>
> ele.getAttribute("data-name");
>
> ele.setAttribute("data-name", "张三");
>
> ele.getAttribute("data-first-name");
>
> ele.setAttribute("data-first-name", "raymond");
>
> ele.dataset.name;
>
> ele.dataset.firstName;
>
> JQuery获取元素
>
> `var $id = $("#id");`
>
> $id.data("name");
>
> $id.data(first-name);

通过`setAttribute(), getAttribute(),dataset`

```html
<body>
  <div id="tree" data-leaves="47" data-plant-height="2.4m"></div>
<script>
    var tree = document.getElementById("tree");
    //getAttribute()取值属性
    console.log(tree.getAttribute("data-leaves"));
    console.log(tree.getAttribute("data-plant-height"));
    //setAttribute()赋值属性
    tree.setAttribute("data-leaves","48");

    //data-前缀属性可以在JS中通过dataset取值，更加方便
    console.log(tree.dataset.leaves);
    console.log(tree.dataset.plantHeight);
    //赋值
    tree.dataset.plantHeight = "3m";
    tree.dataset.leaves--;
    //新增data属性
    tree.dataset.age = "100";
    //删除，设置成null，或者delete
    tree.dataset.leaves = null;
    delete tree.dataset.age;

    //jQuery的data方法
    var $tree = $('#tree');
    console.log($tree.data("plant-height"));
</script>
</body>
```
## map 与 forEach的区别

map & forEach 都是用来更方便地遍历数组的。

### map

map 接收两个参数：callback 函数（它会在 map 执行之后被触发）。上下文变量（即执行 callback 函数时 this 指向的对象）。**map 会返回一个新数组。**不会改变原数组。

作用：对数组中的每一项进行加工。

```js
var arr1 = [1, 3, 6, 2, 5, 6];
var arr2 = arr1.map(function (item, index) {
    return item + 10; //让arr1中的每个元素加10
});
console.log(arr2);
```

### forEach

forEach 接收的参数和 map 相同，但是它**没有返回值**，即它返回的是 undefined。

因为 map & forEach 的主要区别是有无返回，所以，当你想基于一个原数组返回一个新数组，可以选择 map，当你只是想遍历数据不需要考虑返回时可以选择 forEach。

## filter函数

```js
arr.filter(function (item, index, arr) {
     return true;
 });
```

 解释：对数组中的**每一项**运行回调函数，该函数返回结果是 true 的项，将组成新的数组（返回值就是这个新的数组）。不会改变原数组。

 作用：对数组进行过滤。

```js
 let arr1 = [1, 3, 6, 2, 5, 6];
 
 let arr2 = arr1.filter((item) => item > 4); // 将arr1中大于4的元素返回，组成新的数组
 
 console.log(JSON.stringify(arr1)); // 打印结果：[1,3,6,2,5,6]
 console.log(JSON.stringify(arr2)); // 打印结果：[6,5,6]
```

## reduce()函数

reduce() 方法接收一个函数作为累加器，数组中的每个值（从左到右）开始缩减，最终计算为一个值。返回值是回调函数累计处理的结果。

**语法**：

```javascript
arr.reduce(function (previousValue, currentValue, currentIndex, arr) {}, initialValue);
```

参数解释：

-   previousValue：必填，上一次调用回调函数时的返回值

-   currentValue：必填，当前正在处理的数组元素

-   currentIndex：选填，当前正在处理的数组元素下标

-   arr：选填，调用 reduce()方法的数组

-   initialValue：选填，可选的初始值（作为第一次调用回调函数时传给 previousValue 的值）

```js
// 1、数组求和，计算数组中所有元素项的总和
const arr = [2, 0, 1, 9, 6];
const total = arr.reduce((prev, item) => {
    return prev + item;
});

console.log('total:' + total); // 打印结果：18

// 2、统计 value 这个元素在数组 arr 中出现的次数
function repeatCount(arr, value) {
    if (!arr || arr.length == 0) return 0;

    return arr.reduce((totalCount, item) => {
        totalCount += item == value ? 1 : 0;
        return totalCount;
    }, 0);
}
let arr1 = [1, 2, 6, 5, 6, 1, 6];
console.log(repeatCount(arr1, 6)); // 打印结果：3
```

## sort()函数

对数组的元素进行从小到大来排序（会改变原来的数组）。

###### sort()方法：无参时

如果在使用 sort() 方法时不带参，则默认按照**Unicode 编码**，从小到大进行排序。

###### sort()方法：带参时，自定义排序规则

我们可以在 sort()添加一个回调函数，来指定排序规则。回调函数中需要定义两个形参，浏览器将会分别使用数组中的元素作为实参去调用回调函数。

浏览器根据回调函数的返回值来决定元素的排序：（重要）

-   如果返回一个大于 0 的值，则元素会交换位置
-   **如果返回一个小于 0 的值，则元素位置不变**
-   如果返回一个等于 0 的值，则认为两个元素相等，则不交换位置

```js
let arr = [5, 2, 11, 3, 4, 1];
// 自定义排序规则：升序排列
let result = arr.sort((a, b) => a - b);
console.log('arr =' + JSON.stringify(arr));
console.log('result =' + JSON.stringify(result));
```

## offset、client与scroll的区别

###### offsetWidth/offsetHeight,clientWidth/clientHeight 与 scrollWidth/scrollHeight 的区别

`HTMLElement.offsetWidth` 是一个只读属性，返回一个元素的布局宽度。一个典型的（译者注：各浏览器的offsetWidth可能有所不同）offsetWidth是测量包含元素的边框(border)、水平线上的内边距(padding)、竖直方向滚动条(scrollbar)（如果存在的话）、以及CSS设置的宽度(width)的值。

clientWidth = 内部宽度+内边距 （不包括垂直滚动条+边框+外边距）

offsetWidth = 内部宽度 + 边框 + 内边距 + 滚动条

- offsetWidth/offsetHeight 返回值包含 content + padding + border，效果与 e.getBoundingClientRect()相同
- clientWidth/clientHeight 返回值只包含 content + padding，如果有滚动条，也不包含滚动条
- scrollWidth/scrollHeight 返回值包含 content + padding + 溢出内容的尺寸