# Hybird

JS -&gt; Native

* 假跳转， delegate拦截
* UIWebView - JSContext
* WKWebview - MessageHandler

Native -&gt; JS

* evaluatingJavaScript
* WKUserScript

> #### 从输入url地址栏到所有内容显示到界面上做了哪些事

1. 浏览器向 DNS 服务器请求解析该 URL 中的域名所对应的 IP 地址; 
2. 建立TCP连接（三次握手）; 
3. 浏览器发出读取文件\(URL 中域名后面部分对应的文件\)的HTTP 请求，该请求报文作为 TCP 三次握手的第三个报文的数据发送给服务器; 
4. 服务器对浏览器请求作出响应，并把对应的 html 文本发送给浏览器; 
5. 浏览器将该 html 文本并显示内容; 
6. 释放 TCP连接（四次挥手）

