## 1장 오브젝트와 의존관계

##### 1-1 초난감 DAO

###### 오브젝트의 설계와 구현, 동작원리에 집중하기 위해 기본적인 설계로 DAO 를 만들어 보자

#### class User
```java
public class User {

    String id;
    String name;
    String password;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}
```

#### class UserDAO
```java
import java.sql.*;

public class UserDao {

    public void add(User user) throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook", "spring", "book");

        PreparedStatement ps = c.prepareStatement(
                "insert into users(id, name, password) values(?, ?, ?)"
        );
        ps.setString(1, user.getId());
        ps.setString(2, user.getName());
        ps.setString(3, user.getPassword());

        ps.executeUpdate();

        ps.close();
        c.close();
    }

    public User get(String id) throws ClassNotFoundException, SQLException {
        Class.forName("com.mysql.jdbc.Driver");
        Connection c = DriverManager.getConnection("jdbc:mysql://localhost/springbook", "spring", "book");

        PreparedStatement ps = c.prepareStatement(
                "select * from users where id = ?"
        );
        ps.setString(1, id);

        ResultSet rs = ps.executeQuery();
        rs.next();
        User user = new User();
        user.setId(rs.getString("id"));
        user.setName(rs.getString("name"));
        user.setPassword(rs.getString("password"));

        rs.close();
        ps.close();
        c.close();

        return user;
    }

    public static void main(String[] args) throws SQLException, ClassNotFoundException {
        UserDao dao = new UserDao();

        User user = new User();
        user.setId("whiteship");
        user.setName("백기선");
        user.setPassword("married");

        dao.add(user);
        System.out.println(user.getId() + "등록 성공");

        User user2 = dao.get(user.getId());
        System.out.println(user2.getName());
        System.out.println(user2.getName());
        System.out.println(user2.getPassword());

        System.out.println(user2.getId() + "조회 성공");
    }

}
```

#### 내가 생각하는 문제점 :
##### 1.DB 와의 연결을 반복적으로 직접 해주고 닫아 줘야 한다.
##### 2.PreparedStatement 를 통해 쿼리를 하나 날릴 때 마다 작성해 줘야하는 코드들이 많다.

#### 책에서 말하는 문제점 : 
##### 1.add() 메서드 하나에만 적어도 3가지 관심사항이 있다.
##### -> DB 연결과 관련된 관심
##### -> Statement 를 만들고 실행하는 것
##### -> Statement 와 Connection 오브젝트를 담아서 소중한 공유 리소스를 시스템에 돌려주는 것
#### => 관심사가 같은 것끼리 모으고 다른 것은 분리해줌으로써 같은 관심에 효과적으로 집중 할 수 있도록 해야한다.
###### 참고로 예외사항에 대한 처리가 전혀 없는데 이는 나중에 다루도록 하자.