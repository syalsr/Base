# rvalue lvalue reference
lvalue - то у чего есть location
rvalue - то чего нет в памяти(выражение к примеру)

Ссылка на rvalue принимает временный объект

```cpp
void g(const vector<int>& g) {
	vector<int> vv = g;
}

void f(vector<int>&& g) {
	vector<int> vv = move(g);
}

void k(vector<int> g) {
	vector<int> vv = move(g);
}

int main()
{
	vector<int> v = { 1,2,3,4,5,6,7 };
	g(v);//copy v to vv
	vector<int> v1 = { 1,2,3,4,5,6,7 };
	f(move(v1));//move v1 to vv; v1 empty
	f({ 1,2,3,4,5 });//move temporary to vv

	vector<int> v2 = { 1,2,3,4,5,6,7 };
	k(v2);//copy v2 to g and move to vv; vv = v2; v2 not empty
	k({1,2,3,4});//move to vv
}
```

Предпочтительнее передавать в функцию переменную по значению, если бы ее будем копировать, чтобы в дальнейшем мувнуть ее и на вызывающей стороне не потерять данные, но только если тип поддерживает move семантику


Именованная rvalue ref является lvalue, потому что у него есть имя

```cpp
void helper(std::string&& message) { }
void handleMessage(std::string&& message) { helper(message); }//error
void handleMessage(std::string&& message) { helper(std::move(message)); }
```

lvalue ссылка может указывать только на lvalue, в отличии от rvalue

```cpp
int& i { 2 }; // Invalid: reference to a constant
int a { 2 }, b { 3 };
int& j { a + b }; // Invalid: reference to a temporary

int&& i { 2 };
int a { 2 }, b { 3 };
int&& j { a + b };
```

Если rvalue ref присвоить временный объект, то время его жизни  - это время жизни rvalue ref, то есть пока она не покинет область видимости.