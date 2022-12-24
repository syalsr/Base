# Golang
# Указатели
```go
func main() {
        a := 200
        b := &a
        *b++
        fmt.Println(a)
       a := 200 
       var b *int = &a
}
```

Другой способ получить указатель — использовать встроенную функцию `new`:

```
func one(xPtr *int) {
    *xPtr = 1
}
func main() {
    xPtr := new(int)
    one(xPtr)
    fmt.Println(*xPtr) // x is 1
}
```
Также мы можем создать указатель на указатель.

```go
package main

import "fmt"

func main() {
   a := 200
   b := &a
   *b++
   c := &b
   **c++ // указатель на указатель
   fmt.Println(a)
}
```


```go
func NewClient(name string, age int) Client{
	return Client(name, age);//создаем структуру на стеке, возвращаем ее, стек очищается
}

func NewClient(name string, age int) *Client{//возвращаем указатель на структуру
	return &Client(name, age);//т.к. мы возвращаем указатель, то не можем создать ее на стеке, ибо она удалится, поэтому структура создается в куче.
	//либо можно написать так
	return new(Client(name, age));
}
```