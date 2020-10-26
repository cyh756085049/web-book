## 什么是TypeScript

[TypeScript](http://www.typescriptlang.org/) 是强类型的 JavaScript 的一个超集，可以编译成纯JavaScript，编译出来的JavaScript可以运行在任何浏览器上，它支持ES6语法，支持面向对象编程的概念，如：类、接口、继承、泛型等。它由 Microsoft 开发，代码[开源于 GitHub](https://github.com/Microsoft/TypeScript) 上。

## TypeScript特点

##### 优点

* 增加了代码的可读性和可维护性。
* 非常包容，可以定义从简单到复杂的几乎一切类型。
* 拥有活跃的社区。
* 支持所有的JS库。
* 支持ES6，提供了ES6所有优点和更高的生产力。

##### 缺点

* 有一定的学习成本。
* 需要长时间来编译代码。

- 短期可能会增加一些开发成本，毕竟要多写一些类型的定义，不过对于一个需要长期维护的项目，TypeScript 能够减少其维护成本。
- 集成到构建流程需要一些工作量。
- 可能和一些库结合的不是很完美。

## TypeScript安装

1、命令工具安装方式：

```js
npm install -g typescript
```

2、编译ts文件：

```js
tsc hello.ts
```

> 我们约定使用 TypeScript 编写的文件以 `.ts` 为后缀，用 TypeScript 编写 React 时，以 `.tsx` 为后缀。

3、推荐使用[Visual Studio Code](https://code.visualstudio.com/)作为编译器，它自身也是用TypeScript编写的。

### TypeScript学习

##### 1、示例

新建一个`hello.ts`文件如下；

```ts
function sayHello(person: string) {
    return 'hello,' + person;
}
let user = 'ramona';
console.log(sayHello(user));
```

然后**执行`ts hello.ts`，此时会生成一个编译好的文件`hello.js`。**

使用语法：使用 `:` 指定变量的类型，`:` 的前后有没有空格都可以。

> **TypeScript 只会进行静态检查，如果发现有错误，编译的时候就会报错。TypeScript 编译的时候即使报错了，还是会生成编译结果**，我们仍然可以使用这个编译之后的文件。如果要在报错的时候终止 js 文件的生成，可以在 `tsconfig.json` 中配置 `noEmitOnError` 即可。关于 `tsconfig.json`，请参阅[官方手册](http://www.typescriptlang.org/docs/handbook/tsconfig-json.html)（[中文版](https://zhongsp.gitbooks.io/typescript-handbook/content/doc/handbook/tsconfig.json.html)）。

## 变量类型

- boolean
- number（没有整型和浮点型的区分）
- string
- rray
- object
- Symbol
- tuple（元组，数组的一种）
- enum（枚举类型）
- any（任意类型）
- null && undefined （其他类型的子类型）
- void（与any相反表示没有任何类型。函数没有返回值时用void）
- never（其他类型，代表从不会出现的值，never类型只能被never类型所赋值）

```js
let flag: boolean = true;
let num: number = 123;
let str: string = "this is typescript";
// 定义数组
let arr: string[] = ['tom', 'ramona', 'raymond'];
let arr1: Array<number> = [3,5,6,8];
// 元组
let tuple: [string, number, boolean] = ['ramona', 23, true];
// 枚举类型
enum Flag {success = 1, error = -1};
let flag: Flag = Flag.success;
console.log(flag); // -1
// void 空值
function alertName(): void {
    alert('My name is Tom');
}
let u: undefined = undefined;
let n: null = null;
```

## `ts` 常用命令

#### 编译ts文件

```js
ts file.ts
```

#### 将多个ts文件合并成一个js文件

```js
ts --outfile compact.js file.ts file2.ts file3.ts
```

#### 自动编译ts文件并实时修改

```js
ts --watch file.ts
```

## ts接口及特性



## ts类及特性







