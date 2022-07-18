# 20 Python
# Ввод вывод
Вызов команды print() с пустыми скобками ставит перевод строки.

По умолчанию команда print() принимает несколько аргументов (параметров), выводит их через один **пробел**, после чего **ставит перевод строки**. Это поведение можно изменить, используя необязательные именованные параметры sep (separator, разделитель) и end (окончание).

## sep
Значение по умолчанию `sep=' '   # пробел`
```python
print('a', 'b', 'c', sep='*')
print('d', 'e', 'f', sep='**')
//a*b*c
//d**e**f
```

## end
Значение по умолчанию `end='\n'  # перевод строки`

Если перевод строки делать не нужно или требуется указать специальное окончание, то следует явно указать значение для параметра end.

```python
print('a', 'b', 'c', end='@')
print('d', 'e', 'f', end='@@')
a b c@d e f@@
```

По завершении первой печати вставлена строка @ вместо перевода строки. Аналогично, по завершении второй печати вставлена строка @@.

# Модули
В Python модулем называется библиотека функций, которую можно подключать к своим программам.

```python
import math
math.sqrt(a)

from math import *
sqrt(a)

from math import sqrt
sqrt(a)
```

# Переменные
В python реализована длинная арифметика, то есть, по сути, переменная целого типа не имеет ограничений.
```python
num1 = 7 #int 7 - целочисленный литерал
num2 = 7.2 #float 7.2 - литерал с плавающей точкой
a = input() #string всегда
a = float(a) 
a = int(a)
```

## Преобразования
**Неявное преобразование.** Любое целое число (тип int) можно использовать там, где ожидается число с плавающей точкой (тип float), поскольку при необходимости Python автоматически преобразует целые числа в числа с плавающей точкой.

**Явное преобразование.** Число с плавающей точкой нельзя неявно преобразовать в целое число. Для такого преобразования необходимо использовать явное преобразование с помощью команды int().

```python
num1 = 17.89
num2 = -13.56
num3 = int(num1)
num4 = int(num2)
print(num3) 17
print(num4) -13
```

Если у нас строковый литерал число с плавающей точкой, то мы должны его преобразовывать с помощью `float()` иначе будет ошибка

```python
a = input() #17.2
a = int(a) #error
a = float(a) #ok
a = str(a) #ok но бессмысленно, т.к. input и так возвращает строку
```

## Множественное присваивание
```python
name, surname = 'Timur', 'Guev'
print('Имя:', name, 'Фамилия:', surname)
name, surname = input(), input()
print('Имя:', name, 'Фамилия:', surname)
```
# Циклы
## Цикл for
```python
for i in range(5): #0 - 5
    num = int(input())
    print('Квадрат вашего числа равен:', num * num)
print('Цикл завершен')

for i in range(5, 10):#5 - 10
    num = int(input())
    print('Квадрат вашего числа равен:', num * num)
print('Цикл завершен')

for i in range(0, 10, 2):#0 - 10 с шагом 2 - 0, 2, 4...
    num = int(input())
    print('Квадрат вашего числа равен:', num * num)
print('Цикл завершен')
```

Также можно идти в убывание и с отрицательным шагом

## Цикл while
```python
n = int(input())
while n != 0:  # пока в числе есть цифры
    last_digit = n % 10  # получить последнюю цифру
    # код обработки последней цифры
    n = n // 10  # удалить последнюю цифру из числа
```

# break, continue
С помощью break можно прервать выполнение ближайшего цикла

```python
for i in range(3)://этот работает
    for j in range(3)://этот прерывается
        if i == j:
            break
        print(i, j)
```

С помощью continue можно пропустить итерацию цикла и приступить к следующей

## else в циклах
Блок в else, будет выполнен, когда **штатным образом**(без использования break) завершается цикл while или for.

```python
n = 5
while n > 0:
    n -= 1
    print(n)
else:
    print('Цикл завершен.')
```

# Условный оператор
```python
answer = input('Какой язык программирования мы изучаем?')
if answer == 'Python':
    print('Верно! Мы ботаем Python =)')
    print('Python - отличный язык!')
elif answer == 'C++'
	print('Верно! Мы ботаем C++ =)')
    print('C++ - отличный язык!')
else:
	print('Не совсем так!')
```

## Оператор in
```python
s = input()
if 'a' in s:
    print('Введенная строка содержит символ а')
else:
    print('Введенная строка не содержит символ а')

s = input()
if '.' not in s:
    print('Введенная строка не содержит символа точки')
```

# Операции
## Возведение в степень
```python
print(2 ** 0) 1
print(2 ** 1) 2
print(2 ** 2) 4
print(2 ** -1) 0.5
```

## Целочисленное деление
```python
print(10 // 3) 3
print(10 // 4) 2
print(10 // 5) 2
```

## Деление с остатком
```python
print(10 % 3) 1
print(10 % 4) 2
```






# Интерполяция
Иной способ конкатенация строк
```python
first_name = 'Joffrey'
greeting = 'Hello'
print(greeting + ", " + first_name + "!")
# => Hello, Joffrey!

print(f'{greeting}, {first_name}!')
# => Hello, Joffrey!

school = 'Hexlet'

what_is_it = f'{school} - online courses'
print(what_is_it)  # => Hexlet - online courses
```

Буква `f` указывает на то, что мы создаём f-строку. f-строка - это шаблон, в который с помощью фигурных скобок подставляются значения переменных.

# Строки
```python
s1 = 'Python rocks!'
s2 = "Python rocks!"
s = input()  # переменная s имеет строковый тип str

s1 = ''   # пустая строка
s2 = ' '  # строка состоящая из одного символа пробела
s1 = 'abcdef'
length1 = len(s1) # считаем длину строки из переменной s1

s1 = 'ab' + 'bc'
s2 = 'bc' + 'ab'
s3 = s1 + s2 + '!!'
print(s1)#abbc
print(s2)#bcab
print(s3)#abbcbcab!!

s = 'Hi' * 4
print(s) #HiHiHiHi
```

## Преобразование
```python
num1 = 1777    # целое число
num2 = 17.77   # число с плавающей точкой
s1 = str(num1) # преобразовали целое число в строку '1777'
s2 = str(num2) # преобразовали число с плавающей точкой в строку '17.77'
```

## Multi-line strings
```python
text = 'Пример текста,\nсостоящего из\nнескольких строк'
text = '''Пример текста,
состоящего из
нескольких строк'''
```

```python
a = 'A'
b = 'B'

# Слева добавился f
text = f'''{a} и {b}
сидели на трубе
'''
```

## Индексация строк
```python
first_name = 'Alexander'

print(first_name[-1])  # => r
```

Отрицательные индексы начинаются с конца

```python
value = '12-08-2034'

year = value[6:10]
print(year)  # => 2034

value = 'Hexlet'
value[3:]  # 'let'
value[:3]  # 'Hex'

value = 'Hexlet'
# Правая граница отрицательная. Считаем -1 от конца строки
value[3:-1]  # 'le'
# Левая граница отрицательная. Считаем -5 от конца строки
value[-5:3]  # 'ex'

value = 'Hexlet'
value[1:5:2]  # el 2 - шаг
# 1:5 это 'exle'
# шаг 2 это каждый второй, то есть 'e' и 'l'

value = 'Hexlet'
value[:5:2]  # 'Hxe'
value[1::2]  # 'elt'

Можно также использовать отрицательный шаг для переворота строки
value = 'Hexlet'  
# Пропускаем обе границы  
print(value[::-1])  # 'telxeH'

value = 'Hexlet'  
# Символ с индексом 1 не будет включён в подстроку  
value[4:1:-1]  # 'elx'

value = 'Hexlet'
start = 1
end = 5
value[start:end]  # 'exle'
```
При помощи индексов мы не можем менять отдельные символы, нужны менять всю строку

## split()
Метод split() разбивает строку на слова, используя в качестве разделителя последовательность пробельных символов.
```python
s = 'Python is the most powerful language'
words = s.split()
print(words) # ['Python', 'is', 'the', 'most', 'powerful', 'language']

numbers = input().split()
for i in range(len(numbers)):
    numbers[i] = int(numbers[i])
```

По умолчанию  у `split()` разделителем является пробел, это можно изменить

```python
ip = '192.168.1.24'
numbers = ip.split('.')    # указываем явно разделитель
print(numbers) # ['192', '168', '1', '24']
```

## join()
Метод join() собирает строку из элементов списка, используя в качестве разделителя строку, к которой применяется метод.
```python
words = ['Python', 'is', 'the', 'most', 'powerful', 'language']
s = ' '.join(words)
print(s) # Python is the most powerful language
```


# Списки
Чтобы создать список, нужно перечислить его элементы через запятую в квадратных скобках:

```python
numbers = [2, 4, 6, 8, 10]
languages = ['Python', 'C#', 'C++', 'Java']
print(numbers)
print(languages)
print(len(numbers))      # выводим длину списка numbers
# [2, 4, 6, 8, 10]
# ['Python', 'C#', 'C++', 'Java']
# 5
languages[0] == 'Python';
languages[1] == 'C#';
languages[2] == 'C++';
languages[3] == 'Java'.

numbers = [2, 4, 6, 8, 10]

if 2 in numbers:
    print('Список numbers содержит число 2')
else:
    print('Список numbers не содержит число 2')

print([1, 2, 3, 4] + [5, 6, 7, 8])
print([7, 8] * 3)
print([0] * 10)

# [1, 2, 3, 4, 5, 6, 7, 8]
# [7, 8, 7, 8, 7, 8]
# [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]

a = [1, 2, 3, 4]
b = [7, 8]
a += b   # добавляем к списку a список b
b *= 5   # повторяем список b 5 раз 

print(a)
print(b)
# [1, 2, 3, 4, 7, 8]
# [7, 8, 7, 8, 7, 8, 7, 8, 7, 8]
```
Список может содержать значения **разных типов данных**:

```python
info = ['Timur', 1992, 61.5]
```

Строки — **неизменяемые** объекты, а списки – **изменяемые**.

## Создание списка
Создать пустой список можно двумя способами:
1.  Использовать пустые квадратные скобки [];
2.  Использовать встроенную функцию, которая называется list.

Следующие две строки кода создают пустой список:
```python
mylist = []       # пустой список
mylist = list()   # пустой список
```

### Списочные выражения
```python
zeros = [0 for i in range(10)]  # список содержащий десять нулей
numbers = [i for i in range(10)] # список содержит значение 0-10 не включительно

n = int(input())
lines = [input() for _ in range(n)]
lines = [input() for _ in range(int(input()))]

evens = [i for i in range(21) if i % 2 == 0]

numbers = [i * j for i in range(1, 5) for j in range(2)]
print(numbers) # [0, 1, 0, 2, 0, 3, 0, 4]
```

## Вложенные списки
```python
my_list = [[0], [1, 2], [3, 4, 5]]

print(my_list)      # [[0], [1, 2], [3, 4, 5]]
print(my_list[0])   # [0]
print(my_list[1])   # [1, 2]
print(my_list[2])   # [3, 4, 5]
print(my_list[1][0])# 1
print(len(my_list)) # 3

my_list = ['Python', [10, 20, 30], ['Beegeek', 'Stepik!']]
print(my_list[2][-1][-1]) # !
```

### min() max()
Сравнение происходит поэлементно: сначала сравниваются первые элементы списков. Если они не равны, то функция min() вернет тот список, первый элемент которого меньше, max() – наоборот.

```python
list1 = [1, 7, 12, 0, 9, 100] 
list2 = [1, 7, 90] 
list3 = [1, 10]

print(min(list1, list2, list3)) # [1, 7, 12, 0, 9, 100]
print(max(list1, list2, list3)) # [1, 10]
```

## Создание вложенного списка
```python
n, m = int(input()), int(input())    # считываем значения n и m
my_list = []
for _ in range(n):
    my_list.append([0] * m)

n, m = int(input()), int(input())    # считываем значения n и m
my_list = [0] * n
for i in range(n):
    my_list[i] = [0] * m

n, m = int(input()), int(input())    # считываем значения n и m
my_list = [[0] * m for _ in range(n)]

n = 4  # количество строк (элементов)
my_list = []
for _ in range(n):
    elem = [int(i) for i in input().split()]   # создаем список из элементов строки
    my_list.append(elem)


```
## list()
Может преобразовывать элементы в списки

```python
numbers = list(range(5))
```
range возвращает элементы 0-5 а list() создает список на их основе.

```python
s = 'abcde'
chars = list(s)  # список содержит символы 'a', 'b', 'c', 'd', 'e'
```
Полезно, если нужно изменить символы строки

## Срезы
С помощью среза мы можем получить несколько элементов списка, создав диапазон индексов разделенных двоеточием `numbers[x:y]`
```python
print(numbers[1:3])
print(numbers[2:5])
```

```python
fruits = ['apple', 'apricot', 'banana', 'cherry', 'kiwi', 'lemon', 'mango']
fruits[2:5] = ['банан', 'вишня', 'киви']

print(fruits)
# ['apple', 'apricot', 'банан', 'вишня', 'киви', 'lemon', 'mango']
```

## append()
```python
numbers = [1, 1, 2, 3, 5, 8, 13]  # создаем список

numbers.append(21)  # добавляем число 21 в конец списка
numbers.append(34)  # добавляем число 34 в конец списка

words1 = ['iq option', 'stepik', 'beegeek']
words1.append('python')
print(words1) # ['iq option', 'stepik', 'beegeek', 'python']
```

## extend()
```python
numbers = [0, 2, 4, 6, 8, 10]
odds = [1, 3, 5, 7]

numbers.extend(odds)
print(numbers) # [0, 2, 4, 6, 8, 10, 1, 3, 5, 7]

words2 = ['iq option', 'stepik', 'beegeek']
words2.extend('python')
print(words2) # ['iq option', 'stepik', 'beegeek', 'p', 'y', 't', 'h', 'o', 'n']
```

## del
```python
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9]
del numbers[5]    # удаляем элемент имеющий индекс 5
del numbers[2:7]    # удаляем элементы с 2 по 6 включительно
```

## insert()
Метод insert() позволяет вставлять значение в список в заданной позиции. В него передается два аргумента:
1.  index: индекс, задающий место вставки значения;
2.  value: значение, которое требуется вставить.

```python
names = ['Gvido', 'Roman' , 'Timur']
print(names)
names.insert(0, 'Anders')
print(names)
names.insert(3, 'Josef')
print(names)
# ['Gvido', 'Roman' , 'Timur']
# ['Anders', 'Gvido', 'Roman' , 'Timur']
# ['Anders', 'Gvido', 'Roman' , 'Josef', 'Timur']
```

1. Если укажем отрицательный индекс, то значение вставится в начало
2. Если укажем индекс больше размера списка, то в конец

## index()
Метод index() возвращает индекс первого элемента, значение которого равняется переданному в метод значению.
```python
names = ['Gvido', 'Roman' , 'Timur']
position = names.index('Timur')
print(position) # 2
```

## remove
Метод remove() удаляет первый элемент, значение которого равняется переданному в метод значению.

```python
food = ['Рис', 'Курица', 'Рыба', 'Брокколи', 'Рис']
food.remove('Рис')
print(food) # ['Курица', 'Рыба', 'Брокколи', 'Рис']
```

## pop()
Метод pop() удаляет элемент по указанному индексу и возвращает его.

```python
names = ['Gvido', 'Roman' , 'Timur']
item = names.pop(1)
print(item) # Roman
print(names) # ['Gvido', 'Timur']
```

## count()
Метод count() возвращает количество элементов в списке, значения которых равны переданному в метод значению.
```python
names = ['Timur', 'Gvido', 'Roman', 'Timur', 'Anders', 'Timur']
cnt1 = names.count('Timur')
cnt2 = names.count('Gvido')
cnt3 = names.count('Josef')

print(cnt1) # 3
print(cnt2) # 1
print(cnt3) # 0
```

## reverse()
Метод reverse() инвертирует порядок следования значений в списке, то есть меняет его на противоположный.

```python
names = ['Gvido', 'Roman' , 'Timur']
names.reverse()
print(names) # ['Timur', 'Roman', 'Gvido']
```

## clear()
Метод clear() удаляет все элементы из списка.

```python
names = ['Gvido', 'Roman' , 'Timur']
names.clear()
print(names)
```

## copy()
Метод copy() создает поверхностную копию списка.

```python
names = ['Gvido', 'Roman' , 'Timur']
names_copy = names.copy() # создаем поверхностную копию списка names

print(names) # ['Gvido', 'Roman', 'Timur']
print(names_copy) # ['Gvido', 'Roman', 'Timur']
```

## Вывод элементов
```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

for i in range(len(numbers)):
    print(numbers[i])
for num in numbers:
    print(num)
print(*numbers)# передавая * мы выведем список без квадратных скобок через пробел
print(*numbers, sep='\n') # каждый на своей строке

my_list = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
for i in range(len(my_list)):
    for j in range(len(my_list[i])):
        print(my_list[i][j], end=' ')   # используем необязательный параметр end
    print()                             # перенос на новую строку

for row in my_list:
    for elem in row:
        print(elem, end=' ')
    print()
```

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
def get_powers(num):
    return num**2, num**3, num**4
a, b, c = get_powers(2)
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

# Логические выражения
Оператор **ИЛИ** работает так, что его выполнение (слева направо) прерывается и возвращается результат первого аргумента, который можно преобразовать в `True`, если такого аргумента нет, возвращается последний (правый).

Оператор **И** работает так, что его выполнение (слева направо) прерывается и возвращается результат первого аргумента, который можно преобразовать в `False`, если такого аргумента нет, возвращается последний (правый).

```python
age = int(input('Сколько вам лет?: '))
grade = int(input('В каком классе вы учитесь?: '))
if age >= 12 and grade >= 7:
    print('Доступ разрешен.')
else:
    print('Доступ запрещен.')

city = input('В каком городе вы живете?: ')
if city == 'Москва' or city == 'Санкт-Петербург' or city == 'Екатеринбург':
    print('Доступ разрешен.')
else:
    print('Доступ запрещен.')

age = int(input('Сколько вам лет?: '))
grade = int(input('В каком классе вы учитесь?: '))
city = input('В каком городе вы живете?: ')
if age >= 12 and grade >= 7 and (city == 'Москва' or city == 'Санкт-Петербург'):
    print('Доступ разрешен.')
else:
    print('Доступ запрещен.')

age = int(input('Сколько вам лет?: '))
if not (age < 12):
    print('Доступ разрешен.')
else:
    print('Доступ запрещен.')
```

## None
`None` – это специальная константа тип `NonType`, означающая пустоту.
```python
var = None
if var is None:   # используем оператор is, тоже самое, что и ==
  print('None')
else:
  print('Not None')

print(None == 0)
print(None == False)
print(None == '')
# False
# False
# False
```


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

# Полезное
* Что внутри у питона: как работает интерпретатор. https://youtu.be/at30AmjPsy4
* Что внутри у питона: откуда быть пошел async. https://youtu.be/GX7AUAwpQ4I
* Что внутри у питона: как устроена память. https://youtu.be/lSgoYx06L_s
* Канал по питону - https://www.youtube.com/user/zaemiel/videos
* Introduction to scripting in python - https://www.coursera.org/specializations/introduction-scripting-in-python
* Junior Python Developer: полный разбор собеседования и ответы на наиболее частые вопросы интервью - https://www.youtube.com/watch?v=fgXCN7A8yzg
* Чат на питоне https://medium.com/swlh/lets-write-a-chat-app-in-python-f6783a9ac170