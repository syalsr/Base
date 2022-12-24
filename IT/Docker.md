Основы виртуализации https://linkmeup.ru/blog/1260/

# Docker
Контейнер это что-то на подобие виртуальной машины, но потребляет намного меньше ресурсов, контейнер запускается из образа(image), а образ конструируется на основе Dockerfile

Смотри имя которое нужно и подключаемся к нему

```
docker exec -it postgres-server psql -U postgres // postgres-server - имя нашего образа docker
```

Запускаем докер  на основе image, можем указывать либо имя, либо id
```cmd
docker run [docker_image]
docker search <<name>> - поиск образа в хабе
docker pull <<name>> - скачать образ из хабе в машину
docker build <</path/to/dir>> - собрать образ
docker rm <<anem>> - удалить контейнер
docker logs <<name>> логи контейнера
docker start/stop/restart <<name>> - работа с контейнером
```

Список всех наших запущенных контейнеров.
```
docker ps
```

```
docker build -t hello . #с помощью аргумента -t задаем имя образу, . - указывает на то где находится докерфайл, у нас он в корневой директории, поэтому точка
```

```
docker run rusprofile#запустили образ rusprofile
docker run --name rusprofile -d rusprofile#задаем имя контейнеру, иначе задается рандомное, с помощью ключа -d запускаем его в качестве демона, т.е. будет работать в фоне
docker run --name rusprofile --rm -d rusprofile#--rm - когда контейнер остановится, его нужно удалить, т.е. можем написать docker stop rusprofile и докер удалит его
```

```
docker run -p 8080:8080 -p 8081:8081 -p 9000:9000 syalsr #-p - этот ключ указывает на то, что мы прокидываем порты из докеры которые указали в DOckerfile с помощью EXPOSE на наши порты на локальном компьютере. Первым указываемся порт на хосте, вторым порт в который нужно проброситьв  докер
```



# push to hub
You may need to switch your docker repo to private before docker push. Thanks to the [answer](https://stackoverflow.com/a/42403423/4096935) provided by [Dean Wu](https://stackoverflow.com/users/7607604/dean-wu) and [this comment](https://stackoverflow.com/questions/41984399/denied-requested-access-to-the-resource-is-denied-docker/41984666#comment94770203_41984666) by [ses](https://stackoverflow.com/users/369759/ses), before pushing, remember to **log out**, then **log in** from the command line to your docker hub account
```
# you may need log out first `docker logout` ref. https://stackoverflow.com/a/53835882/248616
docker login
```

So, this means you have to **tag** your image before pushing:
```
docker tag firstimage YOUR_DOCKERHUB_NAME/firstimage
```

and then you should be able to push it.
```
docker push YOUR_DOCKERHUB_NAME/firstimage
```

https://docs.microsoft.com/ru-ru/visualstudio/docker/tutorials/docker-tutorial

