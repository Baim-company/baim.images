# Настройка доступа к изображениям через Nginx

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
sudo nano /etc/nginx/sites-available/default
```
* Добавляем следующий блок в конфигурацию:

```
server {
    listen 80;
    server_name example.com;

    location /files/ {
        root /home/azureuser/home/baim;
        autoindex on;
        index index.html;
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

