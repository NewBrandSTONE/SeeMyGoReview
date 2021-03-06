# JDBCTest
---
期中测试代码

* 需求

```xml
1，在数据库jdbcdemo中创建小码哥班级表  t_class 字段和实体类的变量一一对应
	备注：必须手写，不能用工具创建（建表语句放在t_class.sql文件里面）。
2，用jdbc完成t_class 数据的 增（save），删(delete)，改(update)，查(get,list)（查询一条AND多条数据）用dao层进行数据交互。
4，需要抽出DBUtil.java 工具类封装jdbc，数据库的配置参数要从db.properties加载
5，创建测试类，分别测试增删该查各个方法 
6，抽出DQL语句和DML语句模板，取名 JdbcTemplate.java。(可选)
代码规范性（10分）
备注：
数据库一定要用jdbcdemo，用户名 root 密码是 admin  如果不是的，请在提交前改成 admin
```

* 项目结构
  * pps.dao
  * pps.dao.impl
  * pps.domain
  * pps.test
  * pps.util
  * pps.template

* db.properties

```xml
driverClassName=com.mysql.jdbc.Driver
url=jdbc:mysql://127.0.0.1:3306/jdbcdemo?useUnicode=true&characterEncoding=utf-8
username=root
password=admin
```

* JDBCUtil.java

```java
public class JDBCUtil {
  private static Properties p = new Properties();
  
  static {
  try {
    ClassLoader loader = Thread().currentThread().getClassLoader();
    InputStream ins = loader.getResourceAsStream("db.properties");
    p.load(ins);
  } catch (Exception e) {
    e.printStackTrace();
  }
  
  }
  
  public static Connection getConnection() throws Exception {
    DataSource ds = DruidDataSourceFactory.createDataSource(p);
    return ds.getConnection();
  }
  
  public static void close(Connection conn, PreparedStatement pstmt, ResultSet rs) {
    try {
      if(rs != null) {
        rs.close();
      }
    } catch (Exception e) {
      e.printStackTrace();
    } finally {
      try{
        if (pstmt != null) {
          pstmt.close();
        }
      } catch(Exception e) {
        e.printStackTrace();
      } finally {
        try {
          if (conn != null) {
            conn.close();
          }
        } catch (Exception e) {
          e.printStackTrace();
        }
      }
    }
  }
  
}
```



* Classes.java

```java
public class Classes {
  private Long id;
  private String classname;
  private String teacher;
  private BigDecimal fee;
  // 省略了构造方法,Setter，Getter
}
```

* IClassesDAO.java

```java
public interface IClassesDAO {
  void save(Classes c);
  void delete(Long id);
  void update(Classes c);
  Classes get(Classes c) throws Exception;
  List<Classes> list() throws Exception;
}
```

* IClassesDAOBasicImpl.java

```java
public class IClassesDAOBasicImpl implements IClassesDAO {
  void save(Classes c) {
    Connection conn = null;
    PreparedStatemenet pstmt = null;
    try {
      conn = JDBCUtil.getConnection();
      String sql = "INSERT INTO t_class VALUES(NULL, ?, ?, ?)";
      pstmt = conn.prepareStatement();
      pstmt.setString(1, c.getClassName());
      pstmt.setString(2, c.getTeacher());
      pstmt.setBigDecimal(3, c.getFee());
      pstmt.executeUpdate();
    } catch (Exception e) {
      e.printStackTrace();
    } finally {
      JDBCUtil.close(null, pstmt, conn);
    }
  }
  
  void delete(Long id) {
    Connection conn = null;
    PreparedStatement pstmt = null;
    try {
      conn = JDBCUtil.getConnection();
      String sql = "DELETE FROM t_class WHERE id=?";
      pstmt = conn.prepareStatement(sql);
      pstmt.setLong(id);
      pstmt.executeUpdate();
    } catch(Exception e) {
      e.printStackTrace();
    } finally {
      JDBCUtil.close(null, pstmt, conn);
    }
  }
  
  void update(Classes c) {
    Connection conn = null;
    PreparedStatement pstmt = null;
    try {
      conn = JDBCUtil.getConnection();
      String sql = "UPDATE t_class SET classname=?, teacher=?, fee=? WHERE id=?";
      pstmt = conn.prepareStatement(sql);
      
    } catch(Excetpion e) {
      e.printStackTrace();
    } finally {
      JDBCUtil.close(null, pstmt, conn);
    }
  }
}
```




