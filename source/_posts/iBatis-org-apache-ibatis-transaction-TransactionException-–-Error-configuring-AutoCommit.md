title: iBatis org.apache.ibatis.transaction.TransactionException – Error configuring AutoCommit
tags:
  - database
  - solution
  - Error configuring AutoCommit
  - iBatis
  - myBatis
  - TransactionException
date: 2012-02-24 10:41:17
---

最近发现用mybatis实现的登录功能，偶尔会爆出如下异常：

<pre class="brush:java">org.apache.ibatis.exceptions.PersistenceException: 
### Error opening session.  Cause: org.apache.ibatis.transaction.TransactionException: Error configuring AutoCommit.  
Your driver may not support getAutoCommit() or setAutoCommit(). Requested setting: false. 
Cause: com.mysql.jdbc.CommunicationsException: 
Communications link failure due to underlying exception... ...</pre>

多试几次以后，就又正常了，这个异常不是一直存在。不得其解，google 之，得到一种解决方案，不过他遇到的情况发生在mysql重启之时。

源地址： http://burtsev.net/en/2012/01/12/100

解决此问题，需要在mybatis(iBatis)的配置文件中加入两个属性：poolPingQuery 和 poolPingEnabled。具体如下：

<span id="more-599"></span>

<pre class="brush:xml">&lt;?xml version="1.0" encoding="UTF-8" ?&gt;
&lt;!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd"&gt;
&lt;configuration&gt;

    &lt;typeAliases&gt;
        ...
    &lt;/typeAliases&gt;

    &lt;environments default="development"&gt;
        &lt;environment id="development"&gt;
            &lt;transactionManager type="JDBC" /&gt;
            &lt;dataSource type="POOLED"&gt;
                &lt;property name="driver" value="${db.driver}" /&gt;
                &lt;property name="url" value="${db.url}" /&gt;
                &lt;property name="username" value="${db.user}" /&gt;
                &lt;property name="password" value="${db.password}" /&gt;
                &lt;property name="poolPingQuery" value="SELECT id FROM user WHERE id = 1" /&gt;
                &lt;property name="poolPingEnabled" value="true" /&gt;
            &lt;/dataSource&gt;
        &lt;/environment&gt;
    &lt;/environments&gt;

&lt;/configuration&gt;</pre>

其中SELECT id FROM user WHERE id = 1是查询配置中的数据库的某个表，这里是user表，推测这里可以配置成任意一个可以执行的sql语句，当然越简单越好。