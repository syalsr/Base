# Словари Python
Являются [[Hash tables]]. В рамках одного словаря каждый ключ уникален. Начиная с версии Python 3.6 словари являются упорядоченными, то есть сохраняют порядок следования ключей в порядке их внесения в словарь.
```python
languages = {'Python': 'Гвидо ван Россум', 
             'C#': 'Андерс Хейлсберг', 
             'Java': 'Джеймс Гослинг', 
             'C++': 'Бьёрн Страуструп'}
print('Создателем языка C# является', languages['C#'])
```

## Создание словаря
```python
info = dict(name = 'Timur', age = 28, job = 'Teacher')

info_list = [('name', 'Timur'), ('age', 28), ('job', 'Teacher')]  # список кортежей
# Первый элемент списка или кортежа станет ключом, второй — значением.
info_dict = dict(info_list)  # создаем словарь на основе списка кортежей

info_tuple = (['name', 'Timur'], ['age', 28], ['job', 'Teacher'])  # кортеж списков
info_dict = dict(info_tuple)  # создаем словарь на основе кортежа списков

dict1 = dict.fromkeys(['name', 'age', 'job'], 'Missed information')
# ключи - name, age, job - значение у каждого Missed information
dict1 = dict.fromkeys(['name', 'age', 'job']) # значения None

keys = ['name', 'age', 'job']
values = ['Timur', 28, 'Teacher']
info = dict(zip(keys, values))

{ключ: значение for переменная in последовательность}
squares = {i: i**2 for i in range(6)} # ключи 0 - 5 включительно
dct = {c: c * 3 for c in 'ORANGE'} # {'O': 'OOO', 'R': 'RRR', 'A': 'AAA', 'N': 'NNN', 'G': 'GGG', 'E': 'EEE'}

dict1 = {} # пустой словарь
dict2 = dict() # пустой словарь
```

## Вложенные словари
```python
info = {'emp1': {'name': 'Timur', 'job': 'Teacher'},
        'emp2': {'name': 'Ruslan', 'job': 'Developer'},
        'emp3': {'name': 'Rustam', 'job': 'Tester'}}

ids = ['emp1', 'emp2', 'emp3']
emp_info = [{'name': 'Timur', 'job': 'Teacher'},
            {'name': 'Ruslan', 'job': 'Developer'},
            {'name': 'Rustam', 'job': 'Tester'}]
info = dict(zip(ids, emp_info))

print(info['emp1']['name'])
print(info['emp2']['job'])
info['emp1']['job'] = 'Manager'

for emp in info:
    print('Employee ID:', emp)
    for key in info[emp]:
        print(key + ':', info[emp][key])
    print()

squares = {i: {j: j**2 for j in range(i + 1)} for i in range(5)}
for value in squares.values():
    print(value)
{0: 0}
{0: 0, 1: 1}
{0: 0, 1: 1, 2: 4}
{0: 0, 1: 1, 2: 4, 3: 9}
{0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

## Вывод
```python
languages = {'Python': 'Гвидо ван Россум', 
             'C#': 'Андерс Хейлсберг', 
             'Java': 'Джеймс Гослинг'}

info = dict(name = 'Timur', age = 28, job = 'Teacher')

print(languages) # {'Python': 'Гвидо ван Россум', 'C#': 'Андерс Хейлсберг', 'Java': 'Джеймс Гослинг'}
print(info) # {'name': 'Timur', 'age': 28, 'job': 'Teacher'}
```

## get()
```python
info = {'name': 'Bob',
        'age': 25,
        'job': 'Dev'}
print(info['name']) # ok
print(info['salary']) # KeyError
item1 = info.get('salary') # None
item2 = info.get('salary', 'Информации о зарплате нет') # Информации о зарплате нет
```

## update()
Реализует конкатенацию словарей
```python
info1 = {'name': 'Bob',
        'age': 25,
        'job': 'Dev'}

info2 = {'age': 30,
        'city': 'New York',
        'email': 'bob@web.com'}

info1.update(info2) # {'name': 'Bob', 'age': 30, 'job': 'Dev', 'city': 'New York', 'email': 'bob@web.com'}
```

## setdefault()
Позволяет получить значение из словаря по заданному ключу, автоматически добавляя элемент словаря, если он отсутствует

```python
info = {'name': 'Bob',
        'age': 25}

name1 = info.setdefault('name')           # параметр default не задан           
name2 = info.setdefault('name', 'Max')    # параметр default задан
key = info.setdefault('salary', '2') # если не установить значение, то по умолчанию None
print(name1) # Bob
print(name2) # Bob
print(key) # 2
print(info) # {'name': 'Bob', 'age': 25, 'salary': '2'}
```

## copy()
```python
info = {'name': 'Bob',
        'age': 25,
        'job': 'Dev'}

info_copy = info.copy()
info 1 = info
# info_copy - копия словаря
# info1 - ссылка на словарь info
```

# Удаление элементов
```python
info = {'name': 'Sam',
        'age': 28,
        'job': 'Teacher',
        'email': 'timyr-guev@yandex.ru'}

del info['email']    # удаляем элемент имеющий ключ email
del info['job']      # удаляем элемент имеющий ключ job
# {'name': 'Sam', 'age': 28}

email = info.pop('email')          # удаляем элемент по ключу email, возвращая его значение
job = info.pop('job')              # удаляем элемент по ключу job, возвращая его значение

info['surname'] = 'Sinclar'
item = info.popitem() # удаляем последний добавленный элемент и возвращаем в виде кортежа
# ('surname', 'Sinclar')
```