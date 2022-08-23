# 20 Golang - статически типизированный язык
* [[Пакеты Golang]]
* [[Модули Golang]]
* Типы
	* [[Массивы Golang]]
	* [[map Golang]]
	* [[Строки Golang]]
	* [[Указатели Golang]]
	* [[Переменные Golang]]
* Основы
	* [[Функции Golang]]
	* [[Циклы Golang]]
	* [[Условные операторы Golang]]
	* [[Ошибки Golang]]
	* [[Файлы Golang]]
	* [[Ввод вывод Golang]]
	* [[Context Golang]]
* ООП
	* [[Интерфейсы Golang]]
	* [[Структуры Golang]]
* Многопоточность
	* [[Многопоточность Golang]]
	* [[Каналы Golang]]
* [[Тестирование Golang]]
* [[json Golang]]
* [[Работа Golang с базой данных]]
* [[Garbage collector]]

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

# Полезные ссылки
* [golang-developer-roadmap](https://github.com/Alikhll/golang-developer-roadmap)
* [awesome backend](https://github.com/zhashkevych/awesome-backend)
* [awesome go frameworks](https://github.com/avelino/awesome-go)
* [oh-my-backend](https://github.com/bzick/oh-my-backend#oh-my-backend)
* [Visualizing Concurrency in Go](https://divan.dev/posts/go_concurrency_visualize/)
* [Под капотом Golang — как работают каналы. Часть 1.](https://medium.com/@victor_nerd/%D0%BF%D0%BE%D0%B4-%D0%BA%D0%B0%D0%BF%D0%BE%D1%82%D0%BE%D0%BC-golang-%D0%BA%D0%B0%D0%BA-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%D1%8E%D1%82-%D0%BA%D0%B0%D0%BD%D0%B0%D0%BB%D1%8B-%D1%87%D0%B0%D1%81%D1%82%D1%8C-1-e1da9e3e104d)
* [Внутренеее устройство каналов в Golang. Часть 2.](https://medium.com/@victor_nerd/golang-channel-internal-part2-b4e37ad9a118)