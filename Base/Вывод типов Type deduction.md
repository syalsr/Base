# Вывод типов шаблонами
cv-квалификатор - const, volatile, внутренние cv не удаляются
1. Вывод уточненных типов
	1. Ссылки и cv-квалификаторы игнорируются для T, также работает и с auto
2. Вывод уточненных типов
	1. Уточняющая обвязка игнорируется для T, но cv-квалификаторы сохраняются
	2. Типичные уточнители это ссылка и указатель

```cpp
template<typename T> void foo (T t); // неуточнённый 
template<typename T> void bar (T& t); // ссылка 
template<typename T> void buz (T* t); // указатель
```
`

```cpp
template <typename T> void foo(T t);
template <typename T> void bar(T& t);
int x = 42;
int const& y = x;
int const* const z = &x;
foo(x); // → foo<int>(int)
foo(y); // → foo<int>(int)
foo(z); // → foo<int const *>(int const *)
bar(x); // → bar<int>(int&)
bar(y); // → bar<int const>(int const &)
bar(z); // → bar<int const * const>(int const * const &)

template <typename T> void foo(T t);
template <typename T> void buz(T* t);
int x = 42; int* px = &x;
int*& py = px;
int const* const pz = px;
foo(px); // → foo<int*>(int*)
foo(py); // → foo<int*>(int*)
foo(pz); // → foo<int const *>(int const *)
buz(px); // → buz<int>(int*)
buz(py); // → buz<int>(int*)
buz(pz); // → buz<int const>(int const * const)

template <typename T> void foo(T* const& t);
int x = 42;
int const& y = x;
int const* z = &x;
foo(&x); // → foo<int>(int * const &)
foo(&y); // → foo<int const>(int const * const &)
foo(z); // → foo<int const>(int const * const &)
```
