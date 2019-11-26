# Java中的JDBC详解

JDBC 是Java操作数据库的规范，它实际上定义了一组标准的数据库的接口，为了实现通过java操作数据路，必须实现这些接口，不同的数据厂商都提供了对JDBC接口的实现，这些具体的实现被打包成一个jar包(也就是数据库驱动)，供我们在开发的时候直接使用。

## JDBC API主要接口：
### Driver接口：
是所有JDBC程序必须实现的接口，该接口专门提供给数据库厂商使用，定义了驱动的样式
### DriverManager
用于加载JDBC驱动并创建数据库的连接
有两个重要的方法：
    - `DriverManager.registerDriver(Driver driver)` 用于向DriverManager注册给定的JDBC驱动
    - `DriverManger.getConnection(String url,String user,String pwd)`建立与数据库的连接，返回表示连接的Connection对象
### Connection接口 主要有三个方法
- Connection.createStatement()，创建一个Statement对象，静态sql语句查询
- Connection.prepareStatement(String sql),创建一个PreparedStatement对象，实现动态sql语句查询
- Connection.prepareCall(String sql); // 创建一个CallableStatement对象来调用数据库存储过程

### Statement接口
用于执行查询返回结果
 - `Statement.execute(String sql);` // 执行各种SQL语句，返回一个boolean类型值，true表示执行的SQL语句具备查询结果，可通过Statement.getResultSet()方法获取
- `Statement.executeUpdate(String sql);` // 执行SQL中的insert/update/delete语句，返回一个int值，表示受影响的记录的数目
- `Statement.executeQuery(String sql);` // 执行SQL中的select语句，返回一个表示查询结果的ResultSet对象


### ResultSet接口
用于查询结果的操作
- 1 ResultSet.next(); // 将游标由当前位置移动到下一行
- 2 ResultSet.getString(String columnName); // 获取指定字段的String类型值
- 3 ResultSet.getString(int columnIndex); // 获取指定索引的String类型值
- 4 ResuleSet.previous(); // 将游标由当前位置移动到上一行


## JDBC才做数据库的一般步骤
注册驱动（只做一次）
建立连接（Connection）
创建执行SQL语句（Statement）
执行语句并处理执行结果（ResultSet）
释放资源

