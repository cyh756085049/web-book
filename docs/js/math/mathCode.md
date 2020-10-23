## 随机点名

根据上面的例子，我们还可以再延伸一下，来看看随机点名的例子。

```javascript
    /*
    * 生成两个整数之间的随机整数，并且要包含这两个整数
    */
    function getRandom(min, max) {
        return Math.floor(Math.random() * (max - min + 1)) + min;
    }

    const arr = ['许嵩', '邓紫棋', '毛不易', '解忧邵帅'];
    const index = getRandom(0, arr.length - 1); // 生成随机的index
    console.log(arr[index]); // 随机点名
```

## pow()：乘方

**举例1：**

![](http://img.smyhvae.com/20180117_1730.png)

代码实现：

```
	var a = Math.pow(3, Math.pow(2, 2));
	console.log(a);
```

**举例2：**

![](http://img.smyhvae.com/20180117_1740.png)

代码实现：

```
	var a = Math.pow(Math.pow(3, 2), 4);
	console.log(a);
```

## sqrt()：开方

如果想计算数值a的开二次方，可以使用如下函数：

```
	 Math.sqrt(a);
```

sqrt即“square 开方”。比如：

```
	var a = Math.sqrt(36);
```


