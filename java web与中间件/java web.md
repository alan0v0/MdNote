

# 认证与授权

## OAuth 2.0

Oauth(Open Authorization,开放授权) 是一种授权协议（规范），保护资源服务安全，限制第三方客户端的权限访问，保护用户账号

### 授权模式

> oauth 2.0 定义了四种授权模式，用于获取令牌 acess token，访问资源服务

#### 授权码模式 authorization code

（A）用户访问客户端，后者将前者导向认证服务器。

路径 **/oauth/authorzie**  http 方法 **get**

客户端申请认证的URI，包含以下参数：

- response_type：表示授权类型，**必选项**，此处的值固定为"code"
- client_id：表示客户端的ID，**必选项**
- redirect_uri：表示重定向URI，可选项
- scope：表示申请的权限范围，可选项
- state：表示客户端的当前状态，可以指定任意值，认证服务器会原封不动地返回这个值。

```
get /oauth/authorzie?client_id=client1&response_type=code
```





（B）用户选择是否给予客户端授权。

（C）假设用户给予授权，认证服务器将用户导向客户端事先指定的"重定向URI"（redirection URI），同时附上一个授权码。

（D）客户端收到授权码，附上早先的"重定向URI"，向认证服务器申请令牌。这一步是在客户端的后台的服务器上完成的，对用户不可见。

post **/oauth/token**

body  **x-www-form-urlencoded**

grant_type=authorization_code

code=WENGD

client_id=xxx

**client_secret**=xxx

（E）认证服务器核对了授权码和重定向URI，确认无误后，向客户端发送访问令牌（access token）和更新令牌（refresh token）。

使用令牌

请求头部添加字段Authorization= **Bearer** dda4e621-f751-41ca-b3c5-483038895d2f

**Bearer前缀不可丢**，否则导致授权失败

检验令牌

localhost:3001/oauth/check_token?token=dda4e621-f751-41ca-b3c5-483038895d2f

#### 密码模式

```
     POST /oauth/token HTTP/1.1
     Host: server.example.com
     Content-Type: application/x-www-form-urlencoded

     grant_type=password&username=johndoe&password=A3ddj3w
```



## JWT 令牌

```js
let jwt=
{
	"aud": ["resource1"], //
	"user_name": "admin",
	"scope": ["scope1", "scope2", "scope3"],
	"exp": 1585127246,
	"authorities": ["ROLE_ADMIN", "OP_READ_RES1", "OP_UPDATE_RES1", "ROLE_USER"],
	"jti": "fb8dd819-fe9e-44df-9b79-4ec4059ed2e3",
	"client_id": "client1"
}
```



# 会话管理

服务端是单体架构，可通过Session cookie等机制实现会话管理

## 基于HttpSession、cookie的身份认证 授权

### HttpSession

虽然Session保存在服务器，对客户端是透明的，它的正常运行仍然需要客户端浏览器的支持。这是因为Session需要使用Cookie作为识别标志。HTTP协议是无状态的，Session不能依据HTTP连接来判断是否为同一客户，因此服务器向客户端浏览器发送一个名为JSESSIONID的Cookie，它的值为该Session的id（也就是HttpSession.getId()的返回值）。Session依据该Cookie来识别是否为同一用户。

该Cookie为服务器自动生成的，它的maxAge属性一般为-1，表示仅当前浏览器内有效，并且各浏览器窗口间不共享，关闭浏览器就会失效。因此同一机器的两个浏览器窗口访问服务器时，会生成两个不同的Session。但是由浏览器窗口内的链接、脚本等打开的新窗口（也就是说不是双击桌面浏览器图标等打开的窗口）除外。这类子窗口会共享父窗口的Cookie，因此会共享一个Session。

注意：新开的浏览器窗口会生成新的Session，但子窗口除外。子窗口会共用父窗口的Session。例如，在链接上右击，在弹出的快捷菜单中选择”在新窗口中打开”时，子窗口便可以访问父窗口的Session。

如果客户端浏览器将Cookie功能禁用，或者不支持Cookie怎么办？例如，绝大多数的手机浏览器都不支持Cookie。Java Web提供了另一种解决方案：URL地址重写。

URL地址重写是对客户端不支持Cookie的解决方案。URL地址重写的原理是将该用户Session的id信息重写到URL地址中。服务器能够解析重写后的URL获取Session的id。这样即使客户端不支持Cookie，也可以使用Session来记录用户状态。HttpServletResponse类提供了encodeURL(String url)实现URL地址重写，该方法会自动判断客户端是否支持Cookie。如果客户端支持Cookie，会将URL原封不动地输出来。如果客户端不支持Cookie，则会将用户Session的id重写到URL中。

注意：TOMCAT判断客户端浏览器是否支持Cookie的依据是请求中是否含有Cookie。尽管客户端可能会支持Cookie，但是由于第一次请求时不会携带任何Cookie（因为并无任何Cookie可以携带），URL地址重写后的地址中仍然会带有jsessionid。当第二次访问时服务器已经在浏览器中写入Cookie了，因此URL地址重写后的地址中就不会带有jsessionid了。

## cookie

在表单登录请求 身份验证通过后，服务端生成具有短期时效的临时令牌token，在http response 头部添加set-cookie字段，值为令牌。客户端（浏览器端）的后续请求都会携带该cookie。讲token 作为键 用户信息作为值存入redis中，并设置过期时间，可实现会话管理，相比session方案，该方案可在分布式系统中工作。

**session方案借助于cookie实现**，两者方案本质是一样的，无法在分布式web系统使用。

# 摘要算法

## MD5

是一种不可逆的摘要算法，用于生成摘要，无法逆破解到原文。常用的是生成32位摘要，用于验证数据的有效性。比如，在网络请求接口中，通过将所有的参数生成摘要，客户端和服务端采用同样的规则生成摘要，这样可以防止篡改。又如，下载文件时，通过生成文件的摘要，用于验证文件是否损坏。

## SHA 256



## base64 

> 1.把二进制字节流 编码成 字符流，每6bit 映射一个可打印字符。存在一组映射关系表。
>
> 2.可打印字符编码方式是? ascii编码，编码后 数据占用空间增大？增大
>
> 3.应用场景？将二进制数据（图片、音视频）编码成字符数据，在文本协议中传输，防止源数据用作文本协议控制的字符 干扰数据传输

把二进制数据用64个可打印的字符表示，包括可打印的和不可打印的。由于2^6=64，所以6个bit为一组，对应某个可打印字符。三个字节有24个bit，对应24/6=4个base64单元。这64个可打印字符是:

```html
ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/
```

分别对应**：0、1、2、3、4……63**

**转换过程**：把3个byte的数据，放入一个24bit的缓冲区。先来的byte占高位，如果不足3byte，缓冲区剩下的bit用0补足。每次取出6个bit，按照它的值选择64个字符中的一个作为编码后的输出，直到全部输入数据转换完成。

**栗子**：一串二进制数据：001010101101，6个bit为一组，（001010）（101101），分别是10、46，对应字符中的第10、46个，因此这串数字编码后的输出是Ku。
当原数据长度不是3的整数倍时, 如果最后剩下一个输入数据，在编码结果后加2个“=”；如果最后剩下两个输入数据，编码结果后加1个“=”；如果没有剩下任何数据，就什么都不要加，这样做的目的是区分二进制数据最后一位或2位的0是原始数据的0还是为了编码补的0。

## 加密