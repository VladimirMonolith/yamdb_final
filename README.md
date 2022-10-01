# CI/CD API сервиса YaMDb

![ci/cd_api_yamdb workflow](https://github.com/VladimirMonolith/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)

## Описание12

CI/CD API сервиса YaMDb.Workflow подразумевает автоматический запуск тестов, обновление образа проекта на DockerHub, автоматический деплой на боевой сервер и запуск сервиса, отправку уведомления о успешном завершении workflow в Телеграм при выполнении команды push.

Проект YaMDb собирает отзывы пользователей на произведения.Произведения делятся на категории: "Категории", "Фильмы", "Музыка".Список категорий (Category) может быть расширен администратором (например, можно добавить категорию "Артхаус").

### Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или послушать музыку

В каждой категории есть произведения: книги, фильмы или музыка. Например, в категории "Книги" могут быть произведения "Винни-Пух и все-все-все" и "Марсианские хроники", а в категории "Музыка" — песня "Давеча" группы "Насекомые" и вторая сюита Баха.Произведению может быть присвоен жанр из списка предустановленных (например, "Сказка", "Рок" или "Артхаус").Новые жанры может создавать только администратор.
Благодарные или возмущённые пользователи оставляют к произведениям текстовые отзывы (Review) и ставят произведению оценку в диапазоне от одного до десяти (целое число); из пользовательских оценок формируется усреднённая оценка произведения — рейтинг (целое число). На одно произведение пользователь может оставить только один отзыв.

#### Доступный функционал

- Для аутентификации используются JWT-токены.
- У неаутентифицированных пользователей доступ к API только на уровне чтения.
- Создание объектов разрешено только аутентифицированным пользователям.На прочий фунционал наложено ограничение в виде административных ролей и авторства.
- Управление пользователями.
- Получение списка всех категорий и жанров, добавление и удаление.
- Получение списка всех произведений, их добавление.Получение, обновление и удаление конкретного произведения.
- Получение списка всех отзывов, их добавление.Получение, обновление и удаление конкретного отзыва.  
- Получение списка всех комментариев, их добавление.Получение, обновление и удаление конкретного комментария.
- Возможность получения подробной информации о себе и удаления своего аккаунта.
- Фильтрация по полям.

#### Документация к API доступна по адресу <http://158.160.11.40/redoc/> после запуска сервера с проектом

#### Технологии

- Python 3.7
- Django 2.2.16
- Django Rest Framework 3.12.4
- Simple JWT
- Docker
- Docker-compose
- PostgreSQL
- Gunicorn
- Nginx
- GitHub Actions
- Выделенный сервер Linux Ubuntu 22.04 с публичным ip

#### Запуск проекта в dev-режиме

- Склонируйте репозиторий:  
``` git clone <название репозитория> ```
- Перейдите в директорию infra:  
``` cd yamdb_final/infra/ ```  
- Создайте файл .env по образцу:  
``` cp .env.example .env ```  
- Выполнить вход на удаленный сервер
- Установить docker:  
``` sudo apt install docker.io ```
- Установить docker-compose:

``` bash
    sudo apt-get update
    sudo apt-get install docker-compose-plagin
    sudo apt install docker-compose     
```

или воспользоваться официальной [инструкцией](https://docs.docker.com/compose/install/)

- Находясь локально в директории infra/, скопировать файлы docker-compose.yml и nginx.conf на удаленный сервер:

```bash
scp docker-compose.yml <username>@<host>:/home/<username>/
scp -r nginx/ <username>@<host>:/home/<username>/
```

- Для правильной работы workflow необходимо добавить в Secrets данного репозитория на GitHub переменные окружения:

```bash
Переменные PostgreSQL, ключ проекта Django и их значения по-умолчанию можно взять из файла .env.example, затем установить свои.

DOCKER_USERNAME=<имя пользователя DockerHub>
DOCKER_PASSWORD=<пароль от DockerHub>

USER=<username для подключения к удаленному серверу>
HOST=<ip сервера>
PASSPHRASE=<пароль для сервера, если он установлен>
SSH_KEY=<ваш приватный SSH-ключ (для получения команда: cat ~/.ssh/id_rsa)>

TELEGRAM_TO=<id вашего Телеграм-аккаунта>
TELEGRAM_TOKEN=<токен вашего бота>
```

#### Workflow проекта

- **запускается при выполнении команды git push**
- **tests:** проверка кода на соответствие PEP8, запуск pytest.
- **build_and_push_to_docker_hub:** сборка и размещение образа проекта на DockerHub.
- **deploy:** автоматический деплой на боевой сервер и запуск проекта.
- **send_massage:** отправка уведомления пользователю в Телеграм.

#### После успешного результата работы workflow зайдите на боевой сервер

- Примените миграции:  
``` sudo docker-compose exec web python manage.py migrate ```
- Создайте суперпользователя:  
``` sudo docker-compose exec web python manage.py createsuperuser ```
- Загрузите тестовые данные:  
``` sudo docker-compose exec web python manage.py loaddata fixtures.json ```

#### Примеры некоторых запросов API

Регистрация пользователя:  
``` POST /api/v1/auth/signup/ ```  
Получение данных своей учетной записи:  
``` GET /api/v1/users/me/ ```  
Добавление новой категории:  
``` POST /api/v1/categories/ ```  
Удаление жанра:  
``` DELETE /api/v1/genres/{slug} ```  
Частичное обновление информации о произведении:  
``` PATCH /api/v1/titles/{titles_id} ```  
Получение списка всех отзывов:  
``` GET /api/v1/titles/{title_id}/reviews/ ```  
Добавление комментария к отзыву:  
``` POST /api/v1/titles/{title_id}/reviews/{review_id}/comments/ ```  

#### Полный список запросов API находятся в документации

#### Автор

Гут Владимир - [https://github.com/VladimirMonolith](http://github.com/VladimirMonolith)
