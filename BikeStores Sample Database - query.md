# SQL 使用
 **以bike store sales 資料為例**  
 
 **來源**  
> [SQL Server Sample Database](https://www.sqlservertutorial.net/sql-server-sample-database/)

**修改**  
> 原本資料有*product*及*sales*兩個schema, 改為不分schema。 
因此table名稱，由*schema.table*改為*schema_table*


## Entity-Relationship Diagram
![Entity-Relationship Diagram](https://www.sqlservertutorial.net/wp-content/uploads/SQL-Server-Sample-Database.png)


$ SQL SELECT 語法
- [SQite SELECT ](https://www.sqlite.org/lang_select.html)
- [PostgresSQL SELECT](https://docs.postgresql.tw/reference/sql-commands/select)

## SELECT
 https://www.sqlservertutorial.net/sql-server-basics/sql-server-select/

Sqlite, PostgreSQL 不支援 OFFSET FETCH, TOP,

- 加州分店 email 促銷, 名單依照 first_name 排列
```SQL
SELECT
    first_name, last_name, email
FROM
    sales_customers
WHERE
    state = 'CA'
ORDER BY
    first_name;
```

- 選取客戶數大於10的city, 並由客戶大者排到小
```sql
SELECT
    city, COUNT (*)
FROM
    sales_customers
GROUP BY
    city
HAVING
    COUNT (*) > 10
ORDER BY
    2;
```

## ORDER BY
https://www.sqlservertutorial.net/sql-server-basics/sql-server-order-by/
```sql
SELECT
    city,
    first_name,
    last_name
FROM
    sales_customers
ORDER BY
    city,
    first_name;
```

## LIMIT OFFSET
SQL server 不支援 LIMIT。使用 類似的 [OFFSET FETCH](https://www.sqlservertutorial.net/sql-server-basics/sql-server-offset-fetch/)
SQLITE 及 postgreSQL 支援
https://www.runoob.com/sqlite/sqlite-limit-clause.html
```SQL
SELECT
    first_name, last_name, email
FROM
    sales_customers
WHERE
    state = 'CA'
ORDER BY
    first_name
LIMIT 10 OFFSET 10;	
```

## SELECT DISTINCT
https://www.sqlservertutorial.net/sql-server-basics/sql-server-select-distinct/

```SQL
SELECT DISTINCT 
    city, state
FROM
    sales_customers
ORDER BY
    state, city;
```

## NULL
https://www.sqlservertutorial.net/sql-server-basics/sql-server-null/

```SQL
SELECT
    first_name, last_name, email
FROM
    sales_customers 
WHERE
    email IS NULL
ORDER BY
    first_name;
```

```SQL
SELECT
    customer_id,
    first_name,
    last_name,
    phone
FROM
    sales_customers
WHERE
    phone IS NULL
ORDER BY
    first_name,
    last_name;
```

## IN
https://www.sqlservertutorial.net/sql-server-basics/sql-server-in/

```SQL
SELECT
    product_name,
    list_price
FROM
    production_products
WHERE
    list_price NOT IN (89.99, 109.99, 159.99)
ORDER BY
    list_price;
```

```SQL
SELECT
    product_name, list_price
FROM
    production_products
WHERE
    product_id IN (
        SELECT
            product_id
        FROM
            production_stocks
        WHERE
            store_id = 1 AND quantity >= 30
    )
ORDER BY
    product_name;
```

## BETWEEN
https://www.sqlservertutorial.net/sql-server-basics/sql-server-between/

```SQL
SELECT
    order_id, customer_id, order_date, order_status
FROM
    sales_orders
WHERE
    order_date BETWEEN '20170115' AND '20170117'
ORDER BY
    order_date;
```

## LIKE
https://www.sqlservertutorial.net/sql-server-basics/sql-server-like/

 - **%** 通配任意長度字元
 - **_** 通配任一字元
 - **[** abc **]** 通配a,b,c其中之一
```SQL
SELECT
    customer_id, first_name, last_name
FROM
    sales_customers
WHERE
    -- last_name LIKE 'z%'
	-- last_name LIKE '%er'
	-- last_name LIKE 't%s'
	-- last_name LIKE '[YZ]%'    -- SQite 不支此語法援
	-- last_name LIKE '[A-E]%'   -- SQite 不支此語法援
	-- last_name LIKE '[^A-X]%'  -- SQite 不支此語法援
	-- first_name NOT LIKE 'A%'
ORDER BY
    first_name;
```

```SQL
SELECT
    -- first_name + ' ' + last_name AS 'Full Name'   -- SQite + 
	first_name || ' ' || last_name AS 'Full Name' -- SQlite 的連結字元
FROM
    sales_customers
ORDER BY
    first_name;
```

## Joins
https://www.sqlservertutorial.net/sql-server-basics/sql-server-joins/

### INNER JOIN
https://www.sqlservertutorial.net/sql-server-basics/sql-server-inner-join/

```SQL
SELECT
    product_name, category_name, list_price
FROM
    production_products p
INNER JOIN production_categories c 
    ON c.category_id = p.category_id
ORDER BY
    product_name DESC;
```

```SQL
SELECT
    product_name, category_name, brand_name, list_price
FROM
    production_products p
INNER JOIN production_categories c ON c.category_id = p.category_id
INNER JOIN production_brands b ON b.brand_id = p.brand_id
ORDER BY
    product_name DESC;
```

### LEFT JOIN
https://www.sqlservertutorial.net/sql-server-basics/sql-server-left-join/

```SQL
SELECT
    product_name,
    order_id
FROM
    production_products p
LEFT JOIN sales_order_items o ON o.product_id = p.product_id
ORDER BY
    order_id;
```

```SQL
SELECT
    product_name,
    order_id
FROM
    production_products p
LEFT JOIN sales_order_items o ON o.product_id = p.product_id
WHERE order_id IS NULL;
```

### RIGHT JOIN
https://www.sqlservertutorial.net/sql-server-basics/sql-server-right-join/

```SQL
SELECT
    product_name,
    order_id
FROM
    sales_order_items o
    RIGHT JOIN production_products p 
        ON o.product_id = p.product_id
WHERE 
    order_id IS NULL
ORDER BY
    product_name;
```

### self join
https://www.sqlservertutorial.net/sql-server-basics/sql-server-self-join/


```SQL
SELECT
    e.first_name || ' ' || e.last_name employee,
    m.first_name || ' ' || m.last_name manager
FROM
    sales_staffs e
LEFT JOIN sales_staffs m ON m.staff_id = e.manager_id
ORDER BY
    manager;
```
修正原本範例資料庫的資料錯誤，使用前先檢查，staff_id 為9,10的員工，其manager_id是否為8.
```SQL
UPDATE sales_staffs 
SET manager_id = 8
WHERE staff_id > 8;
```

### FULL JOIN
https://www.sqlservertutorial.net/sql-server-basics/sql-server-full-outer-join/

原本FULL JOIN 範例是獨立於主要範例, 此處資料修改，以便融入主要範例中：將project manager設為主要範例中的有管理者身份員工。
)
- FULL JOIN
```SQL
SELECT 
    m.staff_id member, 
    p.title project
FROM 
    pm_members m
    FULL OUTER JOIN pm_projects p 
        ON p.project_id = m.project_id;
```

- LEFT JOIN
```SQL
SELECT 
    m.staff_id member, 
    p.title project
FROM 
    pm_members m
    LEFT JOIN pm_projects p 
        ON p.project_id = m.project_id;
```

- RIGHT JOIN
```SQL
SELECT 
    m.staff_id member, 
    p.title project
FROM 
    pm_members m
    RIGHT JOIN pm_projects p 
        ON p.project_id = m.project_id;

```
- 修改資料
```SQL
CREATE TABLE pm_projects(
    project_id INT PRIMARY KEY,
    title VARCHAR(255) NOT NULL
);

CREATE TABLE pm_members(
    staff_id INT,
    project_id INT,
    FOREIGN KEY (project_id) 
        REFERENCES pm_projects(project_id)
	FOREIGN KEY (staff_id) 
        REFERENCES sales_staffs(id)	
);

INSERT INTO 
    pm_projects(project_id,title)
VALUES
    (1,'New CRM for Project Sales'),
    (2,'ERP Implementation'),
    (3,'Develop Mobile Sales Platform');


INSERT INTO
    pm_members(staff_id, project_id)
VALUES
    (1, 1),
    (2, 1),
    (5, 2),
    (8, NULL);
```

### CROSS JOIN
https://www.sqlservertutorial.net/sql-server-basics/sql-server-cross-join/ 
 
```SQL
SELECT
    product_id,
    product_name,
    store_id,
    0 AS quantity
FROM
    production_products
CROSS JOIN sales_stores
ORDER BY
    product_name,
    store_id;
```

每家分店賣出的各種產品數量。  
原始查詢中，內圈query和外圈guery使用同樣 alias。在sqlite 中無法辨識。內外圈改為不一樣alias。

```SQL
SELECT
    s.store_id,
    p.product_id,
    IFNULL(sales, 0) sales
FROM
    sales_stores s
CROSS JOIN production_products p
LEFT JOIN (
    SELECT
        s1.store_id,
        p1.product_id,
        SUM (quantity * i.list_price) sales
    FROM
        sales_orders o
    INNER JOIN sales_order_items i ON i.order_id = o.order_id
    INNER JOIN sales_stores s1 ON s1.store_id = o.store_id
    INNER JOIN production_products p1 ON p1.product_id = i.product_id
    GROUP BY
        s1.store_id,
        p1.product_id
) c ON c.store_id = s.store_id
AND c.product_id = p.product_id
--WHERE
    --sales IS NULL
ORDER BY
    p.product_id,
    s.store_id;
```

簡化 內層，join production_products 及 sales_stores應無必要，除非要產品名稱。
```SQL
SELECT
    s.store_id,
    p.product_id,
    IFNULL(sales, 0) sales
FROM
    sales_stores s
CROSS JOIN production_products p
LEFT JOIN (
       SELECT
        o.store_id,
        i.product_id,
        SUM (quantity * i.list_price) sales
    FROM
        sales_orders o
    INNER JOIN sales_order_items i ON i.order_id = o.order_id
    GROUP BY
        i.product_id,
        o.store_id
) c ON c.store_id = s.store_id
AND c.product_id = p.product_id
--WHERE
    --sales IS NULL
ORDER BY
    p.product_id,
    s.store_id;
```

另一種寫法，將 CROSS JOIN 放在內圈

```SQL
    SELECT
       p.product_id,
       s.store_id,
       IFNULL(SUM (quantity * i.list_price) , 0) sales
    FROM
        sales_orders o
    INNER JOIN sales_order_items i ON i.order_id = o.order_id
    FULL  JOIN 
        (sales_stores s CROSS JOIN production_products p  ) c 
    ON c.product_id = i.product_id AND c.store_id = o.store_id 
    GROUP BY
        p.product_id,
        s.store_id;
```

## GROUP BY
https://www.sqlservertutorial.net/sql-server-basics/sql-server-group-by/

```SQL
SELECT
    customer_id,
	-- YEAR (order_date) order_year, -- SQL server
	-- EXTRACT(YEAR FROM order_date) order_year, -- PostgreSQL
    -- DATE_PART('Year',order_date) order_year, -- PostgreSQL
    STRFTIME('%Y', order_date) order_year, -- sqlite
    COUNT (order_id) order_placed
FROM
    sales_orders
GROUP BY
    customer_id,
    -- YEAR (order_date) -- SQL server
	-- EXTRACT(YEAR FROM order_date)-- PostgreSQL
    -- DATE_PART('Year',order_date) -- PostgreSQL
    STRFTIME('%Y', order_date) -- sqlite
ORDER BY
    customer_id; 
```

銷售摘要表
```SQL
SELECT
    b.brand_name AS brand,
    c.category_name AS category,
    p.model_year,
    round(
        SUM (quantity * i.list_price * (1 - discount)
    ),0) sales
FROM
    sales_order_items i
INNER JOIN production_products p ON p.product_id = i.product_id
INNER JOIN production_brands b ON b.brand_id = p.brand_id
INNER JOIN production_categories c ON c.category_id = p.category_id
GROUP BY
    b.brand_name,
    c.category_name,
    p.model_year
ORDER BY
    b.brand_name,
    c.category_name,
    p.model_year;
```


原有data資料，YYYYMMDD，SQLite 無法正確解讀為日期改為 YYYY-MM-DD。修正後
資料，再以SQL dump 後修改load_data。就不必執行以下指令。
```sql
UPDATE sales_orders
    SET order_date = SUBSTR(order_date,1,4) ||'-'|| SUBSTR(order_date,5,2)||'-'||SUBSTR(order_date,7,2)
WHERE 
    order_date NOTNULL;
	
UPDATE sales_orders
    SET required_date = SUBSTR(required_date,1,4) ||'-'|| SUBSTR(required_date,5,2)||'-'||SUBSTR(required_date,7,2)
WHERE 
    required_date NOTNULL;		
	
UPDATE sales_orders
    SET shipped_date = SUBSTR(shipped_date,1,4) ||'-'|| SUBSTR(shipped_date,5,2)||'-'||SUBSTR(shipped_date,7,2)
WHERE 
    shipped_date NOTNULL;
	
```

## HAVING
https://www.sqlservertutorial.net/sql-server-basics/sql-server-having/

```SQL
SELECT
    customer_id,
    strftime('%Y', order_date) order_year,
    COUNT (order_id) order_placed
FROM
    sales_orders
GROUP BY
    customer_id,
    strftime('%Y',order_date)
HAVING
    COUNT (order_id) >= 2
ORDER BY
    customer_id; 
```

```SQL
SELECT
    order_id,
    SUM (
        quantity * list_price * (1 - discount)
    ) net_value
FROM
    sales_order_items
GROUP BY
    order_id
HAVING
    SUM (
        quantity * list_price * (1 - discount)
    ) > 20000
ORDER BY
    net_value;
```

```SQL
SELECT
    category_id,
    AVG (list_price) avg_list_price
FROM
    production_products
GROUP BY
    category_id
HAVING
    AVG (list_price) BETWEEN 500 AND 1000;
```

# grouping set
https://www.sqlservertutorial.net/sql-server-basics/sql-server-grouping-sets/

便捷執行多種gouping方式組合。, 相當將多個group by 查詢後 UNION ALL 一起。  
SQLite不支援。


產生銷售摘要表，存為新的table,供以後查詢
```SQL
SELECT
    b.brand_name AS brand,
    c.category_name AS category,
    p.model_year,
    round(
        SUM ( quantity * i.list_price * (1 - discount)),0
	) sales
INTO sales_sales_summary
FROM
    sales_order_items i
INNER JOIN production_products p ON p.product_id = i.product_id
INNER JOIN production_brands b ON b.brand_id = p.brand_id
INNER JOIN production_categories c ON c.category_id = p.category_id
GROUP BY
    b.brand_name,
    c.category_name,
    p.model_year
ORDER BY
    b.brand_name,
    c.category_name,
    p.model_year;
```

SQLite 不支援 SELECT INTO, 用以下SQL
```SQL
CREATE TABLE sales_sales_summary  AS
SELECT
    b.brand_name AS brand,
    c.category_name AS category,
    p.model_year,
    round(
        SUM ( quantity * i.list_price * (1 - discount)),0
	) sales
FROM
    sales_order_items i
INNER JOIN production_products p ON p.product_id = i.product_id
INNER JOIN production_brands b ON b.brand_id = p.brand_id
INNER JOIN production_categories c ON c.category_id = p.category_id
GROUP BY
    b.brand_name,
    c.category_name,
    p.model_year
ORDER BY
    b.brand_name,
    c.category_name,
    p.model_year;
```

執行 grouping set
```sql
SELECT
	brand,
	category,
	SUM (sales) sales
FROM
	sales_sales_summary
GROUP BY
	GROUPING SETS (
		(brand, category),
		(brand),
		(category),
		()
	)
ORDER BY
	brand,
	category;
```

GOUPING(欄位) 可以顯示該欄位aggreate, 1為聚合、0為非聚合

```SQL
SELECT
    GROUPING(brand) grouping_brand,
    GROUPING(category) grouping_category,
    brand,
    category,
    SUM (sales) sales
FROM
    sales_sales_summary
GROUP BY
    GROUPING SETS (
        (brand, category),
        (brand),
        (category),
        ()
    )
ORDER BY
    brand,
    category;
```

SQLite不支援GOUPING SETS。上述 GOUPING SETS, 等同於以下分別GROUP 後，再UNION為1個表格。 

```SQL
SELECT 
    brand,
    category,
    SUM (sales) sales
FROM
    sales_sales_summary
GROUP BY
    brand,
    category
UNION ALL
SELECT
    brand,
    NULL,
    SUM (sales) sales
FROM
     sales_sales_summary
GROUP BY
    brand
UNION ALL
SELECT
    NULL,
    category,
    SUM (sales) sales
FROM
     sales_sales_summary
GROUP BY
    category
UNION ALL
SELECT
    NULL,
    NULL,
    SUM (sales)
FROM
    sales_sales_summary
ORDER BY brand, category;
```


## CUBE
https://www.sqlservertutorial.net/sql-server-basics/sql-server-cube/

便捷執行所有可能gouping方式的組合，相當將多個group by 查詢後 UNION ALL 一起。  
SQlite不支援.

CUBE(brand, category)產生4種grouping
1. (brand, category)
1. (brand)
1. (category)
1. ()
```SQL
SELECT
    brand,
    category,
    SUM (sales) sales
FROM
    sales_sales_summary
GROUP BY
    CUBE(brand, category);
```


## ROLLUP
https://www.sqlservertutorial.net/sql-server-basics/sql-server-rollup/  
層次的gouping, ROLLUP(brand, category)產生3種grouping
1. (brand, category)
1. (brand)
1. ()
通常用於階層性的分類，例如將每筆銷售的日期，分別記錄的銷售量，按照年，季、月、日的進行匯總。
SQLite不支援。

```SQL
SELECT
    brand,
    category,
    SUM (sales) sales
FROM
    sales_sales_summary
GROUP BY
    ROLLUP(brand, category);
```

## subguery
https://www.sqlservertutorial.net/sql-server-basics/sql-server-subquery/

```SQL
SELECT
    order_id, 
    order_date,
    customer_id
FROM
    sales_orders
WHERE
    customer_id IN (  -- subquery 傳 records
        SELECT
            customer_id
        FROM
            sales_customers
        WHERE
            city = 'New York'
    )
ORDER BY
    order_date DESC;
```
