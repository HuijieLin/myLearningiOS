# Hybird

JS -&gt; Native

* 假跳转， delegate拦截
* UIWebView - JSContext
* WKWebview - MessageHandler

Native -&gt; JS

* evaluatingJavaScript
* WKUserScript

> #### 从输入url地址栏到所有内容显示到界面上做了哪些事
>
> [https://juejin.im/post/5e042c00f265da339c03a47e](https://juejin.im/post/5e042c00f265da339c03a47e)

1. 浏览器向 DNS 服务器请求解析该 URL 中的域名所对应的 IP 地址
2. 建立TCP连接（三次握手）
3. 浏览器发出读取文件\(URL 中域名后面部分对应的文件\)的HTTP 请求，该请求报文作为 TCP 三次握手的第三个报文的数据发送给服务器
4. 服务器对浏览器请求作出响应，并把对应的 html 文本发送给浏览器
5. 浏览器将该 html 文本并显示内容
6. 释放 TCP连接（四次挥手）

### WKWebView 系列教程

* WKWebView项目实践分享（二）- WKWebView介绍[https://www.jianshu.com/p/0a1d636b2e96](https://www.jianshu.com/p/0a1d636b2e96)

### 秒开方案

离线Hybrid容器如何做到接近100%秒开？ [https://mp.weixin.qq.com/s/inAFLg85TfmXOWXS50pEfg](https://mp.weixin.qq.com/s/inAFLg85TfmXOWXS50pEfg)

双11主会场性能体验提升 - 秒开优化 [https://mp.weixin.qq.com/s/PJRJBhzatwE3jyeO26iZGg](https://mp.weixin.qq.com/s/PJRJBhzatwE3jyeO26iZGg)

web离线技术原理 [https://juejin.cn/post/6844903845483511815\#heading-8](https://juejin.cn/post/6844903845483511815#heading-8)

今日头条品质优化 - 图文详情页秒开实践 [https://mp.weixin.qq.com/s?spm=ata.13261165.0.0.72e12b8fZeVtZW&\_\_biz=MzI1MzYzMjE0MQ==&mid=2247486645&idx=1&sn=7c9111b70758c65575d7a2b5ee697191&chksm=e9d0c757dea74e418b8002d5eb652166d7edcf38a462dfadfc88377a05815f439ba624fc8565&token=534750968&lang=zh\_CN&scene=21\#wechat\_redirect](https://mp.weixin.qq.com/s?spm=ata.13261165.0.0.72e12b8fZeVtZW&__biz=MzI1MzYzMjE0MQ==&mid=2247486645&idx=1&sn=7c9111b70758c65575d7a2b5ee697191&chksm=e9d0c757dea74e418b8002d5eb652166d7edcf38a462dfadfc88377a05815f439ba624fc8565&token=534750968&lang=zh_CN&scene=21#wechat_redirect)

