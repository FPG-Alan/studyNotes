## Twitter 登录
浏览器和移动端均基于OAuth实现Twitter登录, 下面的例子展示了如何构造请求来获取登录流程中需要的 access token.
以下内容默认读者了解如何使用OAth 1.0a 协议对一个请求进行签名, 如果你还不了解, 请先参阅 [Authorizing a request](https://dev.twitter.com/oauth/overview/authorizing-requests)

### Step1 - 获取一个 request token (请求令牌)
要开始一个登录流程, 你的应用必须获取一个请求令牌, 你可以通过POST方式发送一个经过签名的信息到oauth/request_token节点来获取.
在这个请求中唯一特殊的参数是 oauth_callback, 一个URL编码的地址, 这个参数指定了当第二步完成时, 你的用户将被重定向到的位置. 其他的参数都会在OAuth签名过程中被添加.
![](https://g.twimg.com/dev/sites/default/files/images_documentation/sign-in-oauth-1_0.png)

下面是一个请求的示例:
```
POST /oauth/request_token HTTP/1.1
User-Agent: themattharris' HTTP Client
Host: api.twitter.com
Accept: */*
Authorization:
        OAuth oauth_callback="http%3A%2F%2Flocalhost%2Fsign-in-with-twitter%2F",
              oauth_consumer_key="cChZNFj6T5R0TigYB9yd1w",
              oauth_nonce="ea9ec8429b68d6b77cd5600adbbb0456",
              oauth_signature="F1Li3tvehgcraF8DMJ7OyxO4w9Y%3D",
              oauth_signature_method="HMAC-SHA1",
              oauth_timestamp="1318467427",
              oauth_version="1.0"
```
请求执行的回调内, 通过检查HTTP状态码, 除了200之外的任何值都被认为请求失败. 返回值的body内包含`oauth_toekn`,`oauth_token_secret`和`oauth_callback_confirmed`参数.  
你应该检查`oauth_callback_confirmed`是否为`true`, 然后保存其他两个参数, 他们将被用在下一步.

响应数据示例:
```
HTTP/1.1 200 OK
Date: Thu, 13 Oct 2011 00:57:06 GMT
Status: 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 146
Pragma: no-cache
Expires: Tue, 31 Mar 1981 05:00:00 GMT
Cache-Control: no-cache, no-store, must-revalidate, pre-check=0, post-check=0
Vary: Accept-Encoding
Server: tfe

oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0&
oauth_token_secret=veNRnAWe6inFuo8o2u8SLLZLjolYDmDP7SzL0YfYI&
oauth_callback_confirmed=true
```

### Step2 - 重定向
将用户重定向到Twitter, 用户将在这个页面(`oauth/authenticate`)完成授权(或拒绝授权), 上一步获得的`oauth_token`这一步中将作为参数附在地址上.  
对一个页面来说, 最无缝的方式是, 第一步中前端发起一个`sign in`的请求, 后端在处理(第一步)后, 响应一个302重定向请求.
![](https://g.twimg.com/dev/sites/default/files/images_documentation/sign-in-oauth-2_0.png)

重定向的地址类似下面这样:
```
https://api.twitter.com/oauth/authenticate?oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0
```
基于用户的状态, 这个页面会有三种情况:  
1. 已经登录和授权  
    如果用户处于这个状态, 页面会立刻返回设定的回调地址, 跳转到Twitter的过程对于用户来说是不明显的.
2. 已经登录但没有授权
    显示一个请求授权页面, 在用户选择后跳转到设定的回调地址, 并带有一个有效的OAuth请求令牌(OAuth request token)
3. 没有登录
    先显示一个登录界面, 后续同上.

一旦授权成功, 你设定的回调地址会收到一个请求, 带有`oauth_token`和`oauth_verifier`参数. 你的应用应该验证这个token是否和step1中获得的request token一致.

回调地址收到的请求:
```
GET /sign-in-with-twitter/?
        oauth_token=NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0&
        oauth_verifier=uw7NjWHT6OJ1MpJOXsHfNxoAhPKpgI8BlYDhxEjIBY HTTP/1.1
Host: localhost
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_6_8) AppleWebKit/535.5 (KHTML, like Gecko) Chrome/16.0.891.1 Safari/535.5
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Referer: http://localhost/sign-in-with-twitter/
Accept-Encoding: gzip,deflate,sdch
Accept-Language: en-US,en;q=0.8
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.3
```

### Step3 - 将`request token`转化为`access_token`  
后端需要发起一个POST请求到`oauth/access_token`, 并附加第二步中获得的`oauth_verifier`, header中添加`oauth_token`.
![](https://g.twimg.com/dev/sites/default/files/images_documentation/sign-in-oauth-3_0.png)

请求示例: 
```
POST /oauth/access_token HTTP/1.1
User-Agent: themattharris' HTTP Client
Host: api.twitter.com
Accept: */*
Authorization: OAuth oauth_consumer_key="cChZNFj6T5R0TigYB9yd1w",
                     oauth_nonce="a9900fe68e2573b27a37f10fbad6a755",
                     oauth_signature="39cipBtIOHEEnybAR4sATQTpl2I%3D",
                     oauth_signature_method="HMAC-SHA1",
                     oauth_timestamp="1318467427",
                     oauth_token="NPcudxy0yU5T3tBzho7iCotZ3cnetKwcTIRlX0iwRl0",
                     oauth_version="1.0"
Content-Length: 57
Content-Type: application/x-www-form-urlencoded

oauth_verifier=uw7NjWHT6OJ1MpJOXsHfNxoAhPKpgI8BlYDhxEjIBY
```
如果请求成功, 响应会包含 `oauth_token`, `oauth_token_secret`, 这两个参数也就是后面所有API请求需要用到的令牌, 需要被存储起来.  
如果需要验证用户的身份, 可以使用GET方法请求`account/verify_credentials`.  
响应示例:
```
HTTP/1.1 200 OK
Date: Thu, 13 Oct 2011 00:57:08 GMT
Status: 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 157
Pragma: no-cache
Expires: Tue, 31 Mar 1981 05:00:00 GMT
Cache-Control: no-cache, no-store, must-revalidate, pre-check=0, post-check=0
Vary: Accept-Encoding
Server: tfe

oauth_token=7588892-kagSNqWge8gB1WwE3plnFsJHAZVfxWD7Vb57p0b4&
oauth_token_secret=PbKfYqSryyeKDWz4ebtY3o5ogNLG11WJuZBc9fQrQo
```