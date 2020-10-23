## 模拟日历

要求每天打开这个页面，都能定时显示当前的日期。

代码实现：

```html
<!DOCTYPE html>
<html>
    <head lang="en">
        <meta charset="UTF-8" />
        <title></title>
        <style>
            div {
                width: 800px;
                margin: 200px auto;
                color: red;
                text-align: center;
                font: 600 30px/30px 'simsun';
            }
        </style>
    </head>
    <body>
        <div></div>

        <script>
            //模拟日历
            //需求：每天打开这个页面都能定时显示年月日和星期几
            function getCurrentDate() {
                //1.创建一个当前日期的日期对象
                const date = new Date();
                //2.然后获取其中的年、月、日和星期
                const year = date.getFullYear();
                const month = date.getMonth();
                const hao = date.getDate();
                const week = date.getDay();
                //        console.log(year+" "+month+" "+hao+" "+week);
                //3.赋值给div
                const arr = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六'];
                const div = document.getElementsByTagName('div')[0];
                return '今天是：' + year + '年' + (month + 1) + '月' + hao + '日 ' + arr[week];
            }

            const div = document.getElementsByTagName('div')[0];
            div.innerText = getCurrentDate();
        </script>
    </body>
</html>
```

## 发布会倒计时

实现思路：

- 设置一个定时器，每间隔1毫秒就自动刷新一次div的内容。

- 核心算法：输入的时间戳减去当前的时间戳，就是剩余时间（即倒计时），然后转换成时分秒。

代码实现：

```html
<!DOCTYPE html>
<html>
    <head lang="en">
        <meta charset="UTF-8" />
        <title></title>
        <style>
            div {
                width: 1210px;
                margin: 200px auto;
                color: red;
                text-align: center;
                font: 600 30px/30px 'simsun';
            }
        </style>
    </head>
    <body>
        <div></div>

        <script>
            var div = document.getElementsByTagName('div')[0];

            var timer = setInterval(() => {
                countDown('2022/02/03 11:20:00');
            }, 1);

            function countDown(myTime) {
                var nowTime = new Date();
                var future = new Date(myTime);
                var timeSum = future.getTime() - nowTime.getTime(); //获取时间差：发布会时间减去此刻的毫秒值

                var day = parseInt(timeSum / 1000 / 60 / 60 / 24); // 天
                var hour = parseInt((timeSum / 1000 / 60 / 60) % 24); // 时
                var minu = parseInt((timeSum / 1000 / 60) % 60); // 分
                var sec = parseInt((timeSum / 1000) % 60); // 秒
                var millsec = parseInt(timeSum % 1000); // 毫秒

                //细节处理：所有的时间小于10的时候，在前面自动补0，毫秒值要补双0（比如如，把 8 秒改成 08 秒）
                day = day < 10 ? '0' + day : day; //day小于10吗？如果小于，就补0；如果不小于，就是day本身
                hour = hour < 10 ? '0' + hour : hour;
                minu = minu < 10 ? '0' + minu : minu;
                sec = sec < 10 ? '0' + sec : sec;
                if (millsec < 10) {
                    millsec = '00' + millsec;
                } else if (millsec < 100) {
                    millsec = '0' + millsec;
                }

                // 兜底处理
                if (timeSum < 0) {
                    div.innerHTML = '距离苹果发布会还有00天00小时00分00秒000毫秒';
                    clearInterval(timer);
                    return;
                }

                // 前端要显示的文案
                div.innerHTML = '距离苹果发布会还有' + day + '天' + hour + '小时' + minu + '分' + sec + '秒' + millsec + '毫秒';
            }
        </script>
    </body>
</html>

```
