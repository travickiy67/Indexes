# Травицкий Сергей
# Домашнее задание к занятию «Индексы»

### Инструкция по выполнению домашнего задания

1. Сделайте fork [репозитория c шаблоном решения](https://github.com/netology-code/sys-pattern-homework) к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
2. Выполните клонирование этого репозитория к себе на ПК с помощью команды `git clone`.
3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
   - впишите вверху название занятия и ваши фамилию и имя;
   - в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
   - для корректного добавления скриншотов воспользуйтесь инструкцией [«Как вставить скриншот в шаблон с решением»](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md);
   - при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в [инструкции по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md).
4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`).
5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
6. Любые вопросы задавайте в чате учебной группы и/или в разделе «Вопросы по заданию» в личном кабинете.

Желаем успехов в выполнении домашнего задания.

### Задание 1

Напишите запрос к учебной базе данных, который вернёт процентное отношение общего размера всех индексов к общему размеру всех таблиц.

<details>
<summary>Ответ</summary>

**Если брать общий размер базы, включая индексы, то   запрос будет в таком формате.**  

```
SELECT table_schema AS "DB_имя", SUM(data_length) AS "Объем базы", SUM(index_length) AS "Объем индексов",
SUM(data_length+index_length) AS "Общий объем",
	CONCAT(ROUND((SUM(index_length))/(SUM(data_length+index_length))*100),'%') '% индексов'
FROM information_schema.TABLES where TABLE_SCHEMA = 'sakila'
```

*Скрин 1*  

![img](https://github.com/travickiy67/Indexes/blob/main/img/1.1.png)  

**Если брать полезный объем данных по отношению к индексам, то я думаю так**

```
SELECT table_schema AS "DB_имя", SUM(data_length) AS "Объем базы", SUM(index_length) AS "Объем индексов",
SUM(data_length+index_length) AS "Общий объем",
	CONCAT(ROUND((SUM(index_length))/(SUM(data_length ))*100),'%') '% индексов'
FROM information_schema.TABLES where TABLE_SCHEMA = 'sakila'
```
*Скрин 2*  

![img](https://github.com/travickiy67/Indexes/blob/main/img/1.2.png)  

</details>

### Задание 2

Выполните explain analyze следующего запроса:
```sql
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id
```

<details>
<summary>Ответ</summary>  

```
explain analyze
 select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id, f.title)              --f.title
from payment p, rental r, customer c, inventory i, film f
where date(p.payment_date) = '2005-07-30' and p.payment_date = r.rental_date 
and r.customer_id = c.customer_id and i.inventory_id = r.inventory_id
```
**Выполнен explain**  

*скрин 1*  

![img](https://github.com/travickiy67/Indexes/blob/main/img/2.1.png)  

*Слрин 2*  

![img](https://github.com/travickiy67/Indexes/blob/main/img/2.2.png)  

- перечислите узкие места;  

**для запроса используются лишние таблицы. Можно удалить из запроса inventory и film. Ну использование оператора JOIN будет более оптимальным**
 
- оптимизируйте запрос: внесите корректировки по использованию операторов, при необходимости добавьте индексы.

**Оптимизированный код**

```
select distinct concat(c.last_name, ' ', c.first_name), sum(p.amount) over (partition by c.customer_id )              -- f.title
from payment p 
JOIN rental r ON p.payment_date = r.rental_date
JOIN customer c ON r.customer_id = c.customer_id
where date(p.payment_date) = '2005-07-30';
```
*СКрин 1*  

![img](https://github.com/travickiy67/Indexes/blob/main/img/2.3.png)  

*Скрин 2*  

![img](https://github.com/travickiy67/Indexes/blob/main/img/2.4.png)  

</details>

*Скорость выполнения запроса увеличилась в разы, в добавлении индекса не вижу смысла, особенно после первого задания, оценив отношение веса индексов к весу таблиц.

## Дополнительные задания (со звёздочкой*)
Эти задания дополнительные, то есть не обязательные к выполнению, и никак не повлияют на получение вами зачёта по этому домашнему заданию. Вы можете их выполнить, если хотите глубже шире разобраться в материале.

### Задание 3*

Самостоятельно изучите, какие типы индексов используются в PostgreSQL. Перечислите те индексы, которые используются в PostgreSQL, а в MySQL — нет.

*Приведите ответ в свободной форме.*
