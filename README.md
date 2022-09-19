# yamdb_final
![workflow badge status](https://github.com/Romashovm/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)

Это проект с курсов Яндекс.Практикум.
Спринт 16: DevOps. CI/CD проекта api_yamdb

### Краткое описание проекта
Проект **YaMDb** собирает отзывы (Review) пользователей на произведения (Titles). Произведения делятся на категории: «Книги», «Фильмы», «Музыка». Список категорий (Category) может быть расширен администратором (например, можно добавить категорию «Изобразительное искусство» или «Ювелирка»).

### Пользовательские роли
* **Аноним** — может просматривать описания произведений, читать отзывы и комментарии.
* **Аутентифицированный пользователь** (user) — может, как и Аноним, читать всё, дополнительно он может публиковать отзывы и ставить оценку произведениям (фильмам/книгам/песенкам), может комментировать чужие отзывы; может редактировать и удалять свои отзывы и комментарии. Эта роль присваивается по умолчанию каждому новому пользователю.
* **Модератор** (moderator) — те же права, что и у Аутентифицированного пользователя плюс право удалять любые отзывы и комментарии.
* **Администратор** (admin) — полные права на управление всем контентом проекта. Может создавать и удалять произведения, категории и жанры. Может назначать роли пользователям.
* **Суперюзер Django** — обладет правами администратора (admin)

### Алгоритм регистрации пользователей
1. Пользователь отправляет POST-запрос на добавление нового пользователя с параметрами email и username на эндпоинт /api/v1/auth/signup/.
2. YaMDB отправляет письмо с кодом подтверждения (confirmation_code) на адрес email.
3. Пользователь отправляет POST-запрос с параметрами username и confirmation_code на эндпоинт /api/v1/auth/token/, в ответе на запрос ему приходит token (JWT-токен).
4. При желании пользователь отправляет PATCH-запрос на эндпоинт /api/v1/users/me/ и заполняет поля в своём профайле (описание полей — в документации).

## Примеры запросов:

### Запрос для просмотра категорий анонимным пользователем:
```
    [GET].../api/v1/categories/
```
### Пример ответа:
```
    {
    "count": 3,
    "next": null,
    "previous": null,
    "results": [
        {
            "name": "Книга",
            "slug": "book"
        },
        {
            "name": "Музыка",
            "slug": "music"
        },
        {
            "name": "Фильм",
            "slug": "movie"
        }
    ]
}
```
### Запрос на получение токена от зарегистрированного пользователя:
```
    [POST].../api/v1/auth/token/
{
  "username":"GrumpyCat"
  "confirmation_code":"61o-bb231266379b3908be63"
}
```
### Пример ответа:
```
{
  "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNjU2MDAxNzkxLCJqdGkiOiIxNjlmOWY1MWE5NzE0MDIyYmI2ZDg2MTFhY2YxMTAyMCIsInVzZXJfaWQiOjEwNn0.C3TPNI7HPl6NWFE0fePPp7G-pF6fEvr0ohtC6me4z4A"
}
```

### Запрос на добавление отзыва пользователем GrumpyCat:
```
    [POST].../api/v1/titles/24/reviews/
{
  "text": "Очень люблю эту книгу. YOU ALWAYS GET BACK TO THE BASICS",
  "score": "10"
}
```
### Пример ответа:
```
{
  "id": 1,
  "author": "GrumpyCat",
  "title": "Generation П",
  "text": "Очень люблю эту книгу. YOU ALWAYS GET BACK TO THE BASICS",
  "pub_date": "2022-06-16T16:35:10.330811Z",
  "score": 10
}   
```

## Шаблон наполнения env-файла(распологается в infra/.env)
- SECRET_KEY - ключ к защите подписанных данных
- DB_ENGINE - используемый движок для доступа к БД
- DB_NAME - имя БД
- POSTGRES_USER - логин для подключения к БД
- POSTGRES_PASSWORD - пароль для подключения к БД
- DB_HOST - название сервиса (контейнера)
- DB_PORT - порт для подключения к БД

## Описание команд для запуска приложения в контейнерах
- Скачать  и установить Docker и docker-compose

Перейти в папку с проектом
```
cd yamdb_final
```

Создать .env файл
```
DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
DB_HOST=db
DB_PORT=5432
SECRET_KEY=key
```

Запустите docker-compose командой
``` 
docker-compose up -d
```
У вас развернётся проект, запущенный через Gunicorn с базой данных Postgres.

## Описание команды для заполнения базы данными

В контейнере web выполнить миграции, создать суперпользователя и собрать статику
```
docker-compose exec web python manage.py migrate

docker-compose exec web python manage.py createsuperuser

docker-compose exec web python manage.py collectstatic --no-input 
```


Проект доступен по адресу http://51.250.77.172/redoc/

##### Проект Ромашова Михаил, студент Яндекс практикума, 33 когорта. Почта romashovm@gmail.com