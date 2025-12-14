# Домашнее задание к занятию «SQL. Часть 1»

---

Задание можно выполнить как в любом IDE, так и в командной строке.

## Задание 1

Получите уникальные названия районов из таблицы с адресами, которые начинаются на “K” и заканчиваются на “a” и не содержат пробелов.

# *Решение 1*
```
SELECT DISTINCT district FROM address WHERE district LIKE 'K%a' AND district NOT LIKE '% %';
```

![12-03-01](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/3%20-%20SQL%20Part%201/img/12-03-01.png?raw=true)

---

### Задание 2

Получите из таблицы платежей за прокат фильмов информацию по платежам, которые выполнялись в промежуток с 15 июня 2005 года по 18 июня 2005 года **включительно** и стоимость которых превышает 10.00.

# *Решение 2*
```
SELECT * FROM payment WHERE payment_date BETWEEN '2005-06-15 00:00:00' AND '2005-06-18 23:59:59' AND amount > 10;
```
![12-03-02](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/3%20-%20SQL%20Part%201/img/12-03-02.png?raw=true)

---

### Задание 3

Получите последние пять аренд фильмов.

# *Решение 3*
```
SELECT * FROM rental ORDER BY rental_id DESC LIMIT 5;
```

![12-03-03](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/3%20-%20SQL%20Part%201/img/12-03-03.png?raw=true)


---

### Задание 4

Одним запросом получите активных покупателей, имена которых Kelly или Willie. 

Сформируйте вывод в результат таким образом:
- все буквы в фамилии и имени из верхнего регистра переведите в нижний регистр,
- замените буквы 'll' в именах на 'pp'.

# *Решение 4*
```
SELECT LOWER(REPLACE(first_name, 'LL', 'PP')) AS Имя, LOWER(last_name) AS Фамилия FROM customer WHERE active = 1 AND (first_name LIKE 'Kelly' OR first_name LIKE 'Willie');
```

![12-03-04](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/3%20-%20SQL%20Part%201/img/12-03-04.png?raw=true)

---
## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 5*

Выведите Email каждого покупателя, разделив значение Email на две отдельных колонки: в первой колонке должно быть значение, указанное до @, во второй — значение, указанное после @.

# *Решение 5*
```
SELECT SUBSTRING_INDEX(email, '@', 1) AS `email-name`, SUBSTRING_INDEX(email, '@', -1) AS `email-domain` FROM customer;
```

![12-03-05](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/3%20-%20SQL%20Part%201/img/12-03-05.png?raw=true)

---

### Задание 6*

Доработайте запрос из предыдущего задания, скорректируйте значения в новых колонках: первая буква должна быть заглавной, остальные — строчными.

# *Решение 6*
```
SELECT CONCAT(UPPER( LEFT(SUBSTRING_INDEX(email, '@', 1) , 1)) , LOWER( SUBSTRING(SUBSTRING_INDEX(email, '@', 1) , 2 ))) AS `email-name`, CONCAT(UPPER( LEFT(SUBSTRING_INDEX(email, '@', -1) , 1)) , LOWER( SUBSTRING(SUBSTRING_INDEX(email, '@', -1) , 2 ))) AS `email-domain`  FROM customer;
```

![12-03-06](https://github.com/ilaryhlik17854-stack/HW-relational_databases/blob/main/3%20-%20SQL%20Part%201/img/12-03-06.png?raw=true)
