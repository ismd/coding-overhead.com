---
layout: post
title: Установка WordPress в Ubuntu 16.04
excerpt: Разворачиваем WordPress на LAMP
---

В этой статье я опишу установку WordPress CMS на Ubuntu 16.04. Использовать буду связку LAMP (linux, apache, mysql, php).

Для начала нам необходимо установить нужные пакеты:

```
$ sudo apt-get install apache2 mysql-server php curl libapache2-mod-php php-mcrypt php-mysql php-curl php-gd php-mbstring php-xml php-xmlrpc
```

Создадим директорию для блога:

```
$ sudo mkdir /var/www/<имя сайта>
```

### 1. Apache
Чтобы избежать предупреждения при запуске apache нужно добавить одну строчку в файл `/etc/apache2/apache2.conf`

```
$ sudo nano /etc/apache2/apache2.conf
```

```
ServerName <домен или ip-адрес>
```

С помощью команды

```
$ sudo apache2ctl configtest
```

можем убедиться, что с конфигурацией всё хорошо.

Разрешим выполнение .htaccess файлов. Для этого добавим в файл `/etc/apache2/apache2.conf` нужную конфигурацию:

```
$ sudo nano /etc/apache2/apache2.conf
```

```
<Directory /var/www/html/>
AllowOverride All
</Directory>
```

Активируем модуль mod_rewrite:

```
$ sudo a2enmod rewrite
```

Для применения изменений надо перезапустить apache:

```
$ sudo systemctl restart apache2
```

На основе файла `/etc/apache2/sites-available/000-default.conf` создадим в этой же директории конфигурационный файл для нашего блога:

```
$ sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/<имя сайта>.conf
$ sudo nano /etc/apache2/sites-available/<имя сайта>.conf
$ sudo rm /etc/apache2/sites-enabled/000-default.conf
$ sudo ln -s /etc/apache2/sites-available/<имя сайта>.conf /etc/apache2/sites-enabled/<имя сайта>.conf
```

### 2. MySQL
Чтобы подготовить MySQL к использованию выполним команду

```
$ sudo mysql_secure_installation
```

Нам нужно удалить из базы анонимного пользователя, который создан для теста, и тестовую таблицу. А также запретить root-доступ к базе удалённо. Помимо этого `mysql_secure_installation` предложит включить плагин для проверки сложности паролей. Можно не включать его, чтобы не было проблем с другими пакетами, которые добавляют пользователей в базу (например phpMyAdmin). Если изначально включили этот плагин, то отключить его можно зайдя в консоль mysql и выполнив:

```
uninstall plugin validate_password;
```

Затем надо создать базу данных, которую будем использовать для блога:

```sql
CREATE DATABASE <имя базы> DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;
```

Создаём пользователя для работы с нашей базой и выдаём ему права доступа:

```sql
GRANT ALL ON <имя базы>.* TO '<пользователь>'@'localhost' IDENTIFIED BY '<пароль>';
```

Применим изменения:

```sql
FLUSH PRIVILEGES;
```

### 3. PHP
Как минимум желательно повысить приоритет обработки веб-сервером php-файлов. Для этого `index.php` поставить на первое место:

```
$ sudo nano /etc/apache2/mods-enabled/dir.conf
```

### 4. Установка WordPress
После выполнения этих команд нужно будет переместить директорию wordpress в `/var/www/<имя сайта>`

```
$ curl -O https://wordpress.org/latest.tar.gz
$ tar xzvf latest.tar.gz
$ touch wordpress/.htaccess
$ chmod 660 wordpress/.htaccess
$ cp wordpress/wp-config-sample.php wordpress/wp-config.php
$ mkdir wordpress/wp-content/upgrade
```

### 5. Настройка WordPress
Установим права доступа

```
$ sudo chown -R <пользователь>:www-data /var/www/<имя сайта>
```

Чтобы новые файлы, созданные в директории с блогом, унаследовали группу от родительской директории, а не от пользователя, выполнившего команду, запустим следующую команду:

```
$ sudo find /var/www/<имя сайта> -type d -exec chmod g+s {} \;
```

Дадим права на запись в следующие директории:

```
$ sudo chmod g+w /var/www/<имя сайта>/wp-content
$ sudo chmod -R g+w /var/www/<имя сайта>/wp-content/themes
$ sudo chmod -R g+w /var/www/<имя сайта>/wp-content/plugins
```

Сгенерируем секретные ключи:

```
$ curl -s https://api.wordpress.org/secret-key/1.1/salt/
```

Вывод вставим в файл `wp-config.php` вместо пустых значений.

В файл `wp-config.php` нужно не забыть прописать доступ к базе, которую мы создали ранее. Также рекомендуется добавить следующую строку, чтобы указать тип записи файлов из админки:

```
define('FS_METHOD', 'direct');
```

### 6. Обновление
Чтобы обновить WordPress из админки для начала нужно временно поменять права на файлы:

```
$ sudo chown -R www-data /var/www/<имя сайта>
```
