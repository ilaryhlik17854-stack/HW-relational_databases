# Домашнее задание к занятию «Индексы»
# Домашнее задание к занятию «Индексы» - `Рыхлик Илья Александрович`

---

# Задание 1

Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

## Решение 1
```
SELECT SUM(data_length) AS SUM_Data_Length, SUM(index_length) AS SUM_Index_Length, CONCAT(ROUND ((SUM(index_length)*100.0/SUM(data_length)))) AS `% Отношение`
FROM information_schema.tables
WHERE table_schema='sakila' AND data_length IS NOT NULL;
```
![12-05-01](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/5%20-%20Index/img/12-05-01.png?raw=true)

---

# Задание 2

Выполните explain analyze следующего запроса:
```sql
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id
```
- перечислите узкие места;
- оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.

## Решение 2
Использование функции DATE() на p.payment_date, что может препятствовать использованию индексов.

```
WHERE p.payment_date >= '2005-07-30 00:00:00' AND p.payment_date < '2005-07-31 00:00:00'
```

Использовать явные JOIN-ы для лучшей читаемости и потенциальной оптимизации со стороны оптимизатора запросов.

```
EXPLAIN ANALYZE
SELECT DISTINCT CONCAT(c.last_name, ' ', c.first_name),
       SUM(p.amount) OVER (PARTITION BY c.customer_id, f.title)
FROM payment p
JOIN rental r ON p.payment_date = r.rental_date
JOIN customer c ON r.customer_id = c.customer_id
JOIN inventory i ON i.inventory_id = r.inventory_id
JOIN film f ON f.film_id = i.film_id
WHERE p.payment_date >= '2005-07-30 00:00:00' AND p.payment_date < '2005-07-31 00:00:00';
```

### до 

![12-05-02](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/5%20-%20Index/img/12-05-02.png?raw=true)

### после

![12-05-03](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/5%20-%20Index/img/12-05-03.png?raw=true)