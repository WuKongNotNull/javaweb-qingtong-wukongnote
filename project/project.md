## 1-创建项目，并配置tomcat服务器

## 2-导入jar包

jsp-api.jar

servlet-api.jar

## 3-使用jsp实现新闻页面输出-out将java代码中的信息输出

```jsp

```



## 4-需求：在jsp中使用变量

```jsp
                       <div class="source-bar">
						<%String author = "小K童鞋";%>
						发布者：<%=author%> 分类：新闻信息 更新时间：
                        <%
                        	Date date = new Date();
                        	String time = new SimpleDateFormat("yyyy-MM-dd").format(date);
                         %>
                         <%=time %>
                        
                        </div>
```

## 5-需求：在jsp中声明全局变量和局部变量

```jsp
  
  <body>
  <%--局部变量和全局变量 --%>
  <%int i = 10; %>    
  <%! 
  	int j = 10; 
  	public int add(){
  		return 5+9;
  	}	
  %>    
  i++; <%= i++ %><br/>
  j++; <%= j++ %><br/>
  <%= add() %>
  </body>
```

## 6-jsp实现数据的传递和保存

## 7-编写新闻管理系统-编写数据库脚本

```mysql
CREATE DATABASE news;

USE `news`;


CREATE TABLE `news_category` (
  `id` BIGINT(10) NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(50) COLLATE utf8_unicode_ci DEFAULT NULL,
  `createDate` DATETIME DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`)
) COMMENT='新闻分类表';

INSERT  INTO `news_category`(`id`,`name`,`createDate`) VALUES (1,'国内','2016-09-16 14:41:24'),(2,'国际','2016-09-16 14:42:58'),(3,'娱乐','2016-09-16 14:42:58'),(4,'军事','2016-09-16 14:42:58'),(5,'财经','2016-09-16 14:42:58'),(6,'天气','2016-09-16 14:42:58');



DROP TABLE IF EXISTS `news_comment`;

CREATE TABLE `news_comment` (
  `id` BIGINT(10) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `newsId` BIGINT(10) DEFAULT NULL COMMENT '评论新闻id',
  `content` VARCHAR(2000) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '评论内容',
  `author` VARCHAR(50) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '评论者',
  `ip` VARCHAR(20) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '评论ip',
  `createDate` DATETIME DEFAULT NULL COMMENT '发表时间',
  PRIMARY KEY (`id`)
) COMMENT='新闻评论表';



DROP TABLE IF EXISTS `news_detail`;

CREATE TABLE `news_detail` (
  `id` BIGINT(10) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `categoryId` BIGINT(10) DEFAULT NULL COMMENT '新闻类别id',
  `title` VARCHAR(100) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '新闻标题',
  `summary` VARCHAR(200) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '新闻摘要',
  `content` TEXT COLLATE utf8_unicode_ci COMMENT '新闻内容',
  `picPath` VARCHAR(50) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '新闻图片路径',
  `author` VARCHAR(50) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '发表者',
  `createDate` DATETIME DEFAULT NULL COMMENT '创建时间',
  `modifyDate` DATETIME DEFAULT NULL COMMENT '修改时间',
  PRIMARY KEY (`id`)
) COMMENT='新闻明细表';



INSERT  INTO `news_detail`(`id`,`categoryId`,`title`,`summary`,`content`,`picPath`,`author`,`createDate`,`modifyDate`)
 VALUES (1,1,'Java Web开课啦','Java Web课程重磅开课，学员福利','璇女神主讲，课工场倾力出品，Java Web课程开课了，等靠谱的你来报名！','','admin','2016-05-16 14:43:53','2015-05-16 14:43:53'),(2,1,' 520课工场Java狂欢节','课工场准备了一大波福利：Java大赛、折扣课程，免费线下福利……你准备好了吗？','在这个五月，课工场Java学员突破100万人。为感谢所有学员的支持，我们特将5月20日定为【课工场Java狂欢节】。课工场准备了一大波福利：Java大赛、折扣课程，免费线下福利……你准备好了吗？',NULL,'admin','2016-05-16 14:43:53','2016-05-16 14:43:53');



DROP TABLE IF EXISTS `news_user`;

CREATE TABLE `news_user` (
  `id` BIGINT(10) NOT NULL AUTO_INCREMENT COMMENT '用户ID',
  `username` VARCHAR(50) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '用户名',
  `password` VARCHAR(50) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '密码',
  `email` VARCHAR(50) COLLATE utf8_unicode_ci DEFAULT NULL COMMENT 'email',
  `userType` INT(5) DEFAULT NULL COMMENT '用户类型  0：管理员 1：普通用户',
  PRIMARY KEY (`id`)
)  COMMENT='用户表';



INSERT  INTO `news_user`(`id`,`username`,`password`,`email`,`userType`) 
VALUES (1,'admin','admin','admin@kgc.cn',0),
(2,'user','user','user@kgc.cn',1),
(3,'test','test','test@kgc.cn',1);
```

## 8-连接数据库，通过配置文件properties文件

```
jdbc.driver_class=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf-8
username1=root
password1=root

```

## 9-编写工具类-ConfigManager.java

```java
package com.siliang.util;

import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

/**
 * @Author: 悟空非空也（公众号/CSDN）
 */

//对配置文件进行操作的类-单例模式（饱汉模式）
public class ConfigManager {
    private static ConfigManager configManager;
    private  static   Properties properties;

    //构造器，初始化的时候，构造器加载，仅仅加载一次
    private ConfigManager(){
        String configFile= "database.properties";
        //将配置文件转换成输入流
        InputStream inputStream = ConfigManager.class.getClassLoader().getResourceAsStream(configFile);
        properties = new Properties();
        //对流进行加载
        try {
            properties.load(inputStream);
            inputStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    //通过获得该类的对象（新建该类的对象）
    //configManager对象只有仅有一个
    public static ConfigManager getInstance(){
        if(configManager==null){
            configManager=new ConfigManager();
        }
        return configManager;
    }

    //通过配置文件中的属性名获得属性值
    public String getString(String key){

        return properties.getProperty(key);

    }


}

```

## 10-编写工具类-MysqlUtil.java

```java
package com.siliang.util;

import javax.naming.Context;
import javax.naming.InitialContext;
import javax.naming.NamingException;
import javax.sql.DataSource;
import java.sql.*;

/**
 * @Author: 悟空非空也（公众号/CSDN）
 */
//工具类：连接某个据库中的所有表
public class MySqlUtil {

    //连接数据库
    //属性
    private Connection connection=null;
    private ResultSet resultSet=null;
    private PreparedStatement preparedStatement=null;

    public boolean getConnection(){
        try {
            //获取上下文对象
            Context ctx = new InitialContext();
            DataSource ds = (DataSource)ctx.lookup("java:comp/env/jdbc/news");
            connection = ds.getConnection();
        } catch (NamingException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        } catch (SQLException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        return true;
    }


    public boolean getConnection1(){

        //通过配置文件实现代码和配置信息的分离
        String driver = ConfigManager.getInstance().getString("jdbc.driver_class");
        String url = ConfigManager.getInstance().getString("jdbc.url");
        String username = ConfigManager.getInstance().getString("username1");
        String password = ConfigManager.getInstance().getString("password1");

        try {
            Class.forName(driver);
            connection = DriverManager.getConnection(url, username, password);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
            return false;
        } catch (Exception e) {
            e.printStackTrace();
            return  false;
        }
        return true;

    }

    //连接数据库:缺点 配置参数（属性）和代码混在一起--》分离 分工 归类思想
/*    public boolean getConnection(){
        String driver="com.mysql.jdbc.Driver";
        String url="jdbc:mysql://localhost:3306/test?characterEncoding=utf-8";
        String user="root";
        String pwd="root";

        try {
            Class.forName(driver);
            connection = DriverManager.getConnection(url, user, pwd);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
            return false;
        } catch (SQLException e) {
            e.printStackTrace();
            return  false;
        }
        return true;

    }*/

    //关闭资源
    public void closeResource(){
        try{
            if(resultSet!=null){
                resultSet.close();
            }
            if(preparedStatement!=null){
                preparedStatement.close();
            }
            if(connection!=null){
                connection.close();
            }
        }catch (Exception e){
            e.printStackTrace();
        }
    }

    //通用：增删改
    public int executeUpdateSql(String sql,Object[] params){
        int count=0;
        if(this.getConnection()){
            try {
                preparedStatement = connection.prepareStatement(sql);
                //给占位符赋值
                //INSERT INTO `user`(id,username,`password`)VALUES(?,?,?)"  params={100,"zs","zs"} 1-->params[0] ,2-->params[1],3-->params[2]
                // params=null
                // 数组为null 和数组的长度为-1  是相同的概念吗？ params=null , params 开辟的空间 params={}
                if(params!=null ){
                    for(int i=0;i<params.length;i++){
                        preparedStatement.setObject(i+1,params[i]);
                    }
                }

                //执行sql语句
                count = preparedStatement.executeUpdate();

            } catch (SQLException e) {
                e.printStackTrace();
            }finally {
                this.closeResource();
            }

        }else{
            System.out.println("数据库连接失败");
        }
        return count;
    }

    //通用的查询
    public ResultSet executeQuerySql(String sql,Object[] params){
        if (this.getConnection()){
            try {
                preparedStatement = connection.prepareStatement(sql);
                if(params!=null){
                    for(int i=0;i<params.length;i++){
                        //给每一个占位符赋值
                        preparedStatement.setObject(i+1,params[i]);
                    }
                }
                resultSet = preparedStatement.executeQuery();

            } catch (SQLException e) {
                e.printStackTrace();
            }
        }else{
            System.out.println("连接失败");
        }
        return resultSet;
    }

}

```

## 11-编写登陆功能

```jsp
                <form action="<%=request.getContextPath()%>/LoginServlet" method="post">
                    <label>用户名</label><input type="text" name="uname" value="" />
                    <label>密码</label><input type="text" name="upassword" />
                    <button> 登陆</button>
                </form>

                <span>
                    <% String username = (String) session.getAttribute("usernameKey");
                    if(username!=null && !username.equals("")){
                       out.println("登陆成功，用户名："+username);
                    }
                    %>
                </span>
```

```java
package com.news.servlet;

import com.news.service.UserService;
import com.news.service.UserServiceImpl;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

/**
 * @Author: 悟空非空也（公众号/CSDN）
 */
@WebServlet(name = "LoginServlet",urlPatterns = {"/LoginServlet"})
public class LoginServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
         this.doGet(request,response);
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
         //在这里统一处理请求
        request.setCharacterEncoding("utf-8");
        String username = request.getParameter("uname");
        String password = request.getParameter("upassword");

        UserService userService = new UserServiceImpl();
        boolean b = userService.existUser(username, password);
         //把结果返回给jsp页面，然后浏览器渲染   false 不存在 true 存在
        if(b){//true 存在
            HttpSession session = request.getSession();
            session.setAttribute("usernameKey",username);
            //刷新index.jsp页面
            request.getRequestDispatcher("/index.jsp").forward(request,response);

        }else{//不存在
            request.getRequestDispatcher("/index.jsp").forward(request,response);
        }

    }
}

```



## 12-注销功能

```jsp
 <a href="<%=request.getContextPath() %>/LoginOutServlet">注销</a>
```

```java
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            //处理请求
        HttpSession session = request.getSession();
        session.invalidate();
        //再次发送请求,实现跳转(一次请求)
        request.getRequestDispatcher("/index.jsp").forward(request,response);

    }
```



## 13-注册功能

```jsp
<form name="registerFrm" id="registerFrm" action="<%=request.getContextPath()%>/RegisterServlet" method="get">
<!-- <form name="registerFrm" id="registerFrm" action="doUserCreate.jsp" method="post"> -->
	<table class="tb" border="0" cellspacing="5" cellpadding="0" align="center">
		<tr><td align="center" colspan="2" style="text-align:center;" class="text_tabledetail2">用户注册</td></tr>
		<tr>
			<td class="text_tabledetail2">用户名</td>
			<td><input type="text" name="username" value=""/></td>
		</tr>
		<tr>
			<td class="text_tabledetail2">密码</td>
			<td><input type="password" name="password" value=""/></td>
		</tr>
		<tr>
			<td class="text_tabledetail2">确认密码</td>
			<td><input type="password" name="con_password" value=""/></td>
		</tr>
		<tr>
			<td class="text_tabledetail2">email</td>
			<td><input type="text" name="email" value=""/></td>
		</tr>
<%--		<tr>
			<td class="text_tabledetail2">爱好</td>
			<td>
				<input type="checkbox" name="hobby" value="swim" />游泳<br/>
				<input type="checkbox" name="hobby" value="read" />阅读<br/>
				<input type="checkbox" name="hobby" value="climb" />爬山<br/>
				<input type="checkbox" name="hobby" value="travel" />旅游<br/>
			</td>
		</tr>--%>
		<tr>
			<td style="text-align:center;" colspan="2">				
				<button type="submit" class="page-btn" name="save">注册</button>
				<button type="button" class="page-btn" name="return" onclick="javascript:location.href='<%=request.getContextPath() %>/index.jsp'">返回</button>
			</td>
		</tr>
	</table>
</form>

```

```java
 protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //设置编码格式
        request.setCharacterEncoding("utf-8");
        response.setCharacterEncoding("utf-8");
        //处理请求
        String username = request.getParameter("username");
        String password = request.getParameter("password");
        String email = request.getParameter("email");
        //进行非空判断
        if(!username.equals("")&&username!=null
            &&!password.equals("")&&null!=password&&!email.equals("")&&email!=null){
            //将数据保存到数据库中
            UserService userService = new UserServiceImpl();
            User user = new User();
            user.setUsername(username);
            user.setPassword(password);
            user.setEmail(email);
            boolean b = userService.registerUser(user);
            if(b){//注册成功,发送一个请求，实现页面的跳转
                request.getRequestDispatcher("/pages/registerSuccess.jsp").forward(request,response);
            }else{//注册失败，发送一个请求，实现页面的跳转
                request.getRequestDispatcher("/pages/registerFailure.jsp").forward(request,response);
            }
        }else{
            System.out.println("传输过来的请求数据为空");
        }
    }
```

## 14-查询新闻列表

index.jsp

```jsp
                      <ul class="news-list-ul clearfix">
                          <%
                              NewsService newsService=new NewsServiceImpl();
                              List<News> newsList = newsService.getNewsList();
                              for(News news:newsList){%>
                                  <li><span><%=news.getCreateDate() %></span><a href="<%=request.getContextPath()%>/pages/newsDetail.jsp?newsId=<%=news.getId()%>"><%=news.getTitle()%></a></li>
                           <%   }

                          %>



                      </ul>
```

NewsService.java

```java
/**
 * @Author: 悟空非空也（公众号/CSDN）
 */
public interface NewsService {

    //查询所有的新闻列表
    public List<News> getNewsList();

}

```

## 15-分页查询用户列表

index.jsp

```jsp
<ul class="news-list-ul clearfix">
                          <%
                              NewsService newsService=new NewsServiceImpl();
                            /*  List<News> newsList = newsService.getNewsList();*/
                              //根据 发送含有参数的请求，获得当前页码
                              String currentPageNo = request.getParameter("pageIndex");
                              Integer currentPageNo1=null;
                              if(currentPageNo==null){//首次发送请求
                                  currentPageNo1=1;

                              }else{//非首次发送请求时 会携带请求参数
                                  currentPageNo1= Integer.valueOf(currentPageNo);

                              }
                              //尾页：获得总页数
                              PageSupport pageSupport = new PageSupport();

                              //总记录数
                              int totalCount = newsService.getNewsCount();
                              pageSupport.setTotalCount(totalCount);

                              //页容量
                              int pageSize=5;
                              pageSupport.setPageSize(pageSize);

                              //总页数：总记录数除以页容量获得  总记录51 ，页容量5 ，总页数11
                              int totalPageCount = pageSupport.getTotalPageCount();
                              System.out.println("totalPageCount--->"+totalPageCount);


                              List<News> newsList = newsService.getNewsList_page(currentPageNo1,pageSize);
                              for(News news:newsList){%>
                                  <li><span><%=news.getCreateDate() %></span><a href="<%=request.getContextPath()%>/pages/newsDetail.jsp?newsId=<%=news.getId()%>"><%=news.getTitle()%></a></li>
                           <%   }

                          %>



                      </ul>
                    </div>
                    <!--图片新闻-->
                    <div class="news-pic">
                        <dl>
                            <dt><img src="<%=request.getContextPath() %>/images/new-1.png" /></dt>
                            <dd>PHP从入门到放弃</dd>
                        </dl>
                        <dl>
                            <dt><img src="<%=request.getContextPath() %>/images/new-2.png" /></dt>
                            <dd>Java Web从绝望到希望</dd>
                        </dl>
                        <dl>
                            <dt><img src="<%=request.getContextPath() %>/images/new-3.png" /></dt>
                            <dd>课工场五一回馈</dd>
                        </dl>
                        <dl>
                            <dt><img src="<%=request.getContextPath() %>/images/new-2.png" /></dt>
                            <dd>Java Web从绝望到希望</dd>
                        </dl>
                    </div>
                    <div class="page-bar">
                        <ul class="page-num-ul clearfix">
                            <%
                                if(currentPageNo1>1){%>
                            <li><a href="index.jsp?pageIndex=1">首页</a><li>
                            <li><a href="index.jsp?pageIndex=<%=currentPageNo1-1 %>">上一页</a><li>

                               <% }
                            %>
                            <%
                                 if(currentPageNo1<totalPageCount){
                                     %>
                            <li><a href="index.jsp?pageIndex=<%=currentPageNo1+1 %>">下一页</a><li>
                            <li><a href="index.jsp?pageIndex=<%=totalPageCount %>">尾页</a><li>

                                       <%
                                 }
                                       %>


                        </ul>
                        <span class="page-go-form">
                            <label>跳转至</label>
                            <input id="page-key" type="text" name="numkey" class="page-key" />页
                            <input id="totalPageCount" type="hidden" value="<%=totalPageCount %>">
                            <button type="submit" class="page-btn" onclick="jump_to()">GO</button>
                        </span>

                        <%--实现页码的跳转--%>
                        <script type="text/javascript">

                            function jump_to() {
                                var pageKey = document.getElementById("page-key");
                                var totalPageCount = document.getElementById("totalPageCount").value;

                                 //value为string类型
                                var value = pageKey.value;
                                //正则表达式
                                var regexp=/^[1-9]\d*$/;
                                /*value 必须是正整数，同时小于总页数*/
                                if(regexp.test(value)) {
                                    //下述运算方式可以自动将string类型value转换成num
                                    if (value-0 > 0 && value- totalPageCount<0) {
                                        window.location.href = "index.jsp?pageIndex=" + value;
                                    } else {
                                        alert("输入值必须要大于0，小于总页数");
                                    }
                                }else {
                                    alert("必须是整数");
                                }

                            }
                        </script>

                    </div>
```

```java
/**
 * @Author: 悟空非空也（公众号/CSDN）
 */
public interface NewsService {

    //分页查询新闻列表
    public List<News> getNewsList_page(Integer currentPageSize,Integer pageSize);

    //获得新闻总记录数
    public int getNewsCount();
}
```

```java
package com.news.util;

/**
 * @Author: 悟空非空也（公众号/CSDN）
 */
public class PageSupport {

    private  int currentPageNo=1;//当前页码
    private  int pageSize;//页容量
    private  int totalCount;//总记录数
    private  int totalPageCount;//总页数

    /*
    分析： 当前页码可以通过用户点击或者输入获得；
            页容量可以通过程序员设置；
            总记录数可以通过sql语句查询表获得
            总页数可以通过 总记录数除以页容量获得
     */

    public int getCurrentPageNo() {
        return currentPageNo;
    }

    public void setCurrentPageNo(int currentPageNo) {
        //肯定是大于0，小于总页数
        if(0<currentPageNo && currentPageNo<=this.totalPageCount){
        this.currentPageNo = currentPageNo;
        }else {
            System.out.println("PageSupport==》setCurrentPageNo==》设置错误");
        }
    }

    public int getPageSize() {
        return pageSize;
    }

    public void setPageSize(int pageSize) {
        //满足条件：大于等于
        if(pageSize>=0 && pageSize<=this.totalCount){
        this.pageSize = pageSize;
        }else {
            System.out.println("pageSize 设置错误");
        }

    }

    public int getTotalPageCount() {
        this.setTotalPageCount();
        return totalPageCount;
    }

    public void setTotalPageCount() {
        //满足条件：总页数： 总记录数除以 页容量获得,比如 100/10 余数0   商10  101/10  余数大于0  商 10 需要加1
        if(this.totalCount % this.pageSize == 0){
            this.totalPageCount = this.totalCount / this.pageSize;
        }else if(this.totalCount % this.pageSize > 0){
            this.totalPageCount = this.totalCount / this.pageSize + 1;
        }else{
            this.totalPageCount = 0;
        }
    }



    public static void main(String[] args) {
        PageSupport pageSupport = new PageSupport();
        pageSupport.setTotalCount(51);
        pageSupport.setPageSize(5);
        int totalPageCount = pageSupport.getTotalPageCount();
        System.out.println("aaa"+totalPageCount);
    }




    public int getTotalCount() {
        return totalCount;
    }

    public void setTotalCount(int totalCount) {
        //满足条件：大于等于0
        if(totalCount>=0){
            this.totalCount = totalCount;

        }else{
            System.out.println("总记录数不能小于0");
        }
    }
}

```

