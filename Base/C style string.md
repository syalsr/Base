# C style string
Строка является массив символов, последний символ `\0` null terminate, означает конец строки.

![[../Files/Pasted image 20220317181106.png]]

Соответственно, если мы выделяем память для строки, нужно не забыть выделить 1 символ для нулевого термината.

# Локализация
```cpp
cout.imbue(locale { "en-US" }); // "en_US" for POSIX
cout << 32767 << endl;//32,767
```
# Классификация символов
1. isspace() - пробел
2. isblank() - пустой символ
3. iscntrl()
4. isupper()
5. islower()
6. isalpha()
7. isdigit()
8. ispunct()
9. isxdigit()
10. isalnum()
11. isprint()
12. isgraph()