# Escape analysis
**Escape analysis** — это процесс, который компилятор использует для определения размещения значений, созданных вашей программой. В частности, компилятор выполняет статический анализ кода, чтобы определить, может ли значение быть помещено в стековый фрейм для функции, которая его строит, или значение должно «сбежать» в кучу. **В Go нет ни одного ключевого слова или функции**, **чтобы указать компилятору какое решение ему принять**. Только то, как вы пишете свой код, условно позволяет повлиять на это решение

У каждой горутины свой маленький стек, изначально он равен 2Кб, потом растет по необходимости. Перед вызовом ф-ии производится проверка и увеличение стека при необходимости. Память под стек выделяется в той же куче. Стек может быть перемещен, если он заполнен.

**Куча в Го**
-   Память у OS запрашивается большими кусками Arena = 64Мб (Линукс)
-   Внутри Arena память разбивается на фиксир-ые страницы page = 8Кб
-   Подряд идущие страницы объединяются в интервалы Span, различно длинны

![[../Files/Pasted image 20220928220809.png]]

**Алгоритм выделения памяти**
![[../Files/Pasted image 20220928220833.png]]

**Алгоритм освобождения**

-   В какой-то момент времени в занятом Span освобождается место
-   Возвращаем Span в соответствующий список свободных Span-ов mheap.central[cls].mcentral.nonempty
-   Если Span полностью полностью свободен, пытаемся соединить с соседними ( coalesce ) и возвращаем в дерево свободных Span-ов mheaf.free
https://habr.com/ru/post/497994/