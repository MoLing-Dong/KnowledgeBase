# Servlet 的 DAO 层怎么设计和 MySQL 交互

在使用 Servlet 开发 Web 应用程序时，通常会使用 DAO（Data Access Object）模式来管理与数据库的交互。DAO 层的设计需要考虑到应用程序的需求和数据访问的效率。以下是一个简单的示例，演示了如何设计 Servlet 的 DAO 层与 MySQL 数据库进行交互：

## **创建数据库表：** 首先，在 MySQL 数据库中创建所需的表格，例如

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    password VARCHAR(50) NOT NULL
);
```

## 　**创建 DAO 接口：** 创建一个 DAO 接口来定义数据库操作的方法

```java
public interface UserDao {
    User getUserById(int id);
    User getUserByUsername(String username);
    void addUser(User user);
    void updateUser(User user);
    void deleteUser(int id);
}
```

## **创建 DAO 实现类：** 实现 DAO 接口，并在此类中编写与 MySQL 数据库交互的代码

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class UserDaoImpl implements UserDao {
    private Connection connection;

    public UserDaoImpl(Connection connection) {
        this.connection = connection;
    }

    @Override
    public User getUserById(int id) {
        // 查询数据库获取用户信息
        // 使用PreparedStatement来防止SQL注入攻击
        String sql = "SELECT * FROM users WHERE id = ?";
        try (PreparedStatement statement = connection.prepareStatement(sql)) {
            statement.setInt(1, id);
            try (ResultSet resultSet = statement.executeQuery()) {
                if (resultSet.next()) {
                    User user = new User();
                    user.setId(resultSet.getInt("id"));
                    user.setUsername(resultSet.getString("username"));
                    user.setPassword(resultSet.getString("password"));
                    return user;
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return null;
    }

    // 实现其他方法：getUserByUsername, addUser, updateUser, deleteUser
}
```

## **使用 DAO 层：** 在 Servlet 中使用 DAO 层来执行数据库操作

```java
import java.io.IOException;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

@WebServlet("/user")
public class UserServlet extends HttpServlet {
    private UserDao userDao;

    public void init() {
        // 初始化数据库连接和DAO对象
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "username";
        String password = "password";
        try {
            Connection connection = DriverManager.getConnection(url, username, password);
            userDao = new UserDaoImpl(connection);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        int userId = Integer.parseInt(request.getParameter("id"));
        User user = userDao.getUserById(userId);
        // 处理用户信息并返回给客户端
    }

    // 处理其他HTTP请求，如POST, PUT, DELETE，并调用相应的DAO方法
}
```

在实际项目中，你可能还需要添加异常处理、连接池管理、事务支持等功能来提高应用的稳定性和性能。此外，可以使用 ORM 框架（如 Hibernate 或 MyBatis）来简化数据访问层的开发。
