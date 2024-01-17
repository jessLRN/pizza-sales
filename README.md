# Pizza Sales Проект для портфолио – SQL & Power BI
Исходные данные представлены в 4 CSV файлах. Эти данные представляют собой годовой объем продаж пиццерии. Анализируя их нужно ответить на несколько вопросов, которые помогут принять решения, чтобы улучшить продажи.

Проект выполнен в Microsoft SQL Server и представлен в Power BI. Данные были загружены в 4 таблицы. В проекте я использовал один тип JOIN'ов и подзапросы. 

## ВОПРОСЫ НА КОТОРЫЕ НУЖНО ОТВЕТИТЬ:
### KPIs

 1) Общий доход
 2) Средняя стоимость заказа
 3) Общее кол-во проданных пицц
 4) Общее кол-во заказов
 5) Среднее количество пицц на заказ

### QUESTIONS TO ANSWER 

 1) Общее количество заказов по дням недели
 2) Общее количество заказов по часам
 3) Процент продаж по категориям пиццы
 4) Процент продаж по размеру пиццы
 5) Общее количество проданных пицц по категориям
 6) Топ 5 лучших пицц по количеству продаж
 7) Топ 5 худших пицц по количеству продаж



## Выводы:
### KPIs

 1) Общий доход за год составил $817 860
 2) Средняя стоимость заказа составила $38.31
 3) Общее количество проданных пицц - 50 000
 4) Общее количество заказов - 21 000
 5) Среднее количество пицц на заказ - 2

### ANSWER TO QUESTIONS

 1) Самые загруженные дни - четверг (3239 заказов), пятница (3538 заказов) и суббота (3158 заказов). Больше всего продаж зафиксировано в пятницу
 2) Большинство заказов делается в период с 12:00 до 13:00 и с 17:00 до 19:000
 3) Classic пицца имеет самый высокий процент продаж (26,91 %), за ней следуют пиццы Supreme (25,46 %), Chicken (23,96 %) и Veggie (23,68 %) 
 4) Наибольший объем продаж приходится на пиццу большого размера (45,89%), затем среднего (30,49%) и малого (21,77%). На долю XL и XXL приходится всего 1,72% и 0,12% соответственно. 
 5) Наибольший объем продаж приходится на Classic пиццу (14 888 пицц), за которой идёт Supreme (11 987 пицц), Veggie (11 649 пицц) и Chicken (11 050 пицц).
 6) Топ-5 самых продаваемых пицц - Classic Deluxe (2453 пиццы), Barbecue Chicken (2432 пиццы), Hawaiian (2422), Peperoni (2418 пицц) и Thai Chicken (2371 пицца)
 7) В топ-5 худших продаваемых пицц входят Brie Carre (490 пицц), Mediterranean (934 пиццы), Calabrese (937 пицц), Spinach Supreme (950 пицц) и Soppressata (961).


## CONCLUSION:
 Ставку нужно сделать на большие размеры пицц Classic, Supreme, Veggie и Chicken.

Поскольку на пиццу XL и XXL приходится такой маленький процент продаж (всего 1,94%), от этих размеров можно избавляться.

## ЗАПРОСЫ ИСПОЛЬЗОВАННЫЕ ПРИ РЕШЕНИИ:
### KPIs

#### 1) Общий доход
``` SQL
SELECT 
 round(SUM(quantity * price), 2)
FROM order_details AS o
 JOIN pizzas AS p 
 ON o.pizza_id = p.pizza_id
```

#### 2) Средняя стоимость заказа

``` SQL
SELECT 
 SUM(quantity * price)/ COUNT(DISTINCT order_id) AS [Average Order Value]
FROM order_details AS o
 JOIN pizzas AS p 
 ON o.pizza_id = p.pizza_id
```

#### 3) Общее кол-во проданных пицц
``` SQL
 SELECT
  SUM(quantity) AS [Total Pizzas Sold]
FROM
  order_details
```


#### 4) Процент продаж по размеру пиццы
``` SQL
SELECT
  COUNT(DISTINCT order_id) AS [Total Orders]
FROM
  order_details

```

#### 5) Среднее количество пицц на заказ
``` SQL
SELECT
  ROUND(SUM(quantity)/COUNT(DISTINCT order_id),2) AS [Average Pizzas Per Order]
FROM
  order_details
```

### Вопросы по анализу продаж

#### 1) Общее количество заказов по дням недели
``` SQL
SELECT 
 FORMAT(date, 'dddd') AS DayOfWeek
 ,COUNT(DISTINCT order_id) AS total_orders
FROM orders
GROUP BY FORMAT(date, 'dddd')
ORDER BY total_orders DESC
```


#### 2) Общее количество заказов по часам
``` SQL
SELECT 
    DATEPART(HOUR, time) AS [Hour]
	,COUNT(DISTINCT order_id) AS Total_Orders
FROM orders
GROUP BY DATEPART(HOUR, time)
ORDER BY [Hour]
```


### 3) Процент продаж по категориям пиццы
- a: calculate total revenue per category
- % sales calculated as (a:/total revenue) * 100
``` SQL
SELECT 
    category,
    ROUND(SUM(quantity * price), 2) AS revenue,
    ROUND(SUM(quantity * price) * 100.0 / (SELECT SUM(quantity * price) FROM pizzas AS p2 JOIN order_details AS od2 ON od2.pizza_id = p2.pizza_id), 2) AS percentage_of_sales
FROM 
    pizzas AS p
JOIN 
    pizza_types AS pt ON p.pizza_type_id = pt.pizza_type_id
JOIN 
    order_details AS od ON od.pizza_id = p.pizza_id
GROUP BY 
    category;
```



#### 4) Общее кол-во заказов
``` SQL
SELECT 
    size
    ,ROUND(SUM(quantity * price), 2) AS revenue
    ,ROUND(SUM(quantity * price) * 100.0 / (SELECT SUM(quantity * price) FROM pizzas AS p2 JOIN order_details AS od2 ON od2.pizza_id = p2.pizza_id), 2) AS percentage_of_sales
FROM 
    pizzas AS p
JOIN 
    pizza_types AS pt ON p.pizza_type_id = pt.pizza_type_id
JOIN 
    order_details AS od ON od.pizza_id = p.pizza_id
GROUP BY 
    size;
```


#### 5) Общее количество проданных пицц по категориям
``` SQL
SELECT
 category
 ,SUM(quantity) AS quantity_sold
FROM 
    pizzas AS p
JOIN 
    pizza_types AS pt ON p.pizza_type_id = pt.pizza_type_id
JOIN 
    order_details AS od ON od.pizza_id = p.pizza_id
GROUP BY category;
```


#### 6) Топ 5 лучших пицц по количеству продаж
``` SQL
SELECT top 5
  name
  ,SUM(quantity) AS total_quantity_sold
FROM 
    pizzas AS p
JOIN 
    pizza_types AS pt ON p.pizza_type_id = pt.pizza_type_id
JOIN 
    order_details AS od ON od.pizza_id = p.pizza_id
GROUP BY name
ORDER BY total_quantity_sold DESC;
```


#### 7) Топ 5 худших пицц по количеству продаж
``` SQL
SELECT top 5
  name
  ,SUM(quantity) AS total_quantity_sold
FROM 
    pizzas AS p
JOIN 
    pizza_types AS pt ON p.pizza_type_id = pt.pizza_type_id
JOIN 
    order_details AS od ON od.pizza_id = p.pizza_id
GROUP BY name
ORDER BY total_quantity_sold ASC;
```