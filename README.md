Лабораторная работа №5: Запуск сайта в контейнере
=================================================

Цель работы
-----------

Ознакомиться с работой много контейнерного приложения на базе `docker-compose`.

Задание
-------

1. Создать php приложение на базе трех контейнеров: nginx, php-fpm, mariadb, используя docker-compose.

Подготовка
----------

Для выполнения данной работы необходимо иметь установленный на компьютере Docker.

Работа выполняется на базе лабораторной работы №5.

Выполнение
----------

1. Создайте репозиторий `containers06` и скопируйте его себе на компьютер.

2. В директории `containers06` создайте директорию `mounts/site`. В данную директорию перепишите сайт на php, созданный в рамках предмета по php.

3. Создайте файл .gitignore в корне проекта и добавьте в него строки:
```apacheconf
server {
    listen 80;
    server_name _;
    root /var/www/html;
    index index.php;
    location / {
        try_files $uri $uri/ /index.php?$args;
    }
    location ~ \.php$ {
        fastcgi_pass backend:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```
4. Создайте в директории containers06 файл docker-compose.yml со следующим содержимым:
 ```dockerfile 
version: '3.9'

services:
  frontend:
    image: nginx:1.19
    volumes:
      - ./mounts/site:/var/www/html
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
    ports:
      - "80:80"
    networks:
      - internal
  backend:
    image: php:7.4-fpm
    volumes:
      - ./mounts/site:/var/www/html
    networks:
      - internal
    env_file:
      - mysql.env
  database:
    image: mysql:8.0
    env_file:
      - mysql.env
    networks:
      - internal
    volumes:
      - db_data:/var/lib/mysql

networks:
  internal: {}

volumes:
  db_data: {}
 ```

5. Создайте файл mysql.env в корне проекта и добавьте в него строки:
```dotenv
MYSQL_ROOT_PASSWORD=secret
MYSQL_DATABASE=app
MYSQL_USER=user
MYSQL_PASSWORD=secret
```

6. Запустите контейнеры командой:
```bash
docker-compose up -d
```

Выполнение работы
-----------------

1. Создан репозиторий `containers06`.
2. Настроен конфигурационный файл nginx
3. Создан файл `.gitignore`
4. Создан файл `docker-composer.y`
4. Проверена работоспособность сайта.

Выводы
------

В результате выполнения лабораторной работы был подготовлен образ контейнера для запуска веб-сайта на базе Apache HTTP
Server + PHP (mod_php) + MariaDB с использованием Docker. Работоспособность сайта WordPress была успешно проверена.
