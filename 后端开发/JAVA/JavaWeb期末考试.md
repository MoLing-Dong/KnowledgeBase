# JavaWeb 期末复习

## 简答

### HTTP 请求的 GET 与 POST 方式的区别

#### 标准答案

- GET 在浏览器回退时是无害的，而 POST 会再次提交请求。
- GET 产生的 URL 地址可以被 Bookmark，而 POST 不可以。
- GET 请求会被浏览器主动 cache，而 POST 不会，除非手动设置。
- GET 请求只能进行 url 编码，而 POST 支持多种编码方式。
- GET 请求参数会被完整保留在浏览器历史记录里，而 POST 中的参数不会被保留。
- GET 请求在 URL 中传送的参数是有长度限制的，而 POST 么有。
- 对参数的数据类型，GET 只接受 ASCII 字符，而 POST 没有限制。
- GET 比 POST 更不安全，因为参数直接暴露在 URL 上，所以不能用来传递敏感信息。❔
- GET 参数通过 URL 传递，POST 放在 Request body 中。

#### 补充

HTTP 的底层是 TCP/IP。所以 GET 和 POST 的底层也是 TCP/IP，也就是说，GET/POST 都是 TCP 链接。GET 和 POST 能做的事情是一样一样的。你要给 GET 加上 request body，给 POST 带上 url 参数，技术上是完全行的通的。  
至于安全方面，网上的一些答案说 GET 比 POST 不安全，这个说法是不准确的。GET 和 POST 的安全性是一样的。但是 GET 请求的数据在 URL 中，这个是肉眼可见的，所以更容易被人看到，然后泄露。所以说不安全。但是这个不安全不是技术层面的，是使用层面的。  
具体为了传输过程中的安全问题是推荐使用 HTTPS 的，安全是一个巨大的方面。

推荐阅读：[GET 和 POST 到底有什么区别？](https://www.zhihu.com/question/28586791)
文章中有更为详细的讲解

### JSP 中的作用域的分类

- `page` 作用域：JSP 页面中定义的变量，只能在当前页面中使用。
- `request` 作用域：一次请求中有效，转发有效。
- `session` 作用域：一次会话中有效，多次请求有效。
- `application` 作用域：整个应用中有效，服务器启动到服务器关闭。

### JDBC 的常用 API

- `DriverManager`：用于加载驱动和建立数据库连接。
- `Connection`：表示与数据库的连接。
- `Statement` 或 `PreparedStatement`：用于执行 SQL 语句。
- `ResultSet`：用于处理查询结果集。
- `CallableStatement`：用于执行存储过程。

### Servlet 的生命周期

- 初始化：通过`init()`方法初始化 Servlet。
- 请求处理：通过`service()`方法处理 HTTP 请求。
- 销毁：通过`destroy()`方法释放 Servlet 所占资源。

### JSP 动作标签及其作用

- `<jsp:include>`：包含静态或动态文件。
- `<jsp:forward>`：转发请求到另一个资源。
- `<jsp:param>`：传递参数到包含的页面或转发的目标。
- `<jsp:useBean>`：实例化和使用 JavaBeans。
- `<jsp:setProperty>`：设置 JavaBean 的属性值。
- `<jsp:getProperty>`：获取 JavaBean 的属性值。

### Servlet 在 web.xml 中的配置代码

```xml
<servlet>
    <servlet-name>StudentController</servlet-name>
    <servlet-class>edu.huayu.StudentController</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>StudentController</servlet-name>
    <url-pattern>/student</url-pattern>
</servlet-mapping>
```

### JDBC 操作数据库的步骤

- 加载数据库驱动。
- 建立数据库连接。
- 创建 Statement 或 PreparedStatement。
- 执行 SQL 语句。
- 处理结果集。
- 关闭资源。

### Cookie 与 Session 的区别

- Cookie 存储在客户端，Session 存储在服务器端。
- Cookie 容量有限，Session 理论上没有大小限制。
- Cookie 安全性较低，容易被拦截，Session 相对安全。
- Cookie 依赖于客户端支持，Session 不依赖。

### HttpServletResponse 常用的响应状态码及其意义

- `200 OK`：请求成功。
- `400 Bad Request`：请求语法错误。
- `401 Unauthorized`：未授权。
- `404 Not Found`：请求资源未找到。
- `500 Internal Server Error`：服务器内部错误。

### JSP 的隐式对象及其作用

- `out`：用于输出数据到客户端。
- `request`：封装客户端请求信息。
- `response`：封装服务器响应信息。
- `session`：保存用户会话信息。
- `application`：全局共享数据的上下文。
- `page`：当前页面的引用。
- `pageContext`：提供对当前页面上下文的访问。
- `config`：Servlet 的配置信息。
- `exception`：异常信息（仅在错误页面可用）。注意，这里列出的是常见的五个，但实际有九个隐式对象。
