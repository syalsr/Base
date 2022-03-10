# async и future
```cpp
int SumToVectors (const vector & one , const vector & two) { 
	return accumulate (begin(one), end(one), 0) + accumulate (begin(two), end(two), 0); 
}
```

С помощью асинхронных операций этот код можно ускорить.
Как этот код работает? Сначала суммируются элементы первого вектора, потом второго. Можно запустить асинхронные вычисления первого вектора и синхронные второго, т.е. они будут вычисляться одновременно.

```cpp
future<int> f = async ([&one] { 
	return accumulate (begin(one), end(one), 0); 
});
int result = accumulate (begin(two), end(two), 0);
return result + f.get ();
```

# Задача генерации и суммирования матрицы

```cpp
template<typename ContainerOfVectors>
void GenerateSingleThread(ContainerOfVectors& result, size_t first_row , size_t column_size) { 
	for (auto& row : result) 
	{ 
		row.reserve(column_size);
		for (size_t column = 0; column < column_size ; ++ column) 
		{
			row.push_back ( first_row ^ column); 
		} 
		++first_row; 
	} 
}

vector<vector<int>> GenerateMultiThread (size_t size, size_t page_size) 
{ 
	vector<vector<int>> result(size); 
	vector<future<void>> futures; 
	size_t first_row = 0; 
	for (auto page : Paginate(result , page_size )) { 
		futures.push_back(async([page , first_row , size] { 
			GenerateSingleThread (page , first_row , size); 
		})); 
		first_row += page_size; 
	} 
	return result; 
}
```

# Особенности шаблона future
Если результат вызова функции async не сохранить в переменную, то программа будет выполняться последовательно.