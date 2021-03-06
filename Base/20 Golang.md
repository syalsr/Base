# 20 Golang - статически типизированный язык
* [[Пакеты Golang]]
* [[Модули Golang]]
* [[Массивы Golang]]
* [[Функции Golang]]
* [[Файлы Golang]]
* [[Многопоточность Golang]]
* [[Интерфейсы Golang]]
* [[Указатели Golang]]
* [[Ошибки Golang]]
* [[Структуры Golang]]
* [[Ввод вывод Golang]]
* [[Строки Golang]]
* [[Условные операторы Golang]]
* [[Циклы Golang]]
* [[Переменные Golang]]
* [[map Golang]]
* [[json Golang]]
* [[Работа Golang с базой данных]]
* [[Тестирование Golang]]

# Проект
Отсюда берем архив - https://go.dev/dl/
```
wget https://go.dev/dl/go1.18.4.linux-amd64.tar.gz
tar -vfx go1.18.4.linux-amd64.tar.gz
sudo mv go /usr/local
rm -rf go1.18.4.linux-amd64.tar.gz
```

Добавить в bashrc
```
go env -w GO111MODULE=off //сначала добавлять без этого параметра, если будут какие-то ошибки, попробовать его добавить
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```

Разработка ведется в `$HOME/go/src/project_name`

# go mod
Этот файл нужен, что создавать модули

```
test_go
|______
		hel
		|_______
				hel.go
		hello.go
		go.mod
cd ~/test_go && go mod init test_go
hello.go
package main  
  
import "test_go/hel"  
  
func main() {  
   hel.Print1()  
}

go.mod
module Test_GO  
  
go 1.18
```

Стоит помнить, если функции начинаются с маленькой буквы, то мы их не видим

# require
Можем указать какие библиотеки необходимы проекту

```
module test

go 1.18

require(
	github.com/fatih/color v1.9.0
)
```

## Сторонние библиотеки
```
go get github.com/fatih/color
```

# Полезные ссылки
* [golang-developer-roadmap](https://github.com/Alikhll/golang-developer-roadmap)
* [awesome backend](https://github.com/zhashkevych/awesome-backend)
* [awesome go frameworks](https://github.com/avelino/awesome-go)
* [oh-my-backend](https://github.com/bzick/oh-my-backend#oh-my-backend)