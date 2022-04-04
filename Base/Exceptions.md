Исключение это неожиданная ситуация, допустим при делении знаменатель $=0$, возникает исключительная ситуация которую нужно обработать.

```cpp
double divideNumbers(double numerator, double denominator)
{
	if (denominator == 0) {
		throw invalid_argument { "Denominator cannot be 0." };
	}
	return numerator / denominator;
}
```

Если в место вызова этой функции добавить блок try/catch то программа не упадет, а вернет нам ошибку.

```cpp
try {
	cout << divideNumbers(2.5, 0.5) << endl;
	cout << divideNumbers(2.3, 0) << endl;
	cout << divideNumbers(4.5, 2.5) << endl;
} catch (const invalid_argument& exception) {
	cout << format("Exception caught: {}", exception.what()) << endl;
}
//5
//Exception caught: Denominator cannot be 0.
```