# mutex и lock_guard
mutex нужен чтобы защитить критическую секцию при гонке данных.

```cpp
struct Account {
	int balance = 0; 
	vector history; 
	bool Spend(int value) 
	{ 
		if (value <= balance) 
		{ 
			balance −= value; 
			history.push_back(value); 
			return true; 
		} 
		return false; 
	} 
};
```

Может случиться так, что несколько функций параллельно будут использовать функцию Spend(), и соответственно, может случиться так, что они используют старые значения, нам нужно защитить ее, сделать так, если в критической секции уже есть поток, то второй поток ждет, пока первый выйдет из нее.

```cpp
struct Account {
	int balance = 0; 
	vector history;
	mutex m;
	bool Spend(int value) 
	{
		lock_guard<mutex> g(m);
		if (value <= balance) 
		{ 
			balance −= value; 
			history.push_back(value); 
			return true; 
		} 
		return false; 
	} 
};
```