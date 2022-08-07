# Лямбда выражения Java
Это замена анонимным классам.

```java
Thread thread = new Thread(() ->
						  {
							  System.out.println("Hello");
						  });
```

```java
interface Print{
	void print(String str);
}
public void print(Print f)
{}
print(String str -> System.out.println(str));

interface Print{
	void print(String str1, String str2);
}
print((String str1, String str2) -> System.out.println(str1 + str2));
```

Если мы хотим получим доступ к данным вне лямбды, оно должно быть [[final]], либо не меняться после определения

```java
int a = 1;
print(()-> a + 1);

final int a = 1;
print(()-> a + 1);

int a = 1;
a = 2;
print(()-> a + 1);//error

```

У лямбд область видимости та в которой она определена.

```java
public void func()
{
int a = 1;
print(()-> 
	 {
		 int a = 2;//error, переменная с именем a уже определена
	 });
}
```

# [[Идиома immediately invoked lambda expression (IILE)]]