---
title: Мониторинг Postgres в Zabbix с помошью библиотеки libzbxpgsql 
date: 2016-10-09 19:00
template: blog_post
---

В этой статье я рассмотрю установку и настройку шаблона и модуля libzbxpgsql для мониторинга Postgres в Zabbix
<!--more-->

###### Где взять: [http://cavaliercoder.com/libzbxpgsql/download/](http://cavaliercoder.com/libzbxpgsql/documentation/)

##### Системные требования:
PostgreSQL версии 8.4 или выше.
Zabbix версии 2.2 или выше.

В моем примере я использую версию Zabbix 3.0.4 и базу данных Postgres версии 9.4 установленую на операционную систему Ubuntu 14.04.4 LTS, так же в моем примере уже установлен zabbix_agent

Для установки модуля libzbxpgsql можно воспользоваться двумя способами, первый это скачать и установить модуль для Zabbix в ручную, скачать исходники, второй установить из уже собранных пакетов, я выбрал пусть установки из собранного пакета .deb

##### Качаем, устанавливаем и перезапускаем zabbix агент:
```sh
# wget http://s3.cavaliercoder.com/libzbxpgsql/apt/zabbix3/ubuntu/trusty/amd64/libzbxpgsql_1.0.0-1%2Btrusty_amd64.deb
# dpkg -i libzbxpgsql_1.0.0-1+trusty_amd64.deb
# /etc/init.d/zabbix-agent restart
```

Если у вас не запустился агент и выдал ошибку в виде:
```sh
  2785:20161009:083903.617 cannot load module "libzbxpgsql.so": /usr/lib/modules/libzbxpgsql.so: cannot open shared object file: No such file or directory
то необходимо скопировать модуль libzbxpgsql.so в каталог /usr/lib/modules/
```
```sh
# dpkg -l | grep libzbx
# dpkg -L libzbxpgsql | grep so
# mkdir /usr/lib/modules/
# cp /usr/lib/zabbix/modules/libzbxpgsql.so /usr/lib/modules/
# /etc/init.d/zabbix-agent restart
```

Так как в шаблоне используются Zabbix (Active check) необходмо настроить zabbix агент добавив в конфигурацию имя хоста и адрес сервера:
```sh
ServerActive=zabbix.host:10051
Hostname=postgres.host
```
Так же проверьте, что у вас имя узла сети совпадает с Hostname в zabbix агенте и я рекоминидую подключатся по DNS именя хоста

##### Далее нам нужно импортировать шалон для Zabbix
Его можно скачать с [Github](https://raw.githubusercontent.com/cavaliercoder/libzbxpgsql/v1.0.0/template_postgresql_server.xml) 
1. Открываем Web итерфейс Zabbix
2. Переходим Configuration > Templates
3. Жмем на Import
4. Выбираем скаченный файл template_postgresql_server.xml
5. Ставим галочку Create new / Screens
6. Жмем Import 

##### Настройка шаблона:
1. Для описания подключения к Postgres используются макросы, макросы настраиваются на вкладке "Макросы" в настройке узла.
в нашем примере мы будем применять следющие значения:
```sh
{$PG_CONN} => user=monitoring
{$PG_DB} => postgres
``
2. Для того что бы исключить базы данных которые нам не нужны нужно добавить фильтр в Discover PostgreSQL Databases 
Пример:
```sh
{#DATABASE} => ^my_db$
``
3. Что бы добавить исключения для таблиц нужны нужно добавить фильтр в Discover PostgreSQL Tables и Discover PostgreSQL Indexes
Пример:
```sh
{#DATABASE} => ^my_db$
{#TABLE} => ^history.*|^trend.*
```

##### Настройка доступа для Postgres
1. Добавляем роль в базу данных
```sh
CREATE ROLE monitoring WITH LOGIN NOSUPERUSER NOCREATEDB NOCREATEROLE;
GRANT CONNECT ON DATABASE my_db TO monitoring;
```
2. Zabbix агент у нас установлен на томже сервере что и Postgres и нам нужно добавить разрешения в pg_hba.conf и pg_indent.conf
pg_hba.conf:
```sh
# TYPE  DATABASE        USER            ADDRESS                 METHOD
local   all             monitoring                              ident map=monitoring
```

pg_indent.conf:
```sh
# MAPNAME       SYSTEM-USERNAME         PG-USERNAME
monitoring      zabbix                  monitoring
```
и проверим:
```sh
zabbix-server# zabbix_get -s postres.host -p 10050 -k "pg.checkpoints_req[user=monitoring,postgres]"
3
zabbix-server#
```

Более подробная документация о проекте содержится на официалом сайте: [http://cavaliercoder.com/libzbxpgsql/documentation/](http://cavaliercoder.com/libzbxpgsql/documentation/)



