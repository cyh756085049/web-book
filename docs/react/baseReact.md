# React

## `React`简介

### 介绍

由 `Facebook`开源，用于动态构建用户界面的`JavaScript`库。

### 文档网址

英文网址： [ https://reactjs.org/](https://reactjs.org/)

中文网址：https://react.docschina.org/

### 特点

* 声明式、组件化编码。
* `React Native`编写原生应用。
* 使用虚拟`DOM`，不总直接操作页面真实`DOM`。
* 使用`DOM Diffing`算法，最小化页面重绘。高效。

## `React`基本使用

### 相关`js`库

1. **`react.js`**：`React`核心库。
2. **`react-dom.js`**：用于支持`React`操作`DOM`的`React`扩展库。
3. **`babel.min.js`**：将`JSX`语法代码解析转化为`JS`代码的库。

### `HTML`模板

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>hello_react</title>
</head>
<body>
	<!--容器 -->
	<div id="container"></div>
  
	<!-- 引入react核心库 -->
	<script type="text/javascript" src="../js/react.development.js"></script>
	<!-- 引入react-dom，用于支持react操作DOM -->
	<script type="text/javascript" src="../js/react-dom.development.js"></script>
	<!-- 引入babel，用于将jsx转为js -->
	<script type="text/javascript" src="../js/babel.min.js"></script>

	<script type="text/babel" > /* 此处一定要写babel */
		//1.创建虚拟DOM
		const VDOM = <h1>Hello,React</h1>;
		//2.渲染虚拟DOM到页面
		ReactDOM.render(VDOM,document.getElementById('container'));
	</script>
</body>
</html>
```

### 创建虚拟`DOM`的两种方式

1. 使用`JSX`创建

```js
<script type="text/babel" > 
		const VDOM = (  
			<h1 id="title">
				<span>Hello,React</span>
			</h1>
		);
		ReactDOM.render(VDOM,document.getElementById('container'));
</script>
```

2. 使用`JS`创建（一般不用）

```js
<script type="text/javascript" > 
		const VDOM = React.createElement('h1',{id:'title'},React.createElement('span',{},'Hello,React'));
		ReactDOM.render(VDOM,document.getElementById('container'));
</script>
```

> 虚拟`DOM`：本质是`Object`类型的对象，最终会被`React`转化为真实`DOM`，呈现在页面上。

### `JSX（JavaScript XML）`

**作用**：简化创建虚拟`DOM`。

```js
<script type="text/babel" >
		const myId = 'RaMonA';
		const myData = 'HeLlo,rEaCt';
		const VDOM = (
			<div>
				<h2 className="title" id={myId.toLowerCase()}>
					<span style={{color:'white',fontSize:'29px'}}>{myData.toLowerCase()}</span>
				</h2>
				<h2 className="title" id={myId.toUpperCase()}>
					<span style={{color:'white',fontSize:'29px'}}>{myData.toLowerCase()}</span>
				</h2>
				<input type="text"/>
			</div>
		)
    ReactDOM.render(VDOM, document.getElementById('container'));
</script>
```

#### **`jsx`语法规则：**

- 只有一个根标签，且标签必须闭合。
- 标签中混入`JS`表达式时要用`{}`。
- 遇到 HTML 标签（以 `<` 开头），就用 HTML 规则解析；遇到代码块（以 `{` 开头），就用 JavaScript 规则解析。
- 样式的类名指定不用`class`，要用`className`。
- 内联样式，要用`style={{key:value}}`形式。
- 针对标签首字母，若以小写字母开头，则将该标签转为html中同名元素，若html中无该标签对应的同名元素，则报错。若以大写字母开头，react会去渲染对应的组件，若组件没有定义，则报错。

### `ReactDOM.render()`

**作用**： 将虚拟`DOM`元素渲染到页面中的真实`DOM`中显示。

**语法**：`RenderDOM.render(VDOM, TDOM)`; 其中`VDOM`是创建的虚拟`DOM`对象。`TDOM`是真实`DOM`元素对象。

### 模块/模块化、组件/组件化

模块：向外提供特定功能的`js`程序，一般就是一个`js`文件。可以复用`js`代码，简化`js`的编写，提高运行效率。

模块化：当应用的`js`都以模块来编写，这个应用就是一个模块化的应用。

组件：用来实现局部功能效果的代码和资源的集合。复用编码，简化项目编码，提高运行效率。

组件化：当应用是以多组件的方式实现，这个应用就是一个组件化的应用。

## `React`组件

### React定义组件

* 组件名必须首字母大写。
* 虚拟`DOM`元素只能有一个根元素。
* 虚拟`DOM`元素必须有结束标签。

#### 函数式组件

```js
//创建函数式组件
<script type="text/babel">
		function MyComponent(){
			console.log(this); //undefined，因为babel编译后开启了严格模式
			return <h2>函数定义的组件(适用于【简单组件】定义)</h2>;
		}
		ReactDOM.render(<MyComponent/>,document.getElementById('container'));
</script>
```

`React`首先解析组件标签，找到`MyComponent`组件。发现组件是使用函数定义的，随后调用该函数，将返回的虚拟`DOM`转为真实`DOM`，呈现在页面中。

> 组件自定义方法中`this`为`undefined`，解决方法：
>
> 1. 强制绑定`this`。通过函数对象的`bind()`。
> 2. 使用箭头函数。

#### 类式组件

```js
<script type="text/babel">
		class MyComponent extends React.Component {
			render(){	
				console.log('render中的this:',this); // MyComponent组件实例对象
				return <h2>类定义的组件(适用于【复杂组件】定义)</h2>;
			}
		}
		ReactDOM.render(<MyComponent/>,document.getElementById('container'));
</script>
```

`React`首先解析组件标签，找到`MyComponent`组件。发现组件是使用类定义的，随后`new`出该类的实例，并通过该实例调用到原型上的`render`方法。将`render`返回的虚拟`DOM`转为真实`DOM`，呈现在页面中。

### 组件核心属性（state、props、refs）

#### 1. `state`

将组件看成是一个状态机，一开始有一个初始状态，然后用户互动，导致状态变化，从而触发重新渲染 UI。

通过更新组件的`state`来更新对应的页面显示(重新渲染组件)。它是会随着用户互动而产生变化的特性。

```js
/* 完整版 */
<script type= "text/babel">
  class Weather extends React.Component {
    constructor(props) { 
      console.log('constructor'); // 构造器constructor只在运行首次调用一次
      super(props);
      this.state = {isHot: flase, wind: '微风'};
      this.changeWeather = this.changeWeather.bind(this); // 解决this指向问题
    }
    
    changeWeather() {
      console.log('changeWeather'); // 通过点击调用
      const isHot = this.state.isHot;
      this.setState({isHot: !isHot});
    }
    
    render() { 
      console.log('render'); // render先在初始化时调用一次，之后根据状态更新次数调用
      const {isHot, wind} = this.state;
      return <h1 onClick = {this.changeWeather}>今天天气很{isHot ? '炎热' : '凉爽'}, {wind} </h1>;
    }
  }
  ReactDOM.render(<Weather/>, document.getElementById('container'));
</script>  

/* 简化版 */
<script type="text/babel">
		class Weather extends React.Component{
			//初始化状态
			state = {isHot:false,wind:'微风'};

			render(){
				const {isHot,wind} = this.state;
				return <h1 onClick={this.changeWeather}>今天天气很{isHot ? '炎热' : '凉爽'}，{wind}</h1>;
			}

			//自定义方法—用赋值语句的形式+箭头函数
			changeWeather = ()=>{
				const isHot = this.state.isHot;
				this.setState({isHot: !isHot});
			}
		}
		ReactDOM.render(<Weather/>, document.getElementById('container'));
</script>
```

状态（`state`）数据，要通过`setState`进行更新，不能直接修改或更新。即`this.state.xxx = xxx`。

#### 2. `props`

每个组件对象都会有`props(properties)`属性，组件标签的所有属性都保存在`props`中。

**作用**：通过标签属性从组件外向组件内传递变化的数据（组件内部不要修改`props`数据）。它不会随着用户互动而产生变化。

* 内部读取某个属性值：`tihs.props.name`。

```js
<script type="text/babel">
		class Person extends React.Component{
			render(){
				const {name, age, sex} = this.props;  // props是只读的
				return (
					<ul>
						<li>姓名：{name}</li>
						<li>性别：{sex}</li>
						<li>年龄：{age}</li>
					</ul>
				)
			}
		}
		const p = {name: '张三', age: 18, sex: '女'};
		ReactDOM.render(<Person {...p} />, document.getElementById('container'));
	</script>
```

* 对`props`中的属性值进行类型限制和必要性限制。

```js
<!-- 引入prop-types，用于对组件标签属性进行限制 -->
<script type="text/javascript" src="../js/prop-types.js"></script>
<script type="text/babel">
		class Person extends React.Component{
			render(){
				const {name, age, sex} = this.props;
				return (
					<ul>
						<li>姓名：{name}</li>
						<li>性别：{sex}</li>
						<li>年龄：{age+1}</li>
					</ul>
				)
			}
		}
		//对标签属性进行类型、必要性的限制
		Person.propTypes = {
			name: PropTypes.string.isRequired, //限制name必传，且为字符串
			sex: PropTypes.string, //限制sex为字符串
			age: PropTypes.number, //限制age为数值
		}
		//指定默认标签属性值
		Person.defaultProps = {
			sex: '男',
			age: 18
		}
		const p = {name: '张三', age: 18, sex: '女'};
		ReactDOM.render(<Person {...p} />, document.getElementById('container'));
</script>
```

也可简写为如下方式

```js
<!-- 引入prop-types，用于对组件标签属性进行限制 -->
<script type="text/javascript" src="../js/prop-types.js"></script>
<script type="text/babel">
		class Person extends React.Component{
			constructor(props){
				super(props);
				console.log(props); // 所有属性值
			}
			//对标签属性进行类型、必要性的限制
          static propTypes = {
            name: PropTypes.string.isRequired, //限制name必传，且为字符串
            sex: PropTypes.string, //限制sex为字符串
            age: PropTypes.number, //限制age为数值
          }
          //指定默认标签属性值
          static defaultProps = {
            sex: '男',
            age: 18
          }
			render(){
				const {name,age,sex} = this.props;
				return (
					<ul>
						<li>姓名：{name}</li>
						<li>性别：{sex}</li>
						<li>年龄：{age+1}</li>
					</ul>
				)
			}
		}
		ReactDOM.render(<Person name="jerry"/>,document.getElementById('container));
	</script>
```

* 函数组件使用`props`。

```JS
<script type="text/babel">
		function Person (props){
			const {name, age, sex} = props;
			return (
					<ul>
						<li>姓名：{name}</li>
						<li>性别：{sex}</li>
						<li>年龄：{age}</li>
					</ul>
				)
		}
		//对标签属性进行类型、必要性的限制
		Person.propTypes = {
			name: PropTypes.string.isRequired, //限制name必传，且为字符串
			sex: PropTypes.string, //限制sex为字符串
			age: PropTypes.number, //限制age为数值
			speak: PropTypes.func, //限制speak为函数
		}
		//指定默认标签属性值
		Person.defaultProps = {
			sex: '男',
			age: 18
		}
	ReactDOM.render(<Person name="jerry"/>,document.getElementById('container'));
</script>
```

### 3. `refs`与事件处理

#### `refs`

组件内的标签可以定义`ref`属性来标识自己。

* 字符串形式的`ref`。

```js
<script type="text/babel">
		class Demo extends React.Component{
			showData1 = ()=>{
				const {input1} = this.refs;
				alert(input1.value);
			}
			showData2 = ()=>{
				const {input2} = this.refs;
				alert(input2.value);
			}
			render(){
				return(
					<div>
						<input ref="input1" type="text" placeholder="点击按钮提示数据"/>&nbsp;
						<button onClick={this.showData1}>点击提示左侧数据</button>&nbsp;
						<input ref="input2" onBlur={this.showData2} type="text" placeholder="失去焦点提示数据"/>
					</div>
				)
			}
		}
		ReactDOM.render(<Input />,document.getElementById('container'));
	</script>
```

* 回调形式的`ref`。

```js
<script type="text/babel">
		class Input extends React.Component{
			showData1 = ()=>{
				const {input1} = this;
				alert(input1.value);
			}
			showData2 = ()=>{
				const {input2} = this;
				alert(input2.value);
			}
      	saveInput = (c)=>{
				this.input1 = c;
				console.log('@', c);
			}
			render(){
				return(
					<div>
						<input ref={ c => this.input1 = c } type="text" placeholder="点击按钮提示数据"/>&nbsp; // <input ref={this.saveInput} type="text"/><br/><br/>
						<button onClick={this.showData1}>点击提示左侧数据</button>&nbsp;
						<input onBlur={this.showData2} ref={c => this.input2 = c } type="text" placeholder="失去焦点提示数据"/>&nbsp;
					</div>
				)
			}
		}
	 ReactDOM.render(<Input />,document.getElementById('container'));
</script>
```

* `createRef`创建`ref`容器。

```js
<script type="text/babel">
		class Input extends React.Component{
      	 // React.createRef调用后可以返回一个容器，该容器可以存储被ref所标识的节点
		    myRef1 = React.createRef();
			myRef2 = React.createRef();
			showData1 = ()=>{
				alert(this.myRef1.current.value);
			}
			showData2 = ()=>{
				const {input2} = this;
				alert(this.myRef2.current.value);
			}
			render(){
				return(
					<div>
						<input ref={this.myRef1} type="text" placeholder="点击按钮提示数据"/>&nbsp; // <input ref={this.saveInput} type="text"/><br/><br/>
						<button onClick={this.showData1}>点击提示左侧数据</button>&nbsp;
						<input onBlur={this.showData2} ref={this.myRef2} type="text" placeholder="失去焦点提示数据"/>&nbsp;
					</div>
				)
			}
		}
	 ReactDOM.render(<Input />,document.getElementById('container'));
</script>
```

#### 事件处理

1. 通过onXxx属性指定事件处理函数。

（1）React使用的是自定义(合成)事件, 而不是使用的原生DOM事件 。

（2）React中的事件是通过事件委托方式处理的(委托给组件最外层的元素)。

2. 通过event.target得到发生事件的DOM元素对象 。

### 表单数据

* 非受控组件（借助`refs`实现）

```js
<script type="text/babel">
		class Login extends React.Component{
			handleSubmit = (event)=>{
				event.preventDefault(); //阻止表单提交
				const {username,password} = this;
				alert(`你输入的用户名是：${username.value},你输入的密码是：${password.value}`);
			}
			render(){
				return(
					<form onSubmit={this.handleSubmit}>
						用户名：<input ref={c => this.username = c} type="text" name="username"/>
						密码：<input ref={c => this.password = c} type="password" name="password"/>
						<button>登录</button>
					</form>
				)
			}
		}
	ReactDOM.render(<Login/>,document.getElementById('container'));
</script>
```

* 受控组件（借助`state`实现）

```js
<script type="text/babel">
		class Login extends React.Component{
			//初始化状态
			state = {
				username:'', 
				password:'' 
			}
			//保存用户名到状态中
			saveUsername = (event)=>{
				this.setState({username:event.target.value})
			}
			//保存密码到状态中
			savePassword = (event)=>{
				this.setState({password:event.target.value})
			}
			//表单提交的回调
			handleSubmit = (event)=>{
				event.preventDefault(); //阻止表单提交
				const {username,password} = this.state;
				alert(`你输入的用户名是：${username},你输入的密码是：${password}`)
			}
			render(){
				return(
					<form onSubmit={this.handleSubmit}>
						用户名：<input onChange={this.saveUsername} type="text" name="username"/>
						密码：<input onChange={this.savePassword} type="password" name="password"/>
						<button>登录</button>
					</form>
				)
			}
		}
	ReactDOM.render(<Login />,document.getElementById('container'));
</script>
```

可通过**函数柯里化**进行优化。

> 函数柯里化：通过函数调用继续返回函数的方式，实现多次接收参数最后统一处理的函数编码形式。 

```js
<script type="text/babel">
		class Login extends React.Component{
			state = {
				username:'', 
				password:'' 
			}
			saveFormData = (dataType)=>{
              return (event) =>{
					this.setState({[dataType]: event.target.value});
              }
			}
			//表单提交的回调
			handleSubmit = (event)=>{
				event.preventDefault(); //阻止表单提交
				const {username,password} = this.state;
				alert(`你输入的用户名是：${username},你输入的密码是：${password}`)
			}
			render(){
				return(
					<form onSubmit={this.handleSubmit}>
						用户名：<input onChange={this.saveFormData('username')} type="text" name="username"/>
						密码：<input onChange={this.saveFormData('password')} type="password" name="password"/>
						<button>登录</button>
					</form>
				)
			}
		}
	ReactDOM.render(<Login />,document.getElementById('container'));
</script>
```

也可通过如下方式实现。

```js
<script type="text/babel">
		class Login extends React.Component{
			state = {
				username:'', 
				password:'' 
			}
			saveFormData = (dataType, event)=>{
					this.setState({[dataType]: event.target.value});
              }
			}
			//表单提交的回调
			handleSubmit = (event)=>{
				event.preventDefault(); //阻止表单提交
				const {username,password} = this.state;
				alert(`你输入的用户名是：${username},你输入的密码是：${password}`)
			}
			render(){
				return(
					<form onSubmit={this.handleSubmit}>
						用户名：<input onChange={event => this.saveFormData('username', event)} type="text" name="username"/>
						密码：<input onChange={event => this.saveFormData('password', event)} type="password" name="password"/>
						<button>登录</button>
					</form>
				)
			}
		}
	ReactDOM.render(<Login />,document.getElementById('container'));
</script>
```

## `React`组件生命周期

`React`组件从创建到死亡会经历一些特定的阶段。包含一系列钩子函数，会在特定时刻调用。`React`组件生命周期主要分为3个阶段。

### 旧版本生命周期

<img src="https://tva1.sinaimg.cn/large/008i3skNly1gs9sgyf16sj30nd0imwfe.jpg" alt="2_react生命周期(旧)" style="zoom: 50%;" />

**初始化阶段**: 由`ReactDOM.render()`触发，初次渲染。

（1）`constructor()`

（2）`componentWillMount()`

（3）`render()`

（4）`componentDidMount() ` 【一般在这个钩子中做一些初始化事情，如开启定时器、发送网络请求、订阅消息等】

**2. 更新阶段**: 由组件内`this.setSate()`或父组件`render`触发。

（1）`shouldComponentUpdate()`

（2）`componentWillUpdate()`

（3）`render() `

（4）`componentDidUpdate()`

**3. 卸载组件**: 由`ReactDOM.unmountComponentAtNode()`触发。

（1）`componentWillUnmount()`【一般在这个钩子中做一些收尾的事，例如：关闭定时器、取消订阅消息】

```js
<script type="text/babel">
		class Count extends React.Component{
			constructor(props){
				console.log('Count---constructor');
				super(props);
				this.state = {count:0};
			}
			//加1按钮的回调
			add = ()=>{
				const {count} = this.state;
				this.setState({count:count+1});
			}
			//卸载组件按钮的回调
			death = ()=>{
				ReactDOM.unmountComponentAtNode(document.getElementById('container'));
			}
			//强制更新按钮的回调
			force = ()=>{
				this.forceUpdate();
			}
			//组件将要挂载的钩子
			componentWillMount(){
				console.log('Count---componentWillMount');
			}
			//组件挂载完毕的钩子
			componentDidMount(){
				console.log('Count---componentDidMount');
			}
			//组件将要卸载的钩子
			componentWillUnmount(){
				console.log('Count---componentWillUnmount');
			}
			//控制组件更新的“阀门”
			shouldComponentUpdate(){
				console.log('Count---shouldComponentUpdate');
				return true;
			}
			//组件将要更新的钩子
			componentWillUpdate(){
				console.log('Count---componentWillUpdate');
			}
			//组件更新完毕的钩子
			componentDidUpdate(){
				console.log('Count---componentDidUpdate');
			}
			render(){
				console.log('Count---render');
				const {count} = this.state;
				return(
					<div>
						<h2>当前求和为：{count}</h2>
						<button onClick={this.add}>点我+1</button>
						<button onClick={this.death}>卸载组件</button>
						<button onClick={this.force}>不更改任何状态中的数据，强制更新一下</button>
					</div>
				)
			}
		}
	ReactDOM.render(<Count/>,document.getElementById('container'));
</script>
```

```
输出结果：
Count---constructor
Count---componentWillMount
Count---render
Count---componentDidMount
点击按钮”点我+1“，输出：
Count---shouldComponentUpdate
Count---componentWillUpdate
Count---render
Count---componentDidUpdate
点击按钮”不更改任何状态中的数据，强制更新一下“，输出：
Count---componentWillUpdate
Count---render
Count---componentDidUpdate
点击按钮”卸载组件“，输出：
Count---componentWillUnmount
```

父子组件

```js
 <script type="text/babel">
		//父组件A
		class A extends React.Component{
			state = {carName:'奔驰'};
			changeCar = ()=>{
				this.setState({carName:'奥拓'});
			}
			render(){
				return(
					<div>
						<div>我是A组件</div>
						<button onClick={this.changeCar}>换车</button>
						<B carName={this.state.carName}/>
					</div>
				)
			}
		}		
		//子组件B
		class B extends React.Component{
			//组件将要接收新的props的钩子
			componentWillReceiveProps(props){
				console.log('B---componentWillReceiveProps',props);
			}

			//控制组件更新的“阀门”
			shouldComponentUpdate(){
				console.log('B---shouldComponentUpdate');
				return true;
			}
			//组件将要更新的钩子
			componentWillUpdate(){
				console.log('B---componentWillUpdate');
			}
			//组件更新完毕的钩子
			componentDidUpdate(){
				console.log('B---componentDidUpdate');
			}
			render(){
				console.log('B---render');
				return(
					<div>我是B组件，接收到的车是:{this.props.carName}</div>
				)
			}
		}
	ReactDOM.render(<A />,document.getElementById('container'));
</script>
```

```
点击按钮”换车“，输出：
B---componentWillReceiveProps {carName: "奥拓"}
B---shouldComponentUpdate
B---componentWillUpdate
B---render
B---componentDidUpdate
```

### 新版本生命周期

<img src="https://tva1.sinaimg.cn/large/008i3skNly1gs9tao9vcwj30vh0lwwg0.jpg" alt="3_react生命周期(新)" style="zoom:50%;" />

**1. 初始化阶段**: 由`ReactDOM.render()`触发，初次渲染。

（1）`constructor()`

（2）`getDerivedStateFromProps `

（3）`render()`

（4）`componentDidMount() `【 一般在这个钩子中做一些初始化事情，如开启定时器、发送网络请求、订阅消息】

**2. 更新阶段**: 由组件内部`this.setSate()`或父组件重新`render`触发。

（1）`getDerivedStateFromProps`

（2）`shouldComponentUpdate()`

（3）`render()`

（4）`getSnapshotBeforeUpdate`

（5）`componentDidUpdate()`

**3. 卸载组件**: 由`ReactDOM.unmountComponentAtNode()`触发。

（1）`componentWillUnmount()`【一般在这个钩子中做一些收尾的事，例如：关闭定时器、取消订阅消息】

```js
<script type="text/babel">
		class Count extends React.Component{
			constructor(props){
				console.log('Count---constructor');
				super(props);
				this.state = {count:0};
			}
			add = ()=>{
				const {count} = this.state;
				this.setState({count:count+1});
			}
			//卸载组件按钮的回调
			death = ()=>{
				ReactDOM.unmountComponentAtNode(document.getElementById('container'));
			}
			//强制更新按钮的回调
			force = ()=>{
				this.forceUpdate();
			}
			//若state的值在任何时候都取决于props，那么可以使用getDerivedStateFromProps
			static getDerivedStateFromProps(props,state){
				console.log('getDerivedStateFromProps',props,state);
				return null;
			}
			//在更新之前获取快照
			getSnapshotBeforeUpdate(){
				console.log('getSnapshotBeforeUpdate');
				return 'react';
			}
			//组件挂载完毕的钩子
			componentDidMount(){
				console.log('Count---componentDidMount');
			}
			//组件将要卸载的钩子
			componentWillUnmount(){
				console.log('Count---componentWillUnmount');
			}
			//控制组件更新的“阀门”
			shouldComponentUpdate(){
				console.log('Count---shouldComponentUpdate');
				return true;
			}
			//组件更新完毕的钩子
			componentDidUpdate(preProps,preState,snapshotValue){
				console.log('Count---componentDidUpdate',preProps,preState,snapshotValue);
			}
			render(){
				console.log('Count---render');
				const {count} = this.state;
				return(
					<div>
						<h2>当前求和为：{count}</h2>
						<button onClick={this.add}>点我+1</button>
						<button onClick={this.death}>卸载组件</button>
						<button onClick={this.force}>不更改任何状态中的数据，强制更新一下</button>
					</div>
				)
			}
		}
	ReactDOM.render(<Count count={99}/>,document.getElementById('container'));
</script>
```

> **生命周期中最常用的比较重要的钩子**：
>
> * `render`：初始化渲染或更新渲染调用。
> * `componentDidMount`：开启监听，发送请求
> * `componentWillUnmount`：做一些收尾工作，如定时器清理等。

## `React`应用

### `React`脚手架

使用`React` 脚手架创建项目并启动，具体流程如下：

1. 全局安装：`npm i -g create-react-app`

2. 切换到想创项目的目录，使用命令：`create-react-app react-demo`

3. 进入项目文件夹：`cd react-demo`

4. 启动项目：`npm start`

`React`项目结构

```
public ---- 静态资源文件夹
		favicon.icon ------ 网站页签图标
		index.html -------- 主页面
		logo192.png ------- logo图
		logo512.png ------- logo图
		manifest.json ----- 应用加壳的配置文件
		robots.txt -------- 爬虫协议文件
src ---- 源码文件夹
		App.css -------- App组件的样式
		App.js --------- App组件
		App.test.js ---- 用于给App做测试
		index.css ------ 样式
		index.js ------- 入口文件
		logo.svg ------- logo图
		reportWebVitals.js ------ 页面性能分析文件(需要web-vitals库的支持)
		setupTests.js ------- 组件单元测试的文件(需要jest-dom库的支持)
.gitignore
package.json
README.md
```

## `React ajax`

`React`本身只关注于界面, 并不包含发送`ajax`请求。前端应用需要通过`ajax`请求与后台进行交互。其中常用的`ajax`请求库中`axios`较为常用。

### `axios`

封装`XMLHttpRequest`对象的`ajax`，可以用在浏览器端和`node`服务端，也是一种`promise`风格。

文档：https://github.com/axios/axios

#### 相关API

* `GET请求

```js
axios.get('url')
  .then(function (response) {
    console.log(response.data);
  })
  .catch(function (error) {
    console.log(error);
  });

axios.get('url', {
    params: {
      key: value
    }
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

* `POST`请求

```js
axios.post('url', {
  firstName: 'zhang',
  lastName: 'san'
})
.then(function (response) {
console.log(response);
})
.catch(function (error) {
console.log(error);
});
```

### `Fetch`

是一个原生函数，不再使用`XmlHttpRequest`对象提交`ajax`请求。

文档：https://github.github.io/fetch/

#### 相关API

* `GET`请求

```js
fetch(url).then(function(response) {
    return response.json();
  }).then(function(data) {
    console.log(data);
  }).catch(function(e) {
    console.log(e);
  });
```

* `POST`请求

```js
fetch(url, {
    method: "POST",
    body: JSON.stringify(data),
  }).then(function(data) {
    console.log(data);
  }).catch(function(e) {
    console.log(e);
  })
```

## `React`路由

### `SPA`

是指单页`Web`应用（`single page web application，SPA`）。整个应用只有一个完整的页面。点击页面中的链接不会刷新整个页面，只会做页面的局部更新。数据都需要通过`ajax`请求获取, 并在前端异步展现。

###  路由

一个路由就是一个映射关系(`key:value`)。`key`为路径, `value`可能是`function`或`component`。

#### 路由分类

* 后端路由：

1) value`是`function`, 用来处理客户端提交的请求。

2) 注册路由：` router.get(path, function(req, res))`

3) 工作过程：当`node`接收到一个请求时, 根据请求路径找到匹配的路由, 调用路由中的函数来处理请求, 返回响应数据。

* 前端路由：

1) 浏览器端路由，`value`是`component`，用于展示页面内容。

2) 注册路由: `<Route path="/test" component={Test}>`

3) 工作过程：当浏览器的`path`变为`/test`时, 当前路由组件就会变为`Test`组件

### `react-router-dom`

是`react`的一个插件库。专门用来实现一个`SPA`应用。

内置组件：`<BrowserRouter>、<HashRouter>、<Route>、<Redirect>、<Link>、<NavLink>、 <Switch>`等。

```react
// 路由链接
<Link className="list-group-item" to="/about">About</Link>
// 注册路由
<Route path="/about" component={About}/>
<Redirect to="/about"/>
```

## `Redux`

`redux`是一个专门用于做**状态管理**的`JS`库。在`React`应用中，可以集中式管理`React`应用中多个组件共享的状态。

### 文档网址

英文文档: https://redux.js.org/

中文文档: http://www.redux.org.cn/

Github: https://github.com/reactjs/redux

### `redux`使用场景

* 共享：某个组件的状态，需要让其他组件可以随时拿到。
* 通信：一个组件需要改变另一个组件的状态。

### `redux`工作流程

<img src="https://tva1.sinaimg.cn/large/008i3skNly1gsaqfiwlv3j30zk0k0n04.jpg" alt="redux原理图" style="zoom: 50%;" />

#### `redux`的3个核心概念及API

#### `action`

表示动作的对象，包含2个属性：

`type`：标识属性, 值为字符串, 唯一, 必要属性

`data`：数据属性, 值类型任意, 可选属性

示例：`{ type: 'ADD_STUDENT',data:{name: 'tom',age:18} }`

#### `reducer`

用于初始化状态、加工状态。加工时，根据旧的`state`和`action`， 产生新的`state`的**纯函数**。

> ##### 纯函数
>
> 理解：只要是同样的输入(实参)，必定得到同样的输出(返回)。
>
> 必须遵守以下一些约束 ：
>
> 1) 不得改写参数数据
>
> 2) 不会产生任何副作用，例如网络请求，输入和输出设备。
>
> 3) 不能调用Date.now()或者Math.random()等不纯的方法 。

#### `store`

将`state、action、reducer`联系在一起的对象。

**获取此对象**

```js
import {createStore} from 'redux'
import reducer from './reducers'
const store = createStore(reducer);
```

**此对象的功能**

1) `getState()`: 得到`state`。

2) `dispatch(action)`: 分发`action`, 触发`reducer`调用, 产生新的`state`。

3) `subscribe(listener)`: 注册监听, 当产生了新的`state`时, 自动调用。

#### `combineReducers()`

作用：合并多个reducer函数



