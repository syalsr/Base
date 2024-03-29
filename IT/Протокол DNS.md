# Протокол DNS
Система доменных имен позволяет преобразовывать  имена компьютеров в [[Протокол IP#IP-адреса|IP-адрес]], использует [[Протокол UDP]] и модель клиент-сервер

Для чего это нужн?
1. Чтобы вводить осмысленное имя, а не [[Протокол IP#IP-адреса|IP адрес]]
2. Если адрес поменяется, его лишь нужно будет заменить в системе, не сообщая об этом пользователям, поскольку имя остается прежним.

```
www.yandex.ru - 77.88.55.66
```
| Имя компьютера | Домен второго уровня | Домен верхнего уровня |
| -------------- | -------------------- | --------------------- |
| taxi.          | yandex.              | ru.                   |
| www.           | yandex.              | ru.                   |

![[dns.excalidraw]]

Как происходит работа DNS сервера? Чтение доменного имени происходит справа налево.
1. Допустим, я хочу узнать [[Протокол IP|IP-адрес]] maps.yandex.ru
2. Запрашиваем IP-адрес у корневого сервера, он не знает
3. Но знает адрес DNS сервера который отвечает за ru
4. Отправляем снов запрос, но и он не знает такого IP, но знает DNS сервер который отвечает за yandex
5. И наконец DNS сервер яндекса выдает нам нужный адрес

Сервер DNS предоставляется провайдером/организацией. Компьютер получает адрес локального сервера DNS по [[Протокол DHCP|DHCP]].

Режим работы DNS:
1. Итеративный
	1. Если сервер отвечает за данную доменную зону – он возвращает ответ 
	2. Если не отвечает, то возвращает адрес DNS-сервера, у которого есть более точная информация
2. Рекурсивный - сервер сам выполняет запросы к другим серверам DNS, чтобы найти нужный адрес

Существуют 2 типа ответов DNS:
1. Авторитетный (authoritative, “заслуживающий доверия”)
	1. Ответ от сервера, обслуживающего доменную зону 
	2. Получен из файлов на диске сервера
2. Неавторитетный (non-authoritative, “не заслуживающий доверия”)
	1. Ответ от сервера, который не обслуживает доменную зону
	2. Получен из кэша, данные могли устареть

# Тип записи
Каждая DNS запись (Resource Record, RR) имеет: 
1. Тип записи – для чего предназначена запись
	1. A – IPv4 адрес компьютера 
	2. AAAA – IPv6 адрес компьютера
2. Класс записи – в каких сетях используется (IN – Интернет)
# Формат пакета
![[dnsframe.excalidraw]]
1. Флаги
	1. QR – запрос (0) или ответ (1)
	2.  OPCODE (4 бита) – тип запроса, 0 – стандартный запрос 
	3. AA – авторитетный ответ (1) или нет (0) 
	4. TC – пакет был обрезан (1) или не был (0) 
	5. RD – запрос на рекурсивный режим 
	6. RA – рекурсивный режим доступен 
	7. Z – зарезервировано 
	8. RCODE (4 бита) – статус, 0 – успешно, другие коды - ошибка

Пример запроса:

| Имя           | Тип записи  | Класс записи    |
| ------------- | ----------- | --------------- |
| www.yandex.ru | 1(A запись) | 1(IN, Интернет) |

Пример ответа:

| Имя           | Тип записи  | Класс записи    | Время жизни(TTL) | Длина данных | Данные      |
| ------------- | ----------- | --------------- | ---------------- | ------------ | ----------- |
| www.yandex.ru | 1(A запись) | 1(IN, Интернет) | 90               | 4            | 77.88.55.66 |


