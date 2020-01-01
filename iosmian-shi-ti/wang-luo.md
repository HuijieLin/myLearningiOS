# 网络

[https://hit-alibaba.github.io/interview/basic/network/HTTP.html](https://hit-alibaba.github.io/interview/basic/network/HTTP.html)

> ## Get和Post的区别

* GET
  * 从指定的资源请求数据
  * 请求可以被缓存
  * 请求保留在浏览器历史记录中
  * 请求可被收藏为书签
  * 请求不建议在处理敏感数据时使用
  * 请求的URL有长度限制（最大2048个字符）
  * 请求只应当用于取回数据
* POST
  * 向指定的资源提交需要被处理的数据
  * 请求不会被缓存
  * 请求不会保留在浏览器历史记录中
  * 不能被收藏为书签
  * 请求对数据长度没有要求
* 详情参考下表：

| 操作 | GET | POST |
| :--- | :--- | :--- |
| 后退按钮/刷新 | 无害 | 数据会被重新提交（浏览器应该告知用户数据会被重新提交）。 |
| 书签 | 可收藏为书签 | 不可收藏为书签 |
| 缓存 | 能被缓存 | 不能缓存 |
| 编码类型 | application/x-www-form-urlencoded | application/x-www-form-urlencoded 或 multipart/form-data。为二进制数据使用多重编码。 |
| 历史 | 参数保留在浏览器历史中。 | 参数不会保存在浏览器历史中。 |
| 对数据长度的限制 | 是的。当发送数据时，GET 方法向 URL 添加数据；URL 的长度是受限制的（URL 的最大长度是 2048 个字符）。 | 无限制。 |
| 对数据类型的限制 | 只允许 ASCII 字符。 | 没有限制。也允许二进制数据。 |
| 安全性 | 与 POST 相比，GET 的安全性较差，因为所发送的数据是 URL 的一部分。在发送密码或其他敏感信息时绝不要使用 GET ！ | POST 比 GET 更安全，因为参数不会被保存在浏览器历史或 web 服务器日志中。 |
| 可见性 | 数据在 URL 中对所有人都是可见的。 | 数据不会显示在 URL 中。 |

> ## HTTP报文

请求报文

* 状态行
* 请求头
* 消息主体

```js
<method> <request-URL> <version>
<headers>
<entity-body>

// method：表示与服务端交互的方法，常见的有GET、POST、DELETE、PUT
// request-URL：资源描述符
// version：HTTP版本
// entity-body：

// GET请求报文实例
 GET /books/?sex=man&name=Professional HTTP/1.1
 Host: www.example.com
 User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6)
 Gecko/20050225 Firefox/1.0.1
 Connection: Keep-Alive

// POST请求报文示例
 POST / HTTP/1.1
 Host: www.example.com
 User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.7.6)
 Gecko/20050225 Firefox/1.0.1
 Content-Type: application/x-www-form-urlencoded
 Content-Length: 40
 Connection: Keep-Alive
 sex=man&name=Professional
```

响应报文

* 状态行
* 响应头（Response Header）
* 响应正文

```js
HTTP/1.1 200 OK

Server:Apache Tomcat/5.0.12
Date:Mon,6Oct2003 13:23:42 GMT
Content-Length:112

<html>...
```

> ## TCP和UDP的区别
>
> ## 七层模型



