# Указатель this
Указатель `*this` - этот указатель объекта на самого себя. Нужен к примеру для реализации конструкторов копирования, move, оператора присваивания... 

```cpp
template<typename T>
SimpleVector<T>& SimpleVector<T>::operator=(const SimpleVector<T>& other) { 
	if(this != &other) { 
		delete[] data; 
		data = new T[other.capacity]; 
		size = other.size; 
		capacity = other.capacity; 
		copy(other.begin(), other.end(), begin()); 
	}
	return *this;
}

template<typename T> 
SimpleVector<T>& SimpleVector<T>::operator=(SimpleVector<T>&& other) { 
	if (this != &other) { 
		delete[] data; 
		data = other.data; 
		size = other.size; 
		capacity = other.capacity;

		other.data = nullptr; 
		other.size = other.capacity = 0; 
	}
	return *this;
}
```

```cpp
void printval(const Sheet& sh)
{
	cout << sh.GetVal() << endl;
}
void Sheet::SetValue(int val)
{
	this->val = val;
	printval(*this);
}
```

Указатель `this` является неявным параметром всех методов класса.