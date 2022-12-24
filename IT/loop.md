# Golang
# for
```go
package main  
  
import "fmt"  
  
func main() {  
   sum := 0  
   for i := 1; i < 10; i++ {  
      sum += i  
   }
   con := 0
   for con < 10 {
	   if con % 2 == 0 {
			continue
		} 
      con++  
   } 
   fmt.Println(sum)
   for {//бесконечный цикл
   }

	s := make([]int, 0, 10)  
	s = append(s, 1, 3, 2, 1, 12, 3)  
	for idx := range s {  
	   fmt.Println(s[idx])  
	}
	for idx, val := range s {  
	   fmt.Println(s[idx], val)  
	}
}
```
