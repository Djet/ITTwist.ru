---
title: Мониторинг Postgres в Zabbix с помошью библиотеки libzbxpgsql 
date: 2016-10-09 19:00
template: blog_post
---

В это статье я расмотрю установку и настройку шаблона и модуля libzbxpgsql для мониторинга Postgres в Zabbix


###### Где взять: http://cavaliercoder.com/libzbxpgsql/download/
###### Оф. сайт: 


Системные требования:
PostgreSQL версии 8.4 или выше.
Zabbix версии 2.2 или выше.

В моем примере я использую версию Zabbix 3.0.4 база данных Postgres установлена на операционной системе Ubuntu 14.04.4 LTS, так же в моем примере уже установлен zabbix_agent и снимает основные параметры системы

Для установки модуля libzbxpgsql можно воспользоваться двумя способами, первый это скачать и установить модуль для забикс в ручную второй установить из уже собранных пакетов, я выбрал пусть установки из собраного пакета .deb

Качаем, устанавливаем и перезапускаем zabbix агент:
```sh
# wget http://s3.cavaliercoder.com/libzbxpgsql/apt/zabbix3/ubuntu/trusty/amd64/libzbxpgsql_1.0.0-1%2Btrusty_amd64.deb
# dpkg -i libzbxpgsql_1.0.0-1+trusty_amd64.deb
# /etc/init.d/zabbix-agent restart
```

Если у вас не запустился агент и выдал ошибку в виде:
  2785:20161009:083903.617 cannot load module "libzbxpgsql.so": /usr/lib/modules/libzbxpgsql.so: cannot open shared object file: No such file or directory
то необходимо скопировать модуль libzbxpgsql.so в каталог /usr/lib/modules/

```sh
# dpkg -l | grep libzbx
# dpkg -L libzbxpgsql | grep so
# mkdir /usr/lib/modules/
# cp /usr/lib/zabbix/modules/libzbxpgsql.so /usr/lib/modules/
# /etc/init.d/zabbix-agent restart
```


Далее нам нужно импортировать шалон для Zabbix
Его можно скачать с [Github](https://raw.githubusercontent.com/cavaliercoder/libzbxpgsql/v1.0.0/template_postgresql_server.xml) 
1. Открываем Web итерфейс Zabbix
2. Переходим Configuration > Templates
3. Жмем на Import
4. Выбираем скаченный файл template_postgresql_server.xml
5. Ставим галочку Create new / Screens
6. Жмем Import 



