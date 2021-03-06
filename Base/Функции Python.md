# Функции
```python
# Определение функции
# Определение не вызывает и не выполняет функцию
# Мы лишь говорим, что теперь такая функция существует
def show_greeting():
  # Внутри тела отступ 4 пробела
  text = 'Hello, Hexlet!'
  return text

# Вызов функции
print(show_greeting())  # => 'Hello, Hexlet!'
```

```
pow(x, y[, z])
```
x,y - обязательные параметры, z - необязательный

```
max(arg1, arg2, *args[, key])
```
Функция max принимает 2 параметра и больше

## Возврат кортежа
```python
def get_powers(num=2):
    return num**2, num**3, num**4
a, b, c = get_powers(2)
```

## Переменное количество аргументов
```python
def test(num, *args):  # *args - кортеж
    while num >= 0:  
        print(args[num-1])  
        num -=1       
test(5,23,4,2,1,4)

def my_func(**kwargs):  
    print(kwargs)  
my_func(job='Teacher', language='Python')
```

# Аргументы
Существует два типа **аргументов** — то есть данных, которые передаются в вызов функции.

Первый тип — **позиционные аргументы**. Они передаются в том же порядке, в котором определены параметры функции:
```python
# (text, length)
truncate('My Text', 3)
```
Второй тип — **именованные аргументы**. Они передаются не просто как значения, а как пара «имя=значение». Именно поэтому их можно передавать в любом порядке:

```python
# Аргументы переданы в другом порядке
truncate(length=3, text='My Text')
```

Именованные аргументы полезны, если у нас много аргументов по умолчанию, но нужно передать один.