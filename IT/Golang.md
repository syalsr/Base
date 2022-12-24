# Content

* Basic
	* [[variables]]
	* [[pointers]]
	* [[function]]
	* [[loop]]
	* [[conditional statements]]
	* [[error]]
	* [[file]]
	* [[input output]]
	* [[context]]
	* [[testing]]
	* [[json]]
* Data strufunctioncture
	* [[map]]
	* [[string]]
	* [[slice]]
* OOP
	* [[interface]]
	* [[struct]]
* Threads
	* [[concurrency]]
	* [[channels]]
* Advanced
	* [[sheduler]]
	* [[Working with database]]
	* [[Escape analysis]]

# Полезные ссылки
* [Ardan labs](https://www.ardanlabs.com/training/ultimate-go/)
* [Go by example](https://gobyexample.com)
* [Go concurrency pattern](https://github.com/lotusirous/go-concurrency-patterns)
* [awesome go frameworks](https://github.com/avelino/awesome-go)

# Getting started
## Linux
Отсюда берем архив - https://go.dev/dl/
```
wget https://go.dev/dl/go1.18.4.linux-amd64.tar.gz
tar -xf go1.18.4.linux-amd64.tar.gz
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
