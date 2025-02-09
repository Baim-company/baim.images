# Настройка доступа к изображениям через Nginx
## Как сделать так чтобы Картинки были видны из вне сервиса и каждый мог их видеть по ссылке домена ?
* Для этого нам нужно открыть доступ к папке где находятся картинки и также  создать ngnx file где пропишем путь к нашей директории на сервере и укажем по какому путь мы должны обращаться извне сервера. Также ngnx нам нужен чтобы обращаться к фронтенду не через порт 3000 а напряму к IP

* Начнем :
### 1)Откроем доступ к папке где находятся файлы
### 2)Создадим и настроем файл ngnx
------------


## 1. Открываем доступ к папке с изображениями

### Шаг 1: Определяем путь к папке с файлами

* Перейдите в папку, где хранятся файлы, и выполните команду:
```
pwd
```

* Ожидаемый результат:
```
/home/azureuser/home/baim/files
```

* Сохраните этот путь, он понадобится для настройки Nginx.

### Шаг 2: Устанавливаем права доступа

* Выполните следующие команды:
```
sudo chmod -R 755 /home/azureuser/home/baim/files/
sudo chown -R www-data:www-data /home/azureuser/home/baim/files/
```

--------------
## 2. Установка и настройка Nginx

### Шаг 1: Устанавливаем Nginx

```
sudo apt update
sudo apt install nginx
```

### Шаг 2: Проверяем корректность установки

```
sudo nginx -t
```
* Ожидаемый результат:
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
### Шаг 3: Перезапускаем Nginx для применения изменений
```
sudo systemctl restart nginx
```


## 3. Настройка конфигурации Nginx

### Шаг 1: Определяем путь к конфигурационному файлу

* Выполните команду:
```
sudo nginx -t
```
Или:
```
nginx -V
```
* Ожидаемый результат:
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
### Шаг 2: Редактируем конфигурационный файл

* Открываем конфигурационный файл для редактирования:
```
sudo nano /etc/nginx/nginx.conf
```
Остается тут все что было:
```
user www-data;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 768;
}

http {
    log_format main '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
    access_log /var/log/nginx/access.log main;

    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Включаем конфигурации сайтов
    include /etc/nginx/sites-enabled/*;
}
```

* Нам нужно открыть не это: 
```
sudo nano /etc/nginx/sites-available/default
```
* А это:
```
sudo nano /etc/nginx/sites-available/myconfig
```
* Добавляем следующий блок в конфигурацию:

```
server {
    listen 80;
    server_name 20.200.120.11;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    location /app/images/default/ {
        alias /home/azureuser/home/baim/files/;
        autoindex on;
    }
}
```

* Сохраните изменения и выйдите из редактора (Ctrl + X, затем Y и Enter).

### Шаг 3: Проверяем синтаксис конфигурации
```
sudo nginx -t
```
* Ожидаемый результат:
```
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```
### Шаг 4: Перезапускаем Nginx
```
sudo systemctl restart nginx
```
* Теперь изображения будут доступны по адресу:
http://example.com/files/

