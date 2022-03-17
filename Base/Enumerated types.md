Перечисление дает нам определять последовательность переменных со значениями. Вместо такого кода:

```cpp
const int PieceTypeKing { 0 };
const int PieceTypeQueen { 1 };
const int PieceTypeRook { 2 };
const int PieceTypePawn { 3 };
//etc.
int myPiece { PieceTypeKing };
```

Лучше написать такой:

```cpp
enum class PieceType { 
	King, 
	Queen, 
	Rook, 
	Pawn 
};
PieceType piece { PieceType::King };
```

По умолчанию значению нумеруются с 0, а далее инкрементируются

```cpp
enum class PieceType { 
	King = 1, 
	Queen, //2
	Rook = 10, 
	Pawn //11
};
PieceType piece { PieceType::King };

if(PieceType::Queen == 2) //automatic converted to int
```

По умолчанию значения int, но можно изменить
```cpp
enum class PieceType : unsigned long
{
	King = 1,
	Queen,
	Rook = 10,
	Pawn
};
```

В c++20 можно использовать using декларацию.

```cpp
using enum PieceType;
PieceType piece { King };
```