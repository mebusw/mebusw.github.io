title: 编码导致序列化sql对象时出现StreamCurruptedException异常
date: 2010-09-06 23:58:38
tags:
	- sql
	- serialization
categories:
	- programming
	- java
	
---

最近在序列化某JAVA类时，碰到怪事，原来是sql 连接字符串与数据库的character set不同，字符被转义所致。
环境：Mysql 5.0.51a-community-nt       Java 1.6.0_20-b02

解决办法是先测试数据库与mysql的字符集，然后使connection string与其一致。
show create table t1;
show variables like 'char%';

<!--more--> 
 
序列化的本意是将该类的实例保存到数据库，然后再读出来，结果读出的比写入时多一个byte，导致读取时出现

	StreamCurruptedException: 30。

如果对类做随机修改，则没有问题。
 
代码如下，其中gameTable即为要写入的对象实例：
 
写入：
 
``` [java]
pstmt = this.conn.prepareStatement(sql);  
ByteArrayOutputStream baos = new ByteArrayOutputStream();  
ObjectOutputStream oos = new ObjectOutputStream(baos);  
oos.writeObject(gameTable);  
oos.flush();  
ByteArrayInputStream bais = new ByteArrayInputStream(baos.toByteArray());  
pstmt.setBinaryStream(1, bais, bais.available());  
oos.close();  
baos.close();  
bais.close();  
```
 
读出：
``` [java]
ObjectInputStream ois = new ObjectInputStream(rs.getBinaryStream(1));  
gameTable = (GameTable) ois.readObject();  
ois.close();  
```
 
经过对数据库信息的逐字节比较（过程枯燥且痛苦），发现由于自动生成的serialVersionUID 如果包含0x0 ('/0')，则在调用setBinaryStream时被翻译成 0x5C 0x30 ('/' '0'),tricky！如果serialVersionUID 不含0x0则不会有任何问题，现在还不知是哪里做了这个翻译。
 
 

