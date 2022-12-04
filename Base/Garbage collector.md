# Garbage collector
Сборка мусора — это процесс освобождения места в памяти, которое больше не используется

# Java
Garbage collector ищет в heap неиспользованную память и удаляет ее, но нет гарантии, что как только данные из heap недоступны, они удаляются, все зависит от количества памяти. Если у вас недостаточно памяти, то GC будет запускаться часто => производительность снижается.

# Golang
Сборка мусора выполняется конкурентно.

```go
func printStats(mem runtime.MemStats) {  
   runtime.ReadMemStats(&mem)  
   fmt.Println("mem.Alloc:", mem.Alloc)  
   fmt.Println("mem.TotalAlloc:", mem.TotalAlloc)  
   fmt.Println("mem.HeapAlloc:", mem.HeapAlloc)  
   fmt.Println("mem.NumGC:", mem.NumGC)  
   fmt.Println("-----")  
}  
  
func main() {  
   var mem runtime.MemStats  
   printStats(mem)  
   /*  
      mem.Alloc: 184424      
      mem.TotalAlloc: 184424      
      mem.HeapAlloc: 184424      
      mem.NumGC: 0  
   */   
   for i := 0; i < 10; i++ {  
      s := make([]byte, 50000000)  
      if s == nil {  
         fmt.Println("Operation failed!")  
      }  
   }  
   printStats(mem)  
   /*  
      mem.Alloc: 184424      
      mem.TotalAlloc: 184424      
      mem.HeapAlloc: 184424      
      mem.NumGC: 0   
  */   
  for i := 0; i < 10; i++ {  
      s := make([]byte, 100000000)  
      if s == nil {  
         fmt.Println("Operation failed!")  
      }  
      time.Sleep(5 * time.Second)  
   }  
   printStats(mem)  
   /*  
      mem.Alloc: 173144      
      mem.TotalAlloc: 1500316576      
      mem.HeapAlloc: 173144      
      mem.NumGC: 20  
   */}
```

Если перед любой командой go run поставить `GODEBUG=gctrace=1`, то Go выводит аналитические данные о работе сборщика мусора. Данные представлены в такой форме:
```
GODEBUG=gctrace=1 go run gColl.go
gc 4 @0.025s 0%: 0.002+0.065+0.018 ms clock,
0.021+0.040/0.057/0.003+0.14 ms cpu, 47->47->0 MB, 48 MB goal, 8 P
gc 17 @30.103s 0%: 0.004+0.080+0.019 ms clock,
0.033+0/0.076/0.071+0.15 ms cpu, 95->95->0 MB, 96 MB goal, 8 P
```

Здесь приводится информация о размерах кучи - 47->47->0 - первое число - размер кучи перед запуском GC, второе число - размер кучи, когда GC завершает работу, после число - актуальное значение кучи

## Трехцветный алгоритм
Главный принцип - разделение объектов, находящихся в куче, на 3 группы в соответствии с цветом который определяет алгоритм. Цвета:
1. Черный - объекты не имеют указателей ни на один объект белого цвета
2. Белый - может иметь указатель на объект черного цвета
3. Серый - может иметь указатель на объект белого цвета

Объекты белого цвета являются претендентами на удаление. Ни один объект не может перейти непосредственно из черного множества в белое. Ни один объект из черного множества не может напрямую указывать на объект из белого множества.

Работа алгоритма:
Когда начинается сборка мусора все объекты становятся белыми. Сборщик мусора перебирает все корневые объекты и окрашивает их в серый цвет. Корневые объекты — это объекты, к которым приложение может обращаться напрямую, включая глобальные переменные и другие элементы, находящиеся в стеке.

После этого сборщик мусора выбирает серый объект, помечает его черным и проверяет, есть ли у него указатели на другие объекты из белого множества. Если проверка обнаружит, что у данного объекта есть один или несколько указателей на белые объекты, алгоритм поменяет цвет этих белых объектов на серый. Процесс продолжается до тех пор, пока не будут перебраны все объекты серого множества. Затем объекты белого множества считаются недостижимыми, и занимаемая ими память может использоваться повторно.

![[../Files/Pasted image 20220928220858.png]]