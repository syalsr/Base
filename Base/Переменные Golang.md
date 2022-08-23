# Переменные
Если мы не присваиваем значение, то переменные равны zero-value
```go
a := 5
var a int = 5
var a = 5
var (         
	name string = "Dima"         
	age int = 23     
)
```


# const
Вычисляются на этапе компиляции, если в константе присваиваем какое-то выражение, то сначала оно вычисляется в памяти, потом присваивается в переменную.
```go
const pi float64 = 3.1415 
pi = 2.7182           // ! Ошибка

package main  
import (  
   "fmt"  
)  
const(  
   A int = 45  
   B  
   C float32 = 3.3  
   D  
)  
func main() {  
   fmt.Println(A, B, C, D)  // Вывод: 45 45 3.3 3.3  
}
```

# iota
```go
package main  
  
import (  
   "fmt"  
)  
  
const (  
   Sunday = iota  
   Monday  
   Tuesday   
   Wednesday   
   Thursday   
   Friday   
   Saturday  
    _  
    _   
    Add)  
  
func main() {  
   fmt.Println(Sunday)   // вывод 0  
   fmt.Println(Saturday) // вывод 6  
   fmt.Println(Add)      // вывод 9

	const (  
	   u         = iota * 42 // u == 0 (индекс - 0, поэтому 0 * 42 = 0)  
	   v float64 = iota * 42 // v == 42.0 (индекс - 1, поэтому 1.0 * 42 = 42.0)  
	   w         = iota * 42 // w == 84  (индекс - 2, поэтому 2 * 42 = 84)  
)  
  
// переменные ни в одном блоке const, поэтому индекс не увеличился  
const x = iota  // x == 0  
const y = iota  // y == 0
}
```

```go
const (  
   Sunday = iota  
   Monday  
   Tuesday   Wednesday = 10  
   Thursday  
   Friday   Saturday   _   _   Add)  
  
func main() {  
   fmt.Println(Sunday)   // вывод 0  
   fmt.Println(Saturday) // вывод 10  
   fmt.Println(Friday)   // вывод 10  
   fmt.Println(Add)      // вывод 10  
}
```

# Преобразования
```go
var index int8 = 15  
var bigIndex int32  
bigIndex = int32(index)  
fmt.Println(bigIndex)         // 15  
fmt.Printf("%T \n", bigIndex) // int32  
  
var a int32 = 22  
var b uint64  
b = uint64(a)  
fmt.Println(b)         // 22  
fmt.Printf("%T \n", b) // uint64

var f float64 = 56.231 
var i int = int(f) 
fmt.Println(f) // 56.231 
fmt.Println(i) // 56

a := 5 / 2 
fmt.Println(a) // 2

a := 5.0 / 2 
fmt.Println(a) //2.5
```

Но при преобразовании нужно следить за тем, чтобы не происходило сужения
```go
fmt.Println(math.MaxInt8) // 127 
fmt.Println(math.MaxUint8) // 255 
fmt.Println(math.MaxInt16) // 32767 
fmt.Println(math.MaxUint16) // 65535
```

## Конвертация строк в байты/rune и обратно
Строка в Go это срез байтов, поэтому мы можем конвертировать байты в строку и наоборот:

```go
package main

import (
    "fmt"
)

func main() {
    a := "str"
    b := []byte(a)
    c := string(b)
    fmt.Println(a) // str
    fmt.Println(b) // [115 116 114] - побайтовый срез
    fmt.Println(c) // str

	a := "строка"  
	b := []rune(a) // срез рун  
	c := string(b)  
	fmt.Println(a) // строка  
	fmt.Println(b) // [1089 1090 1088 1086 1082 1072] - срез рун  
	fmt.Println(c) // строка
}
```

## Конвертация в строки
```go
user := "ученик"  
steps := 4  
  
fmt.Println("Поздравляю, " + user + "! Ты прошел " + steps + " шага по приведению типов.")
//ошибка из-за разных типов
```

Для решения проблемы нужно перевести число в строку

```go
package main  
  
import (  
   "fmt"  
   "strconv")  
  
func main() {  
   user := "ученик"  
	steps := 4  
  
	fmt.Println("Поздравляю, " + user + "! Ты прошел " + strconv.Itoa(steps) + " шага по приведению типов.")
}
```

```go
// приставка '0x' означает что число в шестнадцатеричной системе счисления  
var a int64 = 0xB // 'B' в шестнадцатеричной это 11 в десятичной системе  
fmt.Println(strconv.FormatInt(a, 10)) // 11  
fmt.Println(strconv.FormatInt(a, 16)) // b

var a uint64 = 10101 
res := strconv.FormatUint(a, 10) 
fmt.Println(res) // 10101

var a = true 
res := strconv.FormatBool(a) 
fmt.Println(res) // true 
fmt.Printf("%T", res) // string

var a float64 = 1.0123456789  
// 1 параметр - число для конвертации  
// fmt - форматирование  
// prec - точность (кол-во знаков после запятой)  
// bitSize - 32 или 64 (32 для float32, 64 для float64)  
fmt.Println(strconv.FormatFloat(a, 'f', 2, 64)) // 1.01  
// если мы хотим учесть все цифры после запятой, то можем в prec передать -1  
fmt.Println(strconv.FormatFloat(a, 'f', -1, 64)) // 1.0123456789  
// Возможные форматы fmt:  
// 'f' (-ddd.dddd, no exponent),  
// 'b' (-ddddp±ddd, a binary exponent),  
// 'e' (-d.dddde±dd, a decimal exponent),  
// 'E' (-d.ddddE±dd, a decimal exponent),  
// 'g' ('e' for large exponents, 'f' otherwise),  
// 'G' ('E' for large exponents, 'f' otherwise),  
// 'x' (-0xd.ddddp±ddd, a hexadecimal fraction and binary exponent), or  
// 'X' (-0Xd.ddddP±ddd, a hexadecimal fraction and binary exponent).  
var b float64 = 2222 * 1023 * 245 * 2 * 52  
fmt.Println(strconv.FormatFloat(b, 'e', -1, 64)) // 5.791874088e+10
```


## Конвертация из строки
При конвертации строки, которая не содержит в себе число - ваша программа выдаст вам ошибку.
```go
package main  
  
import (  
   "fmt"  
   "strconv")  
  
func main() {  
   foo := "10"  
   bar := "15"  
   barInt, err := strconv.Atoi(bar)  
   if err != nil {  
      panic(err)  
   }  
   fooInt, err := strconv.Atoi(foo)  
   if err != nil {  
      panic(err)  
   }  
   baz := barInt - fooInt  
   fmt.Println(baz) //5  
}
```

```go
s := "23.23456"  
// как и в прошлом шаге, здесь 2 параметр - bitSize  
// bitSize - 32 или 64 (32 для float32, 64 для float64)  
// но нужно понимать что метод все равно вернет float64  
result, err := strconv.ParseFloat(s, 64)  
if err != nil {  
   panic(err)  
}  
fmt.Println(result)                   // 23.23456  
fmt.Printf("%T \n", result)  // float64  
  
// Конкретный пример для разных bitSize:  
s = "1.0000000012345678"  
//  не будем обрабатывать ошибки в примерах, но на практике так не делайте ;)  
result32, _ := strconv.ParseFloat(s, 32)  
result64, _ := strconv.ParseFloat(s, 64)  
fmt.Println("bitSize32:", result32)  // вывод 1 (не уместились)  
fmt.Println("bitSize64:", result64)  // вывод  1.0000000012345678

s := "true"  
res, err := strconv.ParseBool(s)  
if err != nil { // не забываем проверить ошибку  
   panic(err)  
}  
fmt.Println(res)      // true  
fmt.Printf("%T", res)  // bool
```
Так же по аналогии с примерами выше есть методы ParseUint, ParseInt, ParseBool.

