# AJAX
> Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）。

## 什么是 AJAX
> * 异步的 JavaScript 和 XML
> * 通过 AJAX，我们可以在不刷新整个页面的前提下，从后端请求数据，实现页面局部的内容刷新（传统网页要更新内容必须重载整个页面）。

## AJAX 工作原理
> 1、在某个阶段触发一个事件，然后浏览器创建一个 XMLHttpRequest 对象，向服务器发送一个请求信息；
> 2、服务器根据请求的信息作出响应，返回页面所需要的内容；
> 3、浏览器使用请求到的数据通过 JS 处理实现网页的更新。

## AJAX 请求过程
+ 创建 XMLHttpRequest 对象

```
let xhr;
if (window.XMLHttpRequest) {
  xhr = new XMLHttpRequest()
} else {
  xhr = new ActiveXObject('Microsolf.XMLHTTP')
}
```

+ 发送请求
  - GET

  ```
  /**
   * type -- 类型
   * url -- 请求地址
   * async -- 是否异步发起请求
   */
  xhr.open(type, url, async)

  ```
  - POST
  
  ```
  /**
   * POST 请求比 GET 请求多一步，发送数据
   */
  xhr.open()
  xhr.send(data)
  ```
  
  - 设置请求头
  
  ```
  xhr.setRequestHeader(key, value)
  // 一般前后端使用 JSON 数据格式进行交互，所以我们要设置请求头为
  ('Content-Type', 'application/json')
  ```
  
+ 根据 xhr 的 readyState 属性判断状态
  **readyState 属性的值**
    - 0：请求未初始化，open 还没有调用
    - 1：服务器连接已建立，已经调用了 send(),正在发送请求
    - 2：服务器已经接收到请求
    - 3：服务器正在处理请求
    - 4：请求已经完成，浏览器已经接收到响应数据

  **通过 XMLRequest 的 readyStateChange 事件感知 readyState 的变化**
  
  ```
  xmlHttp.onreadyStateChange = function () {
    if (xmlHttp.readyState === 4 || xmlHttp.status === 200) {
      // 请求到正确的相应内容之后的操作
    }
  }
  ```
  
## 封装一个 AJAX，用于处理 POST 和 GET 请求
**参数列表**

| 参数 | 说明 | 类型 | 可选值 | 默认值 |
|-|-|-|
| method | 请求方法 | String | GET/POST | GET |
| url | 请求地址 | String | 略 | 空 |
| async | 是否异步 | Boolean | true/false | true |
| data | 请求参数 | Object | {} | 空 |
| cb | 回调函数 | Function | func | 空 |


```
const request = function (options) {
  let xhr = null
  // 对传递的数据格式化
  let params = fomateParams(options.data)
  // 兼容处理 IE6、IE7
  if (window.XMLHttpRequest) {
    xhr = new XMLHttpRequest()
  } else {
    xhr = new ActiveXObject()
  }
  
  /**
   * 判断请求类型
   * GET 请求直接把格式化后的数据拼接到 URL 后面，不需要设置请求头，不需要调用 send 方法
   * POST 请求，设置请求头，调用 send 方法，传入 处理后的数据
   */
  if (options.type === 'GET') {
    xhr.open(options.type, options.url + params, options.async)
  } else if (options.type === 'POST') {
    xhr.setRequestHeader('Content-type', 'application/x-www-from-urlencoded')
    xhr.open(options.type, options.url, options.async)
    xhr.send(params)
  }

  /**
   * 绑定 readystatechange 事件，判断响应信息
   */
  xhr.addEventListener('readystatechange', function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
      options.success(xhr.responseText)
    }
  })
  
  // 定义格式化数据的方法，把对象形式参数转换成 URL 形式的字符串
  const fomateParams = function (obj) {
    if (obj === null) {
      return null
    }
    let arr = []
    for (let key in obj) {
      arr.push(key + '=' + obj[key])
    }
    return arr.join('&')
  }
}

// 调用方法
request({
 method: 'GET',
 url: '',
 async: true,
 data: {
  username: 'tim',
  password: '123123123'
 },
 success: function (res) {
   console.log(res)
 }
})
```

**上面是第一次写的版本，存在诸多问题**

* onreadystatechange 的字母都是小写，而不是驼峰 onReadyStateChange，readyStete 属性是驼峰
* readystatechange 应该绑定在 open 方法调用之前，不然感知不到（前面明明说了 readyState 的五种状态，粗心阿）
* 如果使用函数表达式定义格式化数据方法，那么就要放在使用它之前声明（函数表达式不会变量提升，函数声明才会）

**改良过后**

```
const request = function (options) {
  let xhr = null
  // 对传递的数据格式化
  let params = fomateParams(options.data)
  // 兼容处理 IE6、IE7
  if (window.XMLHttpRequest) {
    xhr = new XMLHttpRequest()
  } else {
    xhr = new ActiveXObject()
  }

  // readystatechange 事件绑定在 open 方法调用之前
  xhr.addEventListener('readystatechange', function () {
    if (xhr.readyState === 4 && xhr.status === 200) {
      options.success(xhr.responseText)
    }
  })
  
  /**
   * 判断请求类型
   * GET 请求直接把格式化后的数据拼接到 URL 后面，不需要设置请求头，不需要调用 send 方法
   * POST 请求，设置请求头，调用 send 方法，传入 处理后的数据
   */
  if (options.type === 'GET') {
    xhr.open(options.type, options.url + params, options.async)
  } else if (options.type === 'POST') {
    xhr.setRequestHeader('Content-type', 'application/x-www-from-urlencoded')
    xhr.open(options.type, options.url, options.async)
    xhr.send(params)
  }
  
  // 函数表达式改为函数声明
  function fomateParams (obj) {
    if (obj === null) {
      return null
    }
    let arr = []
    for (let key in obj) {
      arr.push(key + '=' + obj[key])
    }
    return arr.join('&')
  }
}
```

  
## 拓展空间
**POST 与 GET 请求的区别**

+ GET 一般用户请求数据，POST 一般用于向服务器发送数据，但不是绝对的，POST 也可用来请求数据。
+ GET 发送数据拼接在 url 后面，这就意味着我们不能通过 GET 传递过大的数据量；POST 传递参数通过 send 方法发送，服务端从请求体中获取数据。
+ GET 发送数据的大小数量有限制（虽然它本身没有限制，但是各浏览器厂商都有所限制），POST 无限制。
+ GET 请求比 POST 请求更加不安全，因为发送数据的时候直接暴露在 url 中。