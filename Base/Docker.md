# Docker
Контейнер это что-то на подобие виртуальной машины, но потребляет намного меньше ресурсов

Смотри имя которое нужно и подключаемся к нему

```
docker exec -it postgres-server psql -U postgres // postgres-server - имя нашего образа docker
```

Запускаем докер  на основе image, можем указывать либо имя, либо id
```cmd
docker run [docker_image]
docker run postgres
docker run go-app
```

Список всех наших запущенных контейнеров.
```
docker ps
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

#do/start 
1. docker cli
2. image
3. container
4. dockerfile
5. docker compose


