# Интерфейсы Golang
Интерфейсы представляют собой новый - абстрактный тип данных, интерфейс определяет методы которым обязан обладать объект.
```go
type calculator interface {
	add(float64, float64) float64
	subtract(float64, float64) float64
	multi(float64, float64) float64
	div(float64, float64) float64
}
type myCalc struct{}

func (m myCalc) add(a, b float64) float64 {
	return a + b
}

func (m myCalc) subtract(a, b float32) float64 {
	return float64(a - b)
}

func (m myCalc) multi(a, b float64) float64 {
	return float64(a * b)
}

func (m myCalc) div(a, b float64) float64 {
	return a / b
}
```

# Пустой интерфейс
Интерфейс, который не содержит ни одного метода называется пустым интерфейсом: interface{}. Пустой интерфейс может содержать значение любого типа. Пустые интерфейсы используются в коде, где необходимо работать со значениями неизвестного типа.

# Встраивание интерфейса
Мы можем объединять интерфейсы, чтобы объекты соответствовал двум и более интерфейсам.
```go
type ReadWriter interface {
    Reader  // это io.Reader
    Writer  // это io.Writer
}
```

# Приведение типов

```go
// Конструкция приведения типа:
// t, ok := i.(T)
var i interface{} = 12
if v, ok := i.(int); ok {
	fmt.Println(v+12) // Суммирование не произойдет, если ok == false
}
```
#do/continue найти в статьях более понятное объяснение всему этому

Если мы не знаем тип объекта, можем воспользоваться конструкцией переключатель типов.
```go
package main

import "fmt"

func do(i interface{}) {
	switch v := i.(type) {
	case int:
		fmt.Println("Умножим на 2:", v*2)
	case string:
		fmt.Println(v + " golang")
	default:
		fmt.Printf("Я не знаю такого типа %T!\n", v)
	}
}

func main() {
	do(21)
	do("hello")
	do(true)
	Умножим на 2: 42
	hello golang
	Я не знаю такого типа bool!

}
```