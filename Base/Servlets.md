# Servlets
В java.net.*
Socket & ServerSocket - tcp
DatagramSocket - udp

в java.nio.* - неблокирующий ввод/вывод
SocketChannel & ServerSocketChannel - tcp
DatagramChannel - udp

# Реализация TCP
```java
client
public static void main(String[] args) throws IOException {  
    var inetAddress = InetAddress.getByName("localhost");  
    try(Socket socket = new Socket(inetAddress, 7777);  
        var outputStream = new DataOutputStream(socket.getOutputStream());  
        var inputStream =  new DataInputStream(socket.getInputStream())){  
        outputStream.writeUTF("Hello from client");  
        System.out.println("Response " + inputStream.readUTF());  
    }  
}

server
public static void main(String[] args) throws IOException, SQLException {  
    try (ServerSocket serverSocket = new ServerSocket(7777);  
         var socket = serverSocket.accept();  
         var outputStream = new DataOutputStream(socket.getOutputStream());  
         var inputStream =  new DataInputStream(socket.getInputStream())) {  
            System.out.println("Request " + inputStream.readUTF());  
            outputStream.writeUTF("Hello from server");  
    }  
}
```
 
```java
server
public static void main(String[] args) throws IOException, SQLException {  
    try (ServerSocket serverSocket = new ServerSocket(7777);  
         var socket = serverSocket.accept();  
         var outputStream = new DataOutputStream(socket.getOutputStream());  
         var inputStream =  new DataInputStream(socket.getInputStream());  
         var scanner = new Scanner(System.in)) {  
        var request = inputStream.readUTF();  
        while(!"stop".equals(request)){  
            System.out.println("Request " + request);  
            var response = scanner.nextLine();  
            outputStream.writeUTF(response);  
            request = inputStream.readUTF();  
        }  
    }  
}

client
public static void main(String[] args) throws IOException {  
    var inetAddress = InetAddress.getByName("localhost");  
    try(Socket socket = new Socket(inetAddress, 7777);  
        var outputStream = new DataOutputStream(socket.getOutputStream());  
        var inputStream =  new DataInputStream(socket.getInputStream());  
        var scanner = new Scanner(System.in)){  
        while(scanner.hasNextLine()){  
            var request = scanner.nextLine();  
            outputStream.writeUTF(request);  
            System.out.println("Response " + inputStream.readUTF());  
        }  
    }  
}
```

# Реализация UDP
```java
server
public static void main(String[] args) {  
    try (DatagramSocket datagramSocket = new DatagramSocket(7777)) {  
        byte[] buffer = new byte[512];  
        DatagramPacket packet = new DatagramPacket(buffer, buffer.length);  
        datagramSocket.receive(packet);  
  
        System.out.println(new String(buffer));  
    } catch (IOException e) {  
        throw new RuntimeException(e);  
    }  
  
}

client
public static void main(String[] args) throws IOException {  
    var inetAddress = InetAddress.getByName("localhost");  
    try (DatagramSocket datagramSocket = new DatagramSocket()) {  
        var data = "Hello from udp client".getBytes();  
        DatagramPacket packet = new DatagramPacket(data, data.length, inetAddress, 7777);  
        datagramSocket.send(packet);  
    } catch (SocketException e) {  
        throw new RuntimeException(e);  
    }  
}
```

# url
```java
URL url = new URL("https://www.gogle.com");  
var urlConnection = url.openConnection();
```



#do/start 
1. Apache TomCat
	1. war artefact
	2. жизненный цикл сервлетов
	3. Работа с headers body cockes session attributes
	4. архитектура современных веб приложений на Java
	5. перенаправление запроов
	6. jsp
	7. el jstl
	8. работа с формами, файлами, картинками, видео
	9. фильтры
	10. аутентификация и авторизация
	11. интернационализация и локализация
2. 
	* https://www.youtube.com/playlist?list=PLAma_mKffTOTTFqIkLXgHqVuL6xJhb0mr

