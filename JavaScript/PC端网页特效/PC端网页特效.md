# 1. 元素偏移量 offset 系列

## 1.1 offset 概述

offset 翻译过来就是偏移量，我们使用 offset 系列相关属性可以**动态的**得到该元素的位置（偏移）、大小等。

- 获得元素距离带有定位父元素的位置

- 获得元素自身的大小（宽度、高度）

- 注意：返回的数值都不带单位

  

  offset 系列常用属性：

| **offset系列属性**   | 作用                                                         |
| :------------------- | :----------------------------------------------------------- |
| element.offsetParent | 返回作为该属性带有定位的父级元素 如果父级元素都没有定位则返回 body |
| element.offsetTop    | 返回元素相对带有定位父元素上方的偏移                         |
| element.offsetLeft   | 返回元素相对带有定位父元素左边框的偏移                       |
| element.offsetWidth  | 返回自身包括 padding、边框、内容区的宽度，返回数值不带单位   |
| element.offsetHeight | 返回自身包括 padding、边框、内容区的高度，返回数值不带单位   |

## 1.2 offset 与 style 区别

**offset**

- offset 可以得到任意样式表中的样式值
- offset 系列获得的数值没有单位
- offsetWidth 包含 padding + border + width
- offsetWidth 等属性是只读属性，只能获取，不能赋值
- **所以，我们想要获取元素大小位置，用 offset 合适**

**style**

- stye只能更改行内样式表中的样式值
- style.width获得的是带有单位的字符串
- style.width获得不包含 padding和 border的
- style.width是可读写属性,可以获取也可以赋值
- **所以,我们想要给元素更改值,则需要用stye改变**

## 案例

### 获取鼠标坐标

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        #box { 
            margin: 100px auto;
            width: 500px;
            height: 500px;
            background-color: #000;
        }
    </style>
    <script>
        window.onload = function () {
            let msg = document.getElementById('msg')
            let box = document.getElementById('box')
            box.addEventListener('mousemove', function(event) {
                event = event || window.event
                let x = event.pageX - this.offsetLeft
                let y = event.pageY - this.offsetTop
                msg.innerHTML = '(' + x + ', ' + y + ')'
            })
        }
    </script>
</head>
<body>
    <div id="box"></div>
    <span id="msg">(0,0)</span>
</body>
</html>
```

### 模态框拖拽

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <style>
        #box {
            position: absolute;
            margin: 0px auto;
            width: 300px;
            height: 300px;
            background-color: #1aa;
        }
    </style>
    <script>
        window.onload = function() {
            let box = document.querySelector('#box')
            // 为 box 添加鼠标按下事件监听
            box.addEventListener('mousedown', function(event) {
                event = event || window.event
                // 获取鼠标相对于 box 的坐标
                let x = event.pageX - box.offsetLeft
                let y = event.pageY - box.offsetTop

                // 为 document 添加鼠标移动事件监听
                document.addEventListener('mousemove', move)

                function move(event) {
                    event = event || window.event
                    box.style.left = event.pageX - x + 'px'
                    box.style.top = event.pageY - y + 'px'
                }

                document.addEventListener('mouseup', function() {
                    document.removeEventListener('mousemove', move)
                })
            })
        }
    </script>
</head>
<body>
    <div id="box">

    </div>
</body>
</html>
```

