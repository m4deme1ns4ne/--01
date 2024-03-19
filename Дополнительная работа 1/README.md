# Как развернуть MYSQL в PHPMYADMIN с помощью DOCKER

Для развёртывания phpMyAdmin с MySQL в Docker, вам нужно создать docker-compose.yml файл, который будет содержать конфигурацию для обоих контейнеров.

---

Для начала вам нужно иметь уже контейнер с развёрнутой базой данных mysql.

Чтобы его создать нужно ввести команду:

```bash
docker run --name mysql-test \
-v /Users/northbug/Development/testing/dbases:/var/lib/mysql \
-p 33060:3306 \
-e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:8.0.36
```

#### Примечание 1

Вам нужно заменить путь к локальному каталогу, который вы мэпите внутрь контейнера с СУБД.
Можно заменить название mysql-test и пароль my-secret-pw.

#### Примечание 2

Если контейнер не запустится, т.е. не будет выведен при запуске команды docker ps -a, то может потребоваться изменить порт, указываемый с помощью ключа -p. Например, так: -p 63306:3306

---

Подключаетесь к контейнеру с помощью MySQLWorkbench. Указываем правильный порт, который определили выше.

---

#### Для просмотра работающих контейнеров:

```docker
docker ps
```

#### Для просмотра всех контейнеров:

```docker
docker ps -a
```

Обращайте внимание на id, name.

#### Для остановки контейнера:

```docker
docker stop <id или name контейнера>
```

id можно указывать частично (первые 3-4 символа).

---

#### Для запуска контейнера:

```docker
docker start <id или name контейнера>
```

id можно указывать частично (первые 3-4 символа).

Эти же действия указываются через Docker Desktop (кнопка "Play" / "Stop")

Чтобы удалить работающий контейнер необходимо его остановить или вызвать удаление с ключом -f

---

#### Например:

```docker
docker rm -f <id или name контейнера>
```

или

```docker
docker stop <id или name контейнера>
```

```docker
docker rm <id или name контейнера>
```

---

Нужно создать файл docker-compose.yml в проекте с содержанием:

```docker
version: '3'

services:
  db:
    image: mysql:latest
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: your_root_password
      MYSQL_DATABASE: your_database_name
      MYSQL_USER: your_username
      MYSQL_PASSWORD: your_password

  phpmyadmin:
    image: phpmyadmin/phpmyadmin:latest
    restart: always
    ports:
      - "8080:80"
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: your_root_password
```

Поменяйте your_root_password, your_database_name, your_username и your_password на ваши значения.

#### Пример:

```docker
version: "3"

services:
  db:
    image: mysql:latest
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 1324
      MYSQL_DATABASE: hello_phpmyadmin
      MYSQL_USER: alexander
      MYSQL_PASSWORD: 1324

  phpmyadmin:
    image: phpmyadmin/phpmyadmin:latest
    restart: always
    ports:
      - "8080:80"
    environment:
      PMA_HOST: db
      MYSQL_ROOT_PASSWORD: your_root_password

```

---

1. После создания docker-compose.yml файла, выполните следующие команды в директории с файлом:

   ```docker
   docker-compose up -d
   ```

2. Откройте браузер и перейдите по адресу [http://localhost:8080](http://localhost:8080) или [http://your_server_ip:8080](http://your_server_ip:8080) для доступа к phpMyAdmin. Введите учётные данные для подключения к MySQL.
