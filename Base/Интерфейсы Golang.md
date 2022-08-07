# Интерфейсы Golang
Интерфейсы представляют собой новый - абстрактный тип данных, интерфейс определяет методы которым обязан обладать объект.
```go
type Article struct {  
   Title  string  
   Author string  
}  
type Another struct {  
   Title  string  
   Author string  
}  
  
type Stringer interface {  
   String() string  
}  
  
func (a Article) String() string {  
   return fmt.Sprintf("The %q article was written by %s.", a.Title, a.Author)  
}  
  
func test(s Stringer) {  
   s.String()  
}  
  
func main() {  
   a := Article{  
      Title:  "Understanding Interfaces in Go",  
      Author: "Sammy Shark",  
   }  
   aa := Another{}  
   test(a)  
   test(aa)//структура Another не определяет метод String(), поэтому ошибка  
}
```

```go
type stardater interface {
    YearDay() int
    Hour() int
}

// stardate возвращает выдуманное измерение времени.
func stardate(t stardater) float64 {
    doy := float64(t.YearDay())
    h := float64(t.Hour()) / 24.0
    return 1000 + doy + h
}
```
# Пустой интерфейс
Интерфейс, который не содержит ни одного метода называется пустым интерфейсом: interface{}. Аналог [[std any]].  Пустой интерфейс может содержать значение любого типа. Пустые интерфейсы используются в коде, где необходимо работать со значениями неизвестного типа.

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

func printType(t interface{}){
	switch t := t.(type){
	case product.SportCat:
		fmt.Println("Sport car")
	default:
		fmt.Println("unexpected type")
	}
}
```

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