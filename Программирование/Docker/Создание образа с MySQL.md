Для этого необходимо создать docker-compose.yaml следующего типа:
```yaml
services:
    mysql:
        image: mysql:latest
        env_file:
            - .env
        ports:
            - '3306:3306'
```
А внутри .env файла указать следующие параметры:
```js
MYSQL_USER = user
MYSQL_PASSWORD = qwerty
MYSQL_ROOT_PASSWORD = root
MYSQL_DATABASE = my_test_db
MYSQL_HOST = 127.0.0.3
```