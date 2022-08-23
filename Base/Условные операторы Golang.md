# if
```go
a := 6  
b := 7  
if a < b {  
   fmt.Println("a меньше b")  
} else if a > b {  
   fmt.Println("a больше b")  
} else {  
   fmt.Println("a равно b")  
}

if v := math.Pow(x, n); v < lim {  
	// ...  
}
```

# switch
Всегда выполняется один case(если нет fallthrough), поэтому break не нужно писать.
```go
switch i {  // i - может быть любым типом данных, значения case-ов не должны повторяться
   case 0: fmt.Println("Zero")  
   case 1: fmt.Println("One")  
   case 2: fmt.Println("Two")  
   case 3: fmt.Println("Three")  
   case 4: fmt.Println("Four")  
   case 5: fmt.Println("Five")  
   default: fmt.Println("Unknown Number")  
}
```
Если в текущем `case` написать `fallthrough`, то тело следующего `case` выполнится вне зависимости от того истинно ли его (следующего `case`) условие:

```go
v := 42  
switch v {  
case 100:  
   fmt.Println(100)  
   fallthrough  
case 42:  
   fmt.Println(42)  
   fallthrough  
case 1:  
   fmt.Println(1)  
   fallthrough  
default:  
   fmt.Println("default")  
}  
// Вывод:  
// 42  
// 1  
// default
```

```cpp
var c uint32  
fmt.Scan(&c)  
switch {  
case 1 <= c && c <= 9:  
   fmt.Println("от 1 до 9")  
case 100 <= c && c <= 250:  
   fmt.Println("от 100 до 250")  
case 1000 <= c && c <= 6000:  
   fmt.Println("от 1000 до 6000")  
}
```
