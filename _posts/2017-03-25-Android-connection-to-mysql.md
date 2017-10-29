---
layout: post
title:  "Android 链接mysql数据库"
date:   2017-03-25 11:00:00 +0800
categories: Android 
tags: Android mysql
author: JiuYang Chen
---
  
  
  
* content
{:toc}




本篇简单介绍`Android`链接`Mysql`数据库的方式及其操作。

1.1 将JDBC jar包导入项目libs中。

2.2 在AndroidManifest.xml配置权限,允许程序打开网络套接字

```xml

<uses-permission android:name="android.permission.INTERNET" />

```

3.3 创建一个mysql操作集合的类,包括我们常用到的数据库连接、数据库查询、数据库修改(增、删、改)

```java

/**
     * @author chenjy
     * @summary connection to mysql
     * @param url 数据库url
     * @param user 数据库用户名
     * @param password 数据库密码
     * @return Connection
     */
    public static Connection connectionToMysql(String url,String user ,String password){

        Connection con = null;
        try {
            final String DRIVER_NAME = "com.mysql.jdbc.Driver";
            Class.forName(DRIVER_NAME);
            con = DriverManager.getConnection(url, user, password);
            if(!con.isClosed())
            {
                System.out.println("数据库连接成功");
            }

        } catch (ClassNotFoundException e) {
            con = null;
            System.out.println("加载驱动程序出错");
        } catch (java.sql.SQLException e) {
            con = null;
            System.out.println(e.getMessage());
        }catch (Exception e) {
            System.out.println(e.getMessage());

        }

        return con;
    }

    /**
     * @author chenjy
     * @summary select from mysql
     * @param con 数据库连接
     * @param sql 数据库用户名
     * @return String
     */
    public static String query(Connection con, String sql) throws SQLException, JSONException {

        Statement statement = null;
        ResultSet result = null;

        if (con == null) {
            return resultSetToJson(result);
        }


        try {
            statement = con.createStatement();
            result = statement.executeQuery(sql);
            return resultSetToJson(result);
        } catch (SQLException e) {
            e.printStackTrace();
            return resultSetToJson(result);
        } catch (JSONException e) {
            e.printStackTrace();
        } finally {
            try {
                if (result != null) {
                    result.close();
                    result = null;
                }
                if (statement != null) {
                    statement.close();
                    statement = null;
                }

            } catch (SQLException sqle) {

            }
        }
        return sql;
    }

    /**
     * @author chenjy
     * @summary add,delete,update from mysql
     * @param con 数据库连接
     * @param sql 数据库用户名
     * @return Boolean
     */
    public static boolean execSQL(Connection con, String sql) {
        boolean execResult = false;
        if (con == null) {
            return execResult;
        }

        Statement statement = null;

        try {
            statement = con.createStatement();
            if (statement != null) {
                execResult = statement.execute(sql);
            }
        } catch (SQLException e) {
            execResult = false;
        }

        return execResult;
    }


    /**
     * @author chenjy
     * @summary ResultSet to JSON string
     * @param rs 结果集
     * @return String
     */
    public static String resultSetToJson(ResultSet rs) throws SQLException,JSONException
    {

        JSONArray array = new JSONArray();

        ResultSetMetaData metaData = rs.getMetaData();
        int columnCount = metaData.getColumnCount();

        while (rs.next()) {
            JSONObject jsonObj = new JSONObject();

            for (int i = 1; i <= columnCount; i++) {
                String columnName =metaData.getColumnLabel(i);
                String value = rs.getString(columnName);
                jsonObj.put(columnName, value);
            }
            array.put(jsonObj);
        }

        return array.toString();
    }


```

4.4 连接数据库

Android4.0 以后网络任务就不能在主线程,所以创建一个线程来连接数据库并且再创建一个线程来执行相关操作。

这里的con就是获取到的数据库连接。

```java


    private static final String URL = "jdbc:mysql://192.168.0.1:3306/DBName";
    private static final String USER = "root";
    private static final String PASSWORD = "root";
    private static Connection con = null;

	private Handler conHandler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            con = (Connection) msg.obj;
        };
    };

	public void connectionToMysql(){

        new Thread(new Runnable(){
            @Override
            public void run() {

                Message msg = Message.obtain();
                Connection conn = MysqlUtils.connectionToMysql(URL, USER, PASSWORD);
                msg.obj = conn;
                conHandler.sendMessage(msg);

            }
        }).start();

    }


```

5.5 从数据库获取数据。

```java

    private static String resultSet;

    private Handler dataHandler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            resultSet =  msg.obj.toString().trim();

        };
    };

    public void getDataFromMysql(final Connection con){

        if(con == null)return;

        new Thread(new Runnable(){
            @Override
            public void run() {

                try {
                    Message msg = Message.obtain();
                    msg.obj = MysqlUtils.query(con, "select * from config_rfid ");
                    System.out.println(msg.obj);
                    dataHandler.sendMessage(msg);

                } catch (SQLException e) {
                    e.printStackTrace();
                } catch (JSONException e) {
                    e.printStackTrace();
                }

            }
        }).start();

    }



```


![outPut](http://wx4.sinaimg.cn/mw690/c584f169ly1fe1e167rwej20fd017dfo.jpg)


[源代码](https://github.com/Chenjy1225/ChenjyDemo/tree/gh-pages/Android_mysql)


























