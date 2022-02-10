# Docker-2
- Создан docker-host
- Создан образ из Dockerfile
- На основе образа запущен контейнер на docker-host
- Образ отправлен в репозиторий DockerHub 

`docker pull vchetverikov/otus-reddit`

# Docker-3

Готовим образы для приложения

```
docker pull mongo:latest
docker build -t vchetverikov/post:1.0 ./post-py
docker build -t vchetverikov/comment:1.0 ./comment
docker build -t vchetverikov/ui:1.0 ./ui
```

Создаем сеть
`docker network create reddit`

Запускаемся
```
docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db mongo:latest
docker run -d --network=reddit --network-alias=post vchetverikov/post:1.0
docker run -d --network=reddit --network-alias=comment vchetverikov/comment:1.0
docker run -d --network=reddit -p 9292:9292 vchetverikov/ui:1.0
```

Проверяем работу
`http://localhost:9292`

Изменим Dockerfile для UI и пересоберем
`docker build -t vchetverikov/ui:2.0 ./ui`

Останавливаем контейнеры
`docker kill $(docker ps -q)`

Запускаемся
```
docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db mongo:latest
docker run -d --network=reddit --network-alias=post vchetverikov/post:1.0
docker run -d --network=reddit --network-alias=comment vchetverikov/comment:1.0
docker run -d --network=reddit -p 9292:9292 vchetverikov/ui:2.0
```

Проверяем работу
`http://localhost:9292`

Создаем volume
`docker volume create reddit_db`

Останавливаем контейнеры
`docker kill $(docker ps -q)`

Запускаемся с volume и проверяем наличие поста
```
docker run -d --network=reddit --network-alias=post_db --network-alias=comment_db -v reddit_db:/data/db mongo:latest
docker run -d --network=reddit --network-alias=post vchetverikov/post:1.0
docker run -d --network=reddit --network-alias=comment vchetverikov/comment:1.0
docker run -d --network=reddit -p 9292:9292 vchetverikov/ui:2.0
```

Проверяем работу
`http://localhost:9292`
