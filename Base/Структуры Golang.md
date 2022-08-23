# Структуры
```go
type Circle struct {
    x float64
    y float64
    r float64
}
type Circle struct { 
	x, y, r float64 
}

var c Circle 
c := Circle{}
c := new(Circle)
c := Circle{x: 0, y: 0, r: 5}
c := Circle{0, 0, 5}
fmt.Println(c.x, c.y, c.r) 
c.x = 10 
c.y = 5

x := c.x //копируем значение структуры c в x
x := &c.x //ссылаемся на значение структуры
x := new(c.x)//new возвращает указатель на x

var a = Android{
	Model: "model",
	Person: Person {
		Name: "name",
	},
}
```

# Инициализация структуры
В go обычно пишут метод инициализации структуры, поскольку ее данные могут быть приватными, поскольку написаны с маленькой буквы и в другом пакете мы просто не сможем ее определенным образом создать.

```go
type Client struct {
	id int64
	name string
}
func NewClient(id int64, name string) Client{
	return Client{id, name};
}
```

# Методы
```go
func (c *Circle) area() float64 {//указтель нужен для того, что работать с оригиналом, а не копией
    return math.Pi * c.r * c.r
}
fmt.Println(c.area())
```

Между ключевым словом `func` и именем функции мы добавили «получателя». Получатель похож на параметр — у него есть имя и тип, но объявление функции таким способом позволяет нам вызывать функцию с помощью оператора `.`

Нам не нужно использовать оператор `&` (Go автоматически предоставляет доступ к указателю на `Circle` для этого метода), и поскольку эта функция может быть использована только для `Circle` мы можем назвать её просто `area`.

# Пустая структура
```go
enptystruct := struct{}{} - весит 0 бит
```

# Встраиваемые типы(наследование)
Наследование поддерживается с помощью композиции анонимных полей.
```go
type Person struct {
    Name string
}
func (p *Person) Talk() {
    fmt.Println("Hi, my name is", p.Name)
}
type Android struct {
    Person Person//отношение has-a
    Model string
}
type Android struct {
    Person//отношение is-a - анонимное поле
    Model string
}
a := new(Android)
a.Person.Talk()
a.Talk()

vr := employee{}  
vr.name = "Tom" //относится к employee а не Person  
vr.Person.name = "Don"  
  
vr.Talk()  
vr.Person.Talk()
```

