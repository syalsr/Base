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
```
func main() {
    err := errors.New("my error")
    fmt.Println("", err)
}
```

## panic
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

## Оператор defer

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

Здесь функция finish вызывается с оператором defer, поэтому данная функция в реальности будет вызываться в самом конце выполнения программы, несмотря на то, что ее вызов определен в начале функции main, а вывод с defer будет идти предпоследним