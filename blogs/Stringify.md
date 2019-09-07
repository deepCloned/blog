# JSON.stringify 与 qs.stringify 的区别

## JSON 是什么
> JSON 是一种语法，用来序列化对象、数组、数值、字符串、布尔值和 null 。它基于 JavaScript 语法，但与之不同：JavaScript不是JSON，JSON也不是JavaScript。

## JSON.stringify
> * 通常用于与服务端交换数据，浏览器向服务器发送的数据格式一般是字符串。
> * JSON.stringify() 用于把 JavaScript 对象转换为 JSON 字符串

```
let userData = {
  username: 'Tim',
  password: '123123123'
}
JSON.stringify(userData)
// userData => '{"username": "Tim","password": "123123123"}'
```

**JSON 解析不了的类型**

* JSON 不能存储 Date 对象，JSON.stringify() 会将所有日期转换为字符串

```
let date = new Date()
// date => Sat Sep 07 2019 11:14:10 GMT+0800 (台北标准时间)
let dateString = JSON.stringify(date)
typeof dateString  // "string"
```

* JSON 不允许包含函数，JSON.stringify() 会把对象中的函数的键和值都删除掉

```
let obj = {
  foo: function () {}
}
let objString = JSON.stringify(obj)
// objString => {}
```

## JSON.parse()
> * 既然说到的 JSON.stringify()，就不得不提到 JSON.parse()。
> * JSON.parse() 方法用来解析JSON字符串，即把 JSON 字符串解析解析成普通的 JavaScript 对象。

*小贴士：我们可以使用 JSON.parse(JSON.stringify(obj))实现对一个对象的深拷贝，但是值得注意的是 JSON 解析不了 Date 类型和 Function，如果有这些需求则需要使用其他方案。*

## qs.stringify

### qs
> qs 是 npm 上面的一个模块，我们可以通过 npm i qs 安装

### qs.stringify() 与 qs.parse()
> 与 JSON 比较类似，都是用来序列化对象和解析字符串为 JavaScript 对象。序列后的形式有所不同。
> qs.stringify() 将对象或者数组序列化成 URL 的格式(a=b&c=d)。

```
let userData = {
  username: 'tim',
  password: '123123123'
}
qs.stringify(userDate) => 'username=tim&password=123123123'
```

当然，通过 qs.parse() 解析的字符串也要是 URL 这种格式。

**实现一个简单的 qs.stringify()**

```
const qsStringify = function (obj) {
  if (obj === null) {
    return null
  }
  let arr = []
  for (let key in obj) {
    arr.push(key + '=' + obj[key])
  }
  return arr.join('&')
}
```

**实现一个简单的 qs.parse()**

```
const qsParse = function (str) {
  if (str === null) {
    return null
  }
  let obj = {}
  str.split('&').forEach((ele) => {
    let arr = ele.split('=')
    obj[arr[0]] = arr[1]
  })
  return obj
}
```

## 使用场景

* JSON.stringify() -- 请求头的 Content-Type 字段的值设为 application/json
* qs.stringify() -- 请求头的 Content-Type 字段的值设为 application/x-www-form-urlencoded

**如何设置请求头**
xhr 是一个 XMLHttpRequest 实例
xhr.setRequestHeader('Content-Type', 'application/json')