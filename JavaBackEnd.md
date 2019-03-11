# Java后端知识点 #

### 1.Servlet

#### 总结参考至[极客学院--Servlet教程](http://wiki.jikexueyuan.com/project/servlet/)

> Java servlet 是运行在 Web 或应用服务器上的程序，作为在来自 Web 浏览器或其他 HTTP 客户机的请求和在HTTP 服务器上的数据库或应用程序的中间层。使用 Servlet，你可以通过 web 页面表单来收集用户的输入，显示从数据库或其他来源的记录，动态地创建 web页面。

##### Java servlet 通常服务于使用 Common Gateway Interface (CGI) 实现的同样的目的程序。但与 CGI 相比，Servlet 具有几个优点。

- 性能更好。
- Servlet 在 Web 服务器的地址空间内执行。没有必要创建一个单独的进程来处理每个客户端请求。
- 由于 Servlet 是用 Java 编写的，所以它是跨平台的。
- 在服务器上的 Java 安全性管理器执行的一些限制来保护服务器上的资源。所以 servlet 是可信的。
- Java 类库的完整的功能是对 Servlet 来说是可用的。它可以与小应用程序、数据库或其他软件通过通信接口和你已经了解的RMI机制进行通信。

##### Servlet架构

![](https://i.imgur.com/nCYWqig.png)

##### Servlet的主要任务

- 读取由客户端（浏览器）发送的显式数据。这包括网页上的 HTML 表单，或者也可以是来自 applet 或自定义的 HTTP 客户端程序的表单。
- 读取由客户端（浏览器）发送的隐式 HTTP 请求数据。这包括 cookies、媒体类型和浏览器能理解的压缩格式等等。
- 处理数据并生成结果。这个过程可能需要访问数据库，执行 RMI 或 CORBA 调用，调用 Web 服务，或者直接计算响应。
- 发送显式数据（即文档）到客户端（浏览器）。该文档可以以多种多样的格式被发送，包括文本文件（HTML 或 XML）、二进制文件（GIF 图像）、Excel 等。
- 发送隐式的 HTTP 响应到客户端（浏览器）。这包括告诉浏览器或其他客户端被返回的文档类型（例如 HTML），设置 cookies 和缓存参数，以及其他类似的任务。

##### Servlet生命周期

- 通过调用 init () 方法 servlet 被初始化。
- Servlet 调用 service() 方法来处理客户端的请求。
- 通过调用 destroy() 方法 servlet 终止。
- 最后，servlet 是由 JVM 的垃圾回收器进行垃圾回收的。

##### 生命周期方法

###### init()只调用一次。它在第一次创建 servlet 时被调用，在后续每次用户请求时不再调用。因此，它用于一次性初始化。通常情况下，当用户第一次调用对应于该 servlet 的 URL 时，servlet 被创建，但是当服务器第一次启动时，你也可以指定 servlet 被加载。当用户调用 servlet 时，每个 servlet 的一个实例就会被创建，并且每一个用户请求都会产生一个新的线程，该线程在适当的时候移交给 doGet 或 doPost 方法。init() 方法简单地创建或加载一些数据，这些数据将被用于 servlet 的整个生命周期。

	public void init() throws ServletException {
	// Initialization code...
	}

###### service() 方法是执行实际任务的主要方法。Servlet 容器（即 web 服务器）调用 service() 方法来处理来自客户端（浏览器）的请求，并将格式化的响应写回到客户端。每次服务器接收到一个 servlet 请求时，服务器会产生一个新的线程并调用服务。service() 方法检查 HTTP 请求类型（GET、POST、PUT、DELETE 等），并在适当的时候调用 doGet、doPost、doPut、doDelete 等方法。

	public void service(ServletRequest request,
	ServletResponse response)
	throws ServletException, IOException{
	}

###### destroy() 方法只在 servlet 生命周期结束时被调用一次。destroy() 方法可以让你的 servlet 关闭数据库连接、停止后台线程、将 cookie 列表或点击计数器写入磁盘，并执行其他类似的清理活动。

	public void destroy() {
	// Finalization code...
	}

##### 结构框图

![](https://i.imgur.com/IfRuxGM.png)

##### Servlet部署：Servlet的部署需要将已编译成字节码的Servlet放置在指定的目录中（ /webapps/ROOT/WEB-INF/classes）并且还需要配置好相应的XML文件。

##### HTTP请求头信息

头信息| 描述
:-|:-
Accept| 这个头信息指定浏览器或其他客户端可以处理的 MIME 类型。值 image/png或 g image/jpeg 是最常见的两种可能值。
Accept-Charset| 这个头信息指定浏览器可以用来显示信息的字符集。例如 ISO-8859-1。
Accept-Encoding| 这个头信息指定浏览器知道如何处理的编码类型。值 p gzip 或 s compress 是最常见的两种可能值。
Accept-Language| 这个头信息指定客户端的首选语言，在这种情况下，Servlet 会产生多种语言的结果。例如，en、en-us、ru 等。
Authorization| 这个头信息用于客户端在访问受密码保护的网页时识别自己的身份。
Connection| 这个头信息指示客户端是否可以处理持久 HTTP 连接。持久连接允许客户端或其他浏览器通过单个请求来检索多个文件。值 Keep-Alive 意味着使用了持续连接。
Content-Length| 这个头信息只适用于 POST 请求，并给出 POST 数据的大小（以字节为单位）。
Cookie| 这个头信息把之前发送到浏览器的 cookies 返回到服务器。
Host| 这个头信息指定原始的 URL 中的主机和端口。
If-Modified-Since| 这个头信息表示只有当页面在指定的日期后已更改时，客户端想要的页面。如果没有新的结果可以使用，服务器会发送一个 304 代码，表示 Not Modified头信息。
If-Unmodified-Since| 这个头信息是 If-Modified-Since 的对立面，它指定只有当文档早于指定日期时，操作才会成功。
Referer| 这个头信息指示所指向的 Web 页的 URL。例如，如果您在网页 1，点击一个链接到网页 2，当浏览器请求网页 2 时，网页 1 的 URL 就会包含在 Referer头信息中。
User-Agent| 这个头信息识别发出请求的浏览器或其他客户端，并可以向不同类型的浏览器返回不同的内容。

##### HTTP响应头信息

头信息| 描述
:-|:-
Allow| 这个头信息指定服务器支持的请求方法（GET、POST 等）。
Cache-Control| 这个头信息指定响应文档在何种情况下可以安全地缓存。可能的值有： public, private 或 no-cache 等。Public 意味着文档是可缓存，Private 意味着文档是单个用户私用文档，且只能存储在私有（非共享）缓存中，no-cache 意味着文档不应被缓存。
Connection| 这个头信息指示浏览器是否使用持久HTTP 连接。值 e close 指示浏览器不使用持久 HTTP 连接，值 e keep-alive 意味着使用持久连接。
Content-Disposition| 这个头信息可以让您请求浏览器要求用户以给定名称的文件把响应保存到磁盘。
Content-Encoding| 在传输过程中，这个头信息指定页面的编码方式。
Content-Language| 这个头信息表示文档编写所使用的语言。例如，en、en-us、ru 等。
Content-Length| 这个头信息指示响应中的字节数。只有当浏览器使用持久（keep-alive）HTTP 连接时才需要这些信息。
Content-Type| 这个头信息提供了响应文档的 MIME（Multipurpose Internet Mail Extension）类型。
Expires| 这个头信息指定内容过期的时间，在这之后内容不再被缓存。
Last-Modified| 这个头信息指示文档的最后修改时间。然后，客户端可以缓存文件，并在以后的请求中通过 e If-Modified-Since 请求头信息提供一个日期。
Location| 这个头信息应被包含在所有的带有状态码的响应中。在 300 s 内，这会通知浏览器文档的地址。浏览器会自动重新连接到这个位置，并获取新的文档。
Refresh| 这个头信息指定浏览器应该如何尽快请求更新的页面。您可以指定页面刷新的秒数。
Retry-After| 这个头信息可以与 503（服务不可用）响应配合使用，这会告诉客户端多久就可以重复它的请求。
Set-Cookie| 这个头信息指定一个与页面关联的cookie。

##### Servlet过滤器

###### 过滤器在部署描述符文件 web.xml 中被部署，然后被映射到 servlet 名称或你的应用程序的部署描述符中的 URL 模式。
###### 当 web 容器启动 web 应用程序时，它会为每个在部署描述符中已声明的过滤器创建一个实例。过滤器按照它们在部署描述符中声明的顺序执行。

###### 作用

- 在访问后端资源之前，拦截这些来自客户端的请求。
- 在发送回客户端之前，处理这些来自服务器端的响应。

###### 方法

- public void doFilter (ServletRequest, ServletResponse, FilterChain)该方法在每次一个请求/响应对因客户端在链的末端请求资源而通过链传递时由容器调用。
- public void init(FilterConfig filterConfig)该方法由 Web 容器调用，指示一个过滤器被放入服务。
- public void destroy()该方法由 Web 容器调用，指示一个过滤器从服务被去除。

##### 其他

###### servlets 是 Java 类，服务于 HTTP 请求并实现了 t javax.servlet.Servlet 接口。Web 应用程序开发人员通常编写扩展 javax.servlet.http.HttpServlet 的 servlets，它是一个实现了 Servlet 接口的抽象类并且是为处理 HTTP 请求专门设计的。

###### Java EE平台构建于Java SE平台之上，Java EE平台提供一组API和运行环境来开发和运行大规模的，多层的，可扩展的，可靠的和安全的网络应用程序。也就是说Java EE是一个标准而已，而这些标准的实现是Web容器提供的，比如Servlet-API，在Tomcat这个Web容器下的lib文件夹就集成了servlet-api.jar的包。官网中下载下来的是glassfish的Web容器，里面是按照Java EE的标准API开发的，能运行Java EE的Web程序，同时这个容器还提供了对EJB的支持。[此段参考来源](https://www.cnblogs.com/EasonJim/p/7104641.html)

### Cookie，Session，Token

###### Cookie

> 会话Cookie与持久Cookie，前者是临时的（保存在计算机的内存中），用户的浏览器被关闭时会被随之被清除；后者的生存时间会更长（保存于硬盘中），不会被清除。它们之间的差别就是过期时间设置的不同。Cookie过期失效后会被删除，不再存储了。Cookie有个数和大小（4KB）的限制,且不能违背同源策略。
[Cookie的一些参考链接](https://wangdoc.com/javascript/bom/cookie.html)

###### Session

> Session存储在服务器中，大体上讲，它的生存期从用户登入网站后开始，直到用户关闭了浏览器结束。实际上，某server端程序调用HttpServletRequest.getSession(true)这样的语句时session才会被创建，而后服务器会把session id信息发还给浏览器。在生存期期间，浏览器会保存session id，浏览器关闭后，这一信息也就随之丢失，也就是说服务器内存储的session就无法再被访问了，当服务器发现某一session长时间未被访问时会自动清除，当然，服务器也能直接删除session（调用HttpSession.invalidate()），比如用户注销账号时。Session不适用于分布式服务集群。

###### Token，这部分可参考[阮一峰老师的回答](http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)

