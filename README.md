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

# Docker-4

- Изменен docker-compose под кейс с множеством сетей
- Параметризован с помощью переменных окружений:
  • порт публикации сервиса ui
  • версии сервисов
- Параметризованные параметры записаны в отдельный файл compose-config.env
- Файл compose-config.env добавлен в .gitignore
- Добавлен файл .env.example

**Задание:**

Узнайте как образуется базовое имя проекта. Можно
ли его задать? Если можно то как?

**Решение**

Для базового имени проекта используется имя каталога запуска проекта.

Задать имя можно с помощью переменной окружения COMPOSE_PROJECT_NAME (например, через .env файл) 
либо с помощью параметра **-p** командной строки

# Gitlab-ci-1

- Подготовлен Gitlab
- Подготовлен репозиторий с кодом
- Описан gitlab-ci с определением окружения

# Monitoring-1

Ссылка на dockerhub https://hub.docker.com/repository/docker/vchetverikov


# Logging-1
 
- Логирование контейнеров, сбор и визуализация логов
- Развертывание окружения для сбора и обработки логов

# Kubernetes-1

- Добавлены манифесты deployment для частей приложения reddit
- Установлен minikube
- Добавлена папка the_hard_way и пустые файлы, которые требовали тесты. Ни про папку,  ни про файлы в методичке ни слова. 

# Kubernetes

- Подготовлены манифесты деплойментов, сервисов, неймспейса для приложения reddit
- Приложение развернуто в minikube
- Приложение развернуто в кластере k8s в yandex cloud
- Можно посмотреть http://51.250.66.188:32138/
- ![img.png](img.png)

# Kubernetes 3
- Развернут ингресс для ui
- Настроены Network policy
- Создано постоянное хранилище для mongo
