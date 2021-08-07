# Filter 过滤器



自定义CharacterEnCodingFilter类，实现javax.servlet.Filter接口

```java
    //服务器启动时就执行对filter进行初始化，随时等待过滤特有的链接
    public void init(FilterConfig filterConfig) throws ServletException {
        System.out.println("CharacterEnCodingFilter已经启动");
    }

    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        servletRequest.setCharacterEncoding("utf-8");
        servletResponse.setCharacterEncoding("utf-8");
        servletResponse.setContentType("text/html;charset=utf-8");
        //让请求继续走下去，可能不止一个filter
        filterChain.doFilter(servletRequest,servletResponse);
    }
    //服务器关闭时执行
    public void destroy() {
        System.out.println("CharacterEnCodingFilter已经销毁");
    }
```



在web.xml文件中配置filter

```xml
    <filter>
        <filter-name>charsetFilter</filter-name>
        <filter-class>com.iandf.filter.CharacterEnCodingFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>charsetFilter</filter-name>
        <!--只要是/servlet/....的请求就会经过这个过滤器-->
        <url-pattern>/servlet/*</url-pattern>
    </filter-mapping>
```



