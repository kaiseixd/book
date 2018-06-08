# 2 简单的http协议
## 2.5

- GET: 用来请求访问已被 URI 识别的资源
- POST: 用来传输实体的主体
- PUT: 用来传输文件
- HEAD: 获得报文首部（不返回报文主体部分，仅响应首部）
- DELETE: 删除文件
- OPTIONS: 查询支持请求指定 URI 的方法

## 2.7

- 最初的 HTTP 协议中，每进行一次 HTTP 通信都要断开一次 TCP连接

```
sequenceDiagram
A->>B: SYN（建立TCP连接）
B->>A: SYN/ACK
A->>B: ACK
A->>B: HTTP请求（请求资源）
B->>A: HTTP响应
B->>A: FIN（断开TCP连接）
A->>B: ACK
A->>B: FIN
B->>A: ACK
```

- 持久连接（HTTP keep-alive）：只要任意一端没有明确提出断开连接，则保持 TCP 连接状态

## 2.8 cookie

- HTTP 本身是无法保留状态的
- Set-Cookie：响应报文内的首部字段信息，通知客户端保存 Cookie，并在下次请求时自动在报文中加入 Cookie 值

# HTTP 报文内的 HTTP 信息
## 3.1

- 报文：由 8 位组字节流组成
- 结构：报文首部 空行（CR+LF） 报文主体

## 3.2

- 请求报文首部：请求行 请求首部字段 通用首部字段 实体首部字段 其他
- 响应报文首部：状态行 响应首部字段 通用首部字段 实体首部字段 其他

## 3.3

- 通过编码提升传输速率
- 编码操作可能会导致报文主体和实体主体不一致
- 常用的内容编码：
    - gzip
    - compress
    - deflate
    - identity（不进行编码）

- 分块传输编码（chunk）：每个块都会用十六进制来标记块的大小，最后一块会用 “0（CR+LF）” 来标记。客户端会负责解码

## 3.4 多部分对象集合（multipart）

- multipart/form-data：在 Web 表单文件上传时使用
- multipart/byteranges
    - 状态码 206 响应报文包含了多个范围的内容时使用

- 需要在首部加上 Content-type
- 使用 boundary 字符串来划分各类实体（起始行插入 '--' 作为标记，最后一个对象需要在尾部也插入 '--'）

## 3.5

- 范围请求（Range Request）：指定范围发送请求
- `Range: bytes=5001-10000`
- 206 Partial Content

## 3.6

- 内容协商（Content Negotiation）：以语言、字符集、编码方式等作为判断的基准，提供给客户端最为合适的资源
- Accept Accept-Charset Accept-Encoding Accept-Language Content-Language
- 3 种技术类型：
    - 服务器驱动协商：以请求的首部字段为参考自动处理
    - 客户端驱动协商：客户端手动选择
    - 透明协商：以上结合

# 4 返回结果的 HTTP 状态码
## 4.1

- 状态码：描述返回的请求的结果

## 4.2

- 请求成功被处理

- 200 OK
- 204 No Content：响应报文中不含实体的主体部分
- 206 Partial Content：响应报文中包含由 Content-Range 指定范围的实体内容

## 4.3

- 浏览器需要执行某些特殊的操作以正确处理请求

- 301 Moved Permanently：永久性重定向，请求的资源已经被分配了新的 URI
- 302 Found：临时性重定向
- 303 See Other：类似 302 ，但是明确表示应当采用 GET 获取资源
- 304 Not Modified：允许请求访问资源，但未满足条件的情况（与重定向无关），返回时不包含响应主体部分
- 307 Temporary Redirect：临时重定向，并且遵照标准禁止 POST 变成 GET

## 4.4

- 表明客户端是发生错误的原因所在

- 400 Bad Request：报文中存在语法错误
- 401 Unauthorized：发送的请求需要有通过 HTTP 认证的认证消息，或者之前的认证失败
- 403 Forbidden：请求被服务器拒绝
- 404 Not Found：服务器上无法找到请求的资源

## 4.5

- 表明服务器本身发生错误

- 500 Internet Server Error：服务端在执行请求时发生了错误
- 503 Service Unavailable：服务器暂时处于超负载或正在进行停机维护，无法处理请求（可以通过 Retry-After 字段将维护时间返回给客户端）

# 5 与 HTTP 协作的 Web 服务器
## 5.2 通信数据转发程序：代理、网关、隧道
### 代理

- 具有转发功能的应用程序，接收由客户端发送的请求并转发给服务器，同时也接收服务器返回的响应并转发给客户端
- 每次通过代理转发时会追加写入 Via 首部信息
- 缓存代理：再次接收到对相同资源的请求时，可以直接返回而不从源服务器获取
- 透明代理：不对报文做任何加工

### 网关

- 转发其他服务器通信数据的服务器，接收从客户端发送来的请求时，它就像源服务器一样对请求进行处理
- 类似代理，能使通信线路上的服务器提供非 HTTP 协议服务
- 可以提高通信的安全性，可以在客户端与网关之间的通信线路上加密以确保连接的安全

### 隧道

- 在相隔甚远的客户端和服务器之间进行中转，并保持双方通信连接
- 确保客户端与服务器进行安全的通信
- 本身不会去解析 HTTP 请求

## 5.3

- 缓存：代理服务器或客户端本地磁盘内保存的资源副本

# 6 HTTP 首部
## 6.2 HTTP 首部字段
### 通用首部字段（General Header Fields）

- 请求报文和响应报文两方都会使用的首部字段

- Cache-Control：控制缓存的工作机制
    - no-cache：防止从缓存中返回过期的资源
    - private / public
    - no-store：请求或响应中有机密信息，不能在本地存储
    - s-maxage：适用于供多位用户使用的公共缓存服务器
    - maxage：代表资源缓存的最长时间(s)，会忽略 Expires 头部字段
    - min-fresh：要求缓存服务器返回至少还未过指定时间的缓存资源
    - max-stale：即使资源过期，只要处于 max-stale 指定的时间内，仍然会被客户端接收
        - 不指定参数的话，无论过多久客户端都会接收响应
    - only-if-cached：仅在缓存服务器本地缓存目标资源时才会返回，否则就 504
    - must-revalidate：必须验证有效性（优先于 max-stale）
    - proxy-revalidate
    - no-transform：缓存不能改变实体主体的媒体类型
    - *cache-extension token

- Connection：控制不再转发给代理的首部字段 / 管理持久连接
    - 不再转发的首部字段名
    - Keep-Alive：想要在旧版本的 HTTP 协议上维持连接需要指定
    - close：明确断开连接

- Date：表明创建 HTTP 报文的日期和时间
    - RFC1123

- Pragma：历史遗留字段
    - no-cache（只用在客户端发送的请求中）

- Trailer：事先说明报文主体记录了哪些首部字段（分块传输）

- Transfer-Encoding：规定了传输报文主体时采用的编码方式
    - chunked

- Upgrage：用于检测 HTTP 协议及其他协议是否可使用更高的版本进行通信

- Via：追踪报文的转发，还可以避免回环

- Warning

### 请求首部字段（Request Header Fields）

- 向服务器发送请求报文时使用的首部，补充了请求的附加内容、客户端信息、响应内容相关优先级等信息

- Accept：能够处理的媒体类型及媒体类型的相对优先级
    - type/subtype：一次指定多种媒体类型
    - q=0~1（用分号分隔，不指定时默认1）

- Accept-Charset：支持的字符集及优先级

- Accept-Encoding：支持的内容编码及优先级

- Accept-Language：支持的语言集及优先级

- Authorization：提供用户代理的认证信息（证书值）

- Expect：期望出现的某种特定行为
    - 服务器无法理解客户端的期望作出回应而发生错误时会返回 417 Expectation Failed
    - 100-continue：等待状态码 100 响应的客户端需要指定

- From：告知用户的邮箱地址

- Host：告知请求的资源所处的互联网主机名和端口号（必须项，因为虚拟主机运行在同一个 IP 上）

- If-Match：判定指定条件为真（对比 ETag）时服务器才会执行请求（不匹配时返回 412 Precondition Failed）
    - *：只要资源存在就处理请求，忽略 Etag 值

- If-Modified-Since：在指定日期后资源发生更新则服务器会接受请求（否则返回 304 Not Modified）

- If-None-Match：!If-Match
    - 在 GET 或 HEAD 方法中使用该字段可获取最新资源

- If-Range：若是跟 ETag 值或更新的日期时间匹配一致则作为范围请求处理，若不一致则忽略返回请求，返回全部资源
    - 相比 If-Match 更省力

- If-Unmodified-Since：!If-Modified-Since（否则返回 412 Precondition Failed）

- Max-Forwards：指定可经过的服务器最大数目，在值为0时直接返回响应（用于 TRACE 方法或 OPTIONS 方法）

- Proxy-Authorization：应对代理服务器发来的认证质询

- Range：告知服务器资源的指定范围（服务器在处理后会返回 206，若无法处理范围要求则返回全部资源及 200）
    - bytes=5001-10000

- Referer：告知服务器请求的原始资源的 URI（由于 URI 中的查询字符串可能含有 ID 和密码，转发给其他服务器可能会导致信息泄露）

- TE：能够响应的传输编码方式及优先级（类似 Accept-Encoding 但是用于传输编码）
    - trailers

- User-Agent：创建请求的浏览器和用户代理名称等信息传达给服务器，经过代理的话可能会添加上代理服务器的名称

### 响应首部字段（Response Header Fields）

- 从服务器向客户端返回响应报文时使用的首部，补充了响应的附加内容、也会要求客户端附加额外的内容信息

- Accept-Ranges：服务器是否能处理范围请求
    - bytes
    - none

- Age：源服务器在多久前创建了响应(s)
    - 创建该响应的是缓存服务器的话则是指缓存后的响应再次发起认证到认证完成的时间值
    - 代理创建响应时必须加上 Age

- ETag：实体标识，可将资源以字符串形式做唯一性标识
    - 资源更新时 ETag 值也需要更新，由服务器决定
    - 强ETag：无论实体发生多么细微的变化都会改变其值
    - 弱ETag：只用于提示资源是否相同，在资源发生根本改变时才会改变值，会在字段值最开始处附加 'W/'

- Location：提供重定向的 URI

- Proxy-Authenticate：把代理服务器所要求的认证信息发送给客户端

- Retry-After：告知客户端多久后再次发送请求(s)，配合 503 或者 3xx 一起使用

- Server：告知服务器上安装的 HTTP 服务器应用程序的信息

- Vary：对缓存进行控制，由源服务器传达向代理服务器
    - 代理服务器收到包含 Vary 指定项的响应后，若再要进行缓存，仅对含有与 Vary 指定项相同字段的请求返回缓存
    - 如果请求中的字段与 Vary 指定项不同，则需要从源服务器重新获取资源

- WWW-Authenticate：用于 HTTP 访问认证，告知客户端适用于访问请求 URI 所指定资源的认证方案（Basic 或 Digest）
    - realm

### 实体首部字段（Entity Header Fields）

- 针对请求和响应报文的实体部分使用的首部，补充了资源内容更新时间等与实体有关的信息

- Allow：通知客户端支持的 HTTP 方法，如果收到不支持的会返回 405 Method Not Allowed 以及所有支持的 HTTP （写入Allow）

- Content-Encoding：告知客户端服务器对实体的主体部分选用的内容编码方式

- Content-Language：告知客户端实体主体使用的自然语言

- Content-Length：实体主体部分的大小（字节）
    - 对实体主体进行内容编码传输时不能使用

- Content-Location：给出与报文主体部分相对应的 URI（返回资源对应的 URI）

- Content-MD5：检查报文主体在传输过程中是否保持完成（客户端也会执行相同的 MD5 算法并与字段值比较）
    - 通过 MD5 算法获得 128 位二进制，再通过 Base64 编码后写入字段值
    - 但是无法检测恶意篡改和内容偶发性改变

- Content-Range：针对范围要求返回的响应需要使用该字段
    - bytes 5001-10000/10000

- Content-Type：说明实体主体内对象的媒体类型
    - charset=UTF-8

- Expires：将资源失效日期告知客户端，缓存服务器在收到含有首部字段 Expires 的响应后，会以缓存来应答请求
    - 优先级低于 max-age

- Last-Modified：资源最终修改时间

### 为 Cookie 服务的首部字段

- Set-Cookie：开始状态管理所使用的 Cookie 信息
    - 响应首部字段
    - NAME=VALUE：必须，Cookie 的名称和值
    - expires=DATE：有效期，不指定则到关闭浏览器为止
    - path=PATH：Cookie 适用对象的目录
    - domain=域名：Cookie 适用对象的域名
    - Secure：仅在 HTTPS 安全通信时才会发送 Cookie
    - HttpOnly：使 Cookie 不能被 JavaScript 脚本访问（document.cookie）

- Cookie：服务器接收到的 Cookie 信息
    - 请求首部字段
    - status=enable

### 其他首部字段

- X-Frame-Options
- X-XSS-Protection
- DNT
- P3P

# 7 确保 Web 安全的 HTTPS
## 7.1 HTTP 的缺点

- 通信使用明文（不加密）
    - TCP/IP 是可能被窃听的网络，通信线路上的网络设备都有被窥视的风险
    - 通信加密：SSL（安全套接层）、TLS（安全层传输协议）
    - 内容加密：对报文主体加密（但是仍有被篡改的风险）
- 不验证通信方的身份，不论是谁发送的请求都会返回响应
    - 无法确定是否是伪装的客户端或服务器，无法确定对方是否有权限，无意义的请求也会接收（Dos攻击）
    - SSL 可以提供证书用于验证身份，证书由第三方机构颁发
- 无法证明报文的完整性，无法确认是否有人从中篡改
    - MD5 和 SHA-1 等散列值校验方法，以及数字签名

## 7.2 HTTPS

- HTTP + 通信加密 + 证书 + 完整性保护
- HTTP 通信接口部分用 SSL 和 TLS 协议代替（先经过 SSL 再与 TCP 通信）
- 公开密钥加密技术：使用一对非对称密钥，一把私有，另一把公开
- 混合机密：使用公开密钥加密方式交换在稍后的共享密钥加密中要使用的密钥，之后使用共享密钥加密方式通信
- 通过证书验证公开密钥（或者服务器把自己的公开密钥登录至数字证书认证机构），客户端使用服务器的公开密钥对报文加密后发送，服务器用私有密钥对报文解密

# 8 确认访问用户身份的认证






