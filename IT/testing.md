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

# Параллельный запуск
```go
func TestBareer(t * testing.T){
	t.Parallel()
}
func TestSpin(t * testing.T){
	t.Parallel()
}
```
Теперь наши тесты будут выполняться параллельно