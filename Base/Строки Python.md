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

## Интерполяция
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