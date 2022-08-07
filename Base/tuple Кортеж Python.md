# Кортеж
**Кортеж** — это неизменяемые аналоги списков
```python
empty_tuple = ()                                      # пустой кортеж
my_tuple = (1,)                                       # кортеж из одного элемента
point = (1.5, 6.0)                                    # кортеж из двух чисел
names = ('Timur', 'Ruslan', 'Roman')                  # кортеж из трех строк
info = ('Timur', 'Guev', 28, 170, 60, False)          # кортеж из 6 элементов разных типов
nested_tuple = (('one', 'two'), ['three', 'four'])    # кортеж из кортежа и списка

def mod(a, b, c):
    return (a, b, c)

output = mod(13, 4, 5)  # output[0] = 13 output[1] = 4 output[2] = 5
otp1, otp2, otp3 = mod(13, 4, 5) # otp1 = 13 otp2 = 4 otp3 = 5
otp1, *otp2 = mod(13, 4, 5) # otp1 = 13 otp2[0] = 4 otp2[1] = 5


print((1, 2, 3, 4) + (5, 6, 7, 8)) # (1, 2, 3, 4, 5, 6, 7, 8)
print((7, 8) * 3) # (7, 8, 7, 8, 7, 8)
print((0,) * 10) # (0, 0, 0, 0, 0, 0, 0, 0, 0, 0)

a = (1, 2, 3, 4)
b = (7, 8)
a += b   # добавляем к кортежу a кортеж b
b *= 5   # повторяем кортеж b 5 раз
print(a) # (1, 2, 3, 4, 7, 8)
print(b) # (7, 8, 7, 8, 7, 8, 7, 8, 7, 8)
``` 

## Вложенные кортежи
```python
colors = ('red', ('green', 'blue'), 'yellow')
numbers = (1, 2, (4, (6, 7, 8, 9)), 10, 11)
print(colors[1][1]) # blue
print(numbers[2][1][3]) # 9
```

## Преобразование кортежа в ...
```python
str_list = ['один', 'два', 'три']
str_tuple = tuple(str_list) # из списка в кортеж
str_tuple = list(str_tuple) # из кортежа в список

text = 'hello python'
str_tuple = tuple(text)
print(str_tuple) # ('h', 'e', 'l', 'l', 'o', ' ', 'p', 'y', 't', 'h', 'o', 'n')
text1 = ''.join(str_tuple) # из кортежа в строку
```

## index()
```python
names = ('Gvido', 'Roman' , 'Timur')
position = names.index('Timur')
print(position) # 2
```

## count()
```python
names = ('Timur', 'Gvido', 'Roman', 'Timur', 'Anders', 'Timur')
cnt1 = names.count('Timur')
cnt2 = names.count('Gvido')
cnt3 = names.count('Josef')

print(cnt1) # 3
print(cnt2) # 1
print(cnt3) # 0
```

## len()
**Длиной кортежа** называется количество его элементов.

```python
numbers = (2, 4, 6, 8, 10)
languages = ('Python', 'C#', 'C++', 'Java')

print(len(numbers))      # 5
print(len(languages))    # 4
```

## Срезы
```python
numbers = (2, 4, 6, 8, 10)
print(numbers[1:3]) # (4, 6)
print(numbers[2:5]) # (6, 8, 10)
```
