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
Операции с помощью atomic будут выполняться атомарно, поскольку выполняются за 1 такт, в отличии от обычной переменной которая может выполниться 3-4 такта.

# Scheduler
Для каждого потока go создает виртуальный процессор P, для каждого процессора OC выделяет поток M. Горутины G создаются по необходимости