# session

## 基本概念



## demo



由request对象获取session,如果没有则创建session，在session中保存数据，服务器把sessionID以cookie的形式发送给客户端

```java
HttpSession session = req.getSession();
String sessionId = session.getId();

if (session.isNew()){
    resp.getWriter().print("session创建完成，sessionID为 :"+sessionId);
}else {
    resp.getWriter().print("session已经在服务器中存在，sessionID为 :"+sessionId);
}
  //给session设置数据
session.setAttribute("person",new Person("wukong",18));

```



服务器把sessionID以cookie的形式发送给客户端，相当于这段代码

```java
 //每个用户对应一个session,服务器会把sessionID保存在JSESSIONID的Cookie中发送给客户端,存活时间是这次会话(expiry = -1)，浏览器退出则删除
 Cookie cookie = new Cookie("JSESSIONID",sessionId);
 cookie.setMaxAge(-1);
 resp.addCookie(cookie);

```



session的存活时间可以在web.xml中进行配置

```xml
  <session-config>
    <!--以分钟为单位-->
    <session-timeout>1</session-timeout>
  </session-config>
```



下次请求时，客户端会把该cookie带上，服务器可以通过该cookie获取session，再通过session获取数据,如果将该cookie在客户端移除，服务器端将拿不到这个session。所以服务端的session的实现对客户端的cookie有依赖关系的

```java
HttpSession session = req.getSession();
Person person = (Person) session.getAttribute("person");
resp.getWriter().print(person.toString());
```



删除session

```java
HttpSession session = req.getSession();
session.invalidate();
```





