# Move-итераторы
`make_move_iterator` возвращает обёртку над итератором, которая, если мы хотим скопировать объект, перемещает его. Функция make_move_iterator меняет семантику итератора, чтобы при обращении к нему данные перемещались бы, а не копировались. Для этого у объекта должны быть определены мув конструктор и мув присваивание

```cpp
class MoveableClass
{
public:
	MoveableClass() {
		cout << "Default constructor" << endl;
	}
	MoveableClass(const MoveableClass& src) {
		cout << "Copy constructor" << endl;
	}
	MoveableClass(MoveableClass&& src) noexcept {
		cout << "Move constructor" << endl;
	}
	MoveableClass& operator=(const MoveableClass& rhs) {
		cout << "Copy assignment operator" << endl;
		return *this;
	}
	MoveableClass& operator=(MoveableClass&& rhs) noexcept {
		cout << "Move assignment operator" << endl;
		return *this;
	}
};

int main()
{
	MoveableClass mv;
	vector<MoveableClass> vec;
	for (size_t i = 0; i < 10; i++)
	{
		vec.push_back(mv);
	}
	cout << "all\n";
	vector<MoveableClass> newvec{	make_move_iterator(begin(vec)), 
									make_move_iterator(end(vec)) };
	//Либо так
	move(begin(vec), end(vec), begin(newvec));
}
```