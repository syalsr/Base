# Hash tables
В упрощенном виде, хеш-таблица - это словарь(одно значение связывается с другим), мы хотим положить в него значение, для этого мы передаем его в хеш-функцию, она определяет его порядковый номер и кладет его туда.

# Hash functions
unordered контейнеры используют хеш-функции для составления хеш-таблицы на основе которых хранятся данные. Допустим нам нужно хранить автомобильные номера, в таком случае хеш-функцией может выступать номер региона, если все номера будут одного региона, то все они будут лежать в одной корзине, что будет крайне не производительно при поиске, а если мы будем умножать простое число на каждый элемент номера, то получится уникальное число.

Хеш-таблица состоит из нескольких корзин по которым раскладываются объекты на основе хеш-функции. Если корзина занята другим объектом, то происходит коллизия. Для разрешения данной проблем используется метод цепочек, корзина состоит из списка объектов, сначала проверяется, что объекта нет в корзине, потом он добавляется по цепочке. Чем длиннее цепочка, тем менее производительнее таблица.

![[../Files/Pasted image 20220408083258.png]]

Для того, чтобы цепочки в корзине были короткими при добавлении нового объекта может случиться рехеширование в результате чего итератор инвалидируются. Удалять итераторы можно не опасаясь за итераторы на другие элементы.

Стандарт C++ для всех встроенных типов данных представляет хеш функции, а также для `error_code, error_condition, optional, variant, bitset, unique_ptr, shared_ptr, type_index, string, string_view, vector<bool>, and thread::id`. Если в ассоциативном контейнере мы используем пользовательский тип данных, то хеш-функцию должны написать сами. Для встроенных типов данных уже есть хеш-функции(`std::hash<double>`).

Чтобы в каждой корзине лежало как можно меньше объектов, нужно подумать над тем как написать хэш для собственных типов данных, идеальный loadfactor - 1, т.е. загруженность корзины.

```cpp
struct Plate { 
	char C1; 
	int Number; 
	char C2; 
	char C3;
	int Region; 
};
bool operator==(const Plate& l, const Plate& r) { 
	return (l.C1 == r.C1) && (l.Number == r.Number) && (l.C2 == r.C2) && (l.C3 == r.C3) && (l.Region == r.Region); 
}
struct PlateHasher {
	size_t x = 9973;
	size_t operator() (const Plate& p) const { 
		size_t result = x * x * ihash(p.Number) + x * ihash(p.Region) + x * chash(p.C1) + x * chash(p.C2) + x * chash(p.C3);
		return result;
	}
	hash<int> ihash;
	hash<char> chash;
};
int main() {
	unordered_set<Plate, PlateHasher> h_plates; 
}
```

```cpp
struct S { std::string first_name, last_name; };
struct SHash
{
	size_t operator()(const S& s) const noexcept
	{
		std::hash<std::string> h;
		auto h1 = h(s.first_name), h2 = h(s.last_name);
		return h1^(h2<<1);
	}
}
```

Каждый unodreder container дает возможность смотреть его статистику:
1. bucket_count() ― количество бакетов 
2. max_bucket_count() ― максимальное количество бакетов без реаллокаций 
3. bucket_size(n) ― размер бакета с номером n 
4. bucket(Key) ― номер бакета для ключа Key 
5. load_factor() ― среднее количество ключей в бакете 
6. max_load_factor() ― максимальное количество ключей в бакете, при достижение контейнер рехешируется, по умолчанию 1

```cpp
struct Foo {  
    float x = 0.0f, y = 0.0f, z = 0.0f;  
};  
  
template <typename T> auto umap_size(const T& umap) {  
    size_t count = 0;  
    for (unsigned i = 0; i < umap.bucket_count(); ++i) {  
        size_t bucket_size = umap.bucket_size(i);  
        count += (bucket_size + 1);  
    }  
    return count;  
}  
  
template <typename T> void snapshot(const T& m1, const T& m2) {  
    std::cout << "#1: size = " << umap_size(m1)  
              << ", bcnt = " << m1.bucket_count() << std::endl;  
    std::cout << "#2: size = " << umap_size(m2)  
              << ", bcnt = " << m2.bucket_count() << std::endl;  
}  
  
int main () {  
    std::unordered_map<int, Foo> mapNoReserve, mapReserve;  
    snapshot(mapNoReserve, mapReserve);  
    mapReserve.reserve(1000);  
    std::cout << "After reserve:" << std::endl;  
    snapshot(mapNoReserve, mapReserve);  
    for(uint32_t i = 0; i < 1000; ++i) {  
        mapNoReserve.emplace(i, Foo{});  
        mapReserve.emplace(i, Foo{});  
    }  
    std::cout << "After fill in:" << std::endl;  
    snapshot(mapNoReserve, mapReserve);  
    mapNoReserve.rehash(1000);  
    std::cout << "After rehash:" << std::endl;  
    snapshot(mapNoReserve, mapReserve);  
    //#1: size = 1, bcnt = 1  
    //#2: size = 1, bcnt = 1    
    //After reserve:    
    //#1: size = 1, bcnt = 1    
    //#2: size = 1031, bcnt = 1031    
    //After fill in:    
    //#1: size = 2109, bcnt = 1109    
    //#2: size = 2031, bcnt = 1031    
    //After rehash:    
    //#1: size = 2031, bcnt = 1031    
    //#2: size = 2031, bcnt = 1031
}
```

```cpp
int main () {  
    std::unordered_map<int, int> m;  
    m.reserve(10);  
    m.max_load_factor(10.0);  
    for (int i = 0; i < 100; ++i)  
        m.insert(std::make_pair(i, i));  
  
    for (auto it = m.begin(); it != m.end(); ++it)  
        std::cout << it->first << " ";  
    std::cout << std::endl;  
  
    for (int i = 0; i < m.bucket_count(); ++i) {  
        std::cout << "Bucket #" << i << std::endl;  
        for (auto it = m.begin(i); it != m.end(i); ++it)  
            std::cout << it->first << " ";  
        std::cout << std::endl;  
    }  
    //98 87 76 65 54 43 32 21 10 97 86 75 64 53 42 31 20 9 96 85 74 63 52 41 30 19 8 95 84 73 62 51 40 29 18 7 94 83 72 61 50 39 28 17 6 93 82 71 60 49 38 27 16 5 92 81 70 59 48 37 26 15 4 91 80 69 58 47 36 25 14 3 90 79 68 57 46 35 24 13 2 89 78 67 56 45 34 23 12 1 99 88 77 66 55 44 33 22 11 0   
    //Bucket #0  
    //99 88 77 66 55 44 33 22 11 0    
    //Bucket #1  
    //89 78 67 56 45 34 23 12 1    
    //Bucket #2  
    //90 79 68 57 46 35 24 13 2    
	//Bucket #3  
    //91 80 69 58 47 36 25 14 3    
    //Bucket #4  
    //92 81 70 59 48 37 26 15 4    
    //Bucket #5  
    //93 82 71 60 49 38 27 16 5    
    //Bucket #6  
    //94 83 72 61 50 39 28 17 6    
    //Bucket #7  
    //95 84 73 62 51 40 29 18 7    
    //Bucket #8  
    //96 85 74 63 52 41 30 19 8    
    //Bucket #9  
    //97 86 75 64 53 42 31 20 9    
    //Bucket #10  
    //98 87 76 65 54 43 32 21 10
}
```

# Представление в памяти
На самом деле в распространённых реализациях (libstdc++, etc) таблица представлена списком элементов, каждый из которых хранит свой хеш и вектором указателей на начало блока.
![[../Files/Pasted image 20220425162941.png]]
Элемент хранятся в forward_list, каждый имеет хэш, обозначенный цветом. Итераторы инвалидируются только во время рехэша.

# Требование к предикату сравнения
Общая концепция называется strict weak ordering. Она включает:
1. Антисимметричность: $pred(x, y) =>\neg pred(y, x)$ 
2. Транзитивность: $pred(x, y) \land pred(y, z) => pred(x, z)$ 
3. Иррефлексивность: $\neg pred(x, x)$
4. Транзитивность эквивалентности: $eq(x, y) \equiv \neg pred(x, y) \land \neg pred(y, x)├ eq(x, y) \land eq(y, z) => eq(x, z)$

Эта же концепция распространяется на предикаты в алгоритмах сортировки