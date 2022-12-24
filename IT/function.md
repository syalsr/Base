# Golang
# Функции
В Go функции являются объектами первого класса - т.е. их можно передать как параметры в функцию и вернуть из функции.
```go
package main
import "fmt"
 
func main() {
    add(4, 5)   // x + y = 9
    add(20, 6)  // x + y = 26
}
 
func add(x int, y int){
    var z = x + y
    fmt.Println("x + y = ", z)
}
func add(x, y int, a, b, c float32){
    var z = x + y
    var d = a + b + c
    fmt.Println("x + y = ", z)
    fmt.Println("a + b + c = ", d)
}
func add(x, y int) int {
    return x + y
}
func add(x, y int, firstName, lastName string) (int , string) {
    var z int = x + y
    var fullName = firstName + " " + lastName
    return z, fullName 
    // Использование - var age, name = add(4, 5, "Tom", "Simpson")
    //                     age, name := add(4, 5, "Tom", "Simpson")
}

func sumInt(n ...int ) (c, s int) { 
	for _, e := range n { 
		c++ 
		s+=e 
	} 
	return 
}
```

Также как и в цикле range for, мы также можем игнорировать возвращаемые значения функций:
```go
func fn() (int, error) {
	// Какая-то полезная работа
	// ...
	return 0, nil
}
func ExampleIgnor() {
	fn()

	i, _ := fn()
	fmt.Println(i)

	_, err := fn()
	if err == nil {
		fmt.Println("Ошибок нет")
	}

	// Output:
	// 0
	// Ошибок нет
}
```

## variadic аргументы функции

```go
func Print(a ...interface{}) (n int, err error)
```
Символ многоточия перед указанием типа указывает, что в функцию можно передать произвольное количество параметров указанного типа. Аргумент с именем a воспринимается функцией как срез элементов заданного типа interface{}, внутри функция работает с элементами этого среза соответствующим образом.

Если функция принимает определенные аргументы и еще произвольное число аргументов определенного типа, то при объявлении функции эти «переменные» аргументы указываются в самом конце. 
```go
func Fprint(w io.Writer, a ...interface{}) (n int, err error)
```

# Функции как объекты первого класса
```go
func Map(mapping func(rune) rune, s string) string
//передаем в функцию Map, функцию имющую следю сигнатуру func(rune) rune - т.е. принимает тип rune и возвращает его же

func invert(r rune) rune {  
   // Если буква строчная, то она возвращается заглавной  
   if unicode.IsLower(r) {  
      return unicode.ToUpper(r)  
   }  
   // Иначе возвращается строчной  
   return unicode.ToLower(r)  
}  
func ExampleFirstClassFunctionArgument() {  
   src := "aBcDeFg"  
   test := "AbCdEfG"  
  
   // Обратите внимание, что скобки после имени функции используются только при ее вызове  
	src = strings.Map(invert, src)  
	fmt.Printf("Инвертированная строка: %s. Результат: %v.\n", src, src == test)    
   // Инвертированная строка: AbCdEfG. Результат: true.
}
func returnFunction() func(rune) rune { 
	return invert 
}
```

# Анонимные функции
```go
func ExampleFunctionWithoutName() {  
   src := "aBcDeFg"  
   test := "AbCdEfG"  
  
   // Обратите внимание, что скобки после имени функции используются только при ее вызове  
   src = strings.Map(func(r rune) rune {  
      if unicode.IsLower(r) {  
         return unicode.ToUpper(r)  
      }  
      return unicode.ToLower(r)  
   }, src)  
  
   fmt.Printf("Инвертированная строка: %s. Результат: %v.\n", src, src == test)    
   // Инвертированная строка: AbCdEfG. Результат: true.
}
```

```go
func ExampleUseNoNameFunction() {  
   // Присваиваем переменной значение анонимной функции  
   fn := func(a, b int) int { return a + b }  
  
   // Выполняем анонимную функцию на месте  
   // Обратите внимание на использование скобок при вызове функции   func(a, b int) {  
      fmt.Println(a + b)  
   }(12, 34)  
  
   fmt.Println(fn(17, 15))  
  
   // Output:  
   // 46   
   // 32
}
```

Анонимная функция имеет доступ к окружению, в котором она была объявлена
```go
func externalFunction() func() {  
   text := "TEXT"  
  
   return func() {  
      fmt.Println(text)  
   }  
}  
  
func ExampleEnvironment() {  
   fn := externalFunction()  
   fn()   
   // TEXT
}
```

Когда анонимная функция использует переменные, объявленные за ее рамками, ее называют замыканием.

```go
func ExampleClosure() {  
   fn := func() func(int) int {  
      count := 0  
      return func(i int) int {  
         count++  
         return count * i  
      }  
   }()  
  
   for i := 1; i <= 5; i++ {  
      fmt.Println(fn(i))  
   }  
   // 1  
   // 4   // 9   // 16   // 25}
```