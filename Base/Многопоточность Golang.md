# Многопоточность Golang
В Go независимо запущенная задача называется **горутиной** - это [[Потоки#User level threads|user level threads]]
Преимущества горутин:
1.  Они легковесны.
2.  Легко и без проблем масштабируют.
3.  Они — практически потоки.
4.  Требуют меньше памяти (2KB).
5.  Предоставляют дополнительную память горутинам во время выполнения.

Для запуска горутины перед вызовом функции нужно прописать `go`
```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	names := [4]string{"Gopher", "Rob Pike", "Ken Thompson", "Linus Torvalds"}
	go hi()
	go name(names)

	time.Sleep(time.Second * 10)
	fmt.Println("Time is over - bye!")
}

func hi() {
	for {
		val := rand.Intn(3)
		time.Sleep(time.Second * time.Duration(val))
		fmt.Println("Hi!")
	}
}

func name(names [4]string) {
	for {
		val := rand.Intn(3)
		time.Sleep(time.Second * time.Duration(val))
		fmt.Println("My name is:", names[val])
	}
}
```

# Изменение количества ядер
```
runtime.GOMAXPROCS(4)
```

# Каналы
Канал представляет собой механизм связи, который позволяет одной горутине отправлять некоторые значения другой горутине. Каждый канал является средством передачи значений определённого типа, который называется типом элементов канала.
```go
channel := make(chan int)    // channel имеет тип 'chan int'
```

Как и отображение, канал является ссылкой на структуру данных. Копируя или передавая канал в функцию в качестве аргумента, мы копируем ссылку на одну и ту же структуру данных. Нулевым значением канала является nil.

Канал имеет две основные операции:
-   **Отправление (запись)** - передаёт через канал значение из одной горутины в другую. `channel <- num // отправляет в канал channel значение num`
-   **Получение (чтение)** - получение через канал значения из другой горутины. `num = <- channel // получение из канала channel в переменную num`

Чтение из канала может производится в цикле:
```go
for v := range channel {//чтение будет осуществляться пока канал открыт
    ...
}
```



Каждый канал имеет длину и емкость (их можно получить с помощью знакомых нам функций len() и cap()): длина - количество значений в канале в текущий момент, емкость - размер буфера.
```go
c := make(chan int, 1)      // здесь 1 - размер буфера  
fmt.Println(len(c), cap(c)) // 0 1  
c <- 1  
fmt.Println(len(c), cap(c)) // 1 1  
<-c  
fmt.Println(len(c), cap(c)) // 0 1
```

## Определение канала на запись/чтение
```go
func worker(done chan<- struct{})//запись
func worker(done <-chan struct{})//чтение
```

## close - закрытие канала
Каналы поддерживают операцию закрытия встроенной функцией close:
```go
close(channel)
```

1. Запись в канал приведет к возникновению аварийной ситуации
2. Но читать можно - будут получены все значения, которые были отправлены, но еще не прочитаны. После получения всех значений из канала, будет возвращаться нулевое значение типа канала.

## select
select - аналог switch case, но нужен для получения данных из канала 
```go
func worker(done chan<- struct{}) {  
   time.Sleep(100 * time.Millisecond)  
   done <- struct{}{}  
}  
func main() {  
   ch1, ch2 := make(chan struct{}), make(chan struct{})  
  
   go worker(ch1)  
   go worker(ch2)  
  
LOOP: //LOOP - метка, чтобы прервать цикл for, а не select  
   for {  
      select { 
      case <-ch1:  
         fmt.Println("first worker done")  
         break LOOP  
      case <-ch2:  
         fmt.Println("first worker done")  
         break LOOP  
      default:  
         time.Sleep(10 * time.Millisecond)  
      }  
   }  
}
```

## Буферизированный
Буферизованные каналы представляют из себя [[queue]], операция отправления в буферизованный канал добавляет элемент в конец очереди `channel <- "A"`, операция извлечения, извлекает первый элемент из очереди `fmt.Println(<-channel) // "A"`.

Если канал заполнен, операция отправления блокирует свою горутину, до тех пор, пока другая горутина не освободит место, получив данные из канала.
```go
chan := make(chan int, 2)
chan <- 1//ok
chan <- 1//ok
chan <- 1//deadlock
```

## Небуферизированный
При взаимодействии с небуферизованным каналом важно знать:
1. При отправление значения в небуферизованный канал, отправляющая горутина блокируется до тех пор, пока другая горутина не выполнит получение из этого канала. После этого обе горутины продолжают работать. 
2. Если горутина получает значение, она блокируется, пока другая горутина не отправит новое значение в канал.
```go
func worker(ch chan int, wg *sync.WaitGroup) {  
   defer wg.Done()  
   value := <-ch  
   fmt.Printf("%d\n", value)  
}  
func main() {  
   wg := new(sync.WaitGroup)  
  
   channel := make(chan int)  
  
   wg.Add(1)  
   go worker(channel, wg)  
   channel <- 1 //без этого был бы deadlock  
   wg.Wait()  
}
```

Если же размер буфера будет 0, то выполнение программы приведет к панике, так как записанное значение одновременно с записью в канал не вычитывается.

```go
pipe := make(chan int)     // небуферизованный канал
pipe := make(chan int, 0)  // небуферизованный канал
pipe := make(chan int, 5)  // буферизованный канал с ёмкостью 5
```

Небуферизованные каналы дают более надежные гарантии синхронизации, потому что каждая операция отправления связана с операцией получения. В случае буферизованных каналов, эти операции разделены.

# Блокировки
Горутины блокируются для повышения производительности неработоспособных каналов.

## Блокировка при записи
Горутина, посылающая данные в канал, блокируется, покуда другая горутина не прочитает данные из него. Всё просто.

## Блокировка при чтении
Горутина, получающая данные из канала,  может быть заблокирована до момента получения данных в канал. Аналогично блокировке при записи.

## select
Используя конструкцию _**select-case**_ можно избежать блокирующего поведения.
```go
package main

import "fmt"

func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-c)
		}
		quit <- 0
	}()
	fibonacci(c, quit)
}
```
Если данные есть, горутина их прочитает, иначе выйдет из функции.

# Синхронизация горутин
```go
func main() {  
   done := make(chan struct{})  
   go func(d chan struct{}) {  
      work()  
      close(d)  
   }(done)
   <-done
}
```

Горутина которая запустила анонимную функцию захватила небуферизированный канал, соответственно, когда основная горутина обращается к этому каналу, блокируется, пока другая горутина полностью не отработает.

## sync.WaitGroup
Можем определить группу горутин, которые должны выполняться как одно целое, то есть, если какая-то одна еще не выполнилась, то остальные ее ждут.
```go
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	wg := new(sync.WaitGroup)

	for i := 0; i < 5; i++ {
		wg.Add(1) // Увеличиваем счетчик горутин в группе
		go work(i, wg) // Вызываем функцию work в отдельной горутине
	}

	wg.Wait() // ожидаем завершения всех горутин в группе
	fmt.Println("Горутины завершили выполнение")
}

func work(id int, wg *sync.WaitGroup)  {
	defer wg.Done()
	fmt.Printf("Горутина %d начала выполнение \n", id)
	time.Sleep(2 * time.Second)
	fmt.Printf("Горутина %d завершила выполнение \n", id)
}
```
1. wg.Add() должно вызываться до запуска горутины
2. wg.Done() внутри горутины

В случае, если не хотим передавать в функцию WaitGroup

```go
func main() {  
   wg := new(sync.WaitGroup)  
  
   for i := 0; i < 10; i++ {  
      wg.Add(1)  
      go func(wg *sync.WaitGroup) {  
         defer wg.Done()  
         work()  
      }(wg)  
   }  
  
   wg.Wait()  
}
```

# mutex
```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var x int
	wg := new(sync.WaitGroup)

	for i := 0; i < 1000; i++ {
		// Запускаем 1000 экземпляров горутины, увеличивающей счетчик на 1
		wg.Add(1)
		go func(wg *sync.WaitGroup) {
			defer wg.Done()
			x++
		}(wg)
	}

	wg.Wait()

	// По идее значение счетчика должно быть 1000, но крайне вероятно, что этого не произойдет
	fmt.Println(x)
}
```

Т.к. переменная x не атомарная, то сколько угодно потоков могут одновременно считать ее значение, допустим 0, увеличить его на 1 и вернуть обратно.

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var x int
	wg := new(sync.WaitGroup)
	mu := new(sync.Mutex)

	for i := 0; i < 1000; i++ {
		// Запускаем 1000 экземпляров горутины, увеличивающей счетчик на 1
		wg.Add(1)
		go func(wg *sync.WaitGroup, mu *sync.Mutex) {
			defer wg.Done()
			mu.Lock()
			x++
			mu.Unlock()
		}(wg, mu)
	}

	wg.Wait()
	fmt.Println(x)
}
```

# read/write mutex
Данные могут читать сколько угодно поток, но писать может лишь один.

```go
type RWM struct {  
   sync.RWMutex  
   v int  
}  
func (t * RWM) read(){  
   t.Lock()  
   defer t.Unlock()  
   fmt.Println(t.v)  
}  
func (t * RWM) write(){  
   t.RLock()  
   defer t.RUnlock()  
   t.v = 5  
   fmt.Println(t.v)  
}  
func main() {  
   rw := RWM{}  
   go rw.read()  
   rw.write()  
}
```

# Отслеживание race condition/data race
Запустить программу с флагом
```
go run -race main.go
```

# atomic
```go
var count int64

for i:= 0; i < 1000; i++{
	go func(){
		atomic.AddInt64(&count, 1)
	}
	go func(){
		atomic.AddInt64(&count, -1)
	}
}
```
Операции с помощью atomic будут выполняться атомарно

