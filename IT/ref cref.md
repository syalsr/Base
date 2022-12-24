# std::ref std::cref
```cpp
void cc(string& s)  
{  
    s = "f";  
}  
template<typename T>  
void printR (T arg) {  
    cc(arg); //error для cref, т.к. нет подходящецй перегруженной функци. 
}  
  
int main()  
{  
    string s = "hello";  
    printR(ref(s));  //объект передается по ссылке, а не копируется
    cout << s;  
    printR(cref(s));  
    cout << s;  
}
```

`cref и ref` оборачивают объект в `reference_wrapper`, поэтому мы ничего не можем с ним сделать в шаблоне функции, поэтому передает его в обычную