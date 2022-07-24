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

# Полезные ссылки
* [golang-developer-roadmap](https://github.com/Alikhll/golang-developer-roadmap)
* [awesome backend](https://github.com/zhashkevych/awesome-backend)
* [awesome go frameworks](https://github.com/avelino/awesome-go)