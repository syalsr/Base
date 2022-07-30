# Exception handling Java
-   Иерархия исключений. Проверяемые и непроверяеме исключения. Ошибки JVM
-   Обработка и использование исключений. Try-catch
-   Логирование. Журналирование работы приложения

# Обработка
```java
public static test() throws FileNotFoundException{//говорим, что функция может выбросить исключение
	File file = new File(path);  
	Scanner scanner = new Scanner(file);
}

public static test() {
	try {
		File file = new File(path);  
		Scanner scanner = new Scanner(file);
	}catch(FileNotFoundException e){
		System.out.println("File not found");
	}
}
```

Можно создавать подряд несколько блоков catch для обработки разных типов исключения, но выше всех должен находиться обработчик  самого нижнего по иерархии классов, исключения.
# Выбрасывание
```java
public static test() throws FileNotFoundException{//говорим, что функция может выбросить исключение
	Scanner scanner = new Scanner(System.in);
	while(true){
		int x = Integer.ParseInt(scanner.nextLine());
		if(x < 0)
			throw new IOException();
	}
}

public static test() {
	Scanner scanner = new Scanner(System.in);
	while(true){
		int x = Integer.ParseInt(scanner.nextLine());
		if(x < 0){
			try {
				throw new IOException();
			}catch(IOException e){
				System.out.Println("Negative")
			}
		}
	}
}
```

# Custom exception
```java
class ScanException extends Exception{  
    public ScanException(String desc){  
        super(desc);//передаем в конструктор класса Exception описание исключения  
    }  
}
public static test() throws ScanException{
	Scanner scanner = new Scanner(System.in);
	while(true){
		int x = Integer.ParseInt(scanner.nextLine());
		if(x < 0)
			throw new ScanException("negative");
	}
}
```

# checked & unchecked
1. checked - compile-time - допустим, сканирование файла, файла может не существовать, поэтому java нас обязывает как-то обрабатывать эту ситуацию.
2. unchecked - run-time - ошибки такого типа состоят в логике программы, которые мы должны исправить - деление на ноль, вызов метода у null объекта, выход за границы массива.
