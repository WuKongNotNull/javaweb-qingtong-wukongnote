# cookie

## 基本概念

## cookie demo

服务器创建一个cookie,并设置数据和属性，再由服务器发送给客户端，客户端在本地保存该cookie

```java
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        req.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=UTF-8");

        //服务器给客户端设置cookie
        // 判断 如果 无cookie
        if(req.getCookies()== null || req.getCookies().length ==0  ){
            Cookie my_cookie = new Cookie("passport", System.currentTimeMillis() + "");
            my_cookie.setMaxAge(24 * 60 * 60);
            resp.addCookie(my_cookie);
        }
        else {
            //服务器获取客户端的cookie
            Cookie[] cookies = req.getCookies();
            for (Cookie cookie : cookies) {
                if (cookie.getName().equals("passport")) {
                    System.out.println("浏览器:"+cookie.getValue()+",刚刚访问了此服务器");

                }
            }

        }

    }
```



