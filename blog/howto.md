---
title: FAQ по командам Unix
date: 07/10/16 18:14:20
template: blog_post
---

На этой странице собраны полезные для меня Unix команды и решения некоторых проблем.


#### Ошибка в puppet:  undefined method `empty?' for nil:NilClass
Необходимо проверить  стоит ли пробел перед названием клаcа в hiera файле

#### Отчистить MAC адрес на CISCO ( Если после добавления dev интерфейса на OpenVZ прободают контейнеры )
```sh
ipsec-gw.a1s#sh ip arp | i 172.16.100.74
ipsec-gw.a1s#clear arp-cache 172.16.100.6
```

#### cd !!:2 или перейтив созданный каталог:
```sh
$ mkdir -p /tmp/dev/deb/guake
$ cd !!:2
```
