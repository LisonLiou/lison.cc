---
title: 从简单的java连接MSSQLSERVER代码中得到的教训
tags:
  - 看黑板
categories:
  - java
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2015-7-7 10:44:38
desc:
keywords: java连接sqlserver,java jdbc基础
---

```
String sqlUrl = "jdbc:sqlserver://127.0.0.1:1433;databaseName=Test;user=sa;password=sa";

		Connection conn = null;
		Statement stmt = null;
		ResultSet rs = null;

		try {
			// Establish the connection.
			Class.forName("com.microsoft.sqlserver.jdbc.SQLServerDriver");
			conn = DriverManager.getConnection(sqlUrl);

			// Create and execute an SQL statement that returns some data.
			String SQL = "select * from tableName";
			stmt = conn.createStatement();
			rs = stmt.executeQuery(SQL);

			ResultSet excel = readExcel();

			while (excel.next())
				System.out.println(excel.getString(2));

		}
		// Handle any errors that may have occurred.
		catch (Exception e) {
			e.printStackTrace();
		} finally {
			if (rs != null)
				try {
					rs.close();
				} catch (Exception e) {
				}
			if (stmt != null)
				try {
					stmt.close();
				} catch (Exception e) {
				}
			if (conn != null)
				try {
					conn.close();
				} catch (Exception e) {
				}
		}
```

<!--more-->

一切都没什么好说的，只有那句：**<font color=red>System.out.println(excel.getString(2));</font>**

最初写的是getString(0)，一直报错，怎么写也报错，getArray，getObject都试了一遍，还是报错：

java.sql.SQLException: [Microsoft][ODBC
at sun.jdbc.odbc.JdbcOdbc.createSQLException(Unknown Source)
at sun.jdbc.odbc.JdbcOdbc.standardError(Unknown Source)
at sun.jdbc.odbc.JdbcOdbc.SQLColAttributes(Unknown Source)
at sun.jdbc.odbc.JdbcOdbcResultSet.getColAttribute(Unknown Source)
at sun.jdbc.odbc.JdbcOdbcResultSet.getColumnType(Unknown Source)
at sun.jdbc.odbc.JdbcOdbcResultSet.getMaxCharLen(Unknown Source)
at sun.jdbc.odbc.JdbcOdbcResultSet.getString(Unknown Source)
at com.lison.practise.Main.main(Main.java:39)

最后是查看了getString的源码，看到了注解：

![ResultSet的getString方法注解](data:image/png;base64,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)

原来这个index是从1开始的， 瞬间清凉到北极啊。。。尼玛不看注解会折寿啊。。。