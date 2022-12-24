# Опасности return
Не всегда return оптимизируется хорошо.
Первый случай:
```cpp
pair MakeStreams (const string& prefix) { 
	ifstream input(prefix + ".in"); 
	ofstream output(prefix + ".out"); 
	return {input , output }; 
}
```

Код не скомпилируется, почему? Компилятор не может составить пару потоков. Потому что мы копируем поток в конструктор пары, нужно использовать `move`.

```cpp
pair MakeStreams (const string& prefix) { 
	ifstream input(prefix + ".in"); 
	ofstream output(prefix + ".out"); 
	return {move(input), move(output) }; 
	//либо 
	return {ifstream(prefix + ".in"), ofstream(prefix + ".out") }; //сначала идет перемещение в конструктор пары, а потом copy elision
}
```

Второй случай:
Если функция должна вернуть некоторый объект, например поток ввода:

```cpp
ifstream MakeInputStream (const string& prefix) { 
	auto streams = MakeStreams (prefix); 
	return streams.first; 
}
```

В данном случае мы пытаемся вернуть не локальную переменную, а поле локальной переменной, `streams.first` не является ни временным объектом, ни название локальной переменной.

Как решить?

```cpp
ifstream MakeInputStream (const string& prefix) { 
	auto streams = MakeStreams (prefix); 
	return move(streams).first; // stream ведет себя как временный объект и ее поля тоже временный объект
	//либо
	return move(streams.first);
	//либо
	return MakeInputStream(prefix).first;
}
```