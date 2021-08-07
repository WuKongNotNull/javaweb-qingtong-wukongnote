# Servlet



## 入门案例

**需求：在浏览器上输出一句话**

web

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>

  <!--在web中注册servlet-->
    <servlet>
      <servlet-name>aa</servlet-name>
      <servlet-class>com.wukong.servlet.HelloWorld</servlet-class>
    </servlet>
    <servlet-mapping>
      <servlet-name>aa</servlet-name>
      <url-pattern>/wukong</url-pattern>
    </servlet-mapping>



</web-app>

```

HelloWorld

```java
package com.wukong.servlet;
/* author: 悟空非空也（B站/知乎/公众号） */
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;

import java.io.IOException;
import java.io.PrintWriter;

public class HelloWorld  extends HttpServlet{

    @Override
    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws IOException, ServletException
    {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<html>");
        out.println("<head>");
        out.println("<title>Hello World wukong!</title>");
        out.println("</head>");
        out.println("<body>");
        out.println("<h1>Hello World wukong!</h1>");
        out.println("</body>");
        out.println("</html>");
    }

}

```





## Servlet生命周期





## ServletContext

当web容器启动时，会创建一个servletContext对象，可以通过这个对象让servlet之间进行通信，代表当前的web应用.

### 数据共享

一个Servlet中通过servlerContext对象保存（set）的数据，可以在另一个servlet通过servlerContext对象get到

```
public class SetServletContext extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = this.getServletContext();
        String name =  "黄鹤";

        resp.setContentType("text/html");
        resp.setCharacterEncoding("utf-8");
        servletContext.setAttribute("username",name);

    }
}
```



```java
public class GetServletContext extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext servletContext = this.getServletContext();
        String username = (String) servletContext.getAttribute("username");

        resp.setContentType("text/html");
        resp.setCharacterEncoding("utf-8");
        resp.getWriter().print("username : " + username);
    }
}
```



### 获取配置参数

```java
String db_url = this.getServletContext().getInitParameter("db_url");

resp.getWriter().print(db_url);
```



### 请求转发

```java
this.getServletContext().getRequestDispatcher("/getParams").forward(request,response);
```



### 读取配置文件

```java
//文件路径为tomcat生成的webapp文件夹下的资源路径/WEB-INF/classes/db.properties，第一个/代表当前项目
InputStream resourceAsStream = this.getServletContext().getResourceAsStream("/WEB-INF/classes/db.properties");
Properties properties = new Properties();
properties.load(resourceAsStream);

String username = properties.getProperty("username");
String password = properties.getProperty("password");

resp.getWriter().print(username+"  :"+password);
```



## HttpServletResponse

web服务器接收了web client的请求，生成两个对象：HttpServletResponse和HttpServletRequest

1. response负责把数据传给浏览器
2. request负责读取client请求的信息



### 文件下载

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //获取文件路径
    String filePath = "D:\\悟空非空也.jpg";
    //获取文件名
    String fileName = filePath.substring(filePath.lastIndexOf("\\") + 1);
    //获取文件输入流
    BufferedInputStream bufferedInputStream = new BufferedInputStream(new FileInputStream(filePath));
    //获取向浏览器的输出流
    BufferedOutputStream bufferedOutputStream = new BufferedOutputStream(resp.getOutputStream());
    //让浏览器知道我们要下载文件，需要设置头，让浏览器支持。中文文件名URLEncoder.encode编码，否则有可能乱码
    resp.addHeader("Content-Disposition", "attachment;filename=" + URLEncoder.encode(fileName,"utf-8"));
    //向浏览器输出数据
    byte[] buffer = new byte[1024];
    int len = 0;
    while ((len = bufferedInputStream.read(buffer))>0){
        bufferedOutputStream.write(buffer,0,len);
    }
    //关闭流
    bufferedInputStream.close();
    bufferedOutputStream.close();
}
```



### 实现验证码

```java
 protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        //写一张图片
        BufferedImage bufferedImage = new BufferedImage(80,20,BufferedImage.TYPE_INT_RGB);//需要一张画板
        Graphics graphics = bufferedImage.getGraphics();//需要一只画笔
        //设置背景
        graphics.setColor(Color.white);//设置颜色
        graphics.fillRect(0,0,80,20);
        //设置内容
        graphics.setColor(Color.blue);
        graphics.setFont(new Font(null,Font.ITALIC,20));
        graphics.drawString(Objects.requireNonNull(makeNum()),0,20);

        //告诉浏览器，这个请求用图片的方式打开
        resp.setContentType("image/jpeg");
        //网站存在缓存，不让浏览器缓存
        resp.setDateHeader("expires",-1);
        resp.setHeader("Cache-Control","no-cache");
        resp.setHeader("Pragma","no-cache");
        //给这个响应设置三秒刷新一次，且把缓存机制关掉
        resp.setHeader("refresh","3");
        //将这张图片写入浏览器
        ImageIO.write(bufferedImage,"jpg",resp.getOutputStream());
}
    private String makeNum() {
        //生成随机数 1Q23
        java.util.Random random = new java.util.Random();
        int a = random.nextInt(9000) + 1000;//生成从0-指定数（不包括指定数）的整数
       return Integer.toString(a);

    }
```



### 响应重定向

```java
@WebServlet("/getRedirectServlet")
public class getRedirectServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("enter doGet Method...");
        //一定要是绝对路径
        resp.sendRedirect(req.getContextPath()+"/getVerificationCodeServlet");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
       this.doGet(req, resp);
    }
}
```







## HttpServletRequest

### 请求转发

```java
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");

        //获取表单数据
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        String[] hobbies = req.getParameterValues("hobbies");
        System.out.println(username);
        System.out.println(password);
        System.out.println(Arrays.toString(hobbies));

        //请求转发
        req.getRequestDispatcher("/success.jsp").forward(req,resp);
    }

```

