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
**
# Печать директории
```go
//Get working directory path
    curdir, err := os.Getwd()


    //check if any error occurs
    if err != nil {
        //display error if
        fmt.Println(err)
    }


    //display the path
    fmt.Println(curdir)

   files, _ := os.ReadDir(curdir)
    path, _ := filepath.Abs(curdir)
    for _, file := range files {
        fmt.Println(filepath.Join(path, file.Name()))
    }
```