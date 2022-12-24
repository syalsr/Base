# Идиома immediately invoked lambda expression (IILE)

# C++
С помощью данной идиомы мы можем выполнить инициализацию объекта на момент его создания, как со списком инициализации.

```cpp
const vector sorted_numbers = [] { 
	vector<int> data = Sorted({5, 4, 2, 1, 5, 1, 3, 4, 5, 6, 8}); 
	auto it = unique(begin(data), end(data)); 
	data.erase(it, end(data)); 
	return data; 
}();
```

Создаем лямбда-функция и сразу ее вызываем.

Также удобно замерять время создания объекта.

```cpp
const vector sorted_numbers = [] { 
	LOG_DURATION("Sorted numbers build"); 
	return Sorted({5, 4, 2, 1, 5, 1, 3, 4, 5, 6, 8}); 
}();
```

IILE никак не сказывается негативно на скорости работы вашей программы.

Не рекомендуется использовать `auto` у инициализированного объекта, нужно указывать тип явно, чтобы читатель понимал, что мы это ILLE, а не лямбда-функция которую в дальнейшем будем использовать.

# Java
## map
```java
int[] arr = new int[10];
List<Integer> list = new ArrayList<>();
//...каким-то образом заполняем контейнеры
arr = Arrays.stream(arr).map(a -> a * 2).toArray();
list = list.stream().map(a -> a * 2).collect(Collectors.toList());

arr = Arrays.stream(arr).map(a -> 5).toArray();//все элементы стали 5
```

## map
```java
arr = Arrays.stream(arr).filer(a -> a % 2 == 0).toArray();//остались четные числа
```

## forEach
```java
Arrays.stream(arr).forEach(a -> System.out.println(a)).toArray();
```

## reduce
Нужен для того, чтобы как-то посчитать все элементы коллекции
```java
int sum = Arrays.stream(arr).reduce(0, (acc, b)-> acc + b).getAsInt();//сумма элементов
int sum = Arrays.stream(arr).reduce((acc, b)-> acc * b).getAsInt();//произведение элементов
```
0 - начальное значение аккумулятора, если не писать, то оно будет равно первому элементу.
b - текущий элемент