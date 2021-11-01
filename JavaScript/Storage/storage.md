# 本地存储

## 特性
1. 数据存储在用户浏览器中
2. 设置、读取方便，甚至页面刷新不丢失数据
3. 容量较大，sessionStorage 约5M、localStorage 约20M
4. 只能存储字符串，可以将对象 JSON.stringify() 编码后存储

## window.sessionStorage
1. 生命周期为关闭浏览器窗口
2. 在同一个窗口（页面）下数据可以共享
3. 以键值对的形式存储使用

**存储数据：**
```js
    sessionStorage.setItem(key, value)
```

**获取数据：**
```js
    sessionStorage.getItem(key)
```

**删除数据：**
```js
    sessionStorage.removeItem(key)
```

**删除所有数据：**
```js
    sessionStorage.clear()
```

## window.localStorage
1. 生命周期永久有效，除非手动删除，否则关闭页面也会存在
2. 可以多窗口（页面）共享（同一浏览器可以共享）
3. 以键值对的形式存储使用

**存储数据：**
```js
    localStorage.setItem(key, value)
```

**获取数据：**
```js
    localStorage.getItem(key)
```

**删除数据：**
```js
    localStorage.removeItem(key)
```

**删除所有数据：**
```js
    localStorage.clear()
```

## 应用

### 记住用户名案例

html:
```html
    <input id="input" type="text">
    <button id="btn">提交</button>
    <input id="check" type="checkbox"> 记住用户名
```

js:
```js
    window.onload = function() {
        let input = document.querySelector('#input');
        let btn = document.querySelector('#btn');
        let check = document.querySelector('#check');

        if(localStorage.getItem(input) != null) {
            input.value = localStorage.getItem(input);
        }

        btn.addEventListener('click', function() {
            // sessionStorage.setItem(input, input.value);
            if(check.checked) {
                localStorage.setItem(input, input.value);
                document.write(localStorage.getItem(input));
            }
        })
    }
```