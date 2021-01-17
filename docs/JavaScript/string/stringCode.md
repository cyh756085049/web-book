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

## 打印字符串的占位长度

提示：一个英文占一个位置，一个中文占两个位置。

思路：判断该字符是否在 0-127 之间（在的话是英文，不在是非英文）。

代码实现：

```html
<script>
    //    sort();   底层用到了charCodeAt();

    var str = 'I love my country!我你爱中国！';

    //需求：求一个字符串占有几个字符位。
    //思路；如果是英文，站一个字符位，如果不是英文占两个字符位。
    //技术点：判断该字符是否在0-127之间。（在的话是英文，不在是非英文）
    alert(getZFWlength(str));
    alert(str.length);

    //定义方法：字符位
    function getZFWlength(string) {
        //定义一个计数器
        var count = 0;
        for (var i = 0; i < string.length; i++) {
            //对每一位字符串进行判断，如果Unicode编码在0-127，计数器+1；否则+2
            if (string.charCodeAt(i) < 128 && string.charCodeAt(i) >= 0) {
                count++;
            } else {
                count += 2;
            }
        }
        return count;
    }
</script>
```

打印结果：

```
    30
    24
```

从打印结果可以看出：字符串的长度是 24，但是却占了 30 个字符位（一个中文占两个字符位）。

另外，sort()方法其实底层也是用到了 charCodeAt()，因为用到了 Unicode 编码。

## 模糊电话号码的后四位

举例2：（模糊字符串的后四位）

```js
const telephone = '13088889999';
const mix_telephone = telephone.slice(0, -4) + '*'.repeat(4); // 模糊电话号码的后四位

console.log(telephone); // 打印结果：13088889999
console.log(mix_telephone); // 打印结果：1308888****
```

## 查找字符串中某一字符所有出现的位置
> "smyhvaevaesmyh"查找字符串中所有 m 出现的位置。

代码实现：

```javascript
var str2 = 'abcoefoxyozzopp';
for (var i = 0; i < str2.length; i++) {
    //如果指定位置的符号=== "o"
    //str2[i]
    if (str2.charAt(i) === 'o') {
        console.log(i);
    }
}
```

## 统计字符串中出现次数最多的字符

```html
<script>
    var str2 = 'smyhvaevaesmyhvae';

    //定义一个json，然后判断json中是够有该属性，如果有该属性，那么值+1;否则创建一个该属性，并赋值为1；
    var json = {};
    for (var i = 0; i < str2.length; i++) {
        //判断：如果有该属性，那么值+1;否则创建一个该属性，并赋值为1；
        var key = str2.charAt(i);
        if (json[key] === undefined) {
            json[key] = 1;
        } else {
            json[key] += 1;
        }
    }
    console.log(json);

    console.log('----------------');
    //获取json中属性值最大的选项
    var maxKey = '';
    var maxValue = 0;
    for (var k in json) {
        //        if(maxKey == ""){
        //            maxKey = k;
        //            maxValue = json[k];
        //        }else{
        if (json[k] > maxValue) {
            maxKey = k;
            maxValue = json[k];
        }
        //        }
    }
    console.log(maxKey);
    console.log(maxValue);
</script>
```
## url 编码和解码

举例：

```javascript
    var url = "http://www.cnblogs.com/smyhvae/";

    var str = encodeURIComponent(url);
    console.log(str);                           //打印url的编码
    console.log(decodeURIComponent(str));       //对url进行编码后，再解码，还原为url
```