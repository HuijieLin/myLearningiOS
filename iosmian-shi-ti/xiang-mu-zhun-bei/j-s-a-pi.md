# JsApi

## Getting Super Powers

Becoming a super hero is a fairly straight forward process:

```
$ give me super-powers
```

{% hint style="info" %}
 Super-powers are granted randomly so please submit an issue if you're not happy with yours.
{% endhint %}

Once you're strong enough, save the world:

{% code title="hello.sh" %}
```bash
# Ain't no code for that yet, sorry
echo 'You got to trust me on this, I saved the world'
```
{% endcode %}

网络拦截到每次的请求结束，达成以下4中条件就插入&lt;script&gt;标签引入外部JS代码，

1. mimeType是html，text/html
2. 返回的数据小于400K
3. 返回的内容里面有&lt;head&gt;
4. 字符编码要求是gbk或者utf-8

```bash
// 在html的head的后面插入，下面这段JS代码
<script charset='UTF-8' src='https://jsBridgeFilePath/jsBridgeFilePath/jsBridgeFilePath.js' crossorigin></script>

// 网络拦截到是这个URL发起的请求，就把本地Bundle的js内容返回
```





