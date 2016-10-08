---
title: FAQ по командам Unix
date: 07/10/12 18:14:20
template: blog_post
---

##На этой странице собраны полезные для меня Unix команды и решения некоторых проблем.

<!--more-->

###Ошибка в puppet:  undefined method `empty?' for nil:NilClass:
Необходимо проверить  стоит ли пробел перед названием клаcа в hiera файле


###Отчистить MAC адрес на CISCO ( Если после добавления dev интерфейса на OpenVZ прободают контейнеры ):
```sh
ipsec-gw#sh ip arp | i 172.16.0.74
ipsec-gw#clear arp-cache 172.16.0.6
```

###cd !!:2 или перейти в созданный каталог:
```sh
$ mkdir -p /tmp/dev/deb/guake
$ cd !!:2
```


###Расширение раздела LVM:

```sh
$ lvextend -l +100%FREE /dev/mapper/a16-root
$ resize2fs /dev/mapper/a16-root
```


###Установка .deb файла с зависимостями:
```sh
$ sudo apt-get install gdebi-core
$ sudo gdebi webmin_1.620_all.deb
```


###Добавить ssh ключ для Cisco:

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

 
###Просмотреть http трафик с выводом запроса и хоста:

```sh
tshark -iany -f 'port 80 and host 172.16.0.8' -l -t ad -n -R 'http.request' -T fields -e http.host -e http.request.uri
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


###Скомпилить Cи программу с оптимизацией:
```sh
gcc exploit.c -O2 -o b.out
```


###Убрать точку в фразе с помощью sed. Использование шаблонов:
```sh
echo sddfdf.sdds | sed -r 's/(^.*)\.(.*)/\1/'
```

###Подсветка tail
```sh
tail -f FILE | grep --color=always KEYWORD
```
###Коннект по telnet без expect:
```sh
(sleep 3; echo username; sleep 3; echo password; sleep 5; echo "command"; sleep 3; echo "exit") | telnet hostname port
```
###Автологин на циско роутеры с помощью espect (телнет):

```sh
#!/usr/bin/expect
set timeout 5
set hostname [lindex $argv 0]
set username "username"
set password "password"
set enablepassword "password"
spawn telnet $hostname
expect "Username:" {
  send "$username\n"
  expect "Password:"
  send "$password\n"
  expect ">" {
    send "en\n"
    expect "Password:"
    send "$enablepassword\n"
  }
  interact
}
```
 
###Сортировка по размеру с помощью ls:
```sh
ls -lS
```

###Просмотр лога за определенное время:
```sh
$ grep -i "`date +%b" "%d" "`13:4[0-5]" syslog
```

###Awk суммируем 3-й столбец из лог файла:
```sh
cat logfile| awk '{s += $3} END {print s}'
```

###Десктоп в спячку с помощью dbus-send:

```sh
dbus-send --system --print-reply --dest="org.freedesktop.Hal"
/org/freedesktop/Hal/devices/computer
org.freedesktop.Hal.Device.SystemPowerManagement.Hibernate
```

###Удаление непечатных символов sed ^М по коду:

```sh
sed 's/'"$(printf '\015')"'$//g' имя_файла
```

###Количество коннектов на порт по ип адресам:

```sh
netstat -anp | grep ":4000" | awk '{print $5}' | cut -d":" -f 1 | sort | uniq -c | sort -nr | head -n 30 | awk '{ print $2 "\t"$1 }'
```

Проверка доступности порта
1. Один порт
```sh
nc -znvvw 3 10.1.1.1 80
```
2. Диапазон
```sh
nc -znvvw 3 10.1.1.1 8080-8081
```

###Из какого пакета файл:
```sh 
root@brownie:# dpkg -S /etc/hdparm.conf
hdparm: /etc/hdparm.conf
```

###Искать, перемещать, архивировать, удалять:
```sh
find /opt/server/default/log/ADR-????-??-$(date --date="1 day ago" +%d)-??.log -exec mv {} /backup/$(date +%Y)/$(date +%m)
find /backup/$(date +%Y)/$(date +%m)/ADR-????-??-$(date --date="1 day ago" +%d)-??.log -exec tar czf '{}.tgz' '{}'
find /backup/$(date +%Y)/$(date +%m)/ADR-????-??-$(date --date="1 day ago" +%d)-??.log -exec rm {}
```
Пример сформированной строки поиска:
/backup/2013/06/ADR-2013-06-13-00.log

###Применение патча (нужно находиться в каталоге к которому применяется патч):
```sh
patch -p1 < {/path/to/patch/file}
```
 
###Сделать загрузочный cd из папки:
```sh
genisoimage -o /new.iso -b isolinux/isolinux.bin  -c isolinux/boot.cat -no-emul-boot -boot-load-size 4  -boot-info-table -J -R -V disks .
```
###Включение / выключение debug на cisco:
```sh
no debug crypto isakmp
no debug crypto isakmp error
no debug crypto isakmp ha
no debug crypto ipsec
no debug crypto ipsec error
no debug crypto routing
no debug crypto ha
no debug crypto engine error
no debug crypto engine packet
 
sh log
```

###Дамп трафика из под суперпользователя писать в файл под правами текущего пользователя:
```sh
sudo tshark -i any -f "host 127.0.0.1" -w - > ~/var/_tmp/dump.pcap
```
 
###Тест скорости:
```sh
wget --output-document=/dev/null \
http://speedtest.wdc01.softlayer.com/downloads/test500.zip
```
###Vim

* 0 ("ноль") — в начало текущей строки;
* $ — в конец текущей строки
* w — на слово вправо
* b — на слово влево
* } — абзац вниз
* { — абзац вверх
* gg — перейти в начало файла
* G — перейти в конец файла
* <number>G — перейти на конкретную строку <number>
* /<text><CR> — перейти к <text>
* ?<text><CR> — то же самое, но искать назад
* n — повторить поиск
• N — повторить поиск назад
Поиск и замена в Vim.

:%s/ussd-qa/ussd-qa2/g

###Сетевые адреса linux udev:

Если хочется чтобы после перестановки хардов на другой комп сетевые карты соответствовали тем же сетевым на предыдущем компе

```sh
vim /etc/udev/rules.d/70-persistent-net.rules
```
 
###Сделать RAM диск:
```sh
mkdir /mnt/tmpfs/
chmod 777 /mnt/tmpfs/
mount -t tmpfs -o size=5000M tmpfs /mnt/tmpfs/
```
 
###Сделать grep по диапазону значений:
```sh
grep [1-3] test.txt
```

###Передача интерактивных команд по telnet/ssh:

```sh
(echo "sh run"; sleep 3; exit;) | ssh admin@172.16.0.111
```

###Монтирование всех разделов для полноценного chroot (после этого в chroot можно запускать сервисы):

```sh
mount /dev/sda2 /mnt #sda2 is my root partition
mount /dev/sda1 /mnt/boot #sda1 is my boot partition
for i in /dev /dev/pts /proc /sys; do mount -B $i /mnt$i; done
cp /etc/resolv.conf /mnt/etc/ #makes the network available after chrooting
chroot /mnt
```

###Универсальная команда для добавления ключей:

```sh
gpg --recv-keys 1C4CBDCDCD2EFD2A &&  gpg --armor --export 1C4CBDCDCD2EFD2A |apt-key add -
```

###По непонятной причине перестает ходить трафик снаружи:
Очистить nat таблицу на шюзе, перезалить конфиг
```sh
iptables -F -t nat && iptables -F -t mangle && iptables -F -t filter && iptables-restore < /etc/network/iptables.conf
```

###Проверить подключение по clns:
Команды:
```sh
tshark -i bond0 -f "clnp or isis or esis"
show isis hostname
show clns ne
show isis database
debug isis adj
show debugging
show log
```

###Проверка GRE туннеля:
```sh
sh int Tu100
ping y.y.y.y source x.x.x.x
 
ping 10.78.2.228 source 10.241.0.2
 
 ping clns zz.0z00.0000.0000.0000.000z.000z.<clns_addr>.00
```

###Добавить пользователя и роль в Postgres:

```sh
$ createuser -P -e --createdb --encrypted --login --createrole -p 5430 tcp_test
CREATE ROLE tcp_test ENCRYPTED PASSWORD 'md548a6f317b149ccc0sdfa16c4871bc9' NOSUPERUSER CREATEDB CREATEROLE INHERIT LOGIN;
```
ещё способ:
```sh
CREATE USER nagios WITH PASSWORD 'password';
GRANT ALL PRIVILEGES ON DATABASE template0 to nagios;
```

###Добавить базу данных в Postgres:
```sh
$ createdb -e --owner=tcp_test --locale=utf8 -p 5430 tcp_test
CREATE DATABASE tcp_test OWNER tcp_test LC_COLLATE 'utf8' LC_CTYPE 'utf8';
```
###Отправить файл на почту:
```sh
mutt -s DUMP -a 127.0.0.1_900.pcap -- user@server.com
```

###Convert SSH2 key to OpenSSH key:

```sh 
#ssh-keygen -i -f ~/.ssh/id_dsa_1024_a.pub > ~/.ssh/id_dsa_1024_a_openssh.pub
```

###Установка и подключение репозитория JAVA7:

```sh
echo "deb http://ppa.launchpad.net/webupd8team/java/ubuntu precise main" > /etc/apt/sources.list.d/webupd8team-java.list
echo "deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu precise main" >> /etc/apt/sources.list.d/webupd8team-java.list
apt-key adv --keyserver keyserver.ubuntu.com --recv-keys EEA14886
apt-get update
apt-get install oracle-java7-installer
```

###Команда для переноса OpenVZ контейнеров:
```sh
DATE1=`date`;
DIR="/var/lib/vz/private/10080/";
/usr/bin/rsync --password-file=/usr/local/comcom/sync/rsync.passwd --whole-file -av rsync://root@a6/root/$DIR $DIR ; DATE2=`date`;
echo "Start $DATE1           Stop $DATE2 "
```

###Добавление READ ONLY доступ к БД Postgres:

1. заходим в нужную БД
2. создаем роль CREATE ROLE <роль> LOGIN NOSUPERUSER INHERIT NOCREATEDB NOCREATEROLE PASSWORD '<пароль>';
3. даем права на все таблицы схемы public - GRANT SELECT ON ALL TABLES IN SCHEMA public TO <роль>;
Если нужны дополнительная права (исключения по обновлению или записи), смотрим здесь: http://www.postgresql.org/docs/9.1/interactive/sql-grant.html
Удаление пользователя (для отзыва привилегий нужно зайти в соотв базу данных).
```sh
REVOKE ALL PRIVILEGES ON ALL TABLES IN SCHEMA public FROM test;
DROP USER test;
```

###Список пользователей Postgres:
```sh
postgres=# \du
```
Вывод хеша пароля (нужно добавить в pg_bouncer для возможности работы через него)
```sh
postgres=# SELECT usename, passwd FROM pg_shadow;
```

###Добавление доступа к MySQL

```sh
CREATE USER "sp"@"%" IDENTIFIED BY '********';
GRANT ALL PRIVILEGES ON databasename*.*\* TO "sp"@"%" IDENTIFIED BY '*\*******' WITH GRANT OPTION;
```
 
###Смена пароля у пользователя Mysql:

```sh
UPDATE user SET password=PASSWORD('password') where User='debian-sys-maint';
flush privileges;
```

 
###Вывод пользователей для базы данных mysql с привилегией на запись:
```sh
select user, Insert_priv from mysql.user;
```

###Удаление пользователей:
```sh
delete from mysql.user WHERE User='tech';
```

##Изменить сетевое имя интерфейса и MTU:
Меняем имя сетевого интерфейса
```sh
ip link set dev pan1 name pan0
```

###Меняем MTU
```sh
ip link set dev eth0 mtu 1480
```

###Управление MegaRAID:

```sh
MegaCli -PDList -aALL
MegaCli -AdpEventLog -GetEvents -f events.log -aALL && cat events.log
MegaCli -AdpBbuCmd -GetBbuStatus -aALL
MegaCli -LDSetProp EnDskCache -LAll -aAll  - включить cache
MegaCli -PDList -aAll | egrep "Enclosure Device ID:|Slot Number:|Inquiry Data:|Error Count:|state" ## Статусы HDD
http://wiki.hetzner.de/index.php/LSI_RAID_Controller/ru
```
 
###Выход из сессии IP-SEC S-Terra

Для выхода из сессии нажмите "Ctrl-Shift-6" затем
клавишу "x". В появившемся промте IOS наберите команду
"disconnect" и нажмите Enter.

###Обновлять вывод команды с интервалом в одну десятую секунды
```sh 
watch --interval=0.1 ptest -Ls
```

###Удалить все маршруты через 172.16.0.254 ипишник используя xargs:
```sh
ip route | grep 172.16.0.254 | awk '{print $1}' | xargs -i ip route del {}
```

###Непонятная ошибка в выводе tshark:
Вида: ENTTEC Unknown (0x000000cc).
Tshark не может распознать трафик, нужно ему помочь вручную, например для smpp трафика:
```sh
tshark -ibond0 -f "host 91.213.5.2 and port 3333" -d tcp.port==3333,smpp
```

###Слушать на локалхосте сайт Яндекса 80 порт через машину a25:
+ С удаленного сервера
```sh
ssh -L 80:ya.ru:80 root@a25
```
+ На удаленный сервер.
```sh
ssh -R 80:ya.ru:80 root@a25
```

###Изменение source и destination адресов у приходящего пакета:

В данном случае сделать source равным destination, а destination - изменить на какой-то третий адрес.

```sh
*mangle
-A PREROUTING -s 1.1.1.1 -d 2.2.2.2 -p tcp -m tcp --dport 22 -j MARK --set-mark 0x1
*nat
-A PREROUTING --match mark --mark 0x1 -j DNAT --to-destination 3.3.3.3
-A POSTROUTING --match mark --mark 0x1 -j SNAT --to-source 2.2.2.2
```

###Установка последней java6/java7 на сервер:

Воспользуемся oab скриптом (параметр -7 означает поставить java7):

```sh
root@t4:~# wget https://github.com/flexiondotorg/oab-java6/archive/master.zip
root@t4:~# unzip master.zip
root@t4:~# cd oab-java6-master/
root@t4:~/oab-java6-master# ./oab-java.sh -7
root@t4:/# apt-get update
root@t4:/# apt-get install oracle-java7-jre
root@t4:/# sudo update-alternatives --config java
There are 2 choices for the alternative java (providing /usr/bin/java).
 
  Selection    Path                                      Priority   Status
------------------------------------------------------------
  0            /usr/lib/jvm/java-6-openjdk/jre/bin/java   1061      auto mode
  1            /usr/lib/jvm/java-6-openjdk/jre/bin/java   1061      manual mode
* 2            /usr/lib/jvm/java-7-oracle/jre/bin/java    53        manual mode
 
Press enter to keep the current choice[*], or type selection number: 2
```

###Дамп/восстановление базы данных mysql

Только схемы:
```sh
mysql> show create database smpp_router;
+-------------+----------------------------------------------------------------------+
| Database    | Create Database                                                      |
+-------------+----------------------------------------------------------------------+
| smpp_router | CREATE DATABASE `smpp_router` /*!40100 DEFAULT CHARACTER SET utf8 */ |
+-------------+----------------------------------------------------------------------+
1 row in set (0.00 sec)
 
root@j2:~# mysqldump -d router > ~/smpp_router_dump_19_03_2013_schema_only.sql
root@j2:~# scp ~/router_dump_19_03_2013_schema_only.sql t4:~/
```
###Восстановление схемы (на сервере куда восстанавливаем):
```sh
mysql> CREATE USER "router"@"%" IDENTIFIED BY '****';
mysql> CREATE DATABASE `router` /*!40100 DEFAULT CHARACTER SET utf8 */
mysql> GRANT ALL PRIVILEGES ON smpp_router.* TO "router"@"%" WITH GRANT OPTION;
mysql> flush privileges;
 
root@smstrn4:~# mysql -u router -p router < ~/router_dump_19_03_2013_schema_only.sql
```

###Поменять активный интерфейс в бондинге (в данном примере на eth0):
```sh
ifenslave -c bond0 eth0
```

###Сравнение двух баз данных Mysql с помощью утилиты pt-table-checksum (пример java3 java2)

На мастере нужно добавить пользователя

```sh
GRANT ALL PRIVILEGES ON *.* TO 'pt_chtables'@'172.16.0.54' IDENTIFIED BY PASSWORD 'password';
GRANT ALL PRIVILEGES ON `lite_demo`.* TO 'pt_chtables'@'172.16.0.54';
GRANT ALL PRIVILEGES ON `lite`.* TO 'pt_chtables'@'172.16.0.54';

на слейве запустить команду:
```sh
pt-table-checksum --databases=lite_icb h=java3,u=pt_chtables,p=SdwCweCS
```
 
###Сортировка файлов по их числовому возрастанию
```sh
webhost:/var/log/nginx# ls  | sort -t '.' -nk 4 | grep archipelag.error
```
###crm_report - генерация отчетов о работе кластера за определенное время:
```sh
crm_report -C -f "2014-05-14 13:05:00"
```

###Способ ловить kernel panic:

1. Должен быть подключен модуль NETCONSOLE
```sh 
root@sc2:/# cat /boot/config-2.6.32-5-amd64|grep NETCONSOLE
CONFIG_NETCONSOLE=m
CONFIG_NETCONSOLE_DYNAMIC=y
```

2. Запускаем отсылку командой
```sh 
root@s2:/home/djet# modprobe netconsole netconsole=6969@10.90.30.130/eth0,6969@10.90.30.129/5c:f3:fc:b6:05:b0
root@s2:/home/djet# dmesg | tail
[89628.171100] netconsole: local port 6969
[89628.171103] netconsole: local IP 10.90.30.130
[89628.171105] netconsole: interface eth0
[89628.171107] netconsole: remote port 6969
[89628.171108] netconsole: remote IP 10.90.30.129
[89628.171110] netconsole: remote ethernet address 5c:f3:fc:b6:05:b0
[89628.351538] console [netcon0] enabled
[89628.351539] netconsole: network logging started
```

3. Запускаем nc на сервере где будем ловить логи
```sh
root@s1:/home/djet# nc -u -l -p 6969
```

###Узнать информацию о файле
```sh
# file /bin/ls
```
 
###Информация о бинарнике
```sh
# ldd  /bin/ls
```

###Вывести все ноды в puppet
```sh
puppet cert list --all
```


 

 

