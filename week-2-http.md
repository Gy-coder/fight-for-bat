## Week 2

> HTTP 相关问题

1. 对于一个 URL，protocol、hostname、port、pathname、search、hash 分别是什么?

   - protocol: 表示这个 URL 的协议 一般是 http / https
   - hostname: 表示这个 URL 的域名 例如: www.baidu.com
   - port: 表示这个 URL 的端口 例如: 8080,3000
   - pathname: 表示这个 URL 的路径部分 例如: http://example.com:1234/test/test.htm#part2 的 pathname 为/test/test.html
   - search: 用于获取 URL 的查询参数 例如: http://www.w3school.com.cn/tiy/t.asp?f=hdom_loc_search 的查询参数为 f=hdom_loc_search
   - hash: 用来获取从#开始的 URL（锚）例如:http://example.com:1234/test.htm#part2 的 Hash 为#part2

2. 同源策略中的同源指的是什么?

   同源策略的同源指的是 协议相同、域名相同、端口相同

   例如:

   - https://www.baidu.com 与 http: //www.baidu.com 不同源(协议不同)
   - http://www.google.com 与 http://www.baidu.com 不同源(域名不同)
   - http://www.google.com:1234 与 http://www.google.com:8080 不同源(端口不同)

3. OSI 有哪七层模型？TCP/IP 是哪四层模型。

   OSI 七层模型:

   1. 物理层
   2. 数据链路层
   3. 网络层
   4. 传输层
   5. 会话曾
   6. 表示层
   7. 应用层

   TCP/IP 四层模型:

   1. 数据链路层
   2. 网络层
   3. 传输层
   4. 应用层

4. 应用层协议有哪些？使用哪些端口?

   1. HTTP 协议 80
   2. HTTPS 协议 443
   3. Telent(远程登录) 协议 23
   4. STMP(简单邮件传输) 协议 25
   5. POP3 协议 110
   6. FTP(文件传输) 协议 21

5. 传输层协议有哪些?

   TCP 协议 & UDP 协议

6. TCP 协议怎么保证可靠的，UDP 为什么不可靠？二者的应用场景分别有哪些?

   1. TCP 协议怎么保证可靠的

      - 确认和重传
      - 数据校验
      - 合理的分片和排序
      - 拥塞控制
      - 流量控制

   2. UDP 为什么不可靠

      - 不保证消息交付
      - 不保证交付顺序
      - 没有拥塞控制
      - 不跟踪链接状态

   3. TCP 和 UDP 的使用场景

      - TCP: 整个数据要准确无误的传递给对方，这往往用于一些要求可靠的应用。比如: HTTP、HTTPS、FTP 等传输文件的协议，POP、SMTP 等邮件传输的协议。
      - UDP:当对网络通讯质量要求不高的时候，要求网络通讯速度能尽量的快，这时就可以使用 UDP，比如:视频传输、实时通信等。

7. 讲一讲三步握手和四步挥手是什么？哪个协议用的？挥手为什么要四步？

   1. 三次握手

      - C -> S [sync(x)]
      - S -> C [ack(x + 1);sync(y)]
      - C -> S [ack(y + 1)]

   2. 四次挥手

      - C -> S [fin(x)]
      - S -> C [ack(x+ 1)]
      - S -> C [fin(y)]
      - C -> S [ack(y + 1)]

   因为服务器收到发送 ack 请求后，只是回应了 fin(x)请求，但是自己可能还要发送一些东西，因此需要 4 步

8. 了解 GBN,SR 吗 ？
9. 滑动窗口是什么？
10. 怎么做拥塞控制？
11. 常见 HTTP 方法有哪些？使用场景分别是什么？

12. GET 与 POST 有什么区别？

    1. 幂等性

       - 由于 GET 是读，POST 是写 所以 get 是幂等的。post 不是

    2. 请求参数

       - 通常，GET 请求都放在 url 里，POST 请求都放在 body 里，
       - GET 比 POST 更不安全，因为 GET 参数直接暴露在 url 上，所以不能传递敏感信息
       - GET 请求在 url 中有长度限制，post 没有

    3. TCP 包

       GET 请求通常辉产生一个 TCP 包，POST 会产生多个 TCP 包

13. 状态码 200、301、302、304、403、404、500、503 分别代表什么？
14. 浏览器 HTTP 请求数目限制，怎么解决呢？

    使用并行连接

    1. 把 css 分成十几个小的 css 文件，然后浏览器就会一次创建十几个连接来处理这些小的 css 文件
    2. 但是不能无穷分割，因为浏览器对每一个域名的并行连接数量都有限制
    3. 因此，对于不同类型的文件，可以使用不同的域名来下载不同的文件

       - (css.cdn.com 专门用于下载 CSS 文件)
       - (js.cdn.com 专门用于下载 JS 文件)
       - (image.cdn.com 专门用于下载图片文件)

15. 什么是非持久连接（短链接），什么是持久连接（长链接），长链接有什么优缺点？如何断开长链接？
16. Keep-Alive: timeout=5, max=100 是什么意思？

    KeepAlive 是用于告知对方自己连接复用的配置，timeout 表示连接最多的等待时间为多少 s，max 表示最多支持 n 次请求

17. 文件传输时服务端怎么确定实体结束位置？
18. 聊一聊浏览器的缓存机制？（HTTP 是如何控制缓存的）

    |          | 缓存                                                              | 内容协商                                                   |
    | -------- | ----------------------------------------------------------------- | ---------------------------------------------------------- |
    | Http 1.1 | Cache-control: max-age: 3600;ETag: ABC                            | If-None-Match: ABC ? 304 + null : 200 + new File           |
    | Http 1.0 | Expire: 时间点 A(过期时间); Last-Modified: 时间点 B(上次修改时间) | If-Modified-Since: 时间点 B ？ 304 + null : 200 + new File |

19. Etag 和 If-None-Match 字段有什么作用？

    If-None-Match 是一个字段，他的意思是，如果没有匹配项，则缓存作废，重新下载并缓存，Etag 是他的值， 这个值是根据文件内容，由哈希函数计算而来

20. Last-Modified 和 Etag 哪个更好？

    Etag 比 lastModified 更加严谨，如果资源发生变化，Etag 就会发生变化，就会把最新的资源给客户端返回去，而 lastModified 不识别 s（秒）单位里的修改，所以如果资源在 s（秒）单位里发生了修改，那 lastModified 也不会发生改变，这样如果只用了 lastModified，客户端得到的资源就不是最新的；但是设定了 Etag 之后，每次客户端发出请求，服务端都会根据资源重新生成一个 Etag，对性能有影响

21. Expires 字段是什么意思？

    Expire 代表过期时间，这个时间受用户电脑时间的影响

22. Expires 和 Cache-Control 有什么区别？

    Expires 是给缓存设置一个过期时间，Cache-control 是设置的缓存可以存在的时间

23. Cache-Control: max-age=3600 是什么意思？

    表示缓存在 3600 秒内都是有效的

24. Cache-Control: no-cache 是什么意思？ Cache-Control: no-store 是什么意思？有什么区别？

    (不加 Cache-control 浏览器也会缓存，比如 301 会缓存)

    (禁用缓存: Cache-control: max-age: 0;must-revalidate) -> 现在不能用，过期了也不能用

    Cache-Control: no-cache 可以缓存，但是需要协商

    Cache-Control: no-store 不可以缓存

25. Cache-Control: private、public 分别是什么意思？

    - public: 公开的，所有的中间代理都可以缓存
    - private: 私有的，只有服务器和客户端可以缓存，中间代理都不可以

26. HTTP1.0，HTTP1.1，HTTP2.0 区别（HTTP1.1 版本新特性？HTTP2 版本新特性？）？

    1. Http1.0 是无状态(服务器不跟踪不记录请求过的状态)无连接(浏览器每次请求都需要建立 tcp 连接)的应用层协议
    2. Http1.1 在 Http1.0 的基础上，默认支持了 keep-alive(连接复用)，支持管道化
    3. Http2.0 不同于 Http1.1，Http1.1 是基于文本的，而 http2.0 是基于二进制流的，在 2.0 中，帧是最小单位，每一个帧都会标注自己属于那个流。

27. HTTP2 多路复用和 HTTP1.1 keep-alive 有什么关系？

    1. Http1.1 keep-alive 是一个 tcp 连接中，可以发送多个请求响应
    2. Http2.0 是把请求和相应拆成二进制流，然后接收方通过对二进制流的组装来完成传送
    3. 相比与 1.1 2.0 可以实现去顺序性

28. HTTPS 的原理是什么(HTTP 和 HTTPS 有什么区别)？

    - 原理： HTTPS 是在 HTTP 上建立 SSL 加密层，并对传输数据进行加密，是 HTTP 协议的安全版。
    - 区别:
      1. HTTPS 比 HTTP 更加安全，对搜索引擎更友好
      2. HTTPS 需要用到 SSL 证书，而 HTTP 不用;
      3. HTTPS 基于传输层，HTTP 基于应用层;

29. SSL/TLS 握手细节(HTTPS 加密过程、证书)?
30. HTTP2 如何加密的？
31. Web 安全中有哪些常见的攻击方式（对于安全了解多少？XSS？CSRF？）？

    1. XSS

       原理: XSS 攻击通常指的是通过利用网页开发时留下的漏洞，通过巧妙的方法注入恶意指令代码到网页，使用户加载并执行攻击者恶意制造的网页程序。攻击成功后，攻击者可能得到包括但不限于更高的权限（如执行一些操作）、私密网页内容、会话和 cookie 等各种内容。

       防护方式:

       - 富文本编辑器，过滤 script 标签
       - 对用户输入的\<script>进行翻译 \&lt;script\&gt;\&lt;/scrip\&gt;
       - 代码需要动态展示内容时用 innerText 取代 innerHTML ，使用 v-text 取代 v-html，尽量少做 HTML 字符串拼接，禁止使用 eval 执行 JS。
       - 服务端 Set Cookie 时 带上 HttpOnly 字段，阻止 JavaScript 获取 Cookie
       - 对于上传图片的场景，禁止使用用户填写的图片地址。特别是 Markdown 编辑器。

    2. CSRF:

       原理： 攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

       防护方式:

       - Referer 判断 : 根据 Referer 来判断请求来源。如果不在白名单，服务器忽略该请求。 该方法不可靠，Referer 伪造太容易了。
       - Semiste: Set-Cookie: sid=1; Samesite=Strict

32. Websockt 是什么？和 HTTP 有什么区别？
33. 服务端推送有哪些技术？
34. 服务器推送和 Websocket 区别? 模块热替换原理?
35. 常见的鉴权方式有哪些？
36. 谈谈 Session/Cookie 机制，如何实现会话跟踪？
37. JWT 是如何做鉴权的？
38. 谈谈 Auth2 鉴权原理?
