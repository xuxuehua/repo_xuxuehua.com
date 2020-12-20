---
title: "http"
date: 2018-09-16 13:07
---


[TOC]

# HTTP

HTTP是一种能够获取如 HTML 这样的网络资源的 [protocol](https://developer.mozilla.org/zh-CN/docs/Glossary/Protocol)(通讯协议)。它是在 Web 上进行数据交换的基础，是一种 client-server 协议，也就是说，请求通常是由像浏览器这样的接受方发起的。一个完整的Web文档通常是由不同的子文档拼接而成的，像是文本、布局描述、图片、视频、脚本等等。

[https://tools.ietf.org/html](https://tools.ietf.org/html)

HTTP 协议是无状态的，任何两次请求之间都没有依赖关系





## HTTP 封装

HTTP被设计于20世纪90年代初期，是一种可扩展的协议。它是应用层的协议，通过[TCP](https://developer.mozilla.org/zh-CN/docs/Glossary/TCP)，或者是[TLS](https://developer.mozilla.org/zh-CN/docs/Glossary/TLS)－加密的TCP连接来发送，理论上任何可靠的传输协议都可以使用。因为其良好的扩展性，时至今日，它不仅被用来传输超文本文档，还用来传输图片、视频或者向服务器发送如HTML表单这样的信息。HTTP还可以根据网页需求，仅获取部分Web文档内容更新网页。



![img](http.assets/HTTP & layers.png)







# 缓存

虽然 HTTP 缓存不是必须的，但重用缓存的资源通常是必要的。然而常见的 HTTP 缓存只能存储 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 响应，对于其他类型的响应则无能为力。缓存的关键主要包括request method和目标URI（一般只有GET请求才会被缓存）。







# 请求类

```
GET / HTTP/1.1  #请求行
Host: 127.0.0.1		# 下面都是请求头
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Iridium/2019.04 Safari/537.36 Chrome/73.0.0.0
DNT: 1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
```







## 请求行

```
GET / HTTP/1.1  #请求行
```

> GET: 请求方法
>
> / : 资源路径
>
> HTTP/1.1 : 协议及版本号



## 请求头

key value格式，尾部追加\r\n换行



### User-Agent 客户端

请求通过一个实体被发出，实体也就是用户代理。大多数情况下，这个用户代理都是指浏览器，当然它也可能是任何东西，比如一个爬取网页生成维护搜索引擎索引的机器爬虫。

请求发出者，兼容性以及定制化需求，如针对手机类的浏览器返回定制类的结果

```
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) 
```



### Referer

表明档期这个请求是从哪个url过来的，常用于反爬虫机制。如果不是从指定页面过来的，就不会响应



### Cookie

cookie用来标识相同的请求

HTTP是无状态的：在同一个连接中，两个执行成功的请求之间是没有关系的。这就带来了一个问题，用户没有办法在同一个网站中进行连续的交互，HTTP Cookies就可以解决这个问题。把Cookies添加到头部中，创建一个会话让每次请求都能共享相同的上下文信息，达成相同的状态。用于告知服务端两个请求是否来自同一浏览器，如保持用户的登录状态。Cookie 使基于[无状态](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview#HTTP_is_stateless_but_not_sessionless)的HTTP协议记录稳定的状态信息成为了可能。



Cookie 主要用于以下三个方面：

- 会话状态管理（如用户登录状态、购物车、游戏分数或其它需要记录的信息）
- 个性化设置（如用户自定义设置、主题等）
- 浏览器行为跟踪（如跟踪分析用户行为等）





### Connection

使用keepalive，一个连接可以发多个请求

```
Connection: keep-alive
```



在客户端（通常指浏览器）与服务器能够交互（客户端发起请求，服务器返回响应）之前，必须在这两者间建立一个 TCP 链接，打开一个 TCP 连接需要多次往返交换消息（因此耗时）。HTTP/1.0 默认为每一对 HTTP 请求/响应都打开一个单独的 TCP 连接。当需要连续发起多个请求时，这种模式比多个请求共享同一个 TCP 链接更低效。

为了减轻这些缺陷，HTTP/1.1引入了流水线（被证明难以实现）和持久连接的概念：底层的TCP连接可以通过[`Connection`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Connection)头部来被部分控制。HTTP/2则发展得更远，通过在一个连接复用消息的方式来让这个连接始终保持为暖连接。 

为了更好的适合HTTP，设计一种更好传输协议的进程一直在进行。Google就研发了一种以UDP为基础，能提供更可靠更高效的传输协议[QUIC](https://en.wikipedia.org/wiki/QUIC)。



### Cache-control 

**HTTP/1.1**定义的 [`Cache-Control`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cache-Control) 头用来区分对缓存机制的支持情况， 请求头和响应头都支持这个属性。通过它提供的不同的值来定义缓存策略。

缓存中不得存储任何关于客户端请求和服务端响应的内容。每次由客户端发起的请求都会下载完整的响应内容。

```html
Cache-Control: no-store
```



* 缓存但重新验证

如下头部定义，此方式下，每次有请求发出时，缓存会将此请求发到服务器（译者注：该请求应该会带有与本地缓存相关的验证字段），服务器端会验证请求中所描述的缓存是否过期，若未过期（注：实际就是返回304），则缓存才使用本地缓存副本。

```html
Cache-Control: no-cache
```



* 私有和公共缓存

"public" 指令表示该响应可以被任何中间人（译者注：比如中间代理、CDN等）缓存。若指定了"public"，则一些通常不被中间人缓存的页面（译者注：因为默认是private）（比如 带有HTTP验证信息（帐号密码）的页面 或 某些特定状态码的页面），将会被其缓存。

而 "private" 则表示该响应是专用于某单个用户的，中间人不能缓存此响应，该响应只能应用于浏览器私有缓存中。

```html
Cache-Control: private
Cache-Control: public
```



* 过期

过期机制中，最重要的指令是 "`max-age=<seconds>`"，表示资源能够被缓存（保持新鲜）的最大时间。相对[Expires](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Expires)而言，max-age是距离请求发起的时间的秒数。针对应用中那些不会改变的文件，通常可以手动设置一定的时长以保证缓存有效，例如图片、css、js等静态资源。

详情看下文关于[缓存有效性](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching_FAQ#Freshness)的内容。

```html
Cache-Control: max-age=31536000
```

通常情况下，对于不含这个属性的请求则会去查看是否包含[Expires](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Expires)属性，通过比较Expires的值和头里面[Date](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Date)属性的值来判断是否缓存还有效。如果max-age和expires属性都没有，找找头里的[Last-Modified](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Last-Modified)信息。如果有，缓存的寿命就等于头里面Date的值减去Last-Modified的值除以10（注：根据rfc2626其实也就是乘以10%）





* 验证方式

当使用了 "`must-revalidate`" 指令，那就意味着缓存在考虑使用一个陈旧的资源时，必须先验证它的状态，已过期的缓存将不被使用。详情看下文关于[缓存校验](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching_FAQ#Cache_validation)的内容。

```html
Cache-Control: must-revalidate
```



### Pragma (for compatibility)

[`Pragma`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Pragma) 是**HTTP/1.0**标准中定义的一个header属性，请求中包含Pragma的效果跟在头信息中定义Cache-Control: no-cache相同，但是HTTP的响应头没有明确定义这个属性，所以它不能拿来完全替代HTTP/1.1中定义的Cache-control头。通常定义Pragma以向后兼容基于HTTP/1.0的客户端



## 请求体

请求体是可选的

GET请求默认没有请求体





# HTTP Cookie

Cookie 曾一度用于客户端数据的存储，因当时并没有其它合适的存储办法而作为唯一的存储手段，但现在随着现代浏览器开始支持各种各样的存储方式，Cookie 渐渐被淘汰。由于服务器指定 Cookie 后，浏览器的每次请求都会携带 Cookie 数据，会带来额外的性能开销（尤其是在移动环境下）。新的浏览器API已经允许开发者直接将数据存储到本地，如使用 [Web storage API](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Storage_API) （本地存储和会话存储）或 [IndexedDB](https://developer.mozilla.org/zh-CN/docs/Web/API/IndexedDB_API) 。



当服务器收到 HTTP 请求时，服务器可以在响应头里面添加一个 [`Set-Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie) 选项。浏览器收到响应后通常会保存下 Cookie，之后对该服务器每一次请求中都通过 [`Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cookie) 请求头部将 Cookie 信息发送给服务器。另外，Cookie 的过期时间、域、路径、有效期、适用站点都可以根据需要来指定。



## Set-Cookie

服务器使用 [`Set-Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie) 响应头部向用户代理（一般是浏览器）发送 Cookie信息。一个简单的 Cookie 可能像这样：

```
Set-Cookie: <cookie名>=<cookie值>
```

服务器通过该头部告知客户端保存 Cookie 信息。

```html
HTTP/1.0 200 OK
Content-type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[页面内容]
```

现在，对该服务器发起的每一次新请求，浏览器都会将之前保存的Cookie信息通过 [`Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cookie) 请求头部再发送给服务器。

```html
GET /sample_page.html HTTP/1.1
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```



如何在以下几种服务端程序中设置 `Set-Cookie` 响应头信息 :

- [PHP](https://secure.php.net/manual/en/function.setcookie.php)
- [Node.JS](https://nodejs.org/dist/latest-v8.x/docs/api/http.html#http_response_setheader_name_value)
- [Python](https://docs.python.org/3/library/http.cookies.html)
- [Ruby on Rails](https://api.rubyonrails.org/classes/ActionDispatch/Cookies.html)



## Cookie 的生命周期

Cookie 的生命周期可以通过两种方式定义：

- 会话期 Cookie 是最简单的 Cookie：浏览器关闭之后它会被自动删除，也就是说它仅在会话期内有效。会话期Cookie不需要指定过期时间（`Expires`）或者有效期（`Max-Age`）。需要注意的是，有些浏览器提供了会话恢复功能，这种情况下即使关闭了浏览器，会话期Cookie 也会被保留下来，就好像浏览器从来没有关闭一样，这会导致 Cookie 的生命周期无限期延长。
- 持久性 Cookie 的生命周期取决于过期时间（`Expires`）或有效期（`Max-Age`）指定的一段时间。

```html
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT;
```



当Cookie的过期时间被设定时，设定的日期和时间只与客户端相关，而不是服务端。

如果您的站点对用户进行身份验证，则每当用户进行身份验证时，它都应重新生成并重新发送会话 Cookie，甚至是已经存在的会话 Cookie。此技术有助于防止[会话固定攻击（session fixation attacks）](https://wiki.developer.mozilla.org/en-US/docs/Web/Security/Types_of_attacks#Session_fixation)，在该攻击中第三方可以重用用户的会话。



## 限制访问 Cookie



有两种方法可以确保 `Cookie` 被安全发送，并且不会被意外的参与者或脚本访问：`Secure` 属性和`HttpOnly` 属性。

标记为 `Secure` 的 Cookie 只应通过被 HTTPS 协议加密过的请求发送给服务端，因此可以预防 [man-in-the-middle](https://developer.mozilla.org/zh-CN/docs/Glossary/MitM) 攻击者的攻击。但即便设置了 `Secure` 标记，敏感信息也不应该通过 Cookie 传输，因为 Cookie 有其固有的不安全性，`Secure` 标记也无法提供确实的安全保障, 例如，可以访问客户端硬盘的人可以读取它。

从 Chrome 52 和 Firefox 52 开始，不安全的站点（`http:`）无法使用Cookie的 `Secure` 标记。

JavaScript [`Document.cookie`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/cookie) API 无法访问带有 `HttpOnly` 属性的cookie；此类 Cookie 仅作用于服务器。例如，持久化服务器端会话的 Cookie 不需要对 JavaScript 可用，而应具有 `HttpOnly` 属性。此预防措施有助于缓解[跨站点脚本（XSS）](https://wiki.developer.mozilla.org/zh-CN/docs/Web/Security/Types_of_attacks#Cross-site_scripting_(XSS))攻击。

```html
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```

###  

## Cookie 的作用域

`Domain` 和 `Path` 标识定义了Cookie的*作用域：*即允许 Cookie 应该发送给哪些URL。



### Domain 属性

`Domain` 指定了哪些主机可以接受 Cookie。如果不指定，默认为 [origin](https://developer.mozilla.org/zh-CN/docs/Glossary/源)，**不包含子域名**。如果指定了`Domain`，则一般包含子域名。因此，指定 `Domain` 比省略它的限制要少。但是，当子域需要共享有关用户的信息时，这可能会有所帮助。 

例如，如果设置 `Domain=mozilla.org`，则 Cookie 也包含在子域名中（如`developer.mozilla.org`）。

当前大多数浏览器遵循 [RFC 6265](http://tools.ietf.org/html/rfc6265)，设置 Domain 时 不需要加前导点。浏览器不遵循该规范，则需要加前导点，例如：`Domain=.mozilla.org`



### Path 属性

`Path` 标识指定了主机下的哪些路径可以接受 Cookie（该 URL 路径必须存在于请求 URL 中）。以字符 `%x2F` ("/") 作为路径分隔符，子路径也会被匹配。

例如，设置 `Path=/docs`，则以下地址都会匹配：

- `/docs`
- `/docs/Web/`
- `/docs/Web/HTTP`



### SameSite attribute

`SameSite` Cookie 允许服务器要求某个 cookie 在跨站请求时不会被发送，（其中  [Site](https://developer.mozilla.org/en-US/docs/Glossary/Site) 由可注册域定义），从而可以阻止跨站请求伪造攻击（[CSRF](https://developer.mozilla.org/zh-CN/docs/Glossary/CSRF)）。

SameSite cookies 是相对较新的一个字段，[所有主流浏览器都已经得到支持](https://developer.mozilla.org/en-US/docs/Web/HTTP/headers/Set-Cookie#Browser_compatibility)。

下面是例子：

```js
Set-Cookie: key=value; SameSite=Strict
```

SameSite 可以有下面三种值：

- `**None**`**。**浏览器会在同站请求、跨站请求下继续发送 cookies，不区分大小写。
- **`Strict`。**浏览器将只在访问相同站点时发送 cookie。（在原有 Cookies 的限制条件上的加强，如上文 “Cookie 的作用域” 所述）
- **`Lax`。**与 **`Strict`** 类似，但用户从外部站点导航至URL时（例如通过链接）除外。 在新版本浏览器中，为默认选项，Same-site cookies 将会为一些跨站子请求保留，如图片加载或者 frames 的调用，但只有当用户从外部站点导航到URL时才会发送。如 link 链接

以前，如果 SameSite 属性没有设置，或者没有得到运行浏览器的支持，那么它的行为等同于 None，Cookies 会被包含在任何请求中——包括跨站请求。

大多数主流浏览器正在将 [SameSite 的默认值迁移至 Lax](https://www.chromestatus.com/feature/5088147346030592)。如果想要指定 Cookies 在同站、跨站请求都被发送，现在需要明确指定 SameSite 为 None。



## Cookie prefixes

cookie 机制的使得服务器无法确认 cookie 是在安全来源上设置的，甚至无法确定 cookie 最初是在哪里设置的。

子域上的易受攻击的应用程序可以使用 Domain 属性设置 cookie，从而可以访问所有其他子域上的该 cookie。会话固定攻击中可能会滥用此机制。有关主要缓解方法，请参阅[会话劫持（ session fixation）](https://wiki.developer.mozilla.org/en-US/docs/Web/Security/Types_of_attacks#Session_fixation)

但是，作为[深度防御措施](https://en.wikipedia.org/wiki/Defense_in_depth_(computing))，可以使用 cookie 前缀来断言有关 cookie 的特定事实。有两个前缀可用：

- `__Host-`

    如果 cookie 名称具有此前缀，则仅当它也用 `Secure` 属性标记，是从安全来源发送的，不包括 `Domain` 属性，并将 `Path` 属性设置为 `/` 时，它才在 [`Set-Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie) 标头中接受。这样，这些 cookie 可以被视为 "domain-locked”。

- `__Secure-`

    如果 cookie 名称具有此前缀，则仅当它也用 `Secure` 属性标记，是从安全来源发送的，它才在 [`Set-Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Set-Cookie) 标头中接受。该前缀限制要弱于 `__Host-` 前缀。

带有这些前缀点 Cookie， 如果不符合其限制的会被浏览器拒绝。请注意，这确保了如果子域要创建带有前缀的 cookie，那么它将要么局限于该子域，要么被完全忽略。由于应用服务器仅在确定用户是否已通过身份验证或 CSRF 令牌正确时才检查特定的 cookie 名称，因此，这有效地充当了针对会话劫持的防御措施。

在应用程序服务器上，Web 应用程序**必须**检查完整的 cookie 名称，包括前缀 —— 用户代理程序在从请求的 [`Cookie`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Cookie) 标头中发送前缀之前，**不会**从 cookie 中剥离前缀。

有关 cookie 前缀和浏览器支持的当前状态的更多信息，请参阅 [Prefixes section of the Set-Cookie reference article](https://wiki.developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#Cookie_prefixes)。







* JavaScript 通过 Document.cookie 访问 Cookie

通过 [`Document.cookie`](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/cookie) 属性可创建新的 Cookie，也可通过该属性访问非`HttpOnly`标记的Cookie。

```js
document.cookie = "yummy_cookie=choco"; 
document.cookie = "tasty_cookie=strawberry"; 
console.log(document.cookie); 
// logs "yummy_cookie=choco; tasty_cookie=strawberry"
```

通过 JavaScript 创建的 Cookie 不能包含 HttpOnly 标志。

请留意在[安全](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies#Security)章节提到的安全隐患问题，JavaScript 可以通过跨站脚本攻击（XSS）的方式来窃取 Cookie。





## Cookie 安全

缓解涉及Cookie的攻击的方法：

- 使用 `HttpOnly` 属性可防止通过 JavaScript 访问 cookie 值。
- 用于敏感信息（例如指示身份验证）的 Cookie 的生存期应较短，并且 `SameSite` 属性设置为`Strict` 或 `Lax`。（请参见上方的 [SameSite Cookie](https://wiki.developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies$edit#)。）在[支持 SameSite 的浏览器](https://wiki.developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#Browser_compatibility)中，这样做的作用是确保不与跨域请求一起发送身份验证 cookie，因此，这种请求实际上不会向应用服务器进行身份验证。
- JSON Web Tokens 之类的替代身份验证/机密机制
- [OWASP CSRF prevention cheat sheet](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)_Prevention_Cheat_Sheet)



### 僵尸Cookie

僵尸Cookie（或称之为“删不掉的Cookie”），这类 Cookie 较难以删除，甚至删除之后会自动重建。这些技术违反了用户隐私和用户控制的原则，可能违反了数据隐私法规，并可能使使用它们的网站承担法律责任。它们一般是使用 [Web storage API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Storage_API)、Flash本地共享对象或者其他技术手段来达到的。相关内容可以看：





# CORS 跨源资源共享

**跨源资源共享** ([CORS](https://developer.mozilla.org/zh-CN/docs/Glossary/CORS)) （或通俗地译为跨域资源共享）是一种基于[HTTP](https://developer.mozilla.org/zh-CN/docs/Glossary/HTTP) 头的机制，该机制通过允许服务器标示除了它自己以外的其它[origin](https://developer.mozilla.org/zh-CN/docs/Glossary/源)（域，协议和端口），这样浏览器可以访问加载这些资源。跨源资源共享还通过一种机制来检查服务器是否会允许要发送的真实请求，该机制通过浏览器发起一个到服务器托管的跨源资源的"预检"请求。在预检中，浏览器发送的头中标示有HTTP方法和真实请求中会用到的头。



跨源资源共享标准新增了一组 HTTP 首部字段，允许服务器声明哪些源站通过浏览器有权限访问哪些资源。另外，规范要求，对那些可能对服务器数据产生副作用的 HTTP 请求方法（特别是 [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET) 以外的 HTTP 请求，或者搭配某些 MIME 类型的 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST) 请求），浏览器必须首先使用 [`OPTIONS`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/OPTIONS) 方法发起一个预检请求（preflight request），从而获知服务端是否允许该跨源请求。服务器确认允许之后，才发起实际的 HTTP 请求。在预检请求的返回中，服务器端也可以通知客户端，是否需要携带身份凭证（包括 [Cookies ](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Cookies)和 HTTP 认证相关数据）



CORS请求失败会产生错误，但是为了安全，在JavaScript代码层面是无法获知到底具体是哪里出了问题。你只能查看浏览器的控制台以得知具体是哪里出现了错误。

接下来的内容将讨论相关场景，并剖析该机制所涉及的 HTTP 首部字段。



某些请求不会触发 [CORS 预检请求](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS#Preflighted_requests)。本文称这样的请求为“简单请求”，请注意，该术语并不属于 [Fetch](https://fetch.spec.whatwg.org/) （其中定义了 CORS）规范。若请求满足所有下述条件，则该请求可视为“简单请求”：

- 使用下列方法之一：

    - [`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET)
    - [`HEAD`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/HEAD)
    - [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST)

- 除了被用户代理自动设置的首部字段（例如 `Connection`,`User-Agent`）和在 Fetch 规范中定义为 

    禁用首部名称的其他首部，允许人为设置的字段为 Fetch 规范定义的,对 CORS 安全的首部字段集合

    该集合为：

    - [`Accept`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept)
    - [`Accept-Language`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Accept-Language)
    - [`Content-Language`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Language)
    - [`Content-Type`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type) （需要注意额外的限制）
    - `DPR`
    - `Downlink`
    - `Save-Data`
    - `Viewport-Width`
    - `Width`

- `Content-Type`的值仅限于下列三者之一：

    ```
    text/plain
    multipart/form-data
    application/x-www-form-urlencoded
    ```

- 请求中的任意[`XMLHttpRequestUpload`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequestUpload) 对象均没有注册任何事件监听器；[`XMLHttpRequestUpload`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequestUpload) 对象可以使用 [`XMLHttpRequest.upload`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/upload) 属性访问。

- 请求中没有使用 [`ReadableStream`](https://developer.mozilla.org/zh-CN/docs/Web/API/ReadableStream) 对象。



## 访问控制

使用 [`Origin`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Origin) 和 [`Access-Control-Allow-Origin`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) 就能完成最简单的访问控制

# 响应类

```
HTTP/1.1 200 OK // 状态行
Bdpagetype: 2 // 响应头
Bdqid: 0x9376a04b0015f045
Cache-Control: private
Connection: keep-alive
Content-Encoding: gzip
Content-Type: text/html;charset=utf-8
Date: Fri, 31 Jan 2020 09:13:00 GMT
Expires: Fri, 31 Jan 2020 09:12:59 GMT
Server: BWS/1.1
Set-Cookie: BDSVRTM=372; path=/
Set-Cookie: BD_HOME=1; path=/
Set-Cookie: H_PS_PSSID=1464_21098_18560_30472_30479; path=/; domain=.baidu.com
Strict-Transport-Security: max-age=172800
Traceid: 1580461980055062605810625856614811693125
X-Ua-Compatible: IE=Edge,chrome=1
Transfer-Encoding: chunked 

<!doctype html>  // 响应体
<html>
<head>
		<title>Example</title>
```



## 状态行

```
HTTP/1.1 200 OK
```

> HTTP/1.1: 协议版本
>
> 200 : 状态码
>
> OK : 原因
>
> \r\n : 结尾换行





## Response Headers

key value格式，尾部追加\r\n换行

```
Bdpagetype: 2 // 响应头
Bdqid: 0x9376a04b0015f045
Cache-Control: private
Connection: keep-alive
Content-Encoding: gzip
Content-Type: text/html;charset=utf-8
Date: Fri, 31 Jan 2020 09:13:00 GMT
Expires: Fri, 31 Jan 2020 09:12:59 GMT
Server: BWS/1.1
Set-Cookie: BDSVRTM=372; path=/
Set-Cookie: BD_HOME=1; path=/
Set-Cookie: H_PS_PSSID=1464_21098_18560_30472_30479; path=/; domain=.baidu.com
Strict-Transport-Security: max-age=172800
Traceid: 1580461980055062605810625856614811693125
X-Ua-Compatible: IE=Edge,chrome=1
Transfer-Encoding: chunked 
```



### Server

Web服务器端使用什么程序响应的



### Content-Type

内容类型

常用 html， 音频，视频等



### Content-Length

内容长度



### Access-Control-Allow-Origin

服务端返回的 `Access-Control-Allow-Origin: *` 表明，该资源可以被**任意**外域访问。

如果服务端仅允许来自 http://foo.example 的访问，该首部字段的内容如下：

```
Access-Control-Allow-Origin: http://foo.example
```

如果服务端指定了具体的域名而非`*`，那么响应首部中的 Vary 字段的值必须包含 Origin。这将告诉客户端：服务器对不同的源站返回不同的内容。



```
access-control-allow-credentials: true
access-control-allow-headers: Origin, X-Requested-With, Content-Type, Accept, Authorization
access-control-allow-methods: GET, PUT, POST, DELETE
access-control-allow-origin: https://www.xurick.com
```



对于附带身份凭证的请求，服务器不得设置 `Access-Control-Allow-Origin` 的值为“`*`”。

这是因为请求的首部中携带了 `Cookie` 信息，如果 `Access-Control-Allow-Origin` 的值为“`*`”，请求将会失败。而将 `Access-Control-Allow-Origin` 的值设置为 `http://foo.example`，则请求将成功执行。

另外，响应首部中也携带了 Set-Cookie 字段，尝试对 Cookie 进行修改。如果操作失败，将会抛出异常。





### Access-Control-Expose-Headers

在跨源访问时，XMLHttpRequest对象的getResponseHeader()方法只能拿到一些最基本的响应头，Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma，如果要访问其他头，则需要服务器设置本响应头。

[`Access-Control-Expose-Headers`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Access-Control-Expose-Headers) 头让服务器把允许浏览器访问的头放入白名单，例如：

```html
Access-Control-Expose-Headers: X-My-Custom-Header, X-Another-Custom-Header
```

这样浏览器就能够通过getResponseHeader访问`X-My-Custom-Header`和 `X-Another-Custom-Header` 响应头了。



### Access-Control-Max-Age

指定了preflight请求的结果能够被缓存多久，请参考本文在前面提到的preflight例子。

```html
Access-Control-Max-Age: <delta-seconds>
```

`delta-seconds` 参数表示preflight请求的结果在多少秒内有效。



### Access-Control-Allow-Credentials

指定了当浏览器的`credentials`设置为true时是否允许浏览器读取response的内容。当用在对preflight预检测请求的响应中时，它指定了实际的请求是否可以使用`credentials`。请注意：简单 GET 请求不会被预检；如果对此类请求的响应中不包含该字段，这个响应将被忽略掉，并且浏览器也不会将相应内容返回给网页。

```html
Access-Control-Allow-Credentials: true
```



### Access-Control-Allow-Methods

首部字段用于预检请求的响应。其指明了实际请求所允许使用的 HTTP 方法。

```html
Access-Control-Allow-Methods: <method>[, <method>]*
```







## Request Headers

### origin

首部字段表明预检请求或实际请求的源站。

```html
Origin: <origin>
```

origin 参数的值为源站 URI。它不包含任何路径信息，只是服务器名称。

**Note:** 有时候将该字段的值设置为空字符串是有用的，例如，当源站是一个 data URL 时。

注意，在所有访问控制请求（Access control request）中，[`Origin`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Origin) 首部字段**总是**被发送。



### OPTIONS

需预检的请求要求必须首先使用 [`OPTIONS`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/OPTIONS)  方法发起一个预检请求到服务器，以获知服务器是否允许该实际请求。"预检请求“的使用，可以避免跨域请求对服务器的用户数据产生未预期的影响。

```
OPTIONS /resources/post-here/ HTTP/1.1
Host: bar.other
User-Agent: Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.5; en-US; rv:1.9.1b3pre) Gecko/20081130 Minefield/3.1b3pre
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-us,en;q=0.5
Accept-Encoding: gzip,deflate
Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
Connection: keep-alive
Origin: http://foo.example
Access-Control-Request-Method: POST
Access-Control-Request-Headers: X-PINGOTHER, Content-Type


HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2.0.61 (Unix)
Access-Control-Allow-Origin: http://foo.example
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: X-PINGOTHER, Content-Type
Access-Control-Max-Age: 86400
Vary: Accept-Encoding, Origin
Content-Encoding: gzip
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain
```





### Access-Control-Request-Method

首部字段用于预检请求。其作用是，将实际请求所使用的 HTTP 方法告诉服务器。

```html
Access-Control-Request-Method: <method>
```



### Access-Control-Request-Headers

首部字段用于预检请求。其作用是，将实际请求所携带的首部字段告诉服务器。

```html
Access-Control-Request-Headers: <field-name>[, <field-name>]*
```









## 响应体

与响应头空行隔开

可以是文本或者是二进制格式，大小由Content-Length头指定







# RESTFUL API 设计

https://github.com/godruoyi/restful-api-specification



## HATEOAS

API 的使用者未必知道，URL 是怎么设计的。一个解决方法就是，在回应中，给出相关链接，便于下一步操作。这样的话，用户只要记住一个 URL，就可以发现其他的 URL。这种方法叫做 HATEOAS。

```
{
  ...
  "feeds_url": "https://api.github.com/feeds",
  "followers_url": "https://api.github.com/user/followers",
  "following_url": "https://api.github.com/user/following{/target}",
  "gists_url": "https://api.github.com/gists{/gist_id}",
  "hub_url": "https://api.github.com/hub",
  ...
}
```









# HTTP 响应状态码

详细查看 [https://tools.ietf.org/html/rfc7231](https://tools.ietf.org/html/rfc7231)



```
module.exports = {
  "100": "Continue",
  "101": "Switching Protocols",
  "102": "Processing",
  "200": "OK",
  "201": "Created",
  "202": "Accepted",
  "203": "Non-Authoritative Information",
  "204": "No Content",
  "205": "Reset Content",
  "206": "Partial Content",
  "207": "Multi-Status",
  "208": "Already Reported",
  "226": "IM Used",
  "300": "Multiple Choices",
  "301": "Moved Permanently",
  "302": "Found",
  "303": "See Other",
  "304": "Not Modified",
  "305": "Use Proxy",
  "307": "Temporary Redirect",
  "308": "Permanent Redirect",
  "400": "Bad Request",
  "401": "Unauthorized",
  "402": "Payment Required",
  "403": "Forbidden",
  "404": "Not Found",
  "405": "Method Not Allowed",
  "406": "Not Acceptable",
  "407": "Proxy Authentication Required",
  "408": "Request Timeout",
  "409": "Conflict",
  "410": "Gone",
  "411": "Length Required",
  "412": "Precondition Failed",
  "413": "Payload Too Large",
  "414": "URI Too Long",
  "415": "Unsupported Media Type",
  "416": "Range Not Satisfiable",
  "417": "Expectation Failed",
  "418": "I'm a teapot",
  "421": "Misdirected Request",
  "422": "Unprocessable Entity",
  "423": "Locked",
  "424": "Failed Dependency",
  "425": "Unordered Collection",
  "426": "Upgrade Required",
  "428": "Precondition Required",
  "429": "Too Many Requests",
  "431": "Request Header Fields Too Large",
  "451": "Unavailable For Legal Reasons",
  "500": "Internal Server Error",
  "501": "Not Implemented",
  "502": "Bad Gateway",
  "503": "Service Unavailable",
  "504": "Gateway Timeout",
  "505": "HTTP Version Not Supported",
  "506": "Variant Also Negotiates",
  "507": "Insufficient Storage",
  "508": "Loop Detected",
  "509": "Bandwidth Limit Exceeded",
  "510": "Not Extended",
  "511": "Network Authentication Required"
}
```



## 1XX 信息相关

API 不需要`1xx`状态码



## 2XX 成功类

### 200 

请求被正常处理

OK. The request has successfully executed. Response depends upon the verb invoked.



### 201 

请求被正常处理，并创建一个新的资源

Created. The request has successfully executed and a new resource has been created in the process. The response body is either empty or contains a representation containing URIs for the resource created. The Location header in the response should point to the URI as well.



### 202 

Accepted. The request was valid and has been accepted but has not yet been processed. The response should include a URI to poll for status updates on the request. This allows asynchronous REST requests



### 204

请求处理成功，无内容返回

No Content. The request was successfully processed but the server did not have any response. The client should not update its display.



### 206

partial content 客户发送了一个带有Range头的GET请求，服务器完成了它。



## 3XX 重定向类

### 301

永久重定向，所请求的页面已经转移至新的url

Moved Permanently. The requested resource is no longer located at the specified URL. The new Location should be returned in the response header. Only GET or HEAD requests should redirect to the new location. The client should update its bookmark if possible.



### 302

临时重定向。

如访问一个需要登录页面，没有登录，会重定向到登录页面

Found. The requested resource has temporarily been found somewhere else. The temporary Location should be returned in the response header. Only GET or HEAD requests should redirect to the new location. The client need not update its bookmark as the resource may return to this URL.



### 303

所请求的页面可在别的url下被找到。

`303`用于`POST`、`PUT`和`DELETE`请求

收到`303`以后，浏览器不会自动跳转，而会让用户自己决定下一步怎么办。下面是一个例子。

```http
HTTP/1.1 303 See Other
Location: /api/orders/12345
```



See Other. This response code has been reinterpreted by the W3C Technical Architecture Group (TAG) as a way of responding to a valid request for a non-network addressable resource. This is an important concept in the Semantic Web when we give URIs to people, concepts, organizations, etc. There is a distinction between resources that can be found on the Web and those that cannot. Clients can tell this difference if they get a 303 instead of 200. The redirected location will be reflected in the Location header of the response. This header will contain a reference to a document about the resource or perhaps some metadata about it.



### 304

Not modified 未按预期修改文档。客户端有缓冲的文档并发出了一个条件性的请求（一般是提供If-Modified-Since头表示客户只想比指定日期更新的文档）。服务器告诉客户，原来缓冲的文档还可以继续使用。即重定向到缓存的资源

这样一来，可以节省一些带宽。若服务器通过 If-None-Match 或 If-Modified-Since判断后发现已过期，那么会带有该资源的实体内容返回。





### 307

`302`和`307`的含义一样，也是"暂时重定向"，区别在于`302`和`307`用于`GET`请求





## 4XX 客户端错误类





### 400

请求到URL在服务器上找不到，也就是客户端请求URL 错误， 请求参数错误，不做任何处理

Bad Request 服务器未能理解请求。

400 Bad Request response status code indicates that the server cannot or will not process the request due to something that is perceived to be a client error (e.g., malformed request syntax, invalid request message framing, or deceptive request routing).



### 401

Unauthorized 未授权请求，需要获取授权信息





### 403

Forbidden， 服务器拒绝访问，权限不够

但是不具有访问资源所需的权限



### 404

Not Found 服务器无法找到被请求的页面。



### 405 

 Method not allowed，若只允许指定的http方法如GET方法。POST方法会放回405，用户已经通过了认证，但是请求方法不允许



### 406

Not Acceptable



### 410

Gone. 所请求的资源已从这个地址转移，不再可用。



### 411

Length Required.



### 412

Precondition Failed.



### 413

Entity Too Large.



### 414

URI Too Long.



### 415

Unsupported Media Type.

客户端要求的返回格式不支持。比如，API 只能返回 JSON 格式，但是客户端要求返回 XML 格式。



### 417

Expectation Failed.



### 422

Unprocessable Entity， 客户端上传的附件无法处理，导致请求失败。



### 429

Too Many Requests， 客户端的请求次数超过限额。





## 5XX 服务器端错误类



### 500

Internet Server Error 请求未完成。客户端请求有效，但服务器遇到不可预知的情况，服务器内部发生错误





### 501

Not Implemented 请求未完成。服务器不支持所请求的功能。	



### 502 

连接超时 我们向服务器发送请求 由于服务器当前链接太多，导致服务器方面无法给于正常的响应,产生此类报错

The 502 Bad Gateway error is an HTTP status code that means that ELB received an invalid response from the EC2 Instance.



解决办法：

1.提高 Web 服务器的响应速度，也即减少内部的调用关系，可以把需要的页面、素材或数据，缓存在内存中，可以是专门的缓存服务器 ，也可以Web服务器自身的缓存，提高响应速度；

2.网络带宽的问题，则对传输的数据包进行压缩处理，或者向IDC申请增加带宽；

3.属于内部网络的故障或设置问题，也即内部网络拥塞，可能内部存在大量的数据调用或交互造成的，则需要优化内部网络传输或协议；

4.数据库的数据读取造成前端服务器 ，响应用户的请求变慢，那么必须提高数据库的处理能力，若是只读业务可以增加数据缓存的模式 或者增加数据库备机，分散读压力；



HTTP 502: Bad gateway

Possible causes:

- The load balancer received a TCP RST from the target when attempting to establish a connection.
- The load balancer received an unexpected response from the target, such as "ICMP Destination unreachable (Host unreachable)", when attempting to establish a connection. Check whether traffic is allowed from the load balancer subnets to the targets on the target port.
- The target closed the connection with a TCP RST or a TCP FIN while the load balancer had an outstanding request to the target. Check whether the keep-alive duration of the target is shorter than the idle timeout value of the load balancer.
- The target response is malformed or contains HTTP headers that are not valid.
- The load balancer encountered an SSL handshake error or SSL handshake timeout (10 seconds) when connecting to a target.
- The deregistration delay period elapsed for a request being handled by a target that was deregistered. Increase the delay period so that lengthy operations can complete.
- The target is a Lambda function and the response body exceeds 1 MB.
- The target is a Lambda function that did not respond before its configured timeout was reached.



当目标类型为 `ip` 时，负载均衡器可支持针对每个唯一目标（IP 地址和端口）的 55000 个并发连接或每分钟约 55000 个连接。如果连接数超过该值，则会增大出现端口分配错误的几率。如果您收到端口分配错误，请将多个目标添加到目标组。
Network Load Balancer不支持 `lambda` 目标类型，仅 Application Load Balancer 支持 `lambda` 目标类型。有关详细信息，请参阅 [Lambda功能作为目标](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/lambda-functions.html) 在 *Application Load Balancer 用户指南*.



### 503

Service Unavailable.

服务器无法处理请求，一般用于网站维护状态。



### 504

Gateway timeout

the proxy server or gateway did not receive a response from the origin server within a specified timeout. Consider for example that an Elastic Load Balancer is sitting between your origin server, and the ELB timed out trying to receiving the response from your server.

Possible causes:

- The load balancer failed to establish a connection to the target before the connection timeout expired (10 seconds).
- The load balancer established a connection to the target but the target did not respond before the idle timeout period elapsed.
- The network ACL for the subnet did not allow traffic from the targets to the load balancer nodes on the ephemeral ports (1024-65535).
- The target returns a content-length header that is larger than the entity body. The load balancer timed out waiting for the missing bytes.
- The target is a Lambda function and the Lambda service did not respond before the connection timeout expired.

