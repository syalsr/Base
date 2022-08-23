# Модули
Модуль, это коллекция пакетов, распространяемых вместе (возможно это компоненты одной программы или одной библиотеки). В корне модуля находится файл go.mod, в котором записано имя модуля, версия Go, в которой он был написан, а также пути ко всем импортированным в модуле пакетам. Модуль включает в себя пакеты, находящиеся ниже корневой директории даже в том случае, если сами эти пакеты содержат файл go.mod.

Модуль инициализируется следующим образом:

```go
// name не обязателен
go mod init <name> // инициализировать новый модуль в текущем каталоге
// другие команды
go mod download    скачать модули в локальный кеш
go mod edit        редактировать go.mod из инструментов или скриптов
go mod graph       напечатать граф требований модуля
go mod tidy        добавить отсутствующие и удалить неиспользуемые модули
go mod vendor      делает вендорную копию зависимостей
go mod verify      проверить зависимости ожидаемого содержания
go mod why         объяснять, зачем нужны пакеты или модули
```

Далее после того, как мы создали модуль, импортировать файлы можно так:

```go
Структура проекта:
test_golang
|____________cmd
				|____main.go
|________internal
				|____some.go
go mod init github.com/syalsr/test_golang
```

Если хотим в main.go импортировать из some.go
```
package main
import "fmt"
import "github.com/syalsr/test_golang/internal"

func main(){
	st := some.TestFunc()
}
```

# require
Можем указать какие библиотеки необходимы проекту

```
module github.com/syalsr/test_golang

go 1.18

require(
	github.com/fatih/color v1.9.0
)
```

## Сторонние библиотеки
go автоматически добавит все зависимости этой библиотеки в require, если у модуля есть go.sum - это файл где содержатся все зависиомсти.
```
go get github.com/fatih/color

go.mod
module github.com/syalsr/awesomeProject  
  
go 1.18  
  
require (  
   github.com/fatih/color v1.13.0 // indirect  
   github.com/mattn/go-colorable v0.1.9 // indirect  
   github.com/mattn/go-isatty v0.0.14 // indirect  
   golang.org/x/sys v0.0.0-20210630005230-0f9fa26af87c // indirect  
)
```