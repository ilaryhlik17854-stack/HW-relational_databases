# Домашнее задание к занятию «Репликация и масштабирование. Часть 1» - `Рыхлик Илья Александрович`

---

# Задание 1

На лекции рассматривались режимы репликации master-slave, master-master, опишите их различия.

*Ответить в свободной форме.*

## Решение 1

#### master-slave
данные копируются с одного сервера базы данных (master) на один или несколько серверов (slave). Master отвечает за приём всех операций записи (вставок, обновлений, удалений), а slave хранит копии данных
#### *Достоинства:*
отказоустойчивость — slave-серверы действуют как горячие резервные копии, готовые стать новым master в случае отказа основного сервера;
масштабируемость — запросы на чтение могут быть распределены между slave-серверами, что разгружает master-сервер.
#### *Недостатки:*
slave-системы не принимают операции прямой записи;
в зависимости от выбранной конфигурации возможны задержки в распространении данных.

#### master-master
оба сервера одновременно принимают записи и изменения. Каждый сервер является и master, и slave для другого. Изменения с одного сервера реплицируются на второй, и наоборот

#### *Достоинства:*
отказоустойчивость — если один сервер падает, второй продолжает работать;
балансировка нагрузки — можно распределять запросы на запись между двумя серверами;
горизонтальное масштабирование — удобно для геораспределённых проектов, когда сервера стоят в разных дата-центрах;
обновления без простоя — можно обновлять один сервер, пока второй обслуживает пользователей.
#### *Недостатки:*
конфликты — если два пользователя одновременно изменят одну и ту же строку на разных серверах, могут возникнуть конфликты, которые сложно разрешить автоматически;
задержки репликации;
сложность поддержки.
---

### Задание 2

Выполните конфигурацию master-slave репликации, примером можно пользоваться из лекции.

*Приложите скриншоты конфигурации, выполнения работы: состояния и режимы работы серверов.*

## Решение 2

Использум две виртуальные машины ubuntu
Устанвливаем на обе MySQL 8.0

```
wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-server_8.0.44-1ubuntu22.04_amd64.deb-bundle.tar
tar -xvf /home/ubuntu/mysql-server_8.0.44-1ubuntu22.04_amd64.deb-bundle.tar
apt install ./mysql-community-server-core_8.0.44-1ubuntu22.04_amd64.deb
apt install ./mysql-common_8.0.44-1ubuntu22.04_amd64.deb
apt install ./mysql-community-client-plugins_8.0.44-1ubuntu22.04_amd64.deb
apt install ./mysql-community-client-core_8.0.44-1ubuntu22.04_amd64.deb
apt install ./mysql-community-client_8.0.44-1ubuntu22.04_amd64.deb
apt install ./mysql-client_8.0.44-1ubuntu22.04_amd64.deb
apt install ./mysql-community-server_8.0.44-1ubuntu22.04_amd64.deb
apt install ./mysql-server_8.0.44-1ubuntu22.04_amd64.deb
```
Теперь на настраиваем мастер ноду

в файле *(sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf)* вносим изменения

![12-06-01](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/6%20-%20Replication%20and%20scaling%20Part%201/img/12-06-01.png?raw=true)

заходим в mysql под root и выполняем следующие команды

```
mysql -u root -p
CREATE USER 'replication'@'%' IDENTIFIED WITH mysql_native_password BY '12345';
GRANT REPLICATION SLAVE ON *.* TO 'replication'@'%';
FLUSH PRIVILEGES;
FLUSH TABLES WITH READ LOCK;
SHOW MASTER STATUS;
```
![12-06-02](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/6%20-%20Replication%20and%20scaling%20Part%201/img/12-06-02.png?raw=true)
![12-06-03](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/6%20-%20Replication%20and%20scaling%20Part%201/img/12-06-03.png?raw=true)

Теперь на настраиваем slave ноду

в файле *(sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf)* вносим изменения

![12-06-04](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/6%20-%20Replication%20and%20scaling%20Part%201/img/12-06-04.png?raw=true)

заходим в mysql под root и выполняем следующие команды

```
CHANGE REPLICATION SOURCE TO 
   SOURCE_HOST='192.168.100.143', 
   SOURCE_USER='replication', 
   SOURCE_PASSWORD='12345', 
   SOURCE_LOG_FILE='mysql-bin.000001', 
   SOURCE_LOG_POS=28118;
START REPLICA;
SHOW REPLICA STATUS\G; 
```
![12-06-05](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/6%20-%20Replication%20and%20scaling%20Part%201/img/12-06-05.png?raw=true)

На мастере создаем базу:
```
CREATE DATABASE db;
```

На слейве проверяем базы 
```
SHOW DATABASES;
```
![12-06-06](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/6%20-%20Replication%20and%20scaling%20Part%201/img/12-06-06.png?raw=true)
