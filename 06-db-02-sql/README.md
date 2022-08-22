# Домашнее задание к занятию "6.2. SQL"

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/tree/master/additional/README.md).

## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose манифест.

Решение:

```
version: "3.9"
volumes:
  postgressql_data:  
  backup_postgressql_data:
services:
  
  postgressql:
    image: postgres:12-bullseye 
    container_name: postgressql
    environment:
      - PGDATA=/var/lib/postgresql/data/
      - POSTGRES_PASSWORD=pspass
    volumes:
      - postgressql_data:/var/lib/postgresql/data
      - backup_postgressql_data:/backup
      - ./config:/docker-entrypoint-initdb.d
    network_mode: "host"
```

## Задача 2

В БД из задачи 1: 
- создайте пользователя test-admin-user и БД test_db
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
- создайте пользователя test-simple-user  
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db

Таблица orders:
- id (serial primary key)
- наименование (string)
- цена (integer)

Таблица clients:
- id (serial primary key)
- фамилия (string)
- страна проживания (string, index)
- заказ (foreign key orders)

Приведите:
- итоговый список БД после выполнения пунктов выше,
- описание таблиц (describe)
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
- список пользователей с правами над таблицами test_db

Решение:

```
 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 test_db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
(4 rows)
                                                         Table "public.orders"
   Column   |         Type          | Collation | Nullable |              Default               | Storage  | Stats target | Description 
------------+-----------------------+-----------+----------+------------------------------------+----------+--------------+-------------
 id         | integer               |           | not null | nextval('orders_id_seq'::regclass) | plain    |              | 
 order_name | character varying(25) |           | not null |                                    | extended |              | 
 price      | integer               |           | not null |                                    | plain    |              | 
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)
Check constraints:
    "orders_order_name_check" CHECK (order_name::text <> ''::text)
    "orders_price_check" CHECK (price > 0)
Referenced by:
    TABLE "clients" CONSTRAINT "clients_order_number_fkey" FOREIGN KEY (order_number) REFERENCES orders(id)
Access method: heap
                                                          Table "public.clients"
    Column    |         Type          | Collation | Nullable |               Default               | Storage  | Stats target | Description 
--------------+-----------------------+-----------+----------+-------------------------------------+----------+--------------+-------------
 id           | integer               |           | not null | nextval('clients_id_seq'::regclass) | plain    |              | 
 last_name    | character varying(45) |           | not null |                                     | extended |              | 
 country      | character varying(35) |           | not null |                                     | extended |              | 
 order_number | integer               |           |          |                                     | plain    |              | 
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
    "country_index" btree (country)
Check constraints:
    "clients_country_check" CHECK (country::text <> ''::text)
    "clients_last_name_check" CHECK (last_name::text <> ''::text)
Foreign-key constraints:
    "clients_order_number_fkey" FOREIGN KEY (order_number) REFERENCES orders(id)
Access method: heap
 table_name |     grantee      | privilege_type 
------------+------------------+----------------
 orders     | postgres         | INSERT
 orders     | postgres         | SELECT
 orders     | postgres         | UPDATE
 orders     | postgres         | DELETE
 orders     | postgres         | TRUNCATE
 orders     | postgres         | REFERENCES
 orders     | postgres         | TRIGGER
 orders     | test-admin-user  | INSERT
 orders     | test-admin-user  | SELECT
 orders     | test-admin-user  | UPDATE
 orders     | test-admin-user  | DELETE
 orders     | test-admin-user  | TRUNCATE
 orders     | test-admin-user  | REFERENCES
 orders     | test-admin-user  | TRIGGER
 orders     | test-simple-user | INSERT
 orders     | test-simple-user | SELECT
 orders     | test-simple-user | UPDATE
 orders     | test-simple-user | DELETE
(18 rows)
 table_name |     grantee      | privilege_type 
------------+------------------+----------------
 clients    | postgres         | INSERT
 clients    | postgres         | SELECT
 clients    | postgres         | UPDATE
 clients    | postgres         | DELETE
 clients    | postgres         | TRUNCATE
 clients    | postgres         | REFERENCES
 clients    | postgres         | TRIGGER
 clients    | test-admin-user  | INSERT
 clients    | test-admin-user  | SELECT
 clients    | test-admin-user  | UPDATE
 clients    | test-admin-user  | DELETE
 clients    | test-admin-user  | TRUNCATE
 clients    | test-admin-user  | REFERENCES
 clients    | test-admin-user  | TRIGGER
 clients    | test-simple-user | INSERT
 clients    | test-simple-user | SELECT
 clients    | test-simple-user | UPDATE
 clients    | test-simple-user | DELETE
(18 rows)

```
## Задача 3

Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL синтаксис:
- вычислите количество записей для каждой таблицы 
- приведите в ответе:
    - запросы 
    - результаты их выполнения.

Решение:

```
 id | order_name | price 
----+------------+-------
  1 | Шоколад    |    10
  2 | Принтер    |  3000
  3 | Книга      |   500
  4 | Монитор    |  7000
  5 | Гитара     |  4000
(5 rows)
 id |      last_name       | country | order_number 
----+----------------------+---------+--------------
  1 | Иванов Иван Иванович | USA     |             
  2 | Петров Петр Петрович | Canada  |             
  3 | Иоганн Себастьян Бах | Japan   |             
  4 | Ронни Джеймс Дио     | Russia  |             
  5 | Ritchie Blackmore    | Russia  |             
(5 rows)
 count 
-------
     5
(1 row)
 count 
-------
     5
(1 row)
```

## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения данных операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.
 
Подсказк - используйте директиву `UPDATE`.

Решение:
```
id |      last_name       | country | order_number 
----+----------------------+---------+--------------
  4 | Ронни Джеймс Дио     | Russia  |             
  5 | Ritchie Blackmore    | Russia  |             
  1 | Иванов Иван Иванович | USA     |            3
  2 | Петров Петр Петрович | Canada  |            4
  3 | Иоганн Себастьян Бах | Japan   |            5
(5 rows)
 id |      last_name       | country | order_number 
----+----------------------+---------+--------------
  1 | Иванов Иван Иванович | USA     |            3
  2 | Петров Петр Петрович | Canada  |            4
  3 | Иоганн Себастьян Бах | Japan   |            5
(3 rows)
```

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

Решение:
```
 QUERY PLAN                
------------------------------------------
 - Plan:                                 +
     Node Type: "Seq Scan"               +
     Parallel Aware: false               +
     Relation Name: "clients"            +
     Alias: "clients"                    +
     Startup Cost: 0.00                  +
     Total Cost: 13.50                   +
     Plan Rows: 348                      +
     Plan Width: 204                     +
     Filter: "(order_number IS NOT NULL)"
(1 row)
```
Директива EXPLAIN дает информацию о запросе к БД и его время выполнения

## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).

Решение:
```
postgres@spb-svc:/$ pg_dumpall > /backup/backup_"`date +"%d-%m-%Y"`"
```
Остановите контейнер с PostgreSQL (но не удаляйте volumes).

Решение:
```
[sovar@spb-mon-serv build-postgressql]$ docker compose down 
[+] Running 1/1
 ⠿ Container postgressql  Removed                                                                                           0.3s                                                                                        0.3s
```

Поднимите новый пустой контейнер с PostgreSQL.

Решение:
```
sovar@spb-mon-serv build-postgressql]$ docker volume ls
DRIVER    VOLUME NAME
local     build-mon-service_grafana_data
local     build-mon-service_prometheus_data
local     build-postgressql_backup_postgressql_data
local     build-postgressql_postgressql_data
[sovar@spb-mon-serv build-postgressql]$ docker volume rm build-postgressql_postgressql_data
build-postgressql_postgressql_data
[sovar@spb-mon-serv build-postgressql]$ docker volume ls
DRIVER    VOLUME NAME
local     build-mon-service_grafana_data
local     build-mon-service_prometheus_data
local     build-postgressql_backup_postgressql_data
```

Восстановите БД test_db в новом контейнере.

```
[sovar@spb-mon-serv build-postgressql]$ docker compose up
[+] Running 2/2
 ⠿ Volume "build-postgressql_postgressql_data"  Created                                                                     0.0s
 ⠿ Container postgressql                        Created                                                                     0.1s
 ```
```
[sovar@spb-mon-serv build-postgressql]$ docker exec -it postgressql bash
postgres@spb-mon-serv:/$ ls backup/
backup_20-02-2022
```
```
postgres@spb-mon-serv:/$ psql -f /backup/backup_20-02-2022
```
```
postgres-# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges   
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 test_db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 | 
(4 rows)
postgres-# \dt
          List of relations
 Schema |  Name   | Type  |  Owner   
--------+---------+-------+----------
 public | clients | table | postgres
 public | orders  | table | postgres
(2 rows)
postgres=# SELECT * FROM orders;
 id | order_name | price 
----+------------+-------
  1 | Шоколад    |    10
  2 | Принтер    |  3000
  3 | Книга      |   500
  4 | Монитор    |  7000
  5 | Гитара     |  4000
(5 rows)
postgres=# SELECT * FROM clients;
 id |      last_name       | country | order_number 
----+----------------------+---------+--------------
  4 | Ронни Джеймс Дио     | Russia  |             
  5 | Ritchie Blackmore    | Russia  |             
  1 | Иванов Иван Иванович | USA     |            3
  2 | Петров Петр Петрович | Canada  |            4
  3 | Иоганн Себастьян Бах | Japan   |            5
(5 rows)
```
---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
