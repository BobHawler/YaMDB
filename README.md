# Проект yamdb_final

![django-app workflow](https://github.com/bobhawler/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)        [![Python](https://img.shields.io/badge/-Python-464646?style=flat&logo=Python&logoColor=56C0C0&color=008080)](https://www.python.org/)
[![Django](https://img.shields.io/badge/-Django-464646?style=flat&logo=Django&logoColor=56C0C0&color=008080)](https://www.djangoproject.com/)
[![Django REST Framework](https://img.shields.io/badge/-Django%20REST%20Framework-464646?style=flat&logo=Django%20REST%20Framework&logoColor=56C0C0&color=008080)](https://www.django-rest-framework.org/)
[![PostgreSQL](https://img.shields.io/badge/-PostgreSQL-464646?style=flat&logo=PostgreSQL&logoColor=56C0C0&color=008080)](https://www.postgresql.org/)
[![JWT](https://img.shields.io/badge/-JWT-464646?style=flat&color=008080)](https://jwt.io/)
[![Nginx](https://img.shields.io/badge/-NGINX-464646?style=flat&logo=NGINX&logoColor=56C0C0&color=008080)](https://nginx.org/ru/)
[![gunicorn](https://img.shields.io/badge/-gunicorn-464646?style=flat&logo=gunicorn&logoColor=56C0C0&color=008080)](https://gunicorn.org/)
[![Docker](https://img.shields.io/badge/-Docker-464646?style=flat&logo=Docker&logoColor=56C0C0&color=008080)](https://www.docker.com/)
[![Docker-compose](https://img.shields.io/badge/-Docker%20compose-464646?style=flat&logo=Docker&logoColor=56C0C0&color=008080)](https://www.docker.com/)
[![Docker Hub](https://img.shields.io/badge/-Docker%20Hub-464646?style=flat&logo=Docker&logoColor=56C0C0&color=008080)](https://www.docker.com/products/docker-hub)
[![GitHub%20Actions](https://img.shields.io/badge/-GitHub%20Actions-464646?style=flat&logo=GitHub%20actions&logoColor=56C0C0&color=008080)](https://github.com/features/actions)
[![Yandex.Cloud](https://img.shields.io/badge/-Yandex.Cloud-464646?style=flat&logo=Yandex.Cloud&logoColor=56C0C0&color=008080)](https://cloud.yandex.ru/)

## Страницы проекта:

http://158.160.31.114/api/v1/<br>
http://158.160.31.114/admin/<br>
http://158.160.31.114/redoc/<br>


## Что делает Workflow в этом проекте:

-   tests - Проверка кода на соответствие стандарту PEP8 (с помощью пакета flake8) и запуск pytest. Дальнейшие шаги выполнятся только если push был в ветку master или main.
-   build_and_push_to_docker_hub - Сборка и доставка докер-образов на Docker Hub
-   deploy - Автоматический деплой проекта на боевой сервер. Выполняется копирование файлов из репозитория на сервер. Сбор и подгрузка статики.
-   send_message - Отправка уведомления об успешном деплое в Telegram

### Подготовка для запуска workflow

Создайте и активируйте виртуальное окружение, обновите pip:

```
python3 -m venv venv
. venv/bin/activate
python3 -m pip install --upgrade pip
```

Отредактируйте файл `nginx/default.conf` и в строке `server_name` впишите IP виртуальной машины (сервера).  
Скопируйте подготовленные файлы `docker-compose.yaml` и `nginx/default.conf` из вашего проекта на сервер:

```
scp docker-compose.yaml <username>@<host>/home/<username>/docker-compose.yaml
sudo mkdir nginx
scp default.conf <username>@<host>/home/<username>/nginx/default.conf
```

В репозитории на Гитхабе добавьте данные в `Settings - Secrets - Actions secrets`:

```
DOCKER_USERNAME - имя пользователя в DockerHub
DOCKER_PASSWORD - пароль пользователя в DockerHub
HOST - ip-адрес сервера
USER - пользователь
SSH_KEY - приватный ssh-ключ (публичный должен быть на сервере)
PASSPHRASE - кодовая фраза для ssh-ключа
DB_ENGINE - django.db.backends.postgresql
DB_HOST - db
DB_PORT - 5432
SECRET_KEY - секретный ключ приложения django (необходимо чтобы были экранированы или отсутствовали скобки)
ALLOWED_HOSTS - список разрешённых адресов
TELEGRAM_TO - id своего телеграм-аккаунта (можно узнать у @userinfobot, команда /start)
TELEGRAM_TOKEN - токен бота (получить токен можно у @BotFather, /token, имя бота)
DB_NAME - postgres (по умолчанию)
POSTGRES_USER - postgres (по умолчанию)
POSTGRES_PASSWORD - postgres (по умолчанию)
```

## Как запустить проект на сервере:

Установите Docker и Docker-compose:

```
sudo apt install docker.io
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Проверьте корректность установки Docker-compose:

```
sudo  docker-compose --version
```

Создайте папку `nginx`:

```
mkdir nginx
```

### После успешного деплоя:

Примените миграции:

```
docker-compose exec web python manage.py makemigrations
docker-compose exec web python manage.py migrate --noinput
```

Создайте суперпользователя:

```
docker-compose exec web python manage.py createsuperuser
```

или

```
docker-compose exec web python manage.py loaddata fixtures.json
```
### Примеры некоторых запросов API

Регистрация пользователя:
```
POST /api/v1/auth/signup/
```
Получение данных своей учетной записи:
```
GET /api/v1/users/me/
```
Добавление новой категории:
```
POST /api/v1/categories/
```
Удаление жанра:
```
DELETE /api/v1/genres/{slug}
```
Частичное обновление информации о произведении:
```
PATCH /api/v1/titles/{titles_id}
```
Получение списка всех отзывов:
```
GET /api/v1/titles/{title_id}/reviews/
```
Добавление комментария к отзыву:
```
POST /api/v1/titles/{title_id}/reviews/{review_id}/comments/
```


### Разработчики api_yamdb:
Dmitriy003, BobHawler, DoctorNew

### CI/CD engineer:
Anatoly Konovalov (BobHawler)
