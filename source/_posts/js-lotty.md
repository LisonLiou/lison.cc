---
title: js随机生成双色球奖券代码
tags:
categories:
  - javascript
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2016-8-24 18:06:14
desc: 使用原生js，没有使用其他开源js或css库。点击一次生成一组红号加蓝号，也可自动滚动生成多组，支持暂停。
keywords: js生成双色球代码
---

![](data:image/png;base64,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)

<!--more-->

源码

```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>LOTTY WINNER~</title>
    <style type="text/css">
        html {
            font-size: 12px;
            font-family: "sans serif", tahoma, verdana, helvetica;
        }

        body {
            text-align: center;
            padding-top: 20%;
        }

        #pool {
            padding: 10px;
            border: dashed 1px orangered;
            width: 40%;
            min-height: 200px;
            margin-left: auto;
            margin-right: auto;
            max-height: 200px;
            overflow: auto;
            overflow-y: yes;
        }

        #pool span {
            font-size: 24px;
            line-height: 36px;
        }

        #pool .red {
            color: indianred;
        }

        #pool .blue {
            color: dodgerblue;
        }

        .title {
        }

        .red {
            color: indianred
        }

        .blue {
            color: dodgerblue
        }

        .green {
            color: forestgreen
        }

        .purple {
            color: mediumpurple
        }

        .pink {
            color: deeppink
        }

        .dark {
            color: black
        }

        .yellow {
            color: yellowgreen
        }
    </style>
    <script type="text/javascript">

        //红色选择六个数　1-33里选
        //蓝色选择一个数　1-16里选

        var ticket = {};
        window.onload = function () {
            var btn = document.getElementById('lotty');

            //摇奖按钮事件
            btn.addEventListener('click', function () {

                reset_ticket();
                for (var i = 0; i < 6; i++) {
                    ticket['red'].push(Math.floor(Math.random() * 33 + 1));
                }

                ticket['blue'] = Math.floor(Math.random() * 16 + 1);

                append_pull(ticket);
            });

            //自动滚动生成
            var scroll = document.getElementById("scroll");
            var interval;
            scroll.addEventListener('click', function () {
                if (scroll.innerHTML == 'SCROLL@') {
                    scroll.innerHTML = "PAUSE-";
                    //开始滚动
                    interval = setInterval(function () {
                        btn.click();
                    }, 1000);
                }
                else {
                    scroll.innerHTML = "SCROLL@";
                    clearInterval(interval);
                }
            });

            var clear = document.getElementById('clear');
            clear.addEventListener('click', function () {
                var pool = document.getElementById('pool');
                pool.innerHTML = '';
            });

            blink();
        };

        //title变换颜色class组
        var colors = ['red', 'blue', 'green', 'purple', 'pink', 'dark', 'yellow'];

        //闪烁标题
        var blink = function () {
            var title = document.getElementById('title');
            var titles = title.innerHTML.split('');

            setInterval(function () {
                var html = '';
                for (var i = 0; i < titles.length; i++) {
                    html += '<span class=' + colors[Math.floor(Math.random() * 7)] + '>' + titles[i] + '</span>';
                }
                title.innerHTML = html;
            }, 1000);
        };

        //生成结果滚动展示
        function append_pull(ticket) {
            var pool = document.getElementById('pool');
            pool.innerHTML = pool.innerHTML + "<span class=\'red\'>" + ticket['red'].join("    ") + "</span><span class=\'blue\'>   (" + ticket['blue'] + ")</span><br />";

            setInterval(scroll_end(pool), 100);
            reset_ticket();
        }

        //重置ticket
        function reset_ticket() {
            ticket = {};
            ticket['red'] = [];
            ticket['blue'] = null;
        }

        //滚动到对象底部
        function scroll_end(e) {
            e.scrollTop = e.scrollHeight;
        }
    </script>
</head>
<body>
<div><h1 class="title" id="title">LOTTY WINNER~!</h1></div>
<br/>
<div id="pool">

</div>
<br/>
<button id="lotty">LOTTY ME~!</button>
<button id="scroll">SCROLL@</button>
<button id="clear">CLEAR^</button>
</body>
</html>
```

