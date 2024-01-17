# Pizza Sales ������ ��� ��������� � SQL & Power BI
�������� ������ ������������ � 4 CSV ������. ��� ������ ������������ ����� ������� ����� ������ ��������. ���������� �� ����� �������� �� ��������� ��������, ������� ������� ������� �������, ����� �������� �������.

������ �������� � Microsoft SQL Server � ����������� � Power BI. ������ ���� ��������� � 4 �������. � ������� � ����������� ���� ��� JOIN'�� � ����������. 

## ������� �� ������� ����� ��������:
### KPIs

 1) ����� �����
 2) ������� ��������� ������
 3) ����� ���-�� ��������� ����
 4) ����� ���-�� �������
 5) ������� ���������� ���� �� �����

### QUESTIONS TO ANSWER 

 1) ����� ���������� ������� �� ���� ������
 2) ����� ���������� ������� �� �����
 3) ������� ������ �� ���������� �����
 4) ������� ������ �� ������� �����
 5) ����� ���������� ��������� ���� �� ����������
 6) ��� 5 ������ ���� �� ���������� ������
 7) ��� 5 ������ ���� �� ���������� ������



## ������:
### KPIs

 1) ����� ����� �� ��� �������� $817 860
 2) ������� ��������� ������ ��������� $38.31
 3) ����� ���������� ��������� ���� - 50 000
 4) ����� ���������� ������� - 21 000
 5) ������� ���������� ���� �� ����� - 2

### ANSWER TO QUESTIONS

 1) ����� ����������� ��� - ������� (3239 �������), ������� (3538 �������) � ������� (3158 �������). ������ ����� ������ ������������� � �������
 2) ����������� ������� �������� � ������ � 12:00 �� 13:00 � � 17:00 �� 19:000
 3) Classic ����� ����� ����� ������� ������� ������ (26,91 %), �� ��� ������� ����� Supreme (25,46 %), Chicken (23,96 %) � Veggie (23,68 %) 
 4) ���������� ����� ������ ���������� �� ����� �������� ������� (45,89%), ����� �������� (30,49%) � ������ (21,77%). �� ���� XL � XXL ���������� ����� 1,72% � 0,12% ��������������. 
 5) ���������� ����� ������ ���������� �� Classic ����� (14 888 ����), �� ������� ��� Supreme (11 987 ����), Veggie (11 649 ����) � Chicken (11 050 ����).
 6) ���-5 ����� ����������� ���� - Classic Deluxe (2453 �����), Barbecue Chicken (2432 �����), Hawaiian (2422), Peperoni (2418 ����) � Thai Chicken (2371 �����)
 7) � ���-5 ������ ����������� ���� ������ Brie Carre (490 ����), Mediterranean (934 �����), Calabrese (937 ����), Spinach Supreme (950 ����) � Soppressata (961).


## CONCLUSION:
 ������ ����� ������� �� ������� ������� ���� Classic, Supreme, Veggie � Chicken.

��������� �� ����� XL � XXL ���������� ����� ��������� ������� ������ (����� 1,94%), �� ���� �������� ����� �����������.

## ������� �������������� ��� �������:
### KPIs

#### 1) ����� �����
``` SQL
SELECT 
 round(SUM(quantity * price), 2)
FROM order_details AS o
 JOIN pizzas AS p 
 ON o.pizza_id = p.pizza_id
```

#### 2) ������� ��������� ������

``` SQL
SELECT 
 SUM(quantity * price)/ COUNT(DISTINCT order_id) AS [Average Order Value]
FROM order_details AS o
 JOIN pizzas AS p 
 ON o.pizza_id = p.pizza_id
```

#### 3) ����� ���-�� ��������� ����
``` SQL
 SELECT
  SUM(quantity) AS [Total Pizzas Sold]
FROM
  order_details
```


#### 4) ������� ������ �� ������� �����
``` SQL
SELECT
  COUNT(DISTINCT order_id) AS [Total Orders]
FROM
  order_details

```

#### 5) ������� ���������� ���� �� �����
``` SQL
SELECT
  ROUND(SUM(quantity)/COUNT(DISTINCT order_id),2) AS [Average Pizzas Per Order]
FROM
  order_details
```

### ������� �� ������� ������

#### 1) ����� ���������� ������� �� ���� ������
``` SQL
SELECT 
 FORMAT(date, 'dddd') AS DayOfWeek
 ,COUNT(DISTINCT order_id) AS total_orders
FROM orders
GROUP BY FORMAT(date, 'dddd')
ORDER BY total_orders DESC
```


#### 2) ����� ���������� ������� �� �����
``` SQL
SELECT 
    DATEPART(HOUR, time) AS [Hour]
	,COUNT(DISTINCT order_id) AS Total_Orders
FROM orders
GROUP BY DATEPART(HOUR, time)
ORDER BY [Hour]
```


### 3) ������� ������ �� ���������� �����
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



#### 4) ����� ���-�� �������
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


#### 5) ����� ���������� ��������� ���� �� ����������
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


#### 6) ��� 5 ������ ���� �� ���������� ������
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


#### 7) ��� 5 ������ ���� �� ���������� ������
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