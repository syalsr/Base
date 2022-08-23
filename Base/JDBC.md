# JDBC
# JDBC driver
С его помощью мы можем взаимодействовать с [[10 Databases|базой данных]], для каждой бд пишется свой драйвер.

Подключение к БД
```java
public class Main {  
  
    public static void main(String[] args) {  
        String sql = """  
                    insert into info (data)  
                    values ('Test1'), ('Test2');  
                    """;  
        try (var connection = ConnectionManager.open();  
             var statement = connection.createStatement()) {  
            var res = statement.execute(sql);  
        } catch (SQLException e) {  
            throw new RuntimeException(e);  
        }  
    }  
}

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
public class ConnectionManager {  
    private static final String URL_KEY = "db.url";  
    private static final String USERNAME_KEY = "db.username";  
    private static final String PASSWORD_KEY = "db.password";  
  
    private ConnectionManager(){  
  
    }  
    public static Connection open(){  
        try{  
            return DriverManager.getConnection(  
                    PropertiesUtil.get(URL_KEY),  
                    PropertiesUtil.get(USERNAME_KEY),  
                    PropertiesUtil.get(PASSWORD_KEY)  
            );  
        }catch (SQLException e) {  
            throw new RuntimeException(e);  
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
С помощью него можно писать запросы [[UPDATE table#insert into]] [[UPDATE table#update table]] [[DELETE]]
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
Может случится ситуация, когда мы хотим выполнить несколько запросов, обычно это происходит так, мы вызываем функцию выполнения, запрос поступает в БД и нам возвращается какой-то ответ, то есть если у нас 10 запросов, то мы 10 раз будем вынуждены отправить и дождаться ответа. При помощи batch мы можем сразу отправить все запросы, тем самым повышая производительность приложения.
Если хотим выполнить несколько запросов, поскольку подряд их писать нельзя

```java
public static void main(String[] args) throws SQLException{  
    long flightId = 9;  
    var deleteFlighSql = "DELETE FROM flight WHERE id = " + flightId;  
    var deleteTicketSql = "DELETE FROM ticker WHERE flight_id = " + flightId;  
  
    Connection connection = null;  
    Statement statement = null;  
    try  {  
        connection = ConnectionManager.open();  
        statement = connection.createStatement();  
          
        statement.addBatch(deleteTicketSql);  
        statement.addBatch(deleteFlighSql);  
  
        statement.executeBatch();  
  
    } catch (SQLException e) {  
        if(connection != null){  
            connection.rollback();  
        }  
        throw e;  
    } finally {  
        if(connection != null){  
            connection.close();  
        }  
        if(statement != null)  
            statement.close();  
    }  
}
```

# blob
В postgres есть аналог binary large object - bytea
clob - character large object - TEXT

```java
public static void main(String[] args) throws IOException, SQLException {  
    saveImage();  
    getImage();  
}  
  
private static void getImage() throws SQLException, IOException {  
    var sql = """  
            SELECT image  
            FROM info  
            WHERE id = ?  
            """;  
    try (var connection = ConnectionManager.open();  
         var preparedStatement = connection.prepareStatement(sql)) {  
        preparedStatement.setInt(1, 1);  
        var resultSet = preparedStatement.executeQuery();  
        if (resultSet.next()) {  
            var image = resultSet.getBytes("image");  
            Files.write(Path.of("src/main/resources", "boing777_new.png"), image, StandardOpenOption.CREATE);  
        }  
    }  
}  
  
private static void saveImage() throws SQLException, IOException {  
    var sql = """  
            UPDATE info  
            SET image = ?  
            WHERE id = 1  
            """;  
    try (var connection = ConnectionManager.open();  
         var preparedStatement = connection.prepareStatement(sql)) {  
        preparedStatement.setBytes(1, Files.readAllBytes(Path.of("src/main/resources", "boing777.png")));  
        preparedStatement.executeUpdate();  
    }  
}
```

# SQL injection
Для защиты от инъекций используются динамические запросы, т.к. во время выполнения данные подставляют вместо ? и проверяются на корректность.

# [[Транзакции]]
```
public static void main(String[] args) throws SQLException{  
    long flightId = 9;  
    var deleteFlighSql = "DELETE FROM flight WHERE id = ?";  
    var deleteTicketSql = "DELETE FROM ticker WHERE flight_id = ?";  
  
    Connection connection = null;  
    PreparedStatement deleteFlightStatement = null;  
    PreparedStatement deleteTickerStatement = null;  
    try  {  
        connection = ConnectionManager.open();  
        deleteFlightStatement = connection.prepareStatement(deleteFlighSql);  
        deleteTickerStatement = connection.prepareStatement(deleteTicketSql);  
  
        connection.setAutoCommit(false);  
  
        deleteFlightStatement.setLong(1, flightId);  
        deleteTickerStatement.setLong(1, flightId);  
  
        deleteTickerStatement.executeUpdate();  
        deleteFlightStatement.executeUpdate();  
        connection.commit();  
    } catch (SQLException e) {  
        if(connection != null){  
            connection.rollback();  
        }  
        throw e;  
    } finally {  
        if(connection != null){  
            connection.close();  
        }  
        if(deleteTickerStatement != null)  
            deleteTickerStatement.close();  
        if(deleteFlightStatement != null)  
            deleteFlightStatement.close();  
    }  
}
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


# fetchsize
Параметр с помощью которые мы контролируем количество элементов которые мы загружаем из бд в свою программу посредством запросов. Допустим, мы делаем `select *` таблицы в которой несколько миллионов записей, в результате без использования этого параметра в нашей программе возникнет исключение `OutOfMemory`. Этот параметр контролирует количество записей перемещаемых из бд в наше приложение, обычно он равен 50-100

![[fetchsize.excalidraw]]

Как только мы дошли до 3 элемента, если есть еще записи, то они загружаются в нашу память.

```java
try (var connection = ConnectionManager.open();  
     var statement = connection.prepareStatement(sql)) {  
    statement.setFetchSize(50);  
	statement.setQueryTimeout(10);//ограничение выполнение запроса в 10 секунд  
	statement.setMaxRows(100);//ограничение на кол-во строк  
} catch (SQLException e) {  
    throw new RuntimeException(e);  
}
```

# metadata
С помощью специального метода можем получить информацию о структуре БД
```java
try (var connection = ConnectionManager.open()) {  
    var metaData = connection.getMetaData();  
    var catalogs = metaData.getCatalogs();  
    while(catalogs.next()){  
        var catalog = catalogs.getString(1);  
        var schemas = metaData.getSchemas();  
        while (schemas.next()){  
            var schema = schemas.getString("TABLE_SCHEM");  
            var tables = metaData.getTables(catalog, schema, "%", null);  
            if(schema.equals("public")){  
                while (tables.next()) {  
                    System.out.println(tables.getString("TABLE_NAME"));  
                }  
            }  
        }  
    }  
} catch (SQLException e) {  
    throw new RuntimeException(e);  
}
```

# Connection pool
Представляет из себя очередь состоящую из нескольких инициализированных соединений, поскольку соединение с БД - дорогостоящая операция всегда используется пул соединений.  По окончанию работы, вместо закрытия соединения, мы его возвращаем в пул.

