# JDBC
# JDBC driver
С его помощью мы можем взаимодействовать с [[10 Databases|базой данных]], для каждой бд пишется свой драйвер.

Подключение к БД
```java
public class Main {  
    private static final String URL = "jdbc:postgresql://localhost:5432/test_jdbc";  
    private static final String USERNAME = "postgres";  
    private static final String PASSWORD = "3525";  
  
    public static void main(String[] args) {  
        Connection connection;  
  
        try {  
            connection = DriverManager.getConnection(URL, USERNAME, PASSWORD);  
            if(!connection.isClosed())  
                System.out.println("Connection successful");
            Statement statement = connection.createStatement();  
			  //здесь пишем наши запросы execute
  
        } catch (SQLException e) {  
            System.out.println("Error connection");  
        }  
    }  
}
```

# Statement
С помощью Statement мы можем выполнять статические запросы, то есть на момент компиляции программы запрос известен.

# PreparedStatement
С помощью PreparedStatement можно выполнять динамические запросы.
```java
public class Main {  
    private static final String URL = "jdbc:postgresql://localhost:5432/test_jdbc";  
    private static final String USERNAME = "postgres";  
    private static final String PASSWORD = "3525";  
    private static final String INSERT = "INSERT INTO table_name (name, age) VALUES(?, ?)";  
    private static final String GET_ALL = """  
                                    SELECT *                                    
                                    FROM table_name  
                                    """;  
  
    public static void main(String[] args) {  
        Connection connection;  
        PreparedStatement statement = null;  
  
        try {  
            connection = DriverManager.getConnection(URL, USERNAME, PASSWORD);  
            if(!connection.isClosed())  
                System.out.println("Connection successful");  
  
            statement = connection.prepareStatement(INSERT);  
  
            //С помощью set методов можно подставлять данные для запроса вместо ?  
            statement.setString(1, "Doваn");  
            statement.setInt(2, 43);  
  
            statement.execute();  
  
            statement = connection.prepareStatement(GET_ALL);  
            ResultSet result = statement.executeQuery();  
            while(result.next()){  
                int id = result.getInt(1);//указываем номер колонки  
                String characterStream = result.getString(2);  
                int age = result.getInt(3);  
                System.out.println(id + " " + characterStream + " " + age);  
            }  
        } catch (SQLException e) {  
            System.out.println("Error connection");  
        }  
    }  
}
```

# execute
```java
statement.execute("INSERT INTO book(book_id, title, isbn) VALUES(10, 'Don', '43231')");
```
# executeUpdate
С помощью него можно писать запросы [[UPDATE table#insert into]] [[UPDATE table#update table]] [[DELETE table]]
```java
var a = statement.executeUpdate("UPDATE book SET title = 'XXXX' WHERE book_id = 10");  
System.out.println(a);//1
```

# executeQuery
Нужен для [[SELECT]]
```java
ResultSet result = statement.executeQuery("SELECT * FROM book");
while(result.next()){  
    int id = result.getInt(1);//указываем номер колонки  
    String characterStream = result.getString(2);  
    int age = result.getInt(3);  
    System.out.println(id + " " + characterStream + " " + age);  
}
1 Don 13
2 Fon 15
3 Fon 25
```

# Пакетная обработка addBatch
Если хотим выполнить несколько запросов, поскольку подряд их писать нельзя

```java
statement.addBatch("INSERT INTO book(book_id, title, isbn) VALUES(11, 'Don', '43231')");  
statement.addBatch("INSERT INTO book(book_id, title, isbn) VALUES(12, 'Dn', '4331')");  
statement.addBatch("INSERT INTO book(book_id, title, isbn) VALUES(15, 'on', '432')");  
  
statement.executeBatch();
statement.clearBatch();
```

# blob
Позволяет работать с бинарными объектами:
Тут у меня возникает исключение при создании блоба - `Method org.postgresql.jdbc.PgConnection.createBlob()` is not yet implemented. В интернете решение связанное с хибернетом, но я еще используя только jdbc, странно, в общем.
```java
public class Main {  
    private static final String URL = "jdbc:postgresql://localhost:5432/test_jdbc";  
    private static final String USERNAME = "postgres";  
    private static final String PASSWORD = "3525";  
    private static final String INSERT = "INSERT INTO table_name (name, age) VALUES(?, ?)";  
    private static final String GET_ALL = "SELECT * FROM table_name";  
  
    public static void main(String[] args) {  
        Connection connection;  
        PreparedStatement statement = null;  
  
        try {  
            connection = DriverManager.getConnection(URL, USERNAME, PASSWORD);  
            if(!connection.isClosed())  
                System.out.println("Connection successful");  
  
            BufferedImage image = ImageIO.read(new File("img.png"));  
            Blob blob = connection.createBlob();  
            try(OutputStream outputStream = blob.setBinaryStream(1)) {  
                ImageIO.write(image, "png", outputStream);  
            }  
  
            statement = connection.prepareStatement("insert into table_name (name, age, image) values (?, ?, ?)");  
            statement.setString(1, "Ricardo");  
            statement.setInt(2, 54);  
            statement.setBlob(3, blob);  
            statement.execute();  
  
            ResultSet resultSet = statement.executeQuery("select * from table_name");  
  
            while(resultSet.next()){  
                Blob blob1 = resultSet.getBlob("image");  
                BufferedImage image1 = ImageIO.read(blob1.getBinaryStream());  
                File outputFile = new File("save.png");  
                ImageIO.write(image1, "png", outputFile);  
            }  
  
        } catch (SQLException | IOException e) {  
            System.out.println("Error");  
            e.printStackTrace();  
        }  
    }  
}
```

# SQL injection
Для защиты от инъекций используются динамические запросы, т.к. во время выполнения данные подставляют вместо ? и проверяются на корректность.

# [[Транзакции]]
```
connection.setAutoCommit(false);
//здесь пишем свои запросы
connection.commit(); или connection.rollback();
```

Также можно установить точки сохранения, все что до `commit`, все что после `rollback`
```
connection.setAutoCommit(false);
//...
Savepoint savepoint = connection.setSavepoint();
//...
connection.rollback(savepoint);
connection.commit();
connection.reeaseSavepoint(savepoint);
```

# properties files
В таком файле хранится служебная информация, к примеру, url, username, password.

```java
Файл application.properties
db.URL = "jdbc:postgresql://localhost:5432/test_jdbc";  
db.USERNAME = "postgres";  
db.PASSWORD = "3525";
```

Реализуем класс для работы с файлом который хранится в папке `util`
```java
public final class PropertiesUtil {  
    private static final Properties PROPERTIES = new Properties();  
  
    static{  
        loadProperties();  
    }  
  
    public static String get(String key){  
        return PROPERTIES.getProperty(key);  
    }  
    private static void loadProperties() {  
        try(var inputStream = PropertiesUtil.class.getClassLoader().getResourceAsStream("application.properties")){  
            PROPERTIES.load(inputStream);  
        } catch (IOException e) {  
            throw new RuntimeException(e);  
        }  
    }  
  
    private PropertiesUtil(){}  
}
```

```java
public class Main {  
    private static final String URL_KEY = "db.URL";  
    private static final String USERNAME_KEY = "db.USERNAME";  
    private static final String PASSWORD_KEY = "db.PASSWORD";  
    private static final String INSERT = "INSERT INTO table_name (name, age) VALUES(?, ?)";  
    private static final String GET_ALL = "SELECT * FROM table_name";  
  
    public static void main(String[] args) {  
        Connection connection;  
        PreparedStatement statement = null;  
  
        try {  
            connection = DriverManager.getConnection(  
                    PropertiesUtil.get(URL_KEY),  
                    PropertiesUtil.get(USERNAME_KEY),  
                    PropertiesUtil.get(PASSWORD_KEY));  
            if(!connection.isClosed())  
                System.out.println("Connection successful");  
  
        } catch (SQLException e) {  
            System.out.println("Error");  
            e.printStackTrace();  
        }  
    }  
}
```