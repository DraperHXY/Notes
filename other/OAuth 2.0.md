# OAuth 2.0 认证码授权是什么

**不要为了 OAuth 2.0 而 OAuth 2.0，一切都是为了安全。**认证码授权模型是一个大部分人会在 OAuth 2.0 中遇到的授权模型。用户授权应用程序后，Web应用程序和本机应用程序都会使用它来获取访问令牌。



## OAuth 2.0 授权模型是什么

在 OAuth 2.0 时代，"授权模型“ 被应用作为获取一个访问令牌的方式。OAuth 2.0 定义了几种授权模型，包括认证码模型。OAuth 2.0 拓展也可以定义新的授权模型。

不论是 Web 应用还是原生应用，针对不能访问 web 浏览器或者服务器之间进行交互的应用的特定使用场景进行优化。



## Authorization Code 流程

Web 还是移动应用使用认证码授权模型，区别于大多数的其他模型就在于第一次访问应用开始开启一个 流程。在较高的级别，流程包括以下几步。

* 应用开启浏览器向认证服务器发送认证信息。

* 用户被重定向回应用并携带了一个认证码。
* 应用将会交换这个认证码返回一个访问令牌



## 获取用户的权限

OAuth 让用户能够授予应用程序访问权限。应用程序首先需要决定请求哪些权限，然后将用户信息发送到授权服务器（QQ，微信）请求获取其权限。要开始授权流，应用程序构造一个URL，如下所示，并打开一个浏览器来访问该URL。

```
https://authorization-server.com/auth
 ?response_type=code
 &client_id=29352915982374239857
 &redirect_uri=https%3A%2F%2Fexample-app.com%2Fcallback
 &scope=create+delete
 &state=xcoiv98y2kd22vusuye3kch
```

每一次都要带有一下的参数：

* ``response_type=code`` - 这是告诉认证服务器所选择的授权流程是什么
* ``client_id`` - 这个是应用程序的公开身份识别，在开发者第一次注册该应用程序时获取。
* ``redirect_uri`` - 告诉认证服务器在批准用户请求后，将用户发往何处
* ``scope`` - 一个或多个空格分隔的字符串表示应用程序请求的权限。scope 支持开发者定义 特定的 OAuth API。
* ``state`` - 应用程序支持在请求中生成随机的字符串，校验返回给用户应用程序的匹配串是否相同来防御 CSRF 攻击。

当用户访问此URL时，授权服务器将向他们显示一个提示，若需要在 QQ，微信登录则登录，已登录则不需要。然后询问他们是否愿意授权此应用程序的请求。



## 重定向回调至应用

当用户请求被允许，认证服务器将通过 ``redirect_uri`` 重定向至应用程序（往往我们开发的程序），添加 ``code`` 和 ``state`` 匹配串。

例如如下的重定向的  URL

```
https://example-app.com/redirect
 ?code=g0ZGZmNjVmOWIjNTk2NTk4ZTYyZGI3
 &state=xcoiv98y2kd22vusuye3kch
```

``state`` 应该与应用程序首次发送请求时的一样，并对此进行校验来防御 ``CRSF`` 以及其他相关攻击。

``code`` 是认证服务器锁生成的认证码。这个 code 根据 OAuth 在 1 到 10 分钟，仅有较短的生命周期。



## 交换认证码来获取访问令牌

现在应用程序已经获取了认证码，可以用来获取访问令牌。

> 用户的登录时在认证服务器那里登录的，并不信任我们，认证服务器背后隐藏的资源，我们不能通过账号密码来获取，只能通过 access token，而获取这个 token 的时，我们需要谨防有恶意攻击，所以认证服务器提供一个简单的 code(认证码) 让我们获取 access token。为什么不直接返回 access_code 而是 code，是因为验证客户端的能力，

应用程序将会发送一个 POST 携带以下参数发送请求至 service 的令牌端点：

* ``grant_type=authorization_code`` - 这是通知令牌端点使用的是认证码授权模型。
* ``code`` - 应用将会携带着这个重定向后生成的认证码
* ``client_id`` - 应用程序的客户端 ID，例如 wechat，qq 等。
* ``client_secret`` - 应用程序客户端秘钥。来确保是仅来自应用程序获取访问令牌的请求，而不是来自可能截获认证码的潜在攻击者。

令牌端点将会对这些参数进行校验，确保认证码没有过期且 client_id 和 secret 都匹配。如果没有问题的话将会生成访问令牌作为返回的 response。

```
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store
Pragma: no-cache

{
  "access_token":"MTQ0NjJkZmQ5OTM2NDE1ZTZjNGZmZjI3",
  "token_type":"bearer",
  "expires_in":3600,
  "refresh_token":"IwOGYzYTlmM2YxOTQ5MGE3YmNmMDFkNTVk",
  "scope":"create delete"
}
```

认证码流程这就结束了。现在应用程序可以用这个令牌来访问资源 API。

