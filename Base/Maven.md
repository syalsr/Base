# Maven
Сборщик Java проектов для упрощения управления зависимостями.

Идем сюда, ищем нужную библиотеку, к примеру [JUnit](https://mvnrepository.com/artifact/junit/junit/4.12), копируем код сборки и вставляем его в наш сборочный файл.
```
<dependecies>
	<dependency>
	    <groupId>junit</groupId>
	    <artifactId>junit</artifactId>
	    <version>4.12</version>
	    <scope>test</scope>
	</dependency>
</dependecies>
```

Между dependecies вставляем все наши зависимости.

* Maven & Gradle
	* maven https://www.youtube.com/playlist?list=PL1zJrLkuWT67KutVoHZ3EhGswNkMcRIX2
	* maven https://habr.com/ru/post/77382/
	* maven https://javarush.ru/groups/posts/2523-chastjh-4osnovih-maven
