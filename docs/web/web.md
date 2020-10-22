## 跨域通信

![截屏2020-09-0310.53.53](https://tva1.sinaimg.cn/large/007S8ZIlly1gid9wvf3vgj30xb0u0wlb.jpg)

### 同源策略的概念和具体限制

**同源策略**：**限制从一个源加载的文档或脚本如何与来自另一个源的资源进行交互**。这是一个用于隔离潜在恶意文件的关键的安全机制。（来自MDN官方的解释）

具体解释：

（1）`源`包括三个部分：协议、域名、端口（http协议的默认端口是80）。如果有任何一个部分不同，则`源`不同，那就是跨域了。

（2）`限制`：**这个源的文档没有权利去操作另一个源的文档**。这个限制体现在：（要记住）

- Cookie、LocalStorage和IndexDB无法获取。
- 无法获取和操作DOM。
- 不能发送Ajax请求。我们要注意，Ajax只适合**同源**的通信。

### 前后端如何通信

主要有以下几种方式：

- Ajax：不支持跨域。
- WebSocket：不受同源策略的限制，支持跨域。
- CORS：不受同源策略的限制，支持跨域。一种新的通信协议标准。可以理解成是：**同时支持同源和跨域的Ajax**。

## 跨域通信的几种方式

方式如下：

- 1、JSONP
- 2、WebSocket
- 3、CORS
- 4、Hash
- 5、postMessage

上面这五种方式，在面试时，都要说出来。

#### 1、JSONP

在CORS和postMessage以前，我们一直都是通过JSONP来做跨域通信的。

**JSONP的原理**：**通过`<script>`标签的异步加载来实现的**。比如说，实际开发中，我们发现，head标签里，可以通过`<script>`标签的src，里面放url，加载很多在线的插件。这就是用到了JSONP。

**JSONP的实现：**

比如说，客户端这样写：

```js
<script src="http://www.smyhvae.com/?data=name&callback=myjsonp"></script>
```

上面的src中，`data=name`是get请求的参数，`myjsonp`是和后台约定好的函数名。 服务器端这样写：

```js
myjsonp({
data: {}

})
```

于是，本地要求创建一个myjsonp 的**全局函数**，才能将返回的数据执行出来。

#### 2、WebSocket

[WebSocket](http://websocket.org/) 是一种网络通信协议，我们已经有了 HTTP 协议，为什么还需要另一个协议？它能带来什么好处？因为 HTTP 协议有一个缺陷：通信只能由客户端发起。**HTTP 协议做不到服务器主动向客户端推送信息。**

这种单向请求的特点，注定了如果服务器有连续的状态变化，客户端要获知就非常麻烦。我们只能使用["轮询"](https://www.pubnub.com/blog/2014-12-01-http-long-polling/)：每隔一段时候，就发出一个询问，了解服务器有没有新的信息。最典型的场景就是聊天室。

WebSocket 协议在2008年诞生，2011年成为国际标准。所有浏览器都已经支持了。

它的最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于[服务器推送技术](https://en.wikipedia.org/wiki/Push_technology)的一种。

其他特点包括：

（1）建立在 TCP 协议之上，服务器端的实现比较容易。

（2）与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。

（3）数据格式比较轻量，性能开销小，通信高效。

（4）可以发送文本，也可以发送二进制数据。

（5）**没有同源限制，客户端可以与任意服务器通信**。

（6）协议标识符是`ws`（如果加密，则为`wss`），服务器网址就是 URL。

WebSocket的用法如下：

```js
var ws = new WebSocket('wss://echo.websocket.org'); //创建WebSocket的对象。参数可以是 ws 或 wss，后者表示加密。

//把请求发出去
ws.onopen = function (evt) {
console.log('Connection open ...');
ws.send('Hello WebSockets!');
};


//对方发消息过来时，我接收
ws.onmessage = function (evt) {
console.log('Received Message: ', evt.data);
ws.close();
};

//关闭连接
ws.onclose = function (evt) {
console.log('Connection closed.');
};
```

#### 3、CORS

CORS 可以理解成是**既可以同步、也可以异步**的Ajax。

fetch 是一个比较新的API，用来实现CORS通信。用法如下：

```
      // url（必选），options（可选）
      fetch('/some/url/', {
          method: 'get',
      }).then(function (response) {  //类似于 ES6中的promise

      }).catch(function (err) {
        // 出错了，等价于 then 的第二个参数，但这样更好用更直观
      });
```

> CORS的推荐链接：http://www.ruanyifeng.com/blog/2016/04/cors.html

另外，如果面试官问：“CORS为什么支持跨域的通信？”

答案：**跨域时，浏览器会拦截Ajax请求，并在http头中加Origin。**

#### 4、Hash

url的`#`后面的内容就叫Hash。**Hash的改变，页面不会刷新**。这就是用 Hash 做跨域通信的基本原理。

补充：url的`?`后面的内容叫Search。Search的改变，会导致页面刷新，因此不能做跨域通信。

**场景**：我的页面 A 通过iframe或frame嵌入了跨域的页面 B。

现在，我这个A页面想给B页面发消息，怎么操作呢？

#### 5、postMessage()方法

> H5中新增的postMessage()方法，可以用来做跨域通信。既然是H5中新增的，那就一定要提到。

**场景**：窗口 A (`http:A.com`)向跨域的窗口 B (`http:B.com`)发送信息。步骤如下。

（1）在A窗口中操作如下：向B窗口发送数据：

```js
	// 窗口A(http:A.com)向跨域的窗口B(http:B.com)发送信息
 	Bwindow.postMessage('data', 'http://B.com'); //这里强调的是B窗口里的window对象
```

（2）在B窗口中操作如下：

```js
    // 在窗口B中监听 message 事件
    Awindow.addEventListener('message', function (event) {   //这里强调的是A窗口里的window对象
        console.log(event.origin);  //获取 ：url。这里指：http://A.com
        console.log(event.source);  //获取：A window对象
        console.log(event.data);    //获取传过来的数据
    }, false);
```

## 对Websocke和HTTP对比

Websocket和HTTP都是基于TCP协议两个不同的协议

首先Websocket是一种在单个TCP连接上进行全双工通信的协议，允许服务端主动向客户端推送数据（只需要一次握手，两者之间创建持久化连接，可进行双向数据传输）；而HTTP则需要定时轮询向服务器请求，然后服务器再向客户端发送数据

其次，HTTP是一个无状态协议，缺少状态意味着后续处理需要先前数据必须重传；而Websocket需要先创建连接，这就使得其成为一种有状态的协议，之后通信时可以省略部分状态信息

应用场景：如果应用是多个用户相互交流，或者展示服务端经常变动的数据都可以考虑websocket；所以体育实况，多媒体聊天等这些都是Websocket不错的应用场景

## 同步和异步

### 概念

- 同步：必须等待前面的任务完成，才能继续后面的任务。

- 异步：不受当前任务的影响。

### 异步更新网站

我们在访问一个普通的网站时，当浏览器加载完`HTML、CSS、JS`以后，网站的内容就固定了。如果想让网站内容发生更改，就必须**刷新**页面才能够看到更新的内容。

可如果用到**异步更新**，情况就大为改观了。比如，我们在访问新浪微博时，看到一大半了，点击底部的**加载更多**，会自动帮我们加载更多的微博，同时**页面并没有刷新**。

试想一下，如果没有异步刷新的话，每次点击“加载更多”，网页都要刷新，体验就太不好了。

`web`前端里的异步更新，就要用到 `Ajax`。

## `Ajax`

### 概念

`Ajax`（`Asynchronous Javascript And XML`：异步 `JavaScript `和 `XML`）。它是对于现有技术的结合。**`Ajax `的核心是 `js `对象**：**`XMLHttpRequest`**。也就是使用 `XMLHttpRequest `对象与服务器通信。 它可以使用 `JSON，XML，HTML` 和 `text` 文本等格式发送和接收数据。

在浏览器中，我们可以在不刷新页面的情况下，通过`ajax`的方式与服务器通信，交换数据，或更新页面，去获取一些新的内容。

### `XMLHttpRequest `对象详解

#### 创建异步对象

```js
var xhr;
if (window.XMLHttpRequest) {
	// Mozilla, Safari, IE7+ ...
  hr = new XMLHttpRequest();
} else if (window.ActiveXObject) {
  // IE 6 and older
  xhr = new ActiveXObject("Microsoft.XMLHTTP");
}
```

#### 发送请求

发送请求的方法：

```javascript
// method：请求的类型；GET 或 POST;url：文件在服务器上的位置;async：true（异步）或 false（同步）
open(method, url, async);
```

#### `GET`请求

```js
xhr.open('get', '请求的url');
xhr.send();
```

#### `POST`请求

如果想让 像`form` 表单提交数据那样使用`POST`请求，就需要使用 `XMLHttpRequest `对象的 `setRequestHeader()`方法 来添加 `HTTP `头。然后在 `send()` 方法中添加想要发送的数据：

```js
xhr.open('post', '请求的url', true);
xhr.setRequestHeader('Content-type', 'application/x-wwww-form-urlencoded');
xhr.send('name=tom&age=18');
```

### `onreadystatechange` 事件

注册 `onreadystatechange` 事件后，每当 `readyState `属性改变时，就会调用 `onreadystatechange` 函数。

```js
xhr.onreadystatechange = function () {
  // Process the server response here.
};
```

##### `readyState`状态

- 0: 请求未初始化

- 1: 服务器连接已建立

- 2: 请求已接收

- 3: 请求处理中

- 4: 请求已完成，且响应已就绪

##### `status`状态

- 200: "OK"。

- 404: 未找到页面。

在 `onreadystatechange` 事件中，**当 `readyState` 等于 4，且状态码为200时，表示响应已就绪**。

#### 服务器响应的内容

- `responseText`：获得字符串形式的响应数据。

- `responseXML`：获得 `XML` 形式的响应数据。

如果响应的是普通字符串，就使用`responseText`；如果响应的是`XML`，使用`responseXML`。

### 发送 Ajax 请求的五个步骤

> （1）创建异步对象。即 `XMLHttpRequest `对象。
>
> （2）使用`open`方法设置请求的参数。`open(method, url, async)`（请求的方法、请求的url、是否异步）。
>
> （3）发送请求。
>
> （4）注册事件。 注册`onreadystatechange`事件，状态改变时就会调用。如果要在数据完整请求回来的时候才调用，我们需要手动写一些判断的逻辑。
>
> （5）获取返回的数据。

#### `Ajax` 请求实例

##### `GET`请求示例

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ajax的get请求</title>
</head>

<body>
    <h1>发送get请求</h1>
    <input type="button" value="发送get请求" id="getAjax">

    <script type="text/javascript">
        document.querySelector("getAjax").onclick = function() {
            // 发送ajax请求
            var xhr = new XMLHttpRequest();
            xhr.open('get', '请求的url');
            xhr.send();
            xhr.onreadystatechange = function() {
                if (xhr.readyState == 4 && xhr.status == 200) {
                    console('数据返回成功');
                    console.log(xhr.responseText);
                    document.querySelector('h1').innerHTML = xhr.responseText;
                }
            }
        }
    </script>
</body>

</html>
```

##### `POST`请求示例

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ajax的Post请求</title>
</head>

<body>
    <h1>发送post请求</h1>
    <input type="button" value="发送Post请求" id="postAjax">

    <script type="text/javascript">
        var xhr = new XMLHttpRequest();
        xhr.open('post', '请求的url');
        // 如果想要使用post提交数据,必须添加此行
        xhr.setRequestHeader('Content-type', 'application/x-wwww-form-urlencoded');
        // 将数据通过send方法传递
        xhr.send('name=tom&age=18');
        xhr.onreadystatechange = function() {
            if (xhr.readyState == 4 && xhr.status == 200) {
                console.log(xhr.responseText);
            }
        }
    </script>
</body>

</html>
```

### `Ajax` 传输 `JSON`

#### `JSON `的语法

`JSON(JavaScript Object Notation)`：是`ECMAScript`的子集。作用是**进行数据的交换**。语法更为简洁，网络传输、机器解析都更为迅速。

##### 语法规则：

- 数据在键值对中

- 数据由逗号分隔

- 花括号保存对象

- 方括号保存数组

##### 数据类型：

- 数字（整数或浮点数）

- 字符串（在双引号中）

- 逻辑值（true 或 false）

- 数组（在方括号中）

- 对象（在花括号中）

- null

示例：

```json
// 对象
{
  "name":"fox",
  "age":"18",
  "sex":"true",
  "car":null
}

// 数组
[
  {
      "name":"小小胡",
      "age":"1"
  },
  {
      "name":"小二胡",
      "age":"2"
  }
]
```

#### json 字符串 与 js 对象转换

基本所有的语言都有**将 json 字符串转化为该语言对象**的语法。

##### `JS`中的转换

`JSON.parse()`：将`JSON`字符串转化为 `js` 对象。例如：

```js
var jsObj = JSON.parse(ajax.responseText);
```

`JSON.stringify()`：将 `JS` 对象转化为`JSON`字符串。例如：

```js
var Obj = {
	  name: "tom",
	  age: 18,
	};
	// 将 js 对象格式化为 JSON 字符串
	var jsonStr = JSON.stringify(Obj);
```

##### `PHP`中的转换

- `json_decode()`方法：将`json`字符串转化为变量。

- `json_encode()`方法：将变量转化为`json`字符串。

#### `jQuery `中的 `Ajax`

`JQuery`作为最受欢迎的`JS`框架之一，常见的`Ajax`已经帮助我们封装好了，只需要调用即可。更为详细的api文档可以查阅：[w3cSchool_JQueryAjax](http://www.w3school.com.cn/jquery/jquery_ref_ajax.asp)

格式：

```js
$.ajax({
            url: '请求地址',
            data: 'name=tom&age=16',
            type: 'POST',
            // 请求成功执行的方法
            success: function(argument) {},
            // 在发送请求之前调用，可以做一些验证之类的处理
            beforeSend: function(argument) {}, 
            // 请求失败调用
            error: function(argument) {},
        })
```

### Ajax 与 cookie

- ajax 会自动带上同源的 cookie，不会带上不同源的 cookie
- 可以通过前端设置 withCredentials 为 true， 后端设置 Header 的方式让 ajax 自动带上不同源的 cookie，但是这个属性对同源请求没有任何影响。会被自动忽略。

[withCredentials | MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/withCredentials)

```js
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://example.com/", true);
xhr.withCredentials = true;
xhr.send(null);
```

## Ajax函数封装

此部分把ajax发送HTTP的常用请求进行封装，方便调用，减少代码冗余，解耦合，在编写项目时可以作为工具类使用。

#### 请求封装

```js
// get请求封装
        function getAjax(url, data) {
            var xhr = new XMLHttpRequest();
            if (data) {
                url += '?';
                url += data;
            } else {}
            xhr.open('get', url);
            xhr.send();
            xhr.onreadystatechange = function() {
                if (xhr.readyState == 4 && ajax.status == 200) {
                    console.log(ajax.responseText);
                }
            }
        }

        // post请求封装
        function postAjax(url, data) {
            var xhr = new XMLHttpRequest();
            xhr.open('post', url);
            xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
            if (data) {
                xhr.send(data);
            } else {
                xhr.send();
            }
            xhr.onreadystatechange = function() {
                if (xhr.readyState == 4 && xhr.status == 200) {
                    console.log(xhr.responseText);
                }
            }
        }

        // get和post一起封装
        /*
            参数1:url
            参数2:数据
            参数3:请求的方法
            参数4:数据成功获取以后，调用的方法
        */
        function ajaxTool(url, data, method, success) {
            var xhr = new XMLHttpRequest();
            // 如果忽略大小写呢
            if (method == 'get') {
                if (data) {
                    url += '?';
                    url += data;
                } else {}
                xhr.open(method, url);
                xhr.send();
            } else {
                xhr.open(method, url);
                xhr.setRequestHeader('Content-type', 'application-x-www-form-urlencoded');
                if (data) {
                    xhr.send(data);
                } else {
                    xhr.send();
                }
            }

            xhr.onreadystatechange = function() {
                if (xhr.readyState == 4 && xhr.status == 200) {
                    console.log(xhr.responseText);
                    success(xhr.responseText);
                }
            }

        }
```

#### 函数调用

```js
// get请求
var resData = ajaxTool('url', 'data', 'get', function(data) {
            console.log(data);
        })
        console.log(resData);
// post请求
var resData = ajaxTool('url', 'data', 'post', function(data) {
            console.log(data);
        })
        console.log(resData);
```

## 同源和跨域

### 同源

同源策略是浏览器的一种安全策略，所谓同源是指**域名，协议，端口完全相同**。

### 跨域

从我们自己的网站访问别人网站的内容，就叫跨域。

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ghozvbow3wj30jj09egos.jpg)

出于安全性考虑，浏览器不允许ajax跨域获取数据。


- `iframe`：出于安全性考虑，浏览器的开发厂商已经禁止了这种方式。

- `JSONP`：`script `标签的 `src `属性传递数据。

#### `JSONP`

`JSONP(JSON with Padding)`：带补丁的 `json`，本质是利用了 `<script src=""></script>`标签具有可跨域的特性，由服务端返回一个预先定义好的JS函数的调用，并且将服务器数据以该函数参数的形式传递过来。此方法需要前后端配合完成。

html标签的 src 属性是支持跨域的：

```html
	<img src="http://xxx.com/xxx.jpg" alt="">
```

`jsonp `就是利用这个特性实现的跨域，但用的是 `script` 标签。如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<body>

<!-- jsonp 就是 利用 src，实现的跨域 用的是 script标签 -->
<script type="text/javascript"  src='http://192.168.141.137/2018-02-28/myData.php'></script>
</body>
</html>
```

上述代码的意思是：刷新A服务器上的`index`页面后，会去请求 B 服务器上的 `myData.php` 这个页面。而且请求的方式是` get` 请求。但是 B 服务器上的页面不是你想请求就可以请求的，大家一起配合才可以。

**具体实现步骤：**

需要首先声明的是，**`jsonp `只能通过 `GET` 方式进行请求**。

（1）A客户端的代码：

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Document</title>
</head>
<body>

</body>
</html>
<script type="text/javascript">

	// 定义 fn() 方法
	function fn(data) {
		console.log('我被调用了哦');
		console.log(data);
	}
</script>

<!-- 使用 script标签 发送了 get请求 去到了一个 php页面 -->
<script type="text/javascript" src='http://192.168.141.137/01.php?callback1=fn'></script>
```


我们来分析上方代码中的最后一行的那个url：A 客户端请求的是 B服务器上的 `01.php`页面。url里有个`callback1=fn`，意思是：callback1是A和B 之间的约定，约定后，将执行方法 fn。

其实，fn方法已经在最后一行代码中执行了。只不过，fn方法里的data数据，是从 B 服务器中获取的。

（2）B服务器端的代码：

```php
<?php
    $mycallBack = $_GET['callback1'];

	$arr = array("zhangsan","lisi","zhaoliu");

    echo $mycallBack.`(`.json_encode($arr).`)`;    //字符串拼接
?>
```

代码解释：

第一行的`callback1` 是A和B之间的约定，二者必须一致。

echo语句中输出的内容，即使要返回给A客户端的内容，此内容会保存在 A 客户端的fn方法的data里。 data[0]指的是 zhangsan。


`json_encode`指的是，将php对象转化为 json。


刷新A页面，输出结果为：

```
	mycallBack(["zhangsan","lisi","zhaoliu"])
```

#### jQuery 中的 JSONP

jQuery 中发送 Ajax 请求，格式是：

```javascript
		$("#btn").click(function(){
			$.ajax({
				url:"./data.php?callback1=fn",
				dataType:"jsonp",
				type:"get",
				//jsonp:"callback1",   //传递给B服务器的回调函数的名字（默认为 callback）
				//jsonCallBack:"fn"    //自定义的函数名称。默认为 jQuery 自动生成的随机函数名
				success:function(data){
					alert(data);
					//$("#showInfo").html(data);
				},
				error:function(e){
					console.log(e);
				}
			});
		});
```


那如果数据是 JSONP，上方代码则改为：


```javascript
		$("#btn").click(function(){
			$.ajax({
				url:"./data.php?fn",
				dataType:"text",
				type:"get",
				success:function(data){
					alert(data);
					//$("#showInfo").html(data);
				},
				error:function(e){
					console.log(e);
				}
			});
		});
```

