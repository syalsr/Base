# Тестирование Golang
Тестирующие функции начинаются со слова Test, дальше идет имя тестируемой функции и специальный параметр
```go
func TestBareer(t * testing.T){
	token, err := Bareer("bar")
	if err != nil{
		t.Errorf("")
	}
}
```

Запуск 
```
go test
```