## 授权
Twitter使用 OAuth 提供授权  
### Twitter API 验证模式
1. 用户验证(user context)
此模式允许一个应用以用户的身份进行活动.  
比如, 如果一个开发者希望用户可以在他的平台上发布Twitter状态, 他就要通过用户验证来得到授权.  
换句话说，签名的请求除了识别应用程序的身份之外，还识别应用程序正在代表用户的访问令牌代表的最终用户授予的权限。  
用户验证需要你的应用的consumer key 和 secret, 以及你所代表的用户授予的访问令牌.

2. 应用验证(app only)  
应用验证模式适用于应用以自己的身份发起API请求, 没有用户环境, 这个模式适用于开发者只需要一些公共信息.  
比如, 一个开发者需要得到公共的tweet或者列表.  
使用这个方法, 你需要使用一个`bearer token`, 你可以将你的consumer key 和 secret 已POST方式发送到`oauth2/token`来获取.




## 验证请求
> 这篇文章展示了如何修改一个HTTP请求来实现向Twitter Api发送一个验证的请求.

所有的Twitter API 都基于HTTP协议. 也就是说, 任何使用了Twitter API的应用都是通过向Twitter服务器发送一系列构造过的信息来完成功能.

一个很简单的请求如下:

```
POST /1.1/statuses/update.json?include_entities=true HTTP/1.1
Accept: */*
Connection: close
User-Agent: OAuth gem v0.4.4
Content-Type: application/x-www-form-urlencoded
Content-Length: 76
Host: api.twitter.com

status=Hello%20Ladies%20%2b%20Gentlemen%2c%20a%20signed%20OAuth%20request%21
```

这个请求实际上是无效的, 因为它缺少以下几点:
1. 什么应用在发起请求
2. 这个请求是以谁的身份发起的
3. 如果这个请求以某个用户的身份发起, 这个用户是否已经授权
4. 这个请求在传递过程中有没有被第三方篡改

Twitter api 使用了OAuth 1.0a 协议来约定应用如何向Twitter提供上面这些信息.简单来说, Twitter需要应用在发起的请求中包含一个 Authorization 头部, 并在这个字段中中提供足够的信息来回答上面列出的问题.

上面的请求如果按照要求修改, 应该是这样的:

```
POST /1.1/statuses/update.json?include_entities=true HTTP/1.1
Accept: */*
Connection: close
User-Agent: OAuth gem v0.4.4
Content-Type: application/x-www-form-urlencoded
Authorization:
OAuth oauth_consumer_key="xvz1evFS4wEEPTGEFPHBog",
oauth_nonce="kYjzVBB8Y0ZFabxSWbWovY3uYSQ2pTgmZeNu2VS4cg",
oauth_signature="tnnArxj06cWHq44gCs1OSKk%2FjLY%3D",
oauth_signature_method="HMAC-SHA1",
oauth_timestamp="1318622958",
oauth_token="370773112-GmHxMAgYyLbNEtIKZeRNFsMKPR9EyMZeS9weJAEb",
oauth_version="1.0"
Content-Length: 76
Host: api.twitter.com

status=Hello%20Ladies%20%2b%20Gentlemen%2c%20a%20signed%20OAuth%20request%21
```

这样的请求对于Twitter来说就是有效的.

### 各参数说明
你可以看到, 在请求的头部信息中包含7个键值对, 键名都以`oauth_`开始,他们的作用分别如下:
1. Consumer key  
    表明哪一个应用发出当前请求, 在应用设置界面获得
2. Nonce  
    随机令牌, 用于防止同一请求多次提交, 通过对32byte的随机数进行base64编码得到.
3. Signature(签名)  
    签名使用其他所有参数加上两个私钥, 通过一个确定的算法生成, 签名可以防止信息传递过程中发生篡改
    计算签名的方法参阅[生成签名](https://developer.twitter.com/en/docs/basics/authentication/guides/creating-a-signature.html)
4. Signature method(签名算法)  
    Twitter使用的算法是 `HMAC-SHA1`
5. Timestamp(时间戳)  
    这个不多说
6. Token(令牌)  
    令牌也就是代表用户授予的权限, 大部分请求都需要令牌(除了登录时)
7. Version 
    API 版本, 应该是1.0




