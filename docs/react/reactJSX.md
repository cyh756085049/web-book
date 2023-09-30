

`React` 是非常流行的用于构建用户界面的`JavaScript`库，也是目前最受欢迎的 `Web` 界面开发工具之一。

React的出现让创建交互式UI变得简单。

给定一段 `react JSX` 代码如下，命名为 `demoJSX.js`

```react
const toLearn = [ 'react' , 'vue' , 'webpack' , 'nodejs'  ]

const TextComponent = ()=> <div> hello , i am function component </div> 

class Index extends React.Component{
    status = false /* 状态 */
    renderFoot=()=> <div> i am foot</div>
    render(){
        /* 以下都是常用的jsx元素节 */
        return <div style={{ marginTop:'100px' }}   >
            { /* element 元素类型 */ }
            <div>hello,world</div>
            { /* fragment 类型 */ }
            <React.Fragment>
                <div> 👽👽 </div>
            </React.Fragment>
            { /* text 文本类型 */ }
            my name is alien 
            { /* 数组节点类型 */ }
            { toLearn.map(item=> <div key={item} >let us learn { item } </div> ) }
            { /* 组件类型 */ }
            <TextComponent/>
            { /* 三元运算 */  }
            { this.status ? <TextComponent /> : <div>三元运算</div> }
            { /* 函数执行 */ } 
            { this.renderFoot() }
            <button onClick={ ()=> console.log( this.render() ) } >打印render后的内容</button>
        </div>
    }
}

```

### JSX怎么编译的，最终会转化成什么？

我们写的 `jsx` 文件中的元素节点首先会被编译成 `React Element` 形式，然后再转化为 `React.fiber`。

### `React.createElement` 怎么用？包含哪些参数？

`React.createElement` 用法：

```react
React.createElement(
  type,
  [props],
  [...children]
)
```

`createElement` 参数：

- 第一个参数：如果是组件类型，会传入组件对应的类或函数；如果是 `dom` 元素类型，传入 `div` 或者 `span` 之类的字符串。
- 第二个参数：一个对象，在 `dom` 类型中为标签属性，在组件类型中为 `props` 。
- 其他参数：依次为 `children`，根据顺序排列。

示例代码如下：

```react
<div style={{ marginTop:'100px' }} >
   <TextComponent />
   <div>hello,world</div>
   let us learn React!
</div>
```

上面的代码会被 babel 先编译成：

```js
React.createElement("div", { style: { marginTop: '100px' } },
        React.createElement(TextComponent, null),
        React.createElement("div", null, "hello,world"),
        "let us learn React!"
    )
```

### 老版本的 React 中，为什么写 jsx 的文件要默认引入 React?

将代码文件 `demoJSX.js` ，编译后会得到如下内容：

![编译](https://p.ipic.vip/g1dis5.jpg)

由上方的可以看出， `jsx` 在被 `babel` 编译后，写的 `jsx` 会变成上述 `React.createElement` 形式，所以需要引入 `React`，防止找不到 `React` 引起报错。

### `jsx` 的转换规则？

1、执行 `demoJSX.js` 代码。

2、查看上述代码转化成的结构：

![转换结构](https://p.ipic.vip/mokhz2.jpg)

3、从上述的转化结构看转化规则如下：

| `jsx`元素类型     | `react.createElement` 转换后                      | `type` 属性                   |
| ----------------- | ------------------------------------------------- | ----------------------------- |
| `element`元素类型 | `react element`类型                               | 标签字符串，例如 `div`        |
| `fragment`类型    | `react element`类型                               | `symbol` `react.fragment`类型 |
| 文本类型          | 直接字符串                                        | 无                            |
| 数组类型          | 返回数组结构，里面元素被`react.createElement`转换 | 无                            |
| 组件类型          | `react element`类型                               | 组件类或者组件函数本身        |
| 三元运算 / 表达式 | 先执行三元运算，然后按照上述规则处理              | 看三元运算返回结果            |
| 函数执行          | 先执行函数，然后按照上述规则处理                  | 看函数执行返回结果            |

### React 底层调和处理后，终将变成什么？

最终，在调和阶段，上述 `React element` 对象的每一个子节点都会形成一个与之对应的 `fiber` 对象，然后通过 `sibling`、`return`、`child` 将每一个 `fiber` 对象联系起来。

1、不同种类的 `fiber Tag`

`React` 针对不同 `React element` 对象会产生不同 `tag` (种类) 的 `fiber` 对象。首先，来看一下 `tag` 与 `element` 的对应关系：

```react
export const FunctionComponent = 0;       // 函数组件
export const ClassComponent = 1;          // 类组件
export const IndeterminateComponent = 2;  // 初始化的时候不知道是函数组件还是类组件 
export const HostRoot = 3;                // Root Fiber 可以理解为根元素 ， 通reactDom.render()产生的根元素
export const HostPortal = 4;              // 对应  ReactDOM.createPortal 产生的 Portal 
export const HostComponent = 5;           // dom 元素 比如 <div>
export const HostText = 6;                // 文本节点
export const Fragment = 7;                // 对应 <React.Fragment> 
export const Mode = 8;                    // 对应 <React.StrictMode>   
export const ContextConsumer = 9;         // 对应 <Context.Consumer>
export const ContextProvider = 10;        // 对应 <Context.Provider>
export const ForwardRef = 11;             // 对应 React.ForwardRef
export const Profiler = 12;               // 对应 <Profiler/ >
export const SuspenseComponent = 13;      // 对应 <Suspense>
export const MemoComponent = 14;          // 对应 React.memo 返回的组件
```

2、`demoJSX.js`  代码最终形成的 `fiber` 结构图如下：

`fiber` 对应关系如下：

- `child`： 一个由父级 `fiber` 指向子级 `fiber` 的指针。
- `return`：一个子级 `fiber` 指向父级 `fiber` 的指针。
- `sibling`: 一个 `fiber` 指向下一个兄弟 `fiber` 的指针。

![fiber](https://p.ipic.vip/g0fh5y.jpg)

> 注意点：
>
> - 在 jsx 中写的 `map` 返回数组结构，会作为 `fragment` 的子节点。

### `React.createElement` 和 `React.cloneElement` 到底有什么区别呢?

可以完全理解为，`React.createElement` 是用来创建 `element` 。`React.cloneElement` 是用来修改 `element`，并返回一个新的 `React.element` 对象。

### 解析 `jsx` 的 `babel` 插件及解析流程？

`JSX` 语法实现来源于这两个 `babel` 插件：

- `@babel/plugin-syntax-jsx` ： 使用这个插件，能够让 `Babel` 有效的解析 `JSX` 语法。
- `@babel/plugin-transform-react-jsx` ：这个插件内部调用了 `@babel/plugin-syntax-jsx`，可以把 `React JSX` 转化成 `JS` 能够识别的 `createElement` 格式。

#### `Automatic Runtime`

新版本 `React` 已经不需要引入 `createElement` ，这种模式来源于 `Automatic Runtime`，看一下是如何编译的。

如下 `jsx` 文件代码：

```react
function Index() {
  return <div>
    <h1>hello,world</h1>
    <span>let us learn React</span>
  </div>
}
```

被编译后的文件：

```react
import { jsx as _jsx } from "react/jsx-runtime";
import { jsxs as _jsxs } from "react/jsx-runtime";
function Index() {
  return  _jsxs("div", {
    children: [
        _jsx("h1", {
           children: "hello,world"
        }),
        _jsx("span", {
            children:"let us learn React" ,
        }),
    ],
});
}
```

`plugin-syntax-jsx` 已经向文件中提前注入了 `_jsxRuntime api`。不过这种模式下需要我们在 `.babelrc` 设置 `runtime: automatic` 。

```js
"presets": [    
    ["@babel/preset-react",{
    "runtime": "automatic"
    }]     
],
```

#### `Classic Runtime`

还有一个就是经典模式，在经典模式下，使用 `JSX` 的文件需要引入 `React` ，不然就会报错。

如下 `JSX` 文件代码：

```react
import React from 'react'
function Index() {
  return <div>
    <h1>hello,world</h1>
    <span>let us learn React</span>
  </div>
}
```

被编译后的文件：

```js
import React from 'react'
function Index(){
  return  React.createElement(
    "div",
    null,
    React.createElement("h1", null,"hello,world"),
    React.createElement("span", null, "let us learn React")
  );
}
```

### 模拟 `Babel` 处理 `JSX` 的流程？

第一步：创建 `element.js`，写下将测试的 `JSX` 代码:

```jsx
import React from 'react'

function TestComponent(){
    return <p> hello,React </p>
}
function Index(){
    return <div>
        <span>模拟 babel 处理 jsx 流程。</span>
        <TestComponent />
    </div>
}
export default Index
```

第二步：因为 `babel` 运行在 `node` 环境，所以同级目录下创建 `jsx.js` 文件。来模拟一下编译的效果。

```js
const fs = require('fs')
const babel = require("@babel/core")

/* 第一步：模拟读取文件内容。 */
fs.readFile('./element.js',(e,data)=>{ 
    const code = data.toString('utf-8')
    /* 第二步：转换 jsx 文件 */
    const result = babel.transformSync(code, {
        plugins: ["@babel/plugin-transform-react-jsx"],
    });
    /* 第三步：模拟重新写入内容。 */
    fs.writeFile('./element.js',result.code,function(){})
})

```

如上经过三步处理之后，再来看一下 `element.js` 变成了什么样子。

```js
import React from 'react';

function TestComponent() {
  return /*#__PURE__*/React.createElement("p", null, " hello,React ");
}

function Index() {
  return /*#__PURE__*/React.createElement("div", null, /*#__PURE__*/React.createElement("span", null, "\u6A21\u62DF babel \u5904\u7406 jsx \u6D41\u7A0B\u3002"), /*#__PURE__*/React.createElement(TestComponent, null));
}
export default Index;
```

如上可以看到已经成功转成 `React.createElement` 形式，从根本上弄清楚了 `Babel` 解析 `JSX` 的大致流程。

