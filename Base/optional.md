Используется, к примеру, когда нужно проверить валидность объекта, т.е. он должен вернуть либо объект, либо ничего, если объект инвалидный.

```cpp
optional<InvalidDateReason> CheckDate(const Date& date) {
	if (date.year < 1) {
		return InvalidDateReason::NONPOSITIVE_YEAR;
	} else if (date.month < 1) {
		return InvalidDateReason::MONTH_TOO_SMALL;
	} else if (date.month > 12) {
		return InvalidDateReason::MONTH_TOO_BIG;
	} // ...
	return nullopt;
}

if (const auto invalid_date_reason = CheckDate(date)) {//если optional содержит значение, то при приведении к bool, вернет true
	LogFailure(date, *invalid_date_reason);//operator * - получить значение
	return;
}

```
`optional.value_or(0)` - возвращает либо значение, либо то что в скобках
# Эффективность
```cpp
optional<HeavyObject> ReturnTemporary() {
	return HeavyObject(/* ... */);// эквивалентно return optional<HeavyObject>(HeavyObject(/* ... */));
}

optional<HeavyObject> ReturnVariable() {
	HeavyObject heavy(/* ... */);
	return heavy;//эквивалентно return optional<HeavyObject>(heavy);
}
```

1.  optional инициализируется временным объектом, так что HeavyObject перемещается в объект optional.
2.  Из функции возвращается временный объект optional, так что срабатывает copy elision.

# Передача optional в функцию
Можем использовать optional, в случаях если нам надо знать передали ли значение в функцию

```cpp
void PrintNumbers(const vector<int>& numbers, optional<size_t> 
				  first_element_count = nullopt) 
{
	const size_t bound = min(numbers.size(),
	first_element_count.value_or(
	numeric_limits<size_t>::max()));
	for (size_t i = 0; i < bound; ++i)
		cout << numbers[i] << ' ';
}
```