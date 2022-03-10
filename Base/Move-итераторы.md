# Move-итераторы
`make_move_iterator` возвращает обёртку над итератором, которая, если мы хотим скопировать объект, перемещает его. Функция make_move_iterator меняет семантику итератора, чтобы при обращении к нему данные перемещались бы, а не копировались.

```cpp
Sentence<Token> sentence; 
for (; tokens_begin != sentence_end ; ++ tokens_begin ) { 
	sentence.push_back(move(∗tokens_begin)); 
} 
sentences.push_back(move(sentence));
```
С помощью move-iterator можно заменить код на такой:

```cpp
sentences.push_back(Sentence<Token>(
	make_move_iterator(tokens_begin),
	make_move_iterator(sentence_end)
));
tokens_begin = sentence_end;
```

```cpp
copy(make_move_iterator(begin(v)), make_move_iterator(end(v)), range_begin);
```

Алгоритм move будет перемещать данные, а не копировать. Это избавит от необходимости вызывать `make_move_iterator`

```cpp
move(begin(v), end(v), range_begin);
```