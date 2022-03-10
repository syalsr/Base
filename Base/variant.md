Нужен когда функция должна вернуть объект, либо причину не возврата, т.е. какой-то текст ошибки. Хранит либо один тип, либо другой.

```cpp
enum class FailReason {
	ZERO_BALANCE,
	LOW_BID,
	INVALID_CURRENCY,
	// ...
};

variant<uint64_t, FailReason> ComputeCost(const Banner& banner) {
	if (banner.balance <= 0) {
		return FailReason::ZERO_BALANCE;
	} else if (banner.bid < MIN_BID) {
		return FailReason::LOW_BID;
	} else if (!IsCurrencyValid(banner.currency)) {
		return FailReason::INVALID_CURRENCY;
	}
	// ...
	return ConvertBid(min(banner.bid, banner.balance), banner.currency);
}

//...
optional<uint64_t> cost;

if (const auto cost_or_failure = ComputeCost(banner);
	holds_alternative<FailReason>(cost_or_failure)) {
	LogFailure(banner, get<FailReason>(cost_or_failure));
} else {
	cost = get<uint64_t>(cost_or_failure);
}
```
-   `holds_alternative<T>(v)` проверяет, правда ли объект v типа variant сейчас хранит в себе объект типа T.
- `get<T>(v)` возвращает ссылку на хранящийся в v объект типа T и бросает исключение в том случае, если v сейчас хранит объект другого типа.
- Не стоит использовать variant с типами, которые могут неявно приводиться друг к другу.