# Operating system and computer architecture
Большинство компьютеров имеют **2 режим работы: режим ядра и режим суперпользователя**.

**[[Операционная система]]** работает в режиме ядра, благодаря которому имеет полный доступ ко всему **[[Аппаратное обеспечение|аппаратному обеспечению]]** и может задействовать любые машинные инструкции. Вся остальная часть ПО работает в режиме пользователя и может использовать только подмножество машинных инструкций(к примеру они не могут осуществлять ввод/вывод, но могут запросить у ОС разрешение на это).

# Для чего нужна OS
Архитектура большинства компьютеров примитивна и не удобна(все что может компьютер это складывать и перемещать биты). Для более упрощенной работой с ним, придумали такую прослойку, как Операционная система. 

OS обеспечивает:
1. Доступ к устройствам - клавиатура, мышь и др.
2. Работу с файлами
3. Создание и взаимодействие процессов
4. Мониторинг ресурсов, время, отладка

Управление ресурсами включает в себя распределение ресурсов двумя различными способами:
1. Во времени - т.е. различные программы пользуются им по очереди - для этого операционной системе есть [[Процессы]].
2. В пространстве - т.е. вместо поочередной работы устройство получает часть ресурса, к примеру часть оперативной памяти. Таким образом, в ней может храниться несколько программ.

# Как запускается система?
1. Включаем компьютер и обращаемся по адресу 0xFFFFFFF0
2. Нам отвечает материнская плата, направляя нас в BIOS/UEFI

## BIOS
Начинает свою работу с POST - power on self test, проверяет, что все необходимые устройства в наличии и все работает, поиск загрузочного диска - это диск с которого можно прочитать первые 512 байт, последние 2 байта этого сектора должны хранить значения 0X55 и 0xAA. Этот сектор загружается в память по физическому адресу 0x7C00. После BIOS передает управления в это место.
# Нужное
* [[Linux]]
* [Direct Mapping — Map cache and main memory](https://medium.com/breaktheloop/direct-mapping-map-cache-and-main-memory-d5e4c1cbf73e)
* [Direct-Mapped Cache and its architecture](http://www.mathcs.emory.edu/~cheung/Courses/355/Syllabus/8-cache/dm.html)
* [MIT 6.004](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-004-computation-structures-spring-2017/)
* [Berkley cs61](https://inst.eecs.berkeley.edu/~cs61c/su20/)
* CMU 15-213
	* [materials]([http://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/schedule.html](http://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/schedule.html))
	* [video](https://scs.hosted.panopto.com/Panopto/Pages/Sessions/List.aspx#folderID="b96d90ae-9871-4fae-91e2-b1627b43e25e"&view=2&sortColumn=1&sortAscending=true&maxResults=250)
	* note
		* https://github.com/JonnyKong/CMU-15-213-Intro-to-Computer-Systems
		* https://github.com/xuwd11/15-213_labs
* [nand to tetris](https://www.nand2tetris.org)
* [virtual memory](https://www.youtube.com/playlist?list=PLiwt1iVUib9s2Uo5BeYmwkDFUh70fJPxX)
* [Berkley CS 162: Operating Systems and System Programming](https://inst.eecs.berkeley.edu/~cs162/fa20/)
* [CS 161 Computer security](https://su20.cs161.org)
* [6.S081: Operating System Engineering](https://pdos.csail.mit.edu/6.S081/2021/index.html)
* [OSTEP](https://pages.cs.wisc.edu/~remzi/OSTEP/)
https://www.youtube.com/watch?v=-knefdASOz8
https://thecode.media/intel_inside/
## ABI - Application binary interface
Это набор соглашений о:
1. Как передавать аргументы в функцию
2. Как функция возвращает значение
3. Какие регистры функция должна сохранять
4. И т.д.

Если мы собираем код разными компиляторами, они должны поддерживать одинаковый ABI
