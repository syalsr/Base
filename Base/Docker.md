# Docker
Контейнер это что-то на подобие виртуальной машины.

Запускаем все наши образы
```
docker ps
```

Смотри имя которое нужно и подключаемся к нему

```
docker exec -it postgres-server psql -U postgres // postgres-server - имя нашего образа docker
```




