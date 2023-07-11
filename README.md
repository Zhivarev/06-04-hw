# Домашнее задание к занятию "`PostgreSQL`" - `Живарев Игорь`


### Задание 1

Используя Docker, поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.

Подключитесь к БД PostgreSQL, используя `psql`.

Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.

Найдите и приведите управляющие команды для:

- вывода списка БД,
- подключения к БД,
- вывода списка таблиц,
- вывода описания содержимого таблиц,
- выхода из psql.


Ответ:

Создание контейнера:

```
root@vm1:~/sql# docker-compose up -d
Pulling postgres (postgres:13)...
13: Pulling from library/postgres
faef57eae888: Pull complete
a33c10a72186: Pull complete
d662a43776d2: Pull complete
a3ba86413420: Pull complete
a627f37e9916: Pull complete
424bade69494: Pull complete
dd8d4fcd466b: Pull complete
03d0efeea592: Pull complete
89495f306b9b: Pull complete
b9ef09ca1802: Pull complete
a283f3964686: Pull complete
2b43fcabe8a8: Pull complete
b8dec84b144c: Pull complete
Digest: sha256:0f18de936266e03891e186db616e530e0e4365ef5fb300d4bb27318538b80604
Status: Downloaded newer image for postgres:13
Creating postgres ... done
root@vm1:~/sql# docker exec -ti postgres bash
root@1273330ffa7b:/#
```

Docker-compose.yaml:

```
version: '3'
services:
  postgres:
    container_name: postgres
    image: postgres:13
    environment:
      POSTGRES_USER: post
      POSTGRES_PASSWORD: post
      POSTGRES_DB: netology_db
    ports:
      - "5432:5432"
    volumes:
      - database_volume:/var/lib/postgresql/data
      - backup_volume:/opt/backup/
    restart: always

volumes:
  database_volume:
  backup_volume:
```


Подключение к БД:

```
root@1273330ffa7b:/# psql -d netology_db -U post
psql (13.11 (Debian 13.11-1.pgdg120+1))
Type "help" for help.

netology_db=#
```

Вывод списка БД:

```
netology_db=# \l
                              List of databases
    Name     | Owner | Encoding |  Collate   |   Ctype    | Access privileges 
-------------+-------+----------+------------+------------+-------------------
 netology_db | post  | UTF8     | en_US.utf8 | en_US.utf8 | 
 postgres    | post  | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0   | post  | UTF8     | en_US.utf8 | en_US.utf8 | =c/post          +
             |       |          |            |            | post=CTc/post
 template1   | post  | UTF8     | en_US.utf8 | en_US.utf8 | =c/post          +
             |       |          |            |            | post=CTc/post
(4 rows)

netology_db=#
```

Подключение к БД:

```
netology_db=# \c postgres;
You are now connected to database "postgres" as user "post".
postgres=# 
```

Вывод списка таблиц:

```
postgres=# \dt
Did not find any relations.
postgres=# \dtS
                  List of relations
   Schema   |          Name           | Type  | Owner 
------------+-------------------------+-------+-------
 pg_catalog | pg_aggregate            | table | post
 pg_catalog | pg_am                   | table | post
 pg_catalog | pg_amop                 | table | post
 pg_catalog | pg_amproc               | table | post
 pg_catalog | pg_attrdef              | table | post
 pg_catalog | pg_attribute            | table | post
 pg_catalog | pg_auth_members         | table | post
 pg_catalog | pg_authid               | table | post
 pg_catalog | pg_cast                 | table | post
 pg_catalog | pg_class                | table | post
...
```

Вывод описания содержимого таблиц:

```
postgres=# \dS+
                                           List of relations
   Schema   |              Name               | Type  | Owner | Persistence |    Size    | Description 
------------+---------------------------------+-------+-------+-------------+------------+-------------
 pg_catalog | pg_aggregate                    | table | post  | permanent   | 56 kB      | 
 pg_catalog | pg_am                           | table | post  | permanent   | 40 kB      | 
 pg_catalog | pg_amop                         | table | post  | permanent   | 80 kB      | 
 pg_catalog | pg_amproc                       | table | post  | permanent   | 64 kB      | 
 pg_catalog | pg_attrdef                      | table | post  | permanent   | 8192 bytes | 
 pg_catalog | pg_attribute                    | table | post  | permanent   | 456 kB     | 
 pg_catalog | pg_auth_members                 | table | post  | permanent   | 40 kB      | 
 pg_catalog | pg_authid                       | table | post  | permanent   | 48 kB      | 
 pg_catalog | pg_available_extension_versions | view  | post  | permanent   | 0 bytes    | 
 pg_catalog | pg_available_extensions         | view  | post  | permanent   | 0 bytes    | 
 pg_catalog | pg_cast                         | table | post  | permanent   | 48 kB      | 
 pg_catalog | pg_class                        | table | post  | permanent   | 136 kB     | 
...
```

Выход из psql:

```
netology_db=# \q
root@1273330ffa7b:/#
```

---

### Задание 2

Используя `psql`, создайте БД `test_database`.

Изучите бэкап БД.

Восстановите бэкап БД в `test_database`.

Перейдите в управляющую консоль `psql` внутри контейнера.

Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.

Используя таблицу pg_stats, найдите столбец таблицы `orders` с наибольшим средним значением размера элементов в байтах.

Приведите в ответе команду, которую вы использовали для вычисления, и полученный результат.


Ответ:

Cоздание БД `test_database`:

```
netology_db=# create database test_database;

CREATE DATABASE
netology_db=# \l
                               List of databases
     Name      | Owner | Encoding |  Collate   |   Ctype    | Access privileges 
---------------+-------+----------+------------+------------+-------------------
 netology_db   | post  | UTF8     | en_US.utf8 | en_US.utf8 | 
 postgres      | post  | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0     | post  | UTF8     | en_US.utf8 | en_US.utf8 | =c/post          +
               |       |          |            |            | post=CTc/post
 template1     | post  | UTF8     | en_US.utf8 | en_US.utf8 | =c/post          +
               |       |          |            |            | post=CTc/post
 test_database | post  | UTF8     | en_US.utf8 | en_US.utf8 | 
(5 rows)

netology_db=#
```

Восстановление бэкап БД в `test_database`:

```
root@1273330ffa7b:/# psql -U post -d test_database < /opt/backup/test_dump.sql
root@1273330ffa7b:/# psql -d test_database -U post
psql (13.11 (Debian 13.11-1.pgdg120+1))
Type "help" for help.

test_database=#
```

Проведение операции `ANALYZE` для сбора статистики по таблице:

```
test_database=# ANALYZE VERBOSE public.orders;
INFO:  analyzing "public.orders"
INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
ANALYZE
test_database=#
```

Поиск столбца таблицы `orders` с наибольшим средним значением размера элементов в байтах:

```
test_database=# select avg_width from pg_stats where tablename='orders';
 avg_width 
-----------
         4
        16
         4
(3 rows)

test_database=#
```
---

### Задание 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и поиск по ней занимает долгое время. Вам как успешному выпускнику курсов DevOps в Нетологии предложили провести разбиение таблицы на 2: шардировать на orders_1 - price>499 и orders_2 - price<=499.

Предложите SQL-транзакцию для проведения этой операции.

Можно ли было изначально исключить ручное разбиение при проектировании таблицы orders?


Ответ:


```
test_database=# CREATE TABLE orders_1 (CHECK (price > 499)) INHERITS (orders);
CREATE TABLE
test_database=# CREATE TABLE orders_2 (CHECK (price <= 499)) INHERITS (orders);
CREATE TABLE
test_database=# INSERT INTO orders_1 SELECT * FROM orders WHERE price > 499;
INSERT 0 3
test_database=# INSERT INTO orders_2 SELECT * FROM orders WHERE price <= 499;
INSERT 0 5
test_database=# DELETE FROM ONLY orders;
DELETE 8
test_database=# \dt
         List of relations
 Schema |   Name   | Type  | Owner 
--------+----------+-------+-------
 public | orders   | table | post
 public | orders_1 | table | post
 public | orders_2 | table | post
(3 rows)

test_database=#
```
Изначально, на этапе проектирования таблиц, можно было сделать её шардированной, тогда не пришлось бы переименовывать исходную таблицу и переносить данные в новую.
### Задание 4

Используя утилиту `pg_dump`, создайте бекап БД `test_database`.

Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`?


Ответ:

```
root@1273330ffa7b:/# ls -lah /opt/backup/
total 36K
drwxr-xr-x 2 root root 4.0K Jul 11 07:11 .
drwxr-xr-x 1 root root 4.0K Jul 10 16:21 ..
-rw-r--r-- 1 root root 4.2K Jul  8 12:57 test_db.backup
-rw-r--r-- 1 root root  18K Jul 11 07:09 test_dump.sql
root@1273330ffa7b:/# pg_dump -U post -d test_database > /opt/backup/test_database_netology.sql
root@1273330ffa7b:/# ls -lah /opt/backup/
total 40K
drwxr-xr-x 2 root root 4.0K Jul 11 15:06 .
drwxr-xr-x 1 root root 4.0K Jul 10 16:21 ..
-rw-r--r-- 1 root root 3.4K Jul 11 15:06 test_database_netology.sql
-rw-r--r-- 1 root root 4.2K Jul  8 12:57 test_db.backup
-rw-r--r-- 1 root root  18K Jul 11 07:09 test_dump.sql
root@1273330ffa7b:/#
```

Например, добавить свойство UNIQUE


---
