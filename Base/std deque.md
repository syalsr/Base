# Deque
[[Категории итераторов#Random access iterators]] Двусторонняя очередь. Новые приходят в конец и удаляются из начала(или наоборот).

```cpp
d.push_back (x)    // добавление в конец 
d.pop_back (x)     // удаление из конца 
d.push_front (x)   // добавление в начало 
d.pop_front (x)    // удаление из начала 
d.emplace_front(x) // создает новый элемент в начале
d[i] // обращение к элементу по индексу
```
Зачем использовать? Если нужно работать с двумя концами.

В отличии от вектора элементы хранятся не по порядку, а блоками, следовательно инвалидации ссылок можно избежать.

![[../Files/Pasted image 20220216140656.png]]

В деке хранится вектор указателей на чанки(блоки), количество элементов и сдвиг.

![[../Files/Pasted image 20220216141001.png]]

Как тогда найти второй элемент? К сдвигу прибавляем индекс, т.е. нам нужен 4 элемент, размер блока 3, $4/3=1$, значит 1 чанк и там идем в элемент 4%3, то есть берем остаток от деления и получаем, что в первом блоке памяти нам нужен первый элемент.

В итоге, нет инвалидации ссылок, просто вставлять в начало и конец, но сложно интерироваться и получать элемент по индексу.

