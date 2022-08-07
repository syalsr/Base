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
