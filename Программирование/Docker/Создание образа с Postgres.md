Для этого необходимо создать docker-compose.yaml следующего типа:
```yaml
services:
    postgres:
        image: postgres:latest
		env_file:
			- .env
		ports:
			- '5432:5432'
```
Как видно из конфига для настройки окружения потребуется файл .env. Можно ввести все необходимые параметры прям внутри докер файла, но обычно такая практика не используется:
```js
services:
	postgres:
		 image: postgres 
		 environment: 
			POSTGRES_USER: your_user 
			POSTGRES_PASSWORD: your_password 
		ports: - "5432:5432"
```
Но лучше создать .env файл:
```js
POSTGRES_DB = my_test_db
POSTGRES_USER = root
POSTGRES_PASSWORD = qwerty
POSTGRES_HOST = 127.0.0.2
POSTGRES_PORT = 5432
```
После этого команда `docker-compose up` создаст образ с Postgres.