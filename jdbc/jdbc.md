# JDBC



1 添加依赖

```xml
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <version>8.0.16</version>
</dependency>
```



2 jdbcUtil

```java
package com.wukong.util;
/* author: 悟空非空也（B站/知乎/公众号） */


import com.wukong.pojo.User;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class JdbcUtil {
   private Connection connection;
   private PreparedStatement preparedStatement;

     // 连接数据库 jdbc  java database  connection
    public boolean getConnection()  {
        // 读出配置信息  com.mysql.jdbc.Driver
        String driver = "com.mysql.cj.jdbc.Driver";
        String url = "jdbc:mysql://127.0.0.1:3306/qq_db?serverTimezone=GMT&characterEncoding=utf-8";
        String username = "root";
        String password = "root";

        try {
            // 加载jdbc驱动
            Class.forName(driver);
            // 创建与数据库的连接对象
            connection = DriverManager.getConnection(url, username, password);
            return true;
        } catch (ClassNotFoundException | SQLException e) {
            e.printStackTrace();
            return false;
        }

    }



    /**
     * 增加操作
     * 此时的user中包含了 username password email
     *
     * @param user
     */
    public boolean add(User user){
        String sql = "INSERT INTO qq_user(username,`password`,email,hobbies)VALUES(?,?,?,?)";

        if(this.getConnection()){
            try {
                // 给占位符？ 赋值
                preparedStatement = connection.prepareStatement(sql);
                preparedStatement.setString(1,user.getUsername());
                preparedStatement.setString(2,user.getPassword());
                preparedStatement.setString(3,user.getEmail());
                preparedStatement.setString(4,user.getHobbies());
                //执行添加操作
                int i = preparedStatement.executeUpdate();
                if(i>0){
                    // 添加成功
                    return true;
                }else{
                    return false;
                }
            } catch (Exception exception) {
                exception.printStackTrace();
                return false;
            }
        }else { //连接失败
            return false;
        }

    }


    // 关闭资源
    public boolean closeResource(){
        if(preparedStatement !=null){
            try {
                preparedStatement.close();

            } catch (SQLException throwables) {
                throwables.printStackTrace();
                return false;
            }
        }
        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
                return false;
            }
        }
        return true;
    }


}
```



3 servlet

```java
package com.wukong.servlet;
/* author: 悟空非空也（B站/知乎/公众号） */


import com.wukong.pojo.User;
import com.wukong.util.JdbcUtil;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.Arrays;

public class MyRegister  extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        super.doGet(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
         // 对于post 请求，request对象接收到请求参数前,进行utf-8编码设置
        request.setCharacterEncoding("utf-8");
         // response 响应信息给浏览器之前，进行Utf-8 编码设置 和 响应内容类型设置
        // 简写   response.setContentType("text/html;charset=utf-8");
        response.setCharacterEncoding("utf-8");
        response.setContentType("text/html");

        PrintWriter out = response.getWriter();

        // 使用jdbcUtil工具类进行保存客户端的请求参数
        JdbcUtil jdbcUtil = new JdbcUtil();
        //接收用户名参数
        String username = request.getParameter("username");
        // 在控制台输出
        System.out.println("用户名为："+ username );
        // 接收密码参数
        String password = request.getParameter("password");
        System.out.println("密码为:" + password);
        // 接收邮箱参数
        String email = request.getParameter("email");
        System.out.println("邮箱为：" + email );

        // 接收爱好参数
        String[] hobbiesArray = request.getParameterValues("hobbies");
        String hobbiesArrayToString = Arrays.toString(hobbiesArray);

        User user = new User();

        user.setUsername(username);
        user.setPassword(password);
        user.setEmail(email);
        user.setHobbies(hobbiesArrayToString);

        boolean isAdd = jdbcUtil.add(user);

        if(isAdd){ // add  success
            out.println("用户注册成功");
        }else{ // add fail
            out.println("用户注册失败");
        }


        // 接收爱好参数
      //  String[] hobbiesArray = request.getParameterValues("hobbies");
        System.out.println(Arrays.toString(hobbiesArray));
        for (String hobby: hobbiesArray) {
            System.out.println("爱好为：" +hobby);
        }
    }
}
```


## Statement和PreparedStatement的区别

**需求：根根指定条件，进行部分查询**

### Statement的应用

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;


public class NewsDao {

	//查找特定标题的新闻信息
	public void getNewsByTitle(String title){
		Connection connection = null;
		Statement stmt = null;
		ResultSet rs = null;
		try {
			Class.forName("com.mysql.jdbc.Driver");
			String url="jdbc:mysql://127.0.0.1:3306/kgcnews";
			connection = DriverManager.getConnection(url, "root", "bdqn");
			stmt = connection.createStatement();
			String sql="select id,title from news_detail where title='"+title+"'";
			System.out.println(sql);
			rs = stmt.executeQuery(sql);
			while(rs.next()){
				int id = rs.getInt("id"); //rs.getInt(1);
				String newsTitle = rs.getString("title");
				
				System.out.println(id+"\t"+newsTitle);
			}
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SQLException e) {
			e.printStackTrace();
		}finally{
			try {
				rs.close();
				stmt.close();
				connection.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
		
	public static void main(String[] args) {
		NewsDao newsDao = new NewsDao();
//		newsDao.getNewsByTitle("Java Web开课啦");
		newsDao.getNewsByTitle("Java Web开课啦' or '1'='1");
	}
}

```

### PreparedStatement的应用

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

/*使用 PreparedStatement*/
public class NewsDao2 {
	//查找特定标题的新闻信息
	public void getNewsByTitle(String title){
		Connection connection = null;
		PreparedStatement pstmt = null;
		ResultSet rs = null;
		try {
			Class.forName("com.mysql.jdbc.Driver");
			String url="jdbc:mysql://127.0.0.1:3306/kgcnews";
			connection = DriverManager.getConnection(url, "root", "bdqn");
			String sql="select id,title from news_detail where title=?";
			pstmt = connection.prepareStatement(sql);
			pstmt.setString(1, title);
			rs = pstmt.executeQuery();
			while(rs.next()){
				int id = rs.getInt("id"); //rs.getInt(1);
				String newsTitle = rs.getString("title");
				
				System.out.println(id+"\t"+newsTitle);
			}
			
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		} catch (SQLException e) {
			e.printStackTrace();
		}finally{
			try {
				rs.close();
				pstmt.close();
				connection.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
	
	public static void main(String[] args) {
		NewsDao2 newsDao = new NewsDao2();
//		newsDao.getNewsByTitle("Java Web开课啦");
		newsDao.getNewsByTitle("Java Web开课啦' or '1'='1");
	}
}
```




