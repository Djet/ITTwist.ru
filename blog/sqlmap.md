---
title: Обзор SQLMAP
date: 2015-10-16 19:00
template: blog_post
---
В данной статье я пробежался с вольным переводом по каталогам и параметрам, всем известного инструмента, sqlmap.
<!--more-->

SQLMAP - Это open source инструмент для автоматического обнаружения и эксплуатирования sql-injection относящихся к базам данных.
Содержит мощный инструментарий по пояску уязвимости основываясь на отпечатках баз данных и использует уязвимости позволяющие исполнять команды на удаленном сервере баз данных.

###### Где взять: https://github.com/sqlmapproject/sqlmap 
######Оф. сайт: http://sqlmap.org/

Первым делом скачаем:
```sh
# git clone https://github.com/sqlmapproject/sqlmap.git
```
Разберем каталоги из которых состоит sqlmap:

+ doc/ - каталог с документацией по использованию sqlmap, cодержит: faq, readme
+ extra/ - содержит различные расширения такие как:
 - beep.py - создает beep звук,
 - cloak.py - утилита для шифрования и сжатия файлов
 - dbgtool.py - утилита для преобразования MS-DOS исполняемого файла в ASCII текст, обратно собрать в исполняемый файл на Windows можно с помощью debug.exe который присутствует во всех версия Windows
 - icmpsh.py - простой shell через icmp
 - mssqlsig/update.py - обновление MS SQL сигнатур
+ runcmd/ - в этом каталоге содержатся файлы которые могут быть использованы для запуска командной строки cmd для windows
 - safe2bin.py - декодер текста в бинарный файл
+ shellcodeexec/ - файлы в этом каталоге содержат исполняемы файлы shell для linux и windows
+ shutils/ - содержат различные утилиты для: duplicates.py - удаляют дубликаты слов в файле; blanks.sh - удаляет пустые сроки в конце строки так же утилиты для самого проекта sqlmap
+ lib/ - библиотеки используемые sqlmap
+ plugins/ - каталог содержащий инструкции к базам данных
+ procs/ - файлы в этом каталоге содержат sql-фрагменты используемые на базе данных
+ shell/ - в этом каталоге содержатся бэкдоры конвертировать их можно с помощью cloak.py на данные файлы реагирует антивирус.
+ tamper/ и thirdparty/ - сценарии для sqlmap
+ txt/ - каталог содержит файлы со списками таблиц, пользователей, агентов и т.д.
+ udf/ - Бинарные файлы в этом каталоге sqlmap использует на целевой системе
+ waf/ - ?
+ xml/ - содержатся xml файлы с полезной нагрузкой на бд


Далее заберем параметры с которыми можно запускать sqlmap:
```sh
$ python sqlmap.py --help
Usage: python sqlmap.py [options]

-h, --help - простая помощь
-hh - расширенная помощь
--version - версия sqlmap
-v VERBOSE - уровни детализации от 0 до 6

Опции для цели (минимум один параметр должен быть определен):
-d DIRECT - строка для прямого соединения с базой данных
-u URL, --url=URL - указать целевой URL вида: http://www.site.com/vuln.php?id=1
-l LOGFILE путь к лог файлу для разбора утилитами Burp или WebScarab
-x SITEMAPURL - путь к файлу или файлам с данными о карте сайта в формате xml
-m BULKFILE - сканирование по нескольким файлам указанным в файле
-r REQUESTFILE - загрузить HTTP запрос из файла
-g GOOGLEDORK использовать результат Google dork как целевые url
-c CONFIGFILE - загрузить параметры запуска sqlmap из файла

Запросы (Это параметры могут быть использованы для указания, как необходимо подключится к целевому url):

--method=METHOD - принудительно указать метод запроса к примеру PUT
--data=DATA - указать данные для запроса POST
--param-del=PARA.. - указать разделитель для параметров
--cookie=COOKIE - значение заголовка HTTP-COOKIE
--cookie-del=COO.. - указать разделитель для параметров
--load-cookies=L.. - файл содержащий cookies в формате Netscape/wget
--drop-set-cookie - игнорировать заголовки Set-Cookie в ответе
--user-agent=AGENT - Указать используемый HTTP User-Agent
--random-agent - использовать случайных HTTP User-Agent’ов
--host=HOST - значение заголовка HTTP-хоста
--referer=REFERER - значение HTTP Referer в заголовке
-H HEADER, --hea.. - дополнительный заголовок. пример: "X-Forwarded-For: 127.0.0.1"
--headers=HEADERS - дополнительный заголовок. пример: "Accept-Language: fr\nETag: 123"
--auth-type=AUTH.. - указать тип аутентификации (Basic, Digest, NTLM или PKI)
--auth-cred=AUTH.. - полномочия для аутентификации (name:password)
--proxy-file=PRO.. - загрузить адреса прокси из файла
--ignore-proxy - игнорировать прокси заданный по умолчанию
--tor - использовать прокси TOR
--tor-port=TORPORT - указать порт на котором запущен TOR
-tor-type=TORTYPE - указать тип прокси TOR ((HTTP (по умолчанию), SOCKS4 or SOCKS5))
--check-tor - Проверить используется ли TOR должным образом
--delay=DELAY - Задержка в секундах перед http запросами
--timeout=TIMEOUT - указать тайм-аут соединения по умолчанию 30 секунд
--retries=RETRIES - количество попыток после тайм-аута, по умолчанию 3
--randomize=RPARAM - случайно менять данные значение параметров
--safe-url=SAFEURL - ссылка которую sqlmap будет часто посещать во время тестирования ?
--safe-post=SAFE.. - POST данные отправляемые на надежный URL ?
--safe-req=SAFER.. - Загрузите безопасный запрос из файла ?
--safe-freq=SAFE.. - Тестовые запросы между двумя запросами к безопасному URL ?
--skip-urlencode - Пропустить URL кодирование данных для полезной нагрузки ?
--csrf-token=CSR.. - Parameter used to hold anti-CSRF token
--csrf-url=CSRFURL - URL address to visit to extract anti-CSRF token
--force-ssl - Принудительное использование SSL/HTTPS
--hpp - Use HTTP parameter pollution method
--eval=EVALCODE - указать Python код до запроса. Пример: "import hashlib;id2=hashlib.md5(id).hexdigest()"

Оптимизация (Эти параметры могут быть использования для оптимизации работы sqlmap):

-o - Включить все параметры оптимизации
--predict-output - Предсказать общий выход запросов
--keep-alive - Использовать стойкие HTTP(s) соединения
--null-connection - Получить длину страницы без фактического тела HTTP-ответа
--threads=THREADS - Максимальное количество одновременных запросов

Инъекции:
-p TESTPARAMETER - проверить параметр
--skip=SKIP - пропустить тестирование для параметра
--skip-static - пропустить тесты не являющимися динамическими
--dbms=DBMS - Принудительно указать значение СУБД
--dbms-cred=DBMS.. - Аутентификация в СУБД (user:password)
--os=OS - указать тип операционной системы
--invalid-bignum - использовать большие цифры для признания недействительных значений
--invalid-logical - использовать логические значения для признания недействительных значений
--invalid-string - использовать случайные строки для признания недействительных значений
--no-cast - Выключение механизма casting для полезной нагрузки
--no-escape - Выключение механизма escaping для полезной нагрузки
--prefix=PREFIX - стока префикса для полезной нагрузки инъекции
--suffix=SUFFIX - стока суфикса для полезной нагрузки инъекции
--tamper=TAMPER использовать данный скрипт для фальсификации данных при инъекций

Определение (Эти параметры могут быть использованы для настройки фазы обнаружения):
--level=LEVEL - уровень проверок для тестирования: от 1 до 5
--risk=RISK - Риск тестов при выполнении: от 1 до 3
--string=STRING - строка соответствующая запросу равна True
--not-string=NOT.. строка соответствующая запросу равна False
--regexp=REGEXP Регулярное выражение, соответствующая запросу равна True
--code=CODE - HTTP код, соответствующая запросу равна True
--text-only - Сравнивать страницы, основанные только на текстовом содержание
--titles    - Сравнивать страницы, основанные только на их названии

Методы (Эти параметры могут быть использованы для настройки тестирования методов конкретных SQL инъекций):

--technique=TECH - методы SQL инъекций по умолчанию BEUSTQ
--time-sec=TIMESEC - Задержка в секундах на запросы к СУБД по умолчанию 5
--union-cols=UCOLS - Диапазон столбцов для проверки UNION запросов в SQL инъекции
--union-char=UCHAR - Количество столбцов для брут-форса
--union-from=UFROM - Таблица для использования в FROM части UNION запроса для SQL инъекции
--dns-domain=DNS.. - Доменное имя для использования DNS exfiltration attack
--second-order=S.. - URL результативной страницы для second-order response

Слепок:
-f, --fingerprint - Использовать все слепки СУБД


Получение данных (Эти параметры необходимы для извлечение данных из БД):
-a, --all - получить всё 
-b, --banner - получить версию СУБД
--current-user - получить текущего пользователя
--current-db - получить текущую базу данных
--hostname - получить хост базы данных
--is-dba - Определить является ли текущий пользователь базы данных DBA
--users - Перечислить пользователей СУБД
--passwords - Перечислить хэши пароли пользователя
--privileges - Перечислить привилегии пользователей 
--roles - Перечислить роли ползователей
--dbs - перечислить базы данных
--tables - перечислить таблици
--columns - перечислить столбцы
--schema - предоставить схему БД
--count - получить количество записей в таблице
--dump - Снять дамп таблиц
--dump-all - сделать полный дамп
--search - Поиск колонок, столбцов и\или баз данных
--comments  - получить комментарии СУБД
-D DB - перечислить базы данных
-T TBL - перечислить таблици
-C COL - перечислить колонки
-X EXCLUDECOL  - Перечисление столбцов в таблице
-U USER - Перечисление пользователей 
--exclude-sysdbs - исключить системные базы данных
--where=DUMPWHERE - использовать WHERE в таблицах дампа
--start=LIMITSTART -  лимит певыех записи вывода
--stop=LIMITSTOP - лимит последних записей вывода
--first=FIRSTCHAR - Получение первого слова в выводе
--last=LASTCHAR - получение последнего слова в выводе
--sql-query=QUERY - выполнить sql запрос
--sql-shell - подсказка для интерактивной оболочки
--sql-file=SQLFILE - запустить sql сигмент для данного файла

Брут форс(параметры для брутфорса):
--common-tables - Поискать таблицы с помощью брутфорса
--common-columns - Поискать столбцы с помощью буртфорса

Функции для пользовательских инъекций:
--udf-inject - ввод пользовательских функций
--shared-lib=SHLIB - локальный путь к бибилиотеке

Файловый доступ к системе:
--file-read=RFILE - Читать из Базы данных в файл
--file-write=WFILE - записать локальный файл на файлувую систему СУБД 
--file-dest=DFILE - абсолютный путь для записи

Доступ к операционной системе:
--os-cmd=OSCMD   Выполнить команду операционной системы
--os-shell - Запрос интегративной оболочки операционной системы 
--os-pwn - подсказка для OOB шела или VNC сервера
--os-smbrelay - запрос для OOB шела или VNC сервера
--os-bof - эксплуатация переполнения буфера хранимой процедуры
--priv-esc - Определить привилегии пользователя из под которого запущена СУБД
--msf-path=MSFPATH - Локальный путь до Metasploit Framework
--tmp-path=TMPPATH - путь на удаленном сервере для временных файлов

Доступ к реестру Windows
--reg-read  - прочитать ключи реестра Windows
--reg-add - прописать значение ключа в реестре  Windows
--reg-del - Удалить ключ из реестра
--reg-key=REGKEY - Ключ реестра Windows
--reg-value=REGVAL - значение реестра Windows
--reg-data=REGDATA - данные ключа реестра Windows
--reg-type=REGTYPE - тип данных ключа реестра Windows

Основные:
-s SESSIONFILE - Сохраняет результат работы в .sqlite файл
-t TRAFFICFILE - вести весть http трафик в текстовый файл
--batch - Не спрашивать пользователя использовать значения по умолчанию
--charset=CHARSET - Принудительная кодировка для извлечения данных
--crawl=CRAWLDEPTH - Сканировать web-сайт начиная c URL
--crawl-exclude=.. - Регулярное выражение для исключения страниц из сканирования, пример "logout"
--csv-del=CSVDEL - Разделительный символ, используемый в CSV (по умолчанию ",")
--dump-format=DU.. - Формат дампа данных (CSV (по умолчанию), HTML или SQLite)
--eta - Вывод расчетного времени
--flush-session - Сброс сессии для текущей цели
--forms - Разбор и испытание формы на целевом URL
--fresh-queries - Пропустить результаты запроса хранящиеся в файле сессии
--hex - Используйте функцию hex СУБД для извлечения данных
--output-dir=OUT.. - пользовательский путь каталога для отчетов
--parse-errors - Разбор и отображение сообщений об ошибках СУБД
--pivot-column=P.. - Имя основной таблицы
--save=SAVECONFIG - Сохранить варианты конфигурации в INI файле
--scope=SCOPE - Regexp to filter targets from provided proxy log
--test-filter=TE.. - Выбрать тест для полезно нагрузки к примеру ROW
--test-skip=TEST.. - пропустить тест полезной нагрузки
--update - обновить sqlmap

Разное:
-z MNEMONICS - Использования коротких мнемоник: "flu,bat,ban,tec=EU"
--alert=ALERT - Запускать команду при нахождении SQL инъекции
--beep - Подать звуковой сигнал при вопросе или нахождении инекции
--cleanup Clean up the DBMS from sqlmap specific UDF and tables
--dependencies - Проверьте отсутствие (непрофильных) sqlmap зависимостей
--disable-coloring - Отключить вывод на консоль
--gpage=GOOGLEPAGE - Используйте результаты Google dork из определенного количества страниц
--identify-waf - Сделать тщательное сканирование на WAF/IPS/IDS
--skip-waf - пропустить проверку на WAF/IPS/IDS
--mobile - имитировать телефон при проверке в заголовке HTTP User-Agent
--offline - Работа в автономном режиме
--page-rank - Показать рейтинг страницы в Google dork
--purge-output - Безопасное удаление всего содержимого из рабочего каталога
--smart - Провести тщательные испытания
--sqlmap-shell Prompt for an interactive sqlmap shell
--wizard простой интерфейс для начинающий пользователей
```


Примеры использования:
```sh
$ python sqlmap.py --tor --wizard --mobile --tamper=space2comment
```
Поясняю:
```sh
--tor - sqlmap будет осуществлять проверку через прокси tor
--wizard - Будет использоваться простой интерфейс с подсказками
--mobile - для тестирования будет подставлены user-agent мобильного телефона
--tamper=space2comment - будет использоваться один из методов обхода систем WAF
```

#### Пример использовался в Test Lab v7:
```sh
$ python sqlmap -u http://192.168.101.5/article.php?art=1 —dbs
$ python sqlmap -u http://192.168.101.5/article.php?art=1 -D blogers —tables
$ python sqlmap -u http://192.168.101.5/article.php?art=1 -D blogers —tables T users —columns
$ python sqlmap -u http://192.168.101.5/article.php?art=1 -D blogers —tables T users —dump
```

Максимальный уровень поиска уязвимых форм используя случайного user-agent’а и в случае удачи определение типа базы данных.
```sh
sqlmap.py -u http://192.168.101.5/ --level=5 --banner  --user-agent
```
На данный момент всё, надеюсь что это кому-то помогло больше разобраться в инструменте, спасибо.

