# Golang
# Ошибки
В отличии от других языков, в Go функция может возвратить тип `error`, а мы должны проверить возвращаемое значение и принять соответствующие меры.

```go
package main

import "fmt"

func divide(a int, b int) int {
	return a / b
}

func main() {
	var input int
	_, err := fmt.Scan(&input) // функция Scan возвращает два параметра, но нам сейчас важно проверить только ошибку
	if err != nil {//nil - пустота, возвращая ошибку, в ней должно что-то содержаться
		fmt.Println("Проверьте типы входных параметров")
	} else {
		fmt.Println(divide(input, 5)) //Выведем результат, если ошибок нет
	}
}
```

Существуют две встроенные функции для создания ошибок: `errors.New` и `fmt.Errorf`.
```go
func checknum(x int) (valid bool, err error){
	if x > 0 && x > 10{
		return false, fmt.Errorf("positive: %v", x)
	}
}
func main() {
    err := errors.New("my error")
    fmt.Println("", err)
}
```


# panic
Оператор **panic** позволяет сгенерировать ошибку и выйти из программы:

```go
package main
import "fmt"
 
func main() {
    fmt.Println(divide(15, 5))
    fmt.Println(divide(4, 0))
    fmt.Println("Program has been finished")
}
func divide(x, y float64) float64{
    if y == 0{ 
        panic("division by zero!")
    }
    return x / y
}
```
 При панике вызываются деферы, потом программа завершается, поэтому панику можно обработать

```go
func somefunc(){
	defer fmt.Println("somefunc")
	panic("critical error")
}
func main(){
	defer func(){
		if err := recover(); err != nil{
			switch err{
			case "critical error":
				fmt.Println("so good")
			default:
				panic("fatal error")
			}
		}
	}()
	somefunc()
}
```


# Оператор defer
Оператор defer позволяет выполнить определенную операцию после каких-то действий (даже если сработает panic), при этом не важно, где в реальности вызывается эта функция. Например:
```go
package main
import "fmt"
 
func main() {
    defer finish()
    defer fmt.Println("Program has been started")
    fmt.Println("Program has been started")
    fmt.Println("Program is working")
}
 
func finish(){
    fmt.Println("Program has been finished")
}
```

Вызывается defer функции по принципу [[std stack|LIFO]]

Также оператор defer сохраняет состояние в момент его определения, но не после

```go
func print(i int){
	fmt.Println(i)
}
func main(){
	i := 1
	defer print(i)
	i++
	fmt.Println(i)
}
2
1
```

Обойти это можно при помощи замыкания

```go
func print(i int){
	fmt.Println(i)
}
func main(){
	i := 1
	defer func(){
		print(i)//теперь ей доступна эта область видимости
	}
	i++
	fmt.Println(i)
}
2
2
```

Существует два основных подхода: 
1) Exceptions (исключения) 
**Минусы:** что угодно может выкинуть исключение; нелокальная обработка, усложнение control flow (как goto); производительность 
**Плюсы:** удобство 

1.2) Unchecked + Checked exceptions 
**Минусы:** нелокальная обработка, усложнение control flow (как goto); производительность; не очень удобно 
**Плюсы:** ошибки - часть API 
2) Error codes (коды ошибок) 
**Минусы:** можно забыть обработать не всегда удобно 
**Плюсы:** ошибки - часть API локальная обработка, работа как с обычным значением
