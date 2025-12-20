# Домашнее задание к занятию «`SQL. Часть 2`» - `Рыхлик Илья Александрович`

---

# Задание 1

Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию: 
- фамилия и имя сотрудника из этого магазина;
- город нахождения магазина;
- количество пользователей, закреплённых в этом магазине.

## Решение 1

```
SELECT CONCAT(s2.first_name, ' ', s2.last_name) AS `Имя сотрудника`, a.address AS `Адрес магазина`, COUNT(c.store_id) AS `Кол-во пользователей`
FROM store s 
JOIN customer c ON s.store_id = c.store_id 
JOIN staff s2 ON s.manager_staff_id = s2.staff_id 
JOIN address a ON s.address_id = a.address_id 
GROUP BY c.store_id 
HAVING COUNT(c.store_id) > 300;
```
![12-04-01](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/4%20-%20SQL%20Part%202/img/12-04-01.png?raw=true)

--- 

# Задание 2

Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

## Решение 2

```
SELECT COUNT(*) AS `Кол-во фильмов с продолжительностьб больше среднего` 
	FROM film 
	WHERE length > (SELECT AVG(length) FROM film);
```
![12-04-02](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/4%20-%20SQL%20Part%202/img/12-04-02.png?raw=true)

---

# Задание 3

Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

# Решение 3

```
SELECT MONTH(payment_date) AS `Месяц`, COUNT(payment_id) As `Кол-во аренд`, SUM(amount) AS `Сумма платежей`
	FROM payment
	GROUP BY MONTH(payment_date) 
	ORDER BY COUNT(payment_id)  DESC LIMIT 1 ;
```
![12-04-03](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/4%20-%20SQL%20Part%202/img/12-04-03.png?raw=true)

---

# Задание 4*

Посчитайте количество продаж, выполненных каждым продавцом. Добавьте вычисляемую колонку «Премия». Если количество продаж превышает 8000, то значение в колонке будет «Да», иначе должно быть значение «Нет».

## Решение 4*

```
SELECT CONCAT(s.first_name, ' ', s.last_name) AS `ФИО`, COUNT(1) AS `Кол-во продаж`,
	CASE
		WHEN COUNT(1) > 8000 THEN 'Yes'
		ELSE 'No'
	END AS `Премия`
FROM payment p 
JOIN staff s ON p.staff_id = s.staff_id 
GROUP BY p.staff_id;
```

![12-04-04](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/4%20-%20SQL%20Part%202/img/12-04-04.png?raw=true)

---

# Задание 5*

Найдите фильмы, которые ни разу не брали в аренду.

## Решение 5*

```
SELECT f.title AS 'Без Продаж'
	FROM film f
	LEFT JOIN inventory i ON f.film_id = i.film_id
	LEFT JOIN rental r ON i.inventory_id = r.inventory_id
	WHERE r.inventory_id IS NULL;
```
![12-04-05](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/4%20-%20SQL%20Part%202/img/12-04-05.png?raw=true)