## 记住用户名和密码

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>登录记住用户名和密码</title>
</head>

<body>
    <label for="">
        用户名：<input type="text" class="userName">
    </label>
    <br/>
    <label for="">
        密码：<input type="text" class="pwd">
    </label>
    <br/>
    <label for="">
        <input type="checkbox" class="check"></input>记住密码
    </label>
    <br/>
    <button>登录</button>

    <script>
        var userName = document.querySelector(".userName");
        var pwd = document.querySelector(".pwd");
        var check = document.querySelector(".check");
        var btn = document.querySelector(".button");
        // 当点击登录的时候 如果勾选“记住密码”，就存储密码；否则就清除密码
        btn.onclick = function() {
                if (check.checked) {
                    // 记住数据
                    window.localStorage.setItem('userName', userName.value);
                    window.localStorage.setItem('pwd', pwd.value);
                } else {
                    // 清除数据
                    window.localStorage.removeItem('userName');
                    window.localStorage.removeItem('pwd');
                }
            }
            // 下次登录时，如果记录的有数据，就直接填充
        window.onload = function() {
            userName.value = window.localStorage.getItem('userName');
            pwd.value = window.localStorage.getItem('pwd');
        }
    </script>
</body>

</html>
```

