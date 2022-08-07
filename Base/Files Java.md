# Files Java
## Read
Считываем по строчкам
```java
String separator = File.separator;//у каждой OS свой разделитель
String path = "D:"+separator + "06" + separator + "test" + separator + "test.txt";  
File file = new File(path);  
Scanner scanner = new Scanner(file);  
while(scanner.hasNextLine()){  
    System.out.println(scanner.nextLine());  
}  
123  
123  
432  
543  
654  
scanner.close();
```

Считываем посимвольно
```java
File file = new File("test.txt");  
try {  
    file.createNewFile();  
} catch (IOException e) {  
    e.printStackTrace();  
}  
try(InputStream inputStream = new FileInputStream(file)) {
    int a = inputStream.read();  
    while (a != -1) {  
        System.out.print((char)a);  
        a = inputStream.read();  
    }  
}catch (Exception e){  
    e.printStackTrace();  
}
```

 Сначала считываем все в [[Strings Java#String builder]] а потом выводим
```java
File file = new File("test.txt");  
try {  
    file.createNewFile();  
} catch (IOException e) {  
    e.printStackTrace();  
}  
try(InputStream inputStream = new FileInputStream(file)) {
    int a = inputStream.read();
    StringBuilder result = new StringBuilder();
    while (a != -1) {
	    result.append((char) a);
        a = inputStream.read();  
    }  
        System.out.print(result;  
}catch (Exception e){  
    e.printStackTrace();  
}
```

А можно использовать [[Strings Java#String Buffer]] который является потокобезопасным

Чтобы ускорить программу, лучше не читать данные по одному байту

```java
File file = new File("test.txt");  
try {  
    file.createNewFile();  
} catch (IOException e) {  
    e.printStackTrace();  
}  
try(InputStream inputStream = new FileInputStream(file)) {  
    byte[] arr = new byte[1024];//допустим у нас файл весит несколько от 10 байт до 10 МБ  
    int count = inputStream.read(arr);  
    StringBuilder result = new StringBuilder();  
    while (count != -1) {  
        result.append(new String(arr, 0, count));//поскольку файл может весить меньше 1024 байт, указываем фактический размер массива  
        count = inputStream.read();  
    }  
    System.out.print(result);  
}catch (Exception e){  
    e.printStackTrace();  
}
```

Некоторые символы занимают больше 1 байта(кириллица), для этого нужно использовать специальную функцию которые считывает не байты, а символы.

```java
File file = new File("test.txt");  
try {  
    file.createNewFile();  
} catch (IOException e) {  
    e.printStackTrace();  
}  
try(Reader reader = new InputStreamReader(new FileInputStream(file))) {  
    int a = reader.read();//если хотим читать больше одного символа, то нужно создать массив char
    StringBuilder result = new StringBuilder();  
    while (a != -1) {  
        result.append((char) a);
        a = reader.read();  
    }  
    System.out.print(result);  
}catch (Exception e){  
    e.printStackTrace();  
}
```

## Write
```java
File file = new File(path);  
PrintWriter pw = new PrintWriter(file);  
pw.println("Test1");  
pw.println("Test2");  
//произошла перезапись файла  
pw.close();
```

```java
File file = new File("test.txt");  
try {  
    file.createNewFile();  
} catch (IOException e) {  
    e.printStackTrace();  
}  
try(OutputStream outputStream = new FileOutputStream(file, true)) {//добавили true чтобы не перезаписывать данные, а добавлять новые  
    String text = "\nHello godd, Hello godd, Hello godd,Hello godd ";  
    outputStream.write(text.getBytes());  
}catch (Exception e){  
    e.printStackTrace();  
}
```

Опять же, не так эффективно побайтово писать данные.

```java

```

# Create
```java
File directory = new File("folder");
File directory1 = new File("folder/folder/folder");
File file = new File("folder/test.txt");
try{
	directory.mkdir();//создаем папку
	directory1.mkdirs();//создаем иерархию папок
	file.createNewFile();
} catch(IOException e){
	e.printStackTrace();
}
```

# work with directory
```java
File[] files = directory.listFiles();
for(var f : files)
	System.out.prinln(f.getNam());

File[] files = directory.listFiles((dir, name) -> name.contains("1"));//нужны файлы которые содержат в имени 1
for(var f : files)
	System.out.prinln(f.getNam());

```

# Random access file
Используя обычный поток, после прочтения байта, мы не сможем к нему вернуться.

```java
File file = new File("test.txt");  
try (RandomAccessFile randomAccessFile = new RandomAccessFile(file, "rw")){  
    randomAccessFile.seek(1024);//переместились на 1024 символ  
    byte[] arr = new byte[1024];  
    randomAccessFile.read(arr);  
    System.out.println(new String(arr));  
    randomAccessFile.seek(0);//переместились к началу  
    randomAccessFile.read(arr);
    System.out.println(new String(arr));   
} catch(IOException e){  
    e.printStackTrace();  
}
```

