# Файлы Golang
## read file
```go
func main() {  
   content, err := os.ReadFile("text.txt")     // the file is inside the local directory  
   if err != nil {  
      fmt.Println("Err")  
   }  
   fmt.Println(string(content))    // This is some content  
}
```