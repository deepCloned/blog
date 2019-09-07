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
// 不能这样写，会提示 ActiveXObject is not defined
const xmlHttp = new XMLHttpRequest() || new ActiveXObject('Microsoft.XMLHTTP') // 兼容 IE5 IE6
```

```
let xmlHttp;
if (window.XMLHttpRequest) {
  xmlHttp = new XMLHttpRequest()
} else {
  xmlHttp = new ActiveXObject('Microsolf.XMLHTTP')
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
  xmlHttp.open(type, url, async)
  ```
  - POST
  
  ```
  /**
   * POST 请求比 GET 请求多一步，发送数据
   */
  xmlHttp.open()
  xmlHttp.send(data)
  ```
  
  - 设置请求头
  
  ```
  xmlHttp.setRequestHeader(key, value)
  // 一般前后端使用 JSON 数据格式进行交互，所以我们要设置请求头为
  ('Content-Type', 'application/json')
  ```
  
+ 根据 xmlHttp 的 readyState 属性判断状态
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
| flag | 是否异步 | Boolean | true/false | true |
| params | 请求参数 | Object | {} | 空 |
| cb | 回调函数 | Function | func | 空 |

*注意事项*

* 大家分开

```
const request = function ()
```

  
## 拓展空间
**POST 与 GET 请求的区别**

+ GET 一般用户请求数据，POST 一般用于向服务器发送数据，但不是绝对的，POST 也可用来请求数据。
+ GET 发送数据拼接在 url 后面，这就意味着我们不能通过 GET 传递过大的数据量；POST 传递参数通过 send 方法发送，服务端从请求体中获取数据。
+ GET 发送数据的大小数量有限制（虽然它本身没有限制，但是各浏览器厂商都有所限制），POST 无限制。
+ GET 请求比 POST 请求更加不安全，因为发送数据的时候直接暴露在 url 中。