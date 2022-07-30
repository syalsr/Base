# Операторы
Вместо определения классовых операторов, нужно определять либо глобальные, либо определять методы(less, equal и т.д.). В случае с арифметическими операторами, чтобы избежать ошибок:

```cpp
struct Quat {  
    int x, y, z, w;  
    Quat(){}  
    Quat(int x){}  
    Quat &operator+=(const Quat &rhs);  
  
    Quat operator+(const Quat &rhs) {  
        Quat tmp(*this);  
        tmp += rhs;  
        return tmp;  
    }  
};  
  
  
int main() {  
    Quat x,y;  
    Quat t = x + 2; // ok, int -> Quat, есть конструкто который принимает int
    Quat t = 2 + x; // FAIL, у int нет оператора который бы принимал Quat
}
```
 Для решения, стоит определить вместо классового оператора, глобальный оператор с помощью оператора += который всегда определяется в классе.

Но если Quat у нас будет шаблоном класса, это не будет работать, компилятор не может преобразовать типы, а только подставить. Решение - определить операторы на все, что нужно

```cpp
template<typename T> struct Quat {  
    T x_, y_, z_, w_;  
  
    // implicit cast T -> Quat  
    Quat(T x = 0, T y = 0, T z = 0, T w = 0) : x_(x), y_(y), z_(z), w_(w) {}  
  
    // operator += in class  
    Quat& operator+=(const Quat& rhs) {  
        x_ += rhs.x_; y_ += rhs.y_; z_ += rhs.z_; w_ += rhs.w_;  
        return *this;  
    }  
};  
  
  
template <typename T>  
Quat<T> operator+(Quat<T> lhs, Quat<T> rhs) { Quat<T> tmp(lhs); tmp += rhs; return tmp; }  
  
template <typename T>  
Quat<T> operator+(T lhs, Quat<T> rhs) { Quat<T> tmp(lhs); tmp += rhs; return tmp; }  
  
template <typename T>  
Quat<T> operator+(Quat<T> lhs, T rhs) { Quat<T> tmp(lhs); tmp += rhs; return tmp; }  
  
  
int main() {  
    Quat<int> q;  
    Quat<int> p = q + 1;  
    Quat<int> t = 1 + q;
}
```

Перегруженные операторы должны определять в одном месте, то есть у нас 3 оператора +, они либо определяют глобально, либо в классе, раздельно не стоит.

# Перегрузка операторов
## Арифметические операторы
```cpp
SizesOfFigure operator+(const SizesOfFigure& lhs, const SizesOfFigure& rhs)
{
	auto result {lhs};
	lhs += rhs;
	return result;
}//глобальная функция
SizesOfFigure operator/(const SizesOfFigure& lhs, const SizesOfFigure& rhs)
{
	if(rhs.height == 0 || rhs.width == 0)
			throw invalid_argument{"Divide by zero"};
	auto result {lhs};
	lhs /= rhs;
	return result;
}
struct SizesOfFigure
{
	SizesOfFigure(size_t height, size_t width) {}
	SizesOfFigure operator+(const SizesOfFigure& fig) const
	{
		return SizesOfFigure{ height + fig.height, width + fig.width };
	}
	SizesOfFigure operator/(const SizesOfFigure& fig) const
	{
		if(fig.height == 0 || fig.width == 0)
			throw invalid_argument{"Divide by zero"};
		return SizesOfFigure{ height / fig.height, width / fig.width };
	}
	//operator - * as well as operator +
	
	SizesOfFigure& operator+=(const SizesOfFigure& fig)
	{
		height += fig.height; width += fig.width;
		return *this;
	}
	//operator -= *= /= as well as operator +
	size_t height{ 0 };
	size_t width{ 0 };
};
```

## Логические операторы
```cpp
bool operator==(const SizesOfFigure& lhs, const SizesOfFigure& rhs)
{
	return tie(height, width) == tie(height, width);
}//operator ><!= as well as operator +
bool operator>=(const SizesOfFigure& lhs, const SizesOfFigure& rhs)
{
	return !(lhs < rhs);
}//operator <= as well as operator >=
struct SizesOfFigure
{
	SizesOfFigure(size_t height, size_t width) {}
	size_t height{ 0 };
	size_t width{ 0 };
};
```

# addressof
Вместо того, чтобы полагаться на оператор & у класса, лучше использовать функцию `addressof` которая возвращает реальный адрес объекта

# Триплетный оператор C++20
https://www.youtube.com/watch?v=lHxf3_uHbKg&list=PL3BR09unfgciJ1_K_E914nohpiOiHnpsK&index=7 56:25
#do/start 