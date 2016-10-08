---
title: FAQ по командам Unix
date: 07/10/16 18:14:20
template: blog_post
---

На этой странице собраны полезные для меня Unix команды и решения некоторых проблем.



####Ошибка в puppet:  undefined method `empty?' for nil:NilClass
Необходимо проверить  стоит ли пробел перед названием клаcа в hiera файле


####Отчистить MAC адрес на CISCO ( Если после добавления dev интерфейса на OpenVZ прободают контейнеры )
```sh
ipsec-gw.a1s#sh ip arp | i 172.16.100.74
ipsec-gw.a1s#clear arp-cache 172.16.100.6
```

####cd !!:2 или перейтив созданный каталог:
```sh
$ mkdir -p /tmp/dev/deb/guake
$ cd !!:2
```


####Расширение раздела LVM:

```sh
$ lvextend -l +100%FREE /dev/mapper/a1s16-root
$ resize2fs /dev/mapper/a1s16-root
```


####Установка .deb файла с зависимостями:
```sh
$ sudo apt-get install gdebi-core
$ sudo gdebi webmin_1.620_all.deb
```


####Добавить ssh ключ для Cisco

```sh
ipsec-gw#sh ip ssh 
 
SSH Enabled - version 1.99
Authentication timeout: 120 secs; Authentication retries: 3
Minimum expected Diffie Hellman key size : 1024 bits
IOS Keys in SECSH format(ssh-rsa, base64 encoded):
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAsdAgQCSAG0MO4Cv1HrCwaUAoU4ofO7DLJp4a1mqsg
+QKI+FGm043XR0pCX73faHI/D2BS+soBOhaOwTXLzYasdQRJ8kH36pEIvdCW+E077wov
R32kLJ4IZ6DO5DJXc9f1jircPXxul0167C8u/g6ShGGDE27FJ31Y09yeB9jrRYjD
3Bl6WwvvBw==
```

 
####Просмотреть http трафик с выводом запроса и хоста

```sh
tshark -iany -f 'port 80 and host 172.16.100.8' -l -t ad -n -R 'http.request' -T fields -e http.host -e http.request.uri
```

 
###Посмотреть статистику по использованию CPU за день, она собирается в debian по умолчанию каждые 10 минут командой atsa и складывается в бинарный лог. С сортировкой по первому load average.

```sh
sar -P -f /var/log/atsar/atsa01 | tr -s ' ' | sort -t' ' -k5,5
```


###Проверить http сайт по telnet:
```sh
telnet host 80
GET /index.htm HTTP/1.1
host: www.esqsoft.globalservers.com
```


###Скукожить повторяющиеся пробелы до одного пробела  (параметр sqeeeze у tr):
```sh
ipmitool -I lanplus -H 91.143.6.178 -U ipmi -P w**** sensor | grep "Ambient Temp" | tr -s ' ' | cut -d' ' -f 4
```

###Узнать внешний ip адрес:
```sh
wget -q -O - http://formyip.com/ | awk '/The/{print $5}'
```


####Скомпилить Cи программу с оптимизацией
```sh
gcc exploit.c -O2 -o b.out
```


####Убрать точку в фразе с помощью sed. Использование шаблонов
```sh
echo sddfdf.sdds | sed -r 's/(^.*)\.(.*)/\1/'
```


 

 
