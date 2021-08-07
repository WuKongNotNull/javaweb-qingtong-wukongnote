# 邮件注册/发送

## 1 邮件系统简介

电子邮件系统最主要的组成构建

1. 用户代理（qq邮箱等电子邮件系统程序）
2. 邮件服务器
3. 电子邮件使用的协议 ：smtp (simple mial transfer protocol) pop3 (post office protocol)邮局协议第三版

工作原理图

![image-20200814164915857](https://i.loli.net/2020/08/14/M37A6bCGu4XkwVO.png)

步骤：

①发信人调用用户代理来撰写和编辑要发送的邮件。用户代理用SMTP把邮件传送给发送方邮件服务器。
②发送方邮件服务器将邮件放入邮件缓存队列中，等待发送。
③运行在发送方邮件服务器的SMTP客户进程，发现邮件缓存中有待发送的邮件，就向运行在接收方邮件服务器的SMTP服务器进程发起建立TCP连接
④TCP连接建立后，SMTP客户进程开始向远程SMTP服务器进程发送邮件。当所有待发送邮件发完后，SMTP就关闭所建立的TCP连接。
⑤运行在接收方邮件服务器中的SMTP服务器进程收到邮件后，将邮件放入收信人的用户邮箱，等待收信人在方便时进行读取。
⑥收信人打算收信时，调用用户代理，使用POP3(或IMAP）协议将自己的邮件从接收方邮件服务器的用户邮箱中取回（如果邮箱中有来信的话）。

**smtp协议用于发送（push）邮件，pop3协议用于拉（pull）邮件**

用户读取邮件时，用户代理向邮件服务器发送请求 ，拉取用户邮箱中的邮件

## 2 简单文本邮件代码测试

**jar包的支持：**

1. activation-1.1.1.jar
2. mail-1.4.7.jar

简易邮件的实现原理图

![image-20200814170527980](https://i.loli.net/2020/08/14/Us5pMNckiPfeajA.png)

步骤如下：

1. 创建定义整个应用程序所需要的环境信息的session对象
2. 使用session对象创建transport对象
3. 使用邮件用户名和授权码连上邮件服务器
4. 创建邮件
    - 发送人，收件人，主题，要发送的文本
5. 发送邮件，关闭连接

> 代码如下

```java
	    // write your code here
        Properties prop=new Properties();
        prop.setProperty("mail.host","smtp.qq.com");///设置QQ邮件SMTP服务器
        prop.setProperty("mail.transport.protocol","smtp");//邮件发送协议
        prop.setProperty("mail.smtp.auth","true");//需要验证用户密码 authentication

        //QQ邮箱需要设置SSL加密
        MailSSLSocketFactory sf=new MailSSLSocketFactory();
        sf.setTrustAllHosts(true);
        prop.put("mail.smtp.ssl.enable","true");
        prop.put("mail.smtp.ssl.socketFactory",sf);
        //=========================================使用javaMail发送邮件的5个步骤===========================================
        //1.创建定义整个应用程序所需要的环境信息的session对象
        Session session = Session.getDefaultInstance(prop, new Authenticator() {
            @Override
            protected PasswordAuthentication getPasswordAuthentication() {
                return new PasswordAuthentication("1289557927@qq.com","wjuumtponiowfgfg");
            }
        });
        //开启session的debug模式，这样可以查看到程序发送Email的运行状态
        session.setDebug(true);
        //2.通过session得到transport对象
        Transport ts=session.getTransport();
        //3.使用邮箱的用户名和授权码连上邮件服务器 wjuumtponiowfgfg
        ts.connect("smtp.qq.com","1289557927@qq.com","wjuumtponiowfgfg");
        //4.创建邮件：写文件
        //注意需要传递session
        MimeMessage message=new MimeMessage(session);
        //指明邮件的发件人
        message.setFrom(new InternetAddress("1289557927@qq.com"));
        //指明邮件的收件人
        message.setRecipient(Message.RecipientType.TO,new InternetAddress("1289557927@qq.com"));
        //邮件标题
        message.setSubject("发送的标题");
        //邮件的文本内容
        message.setContent("内容","text/html;charset=UTF-8");
        //5.发送邮件
        ts.sendMessage(message,message.getAllRecipients());
        //6.关闭连接
        ts.close();
```

> 测试结果

```
EHLO DESKTOP-16OBIHF
MAIL FROM:<1289557927@qq.com>
250 OK.
RCPT TO:<1289557927@qq.com>
250 OK
DEBUG SMTP: Verified Addresses
DEBUG SMTP:   1289557927@qq.com
DATA
354 End data with <CR><LF>.<CR><LF>.
From: 1289557927@qq.com
To: 1289557927@qq.com
Message-ID: <1073502961.0.1597397272807.JavaMail.IANDF@smtp.qq.com>
Subject: =?UTF-8?B?5Y+R6YCB55qE5qCH6aKY?=
MIME-Version: 1.0
Content-Type: text/html;charset=UTF-8
Content-Transfer-Encoding: base64

5YaF5a65
.
250 OK: queued as.
QUIT
```

## 3 MIME支持

**多用途互联网邮件扩展**（英语：**M**ultipurpose **I**nternet **M**ail **E**xtensions，缩写：**MIME**）是一个[互联网标准](https://zh.wikipedia.org/wiki/互联网标准)，它扩展了[电子邮件](https://zh.wikipedia.org/wiki/电子邮件)标准，使其能够支持：

- 非[ASCII](https://zh.wikipedia.org/wiki/ASCII)字符文本；
- 非文本格式附件（[二进制](https://zh.wikipedia.org/wiki/二进制)、声音、图像等）；
- 由多部分（multiple parts）组成的消息体；
- 包含非[ASCII](https://zh.wikipedia.org/wiki/ASCII)字符的头信息（Header information）。这个标准被定义在 [RFC 2045](https://tools.ietf.org/html/rfc2045)、[RFC 2046](https://tools.ietf.org/html/rfc2046)、[RFC 2047](https://tools.ietf.org/html/rfc2047)、[RFC 2048](https://tools.ietf.org/html/rfc2048)、[RFC 2049](https://tools.ietf.org/html/rfc2049) 等[RFC](https://zh.wikipedia.org/wiki/RFC)中。

![image-20200814205201762](https://i.loli.net/2020/08/14/mowjOAuiry7ZN2U.png)

**每一个文本、图片、附件可以分为一个MimeBodyPart，由MimeMultipart完成组装**

```java
        Properties prop = new Properties();
        prop.setProperty("mail.host","smtp.qq.com");///设置QQ邮件服务器
        prop.setProperty("mail.transport.protocol","smtp");///邮件发送协议
        prop.setProperty("mail.smtp.auth","true");//需要验证用户密码
        //QQ邮箱需要设置SSL加密
        MailSSLSocketFactory sf=new MailSSLSocketFactory();
        sf.setTrustAllHosts(true);
        prop.put("mail.smtp.ssl.enable","true");
        prop.put("mail.smtp.ssl.socketFactory",sf);

        //使用javaMail发送邮件的5个步骤
        //1.创建定义整个应用程序所需要的环境信息的session对象
        Session session=Session.getDefaultInstance(prop, new Authenticator() {
            @Override
            protected PasswordAuthentication getPasswordAuthentication() {
                return new PasswordAuthentication("1289557927@qq.com","wjuumtponiowfgfg");
            }
        });
        //开启session的debug模式，这样可以查看到程序发送Email的运行状态
        session.setDebug(true);
        //2.通过session得到transport对象
        Transport ts=session.getTransport();
        //3.使用邮箱的用户名和授权码连上邮件服务器
        ts.connect("smtp.qq.com","1289557927@qq.com","wjuumtponiowfgfg");
        //4.创建邮件：写文件
        //注意需要传递session
        MimeMessage message=new MimeMessage(session);
        //指明邮件的发件人
        message.setFrom(new InternetAddress("1289557927@qq.com"));
        //指明邮件的收件人
        message.setRecipient(Message.RecipientType.TO,new InternetAddress("1289557927@qq.com"));
        //邮件标题
        message.setSubject("java发出");

        //邮件的文本内容
        //=================================准备图片数据=======================================
        MimeBodyPart image=new MimeBodyPart();
        //图片需要经过数据化的处理
        DataHandler dh=new DataHandler(new FileDataSource("D:\\ForStrong_java\\javaweb\\javaweb-08-sendMail\\src\\20200708003918960.png"));
        //在part中放入这个处理过图片的数据
        image.setDataHandler(dh);
        //给这个part设置一个ID名字
        image.setContentID("bz.jpg");

        //准备正文的数据
        MimeBodyPart text=new MimeBodyPart();
        text.setContent("这是一张正文<img src='cid:bz.jpg'>","text/html;charset=UTF-8");

        //描述数据关系
        MimeMultipart mm=new MimeMultipart();
        mm.addBodyPart(text);
        mm.addBodyPart(image);
        mm.setSubType("related");

        //设置到消息中，保存修改
        message.setContent(mm);
        message.saveChanges();
        //5.发送邮件
        ts.sendMessage(message,message.getAllRecipients());

        //6.关闭连接
        ts.close();
```

