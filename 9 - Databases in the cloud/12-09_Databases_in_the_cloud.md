# Домашнее задание к занятию «Базы данных в облаке» - `Рыхлик Илья Александрович`

---

Домашнее задание подразумевает, что вы уже делали предыдущие работы в Яндекс.Облаке, и у вас есть аккаунт и каталог.


Используйте следующие рекомендации во избежание лишних трат в Яндекс.Облаке:
1) Сразу после выполнения задания удалите кластер.
2) Если вы решили взять паузу на выполнение задания, то остановите кластер.

### Задание


#### Создание кластера
1. Перейдите на главную страницу сервиса Managed Service for PostgreSQL.
1. Создайте кластер PostgreSQL со следующими параметрами:
- класс хоста: s2.micro, диск network-ssd любого размера;
- хосты: нужно создать два хоста в двух разных зонах доступности и указать необходимость публичного доступа, то есть публичного IP адреса, для них;
- установите учётную запись для пользователя и базы.

Остальные параметры оставьте по умолчанию либо измените по своему усмотрению.

* Нажмите кнопку «Создать кластер» и дождитесь окончания процесса создания, статус кластера = RUNNING. Кластер создаётся от 5 до 10 минут.

![12-09-02](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/9%20-%20Databases%20in%20the%20cloud/img/12-09-02.png?raw=true)

#### Подключение к мастеру и реплике 

```
psql "host=rc1b-fudp96f6n3tpfkeq.mdb.yandexcloud.net,rc1d-b0p46v4popsekssj.mdb.yandexcloud.net \
    port=6432 \
    sslmode=verify-full \
    dbname=db1 \
    user=wah3299 \
    target_session_attrs=read-write"
```

* Используйте инструкцию по подключению к кластеру, доступную на вкладке «Обзор»: cкачайте SSL-сертификат и подключитесь к кластеру с помощью утилиты psql, указав hostname всех узлов и атрибут ```target_session_attrs=read-write```.

![12-09-03](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/9%20-%20Databases%20in%20the%20cloud/img/12-09-03.png?raw=true)

* Проверьте, что подключение прошло к master-узлу.
```
select case when pg_is_in_recovery() then 'REPLICA' else 'MASTER' end;
```
![12-09-04](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/9%20-%20Databases%20in%20the%20cloud/img/12-09-04.png?raw=true)


* Посмотрите количество подключенных реплик:
```
select count(*) from pg_stat_replication;
```

![12-09-05](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/9%20-%20Databases%20in%20the%20cloud/img/12-09-05.png?raw=true)

### Проверьте работоспособность репликации в кластере

* Создайте таблицу и вставьте одну-две строки.
```
CREATE TABLE test_table(text varchar);
```

![12-09-06](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/9%20-%20Databases%20in%20the%20cloud/img/12-09-06.png?raw=true)

```
insert into test_table values('Строка 1');
```

![12-09-07](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/9%20-%20Databases%20in%20the%20cloud/img/12-09-07.png?raw=true)


* Выйдите из psql командой ```\q```.

* Теперь подключитесь к узлу-реплике. Для этого из команды подключения удалите атрибут ```target_session_attrs```  и в параметре атрибут ```host``` передайте только имя хоста-реплики. Роли хостов можно посмотреть на соответствующей вкладке UI консоли.

```
ubuntu@ubuntuserver2204:~$ psql "host=rc1d-b0p46v4popsekssj.mdb.yandexcloud.net \
    port=6432 \
    sslmode=verify-full \
    dbname=db1 \
    user=wah3299 "
```

* Проверьте, что подключение прошло к узлу-реплике.
```
select case when pg_is_in_recovery() then 'REPLICA' else 'MASTER' end;
```

![12-09-08](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/9%20-%20Databases%20in%20the%20cloud/img/12-09-08.png?raw=true)


* Проверьте состояние репликации
```
select status from pg_stat_wal_receiver;
```

![12-09-09](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/9%20-%20Databases%20in%20the%20cloud/img/12-09-09.png?raw=true)


* Для проверки, что механизм репликации данных работает между зонами доступности облака, выполните запрос к таблице, созданной на предыдущем шаге:
```
select * from test_table;
```

![12-09-10](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/9%20-%20Databases%20in%20the%20cloud/img/12-09-10.png?raw=true)