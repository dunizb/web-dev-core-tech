# 附录1：GET和POST的区别

GET在浏览器**回退时**是无害的，而POST会再次提交请求。

GET产生的URL地址可以被**Bookmark**（书签），而POST不可以。

GET请求会被浏览器主动**cache**（缓存），而POST不会，除非手动设置。

GET请求只能进行url**编码**，而POST支持多种编码方式。

GET**请求参数**会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。

GET请求在URL中传送的**参数是有长度限制**的，而POST么有。

对**参数的数据类型**，GET只接受ASCII字符，而POST没有限制。

GET比POST更不**安全**，因为参数直接暴露在URL上，所以不能用来传递敏感信息。

GET参数通过URL**传递**，POST放在Request body中。

