# SQL 使用
 **以bike store sales 資料為例，並調整為Sqlite適用格式**  
 
 **資料來源**  
> [SQL Server Sample Database](https://www.sqlservertutorial.net/sql-server-sample-database/)
 
**資料修改**  
> - 整體修正：因SQLite 不支援多個schema,原本資料有*product*及*sales*兩個schema, 改為不分schema。 
因此table名稱，由*schema.table*改為*schema_table*
> - 個別修正：各範例中個別以資料修改標示
> - 課程所提供資料庫，已修改。


## Entity-Relationship Diagram
![UML class ](https://www.sqlservertutorial.net/wp-content/uploads/SQL-Server-Sample-Database.png)


# SQL SELECT 語法
- [SQite SELECT 語法](https://www.sqlite.org/lang_select.html)
- [PostgresSQL SELECT 語法](https://docs.postgresql.tw/reference/sql-commands/select)
  - [PostgresSQL SELECT 教學](https://docs.postgresql.tw/reference/sql-commands/select)


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
    order_date BETWEEN '2017-01-15' AND '2017-01-17'
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
    -- last_name LIKE 'z%'    -- SQlite 不分大小寫
    -- last_name like 'Z%'   -- PostgresSQL 區分大小寫
    -- last_name LIKE '%er'  
    -- last_name LIKE 't%s'  -- SQlite 不分大小寫
    -- last_name LIKE 'T%s'  -- PostgresSQL 區分大小寫
    -- last_name SIMILAR TO  '[YZ]%' -- 使用regular expression, PostgresSQL 使用 SIMILAR TO
    -- last_name LIKE '[YZ]%'    -- SQLite 不支reqular expression 語法
    -- last_name SIMILAR TO '[A-E]%'   -- 使用regular expression, PostgresSQL 使用 SIMILAR TO
    -- last_name LIKE '[^A-X]%'  -- SQLite 不支reqular expression 語法
    -- first_name NOT LIKE 'A%'
ORDER BY
    first_name;
```

##  Alias
https://www.sqlservertutorial.net/sql-server-basics/sql-server-alias/

表格及欄位可以別名取代

欄位別名
```SQL
SELECT
    -- first_name + ' ' + last_name AS 'Full Name' --  SQL Server 使用 + 連結字串
	first_name || ' ' || last_name AS "Full Name" -- SQlite 及 PostgesSQL使用 || 連結字串字元
	-- CONCAT(first_name, ' ', last_name) AS "Full name" -- PostgesSQL 使用CCONCAT 函數連結字串
FROM
    sales_customers
ORDER BY
    first_name;
```

表格別名，通常用在查詢中使用兩個以上Table的情況。
```SQL
SELECT
    c.customer_id,
    first_name,
    last_name,
    order_id
FROM
    sales_customers c
INNER JOIN sales_orders o ON o.customer_id = c.customer_id;
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
-- LEFT OUTER JOIN sales_order_items o ON o.product_id = p.product_id  -- 意義同上　
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

在sales_staffs中的manager_id欄位該記載各員工的管理者的staff_id，使用self join 列出員工及管理者姓名。
```SQL
SELECT
   -- CONCAT(e.first_name,' ',e.last_name)  employee, -- PostgreSQL
   -- CONCAT(m.first_name,' ',m.last_name)  manager  -- PostgreSQL
   e.first_name || ' ' || e.last_name employee,  -- SQLite
   m.first_name || ' ' || m.last_name manager    -- SQLite
FROM
    sales_staffs e
LEFT JOIN sales_staffs m ON m.staff_id = e.manager_id
ORDER BY
    manager;
```

----
> #### 資料修改
>  修正原本範例資料庫的資料錯誤，使用前先檢查，staff_id 為9,10的員工，其manager_id是否為8.
```SQL
UPDATE sales_staffs 
SET manager_id = 8
WHERE staff_id > 8;
```
----
### FULL JOIN
https://www.sqlservertutorial.net/sql-server-basics/sql-server-full-outer-join/

原本FULL JOIN 範例是獨立於主要範例, 此處資料修改，以便融入主要範例中：將project manager設為主要範例中的有管理者身份的員工。
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
  
----
> #### 資料修改
> 增加 pm_projects 及 pm_members 兩個資料表格
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
----

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

### HAVING
https://www.sqlservertutorial.net/sql-server-basics/sql-server-having/
使用於 GROUP BY 的子語句。對GROUP BY 後結果，進行挑選。

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

### grouping set
https://www.sqlservertutorial.net/sql-server-basics/sql-server-grouping-sets/

使用於 GROUP BY 的子語句.
便於快捷執行多種gouping方式組合。, 相當將多個group by 查詢後 UNION ALL 一起。  
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
ORDER BY brand, 
         category;
```


### CUBE
https://www.sqlservertutorial.net/sql-server-basics/sql-server-cube/

使用於 GROUP BY 的子語句。便捷執行所有可能gouping方式的組合，相當將多個group by 查詢後 UNION ALL 一起。  
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


### ROLLUP
https://www.sqlservertutorial.net/sql-server-basics/sql-server-rollup/  

使用於 GROUP BY 的子語句。層次的gouping, ROLLUP(brand, category)產生3種grouping
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

## subguery 子查詢
https://www.sqlservertutorial.net/sql-server-basics/sql-server-subquery/  

查尋內有子查尋。
子查尋的結果為表格。所有理論上可以用在，使用TABLE作為參數的位置。常配合**IN**、**ANY**、**ALL**、**EXITS**等SQL關鍵詞使用。

```SQL
SELECT
    order_id, 
    order_date,
    customer_id
FROM
    sales_orders  -- outter query 
WHERE
    customer_id IN (  -- subquery 
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

**Nesting subquery  巢狀子查尋**  
以下找出產品價格大於 Strdeer 及 Trek 兩品牌所有產品的平均價格的產品。
```SQL

SELECT
    product_name,
    list_price
FROM
    production_products
WHERE
    list_price > (
        SELECT
            AVG (list_price)
        FROM
            production_products
        WHERE
            brand_id IN (
                SELECT
                    brand_id
                FROM
                    production_brands
                WHERE
                    brand_name = 'Strider'
                OR brand_name = 'Trek'
            )
    )
ORDER BY
    list_price;
```

前面說明 GROUPING SET, CUBE, ROLLUP 時，先產生 sales_sales_summary 表格，然後再進行查尋，但可以將產生 sales_sales_summary 的 SQL 作為子查詢。

```SQL
SELECT
    brand,
    category,
    SUM (sales) sales
FROM
    (  -- 產生 sales_sales_summary 的子查詢
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
      p.model_year
    ) sales_sales_summary
GROUP BY
    CUBE(brand, category);

```

### EXISTS
https://www.sqlservertutorial.net/sql-server-basics/sql-server-exists/  

EXISTS 運算符是一個邏輯運算符，可讓檢查**子查詢**是否返回任何行。果子查詢返回一行或多行，則 EXISTS 運算符返回 TRUE

```SQL
SELECT
    customer_id,
    first_name,
    last_name
FROM
    sales_customers c
WHERE
    EXISTS (　-- 檢查 sales_customers C 的每一筆資料　　
        SELECT
            COUNT (*)
        FROM
            sales_orders o
        WHERE
            customer_id = c.customer_id -- 隱含的INNER　JOIN
        GROUP BY
            customer_id
        HAVING
            COUNT (*) > 2
    )
ORDER BY
    first_name,
    last_name;
```

下面SQL不使用EXIT，使用 INNER JOIN ;結果和使用EXIT的SQL相同
```SQL
SELECT
    c.customer_id,
    first_name,
    last_name,
    COUNT (*) orders
FROM
    sales_customers c
INNER JOIN    
    sales_orders o 
  ON
    o.customer_id = c.customer_id
GROUP BY
    o.customer_id
  HAVING
    COUNT (*) > 2
ORDER BY
    first_name,
    last_name;
```

### ANY
https://www.sqlservertutorial.net/sql-server-basics/sql-server-any/  

ANY運算符是一個邏輯運算符，**子查詢**必須產生只一個欄位的返回值。如果該返回欄位任一值滿足條件即可為真。

```SQL
SELECT
    product_name,
    list_price
FROM
    production_products
WHERE
    product_id = ANY (
        SELECT
            product_id
        FROM
            sales_order_items
        WHERE
            quantity >= 2
    )
ORDER BY
    product_name;
```

SQLIte 不支援 ANY，用 IN

```SQL
SELECT
    product_name,
    list_price
FROM
    production_products
WHERE
    product_id IN (
        SELECT
            product_id
        FROM
            sales_order_items
        WHERE
            quantity >= 2
    )
ORDER BY
    product_name;
```

### ALL
https://www.sqlservertutorial.net/sql-server-basics/sql-server-all/  

ANY運算符是一個邏輯運算符，**子查詢**必須產生只一個欄位的返回值。如果該返回欄位所有的值均滿足條件即可為真。

以下查詢，列出大於每個品牌產品平均值的產品
```SQL
SELECT
    product_name,
    list_price
FROM
    production_products
WHERE
    list_price > ALL (
        SELECT
            AVG (list_price) avg_list_price
        FROM
            production_products
        GROUP BY
            brand_id
    )
ORDER BY
    list_price;
```

## UNION
https://www.sqlservertutorial.net/sql-server-basics/sql-server-union/  

兩個查詢結果的聯集,兩查詢結果必須的欄位必須同樣類型。兩查詢有相同紀錄時，僅會保留一個。

以下查詢有中客戶中有兩筆同first_name及last_name,因此僅保留一筆。
```SQL
SELECT
    first_name,
    last_name
FROM
    sales_staffs
UNION
SELECT
    first_name,
    last_name
FROM
    sales_customers;
```
UNION ALL 重複的紀錄將會保留。
```SQL
SELECT
    first_name,
    last_name
FROM
    sales_staffs
UNION ALL
SELECT
    first_name,
    last_name
FROM
    sales_customers;
```

## INTERSET
https://www.sqlservertutorial.net/sql-server-basics/sql-server-intersect/  


兩個查詢結果的交集,兩查詢結果必須的欄位必須同樣類型。

選擇有店及客戶的城市
```SQL
SELECT
    city
FROM
    sales_customers
INTERSECT
SELECT
    city
FROM
    sales_stores
ORDER BY
    city;
```

## EXCEPT
https://www.sqlservertutorial.net/sql-server-basics/sql-server-except/  

兩個查詢結果的交集,兩查詢結果必須的欄位必須同樣類型。

哪些產品沒有訂單
```SQL
SELECT
    product_id
FROM
    production_products
EXCEPT
SELECT
    product_id
FROM
    sales_order_items
ORDER BY 
	product_id;
```

## CTE (Common Table Expression)
https://www.sqlservertutorial.net/sql-server-basics/sql-server-cte/  

CTE  (Common Table Expression) 允許定義臨時命名結果集，該結果集在 SELECT、INSERT、UPDATE、DELETE等語句的執行範圍內臨時可用。

語法如下
```SQL
WITH expression_name[(column_name [,...])]
AS
    (CTE_definition)
SQL_statement;
```

以下查詢先產生cte_sales_amounts- 每個員工歷年的銷售額，然後查詢2018年的員工銷售額
```SQL
WITH cte_sales_amounts (staff, sales, year) AS (
    SELECT    
          -- first_name + ' ' + last_name AS staff,   -- SQite + 
	    first_name || ' ' || last_name AS staff, -- SQlite 的連結字元
        SUM(quantity * list_price * (1 - discount)) AS sales,
        -- YEAR (order_date) year, -- SQL server
	    -- EXTRACT(YEAR FROM order_date) year, -- PostgreSQL
        -- DATE_PART('Year',order_date) year, -- PostgreSQL
        STRFTIME('%Y', order_date) AS year -- sqlite
    FROM    
        sales_orders o
    INNER JOIN sales_order_items i ON i.order_id = o.order_id
    INNER JOIN sales_staffs s ON s.staff_id = o.staff_id
    GROUP BY 
        1, 3
)
SELECT
    staff, 
    sales
FROM 
    cte_sales_amounts
WHERE
  -- year = 2018; -- SQL Server, PostgresSQL
    year = '2018'; -- SQLite
```

前面說明 GROUPING SET, CUBE, ROLLUP 時，先產生 sales_sales_summary 表格，然後再進行查尋，但可以將產生 sales_sales_summary 的 以CTE語法查詢。 
(SQLite 支援 CTE，但不支援 CUBE, 所以以下範例無法執行)
```SQL
WITH  sales_sales_summary(brand,category,model_year,sales) AS (
    SELECT
       b.brand_name AS brand,
       c.category_name AS category,
       p.model_year,
       round(
              SUM ( quantity * i.list_price * (1 - discount)),0
	        ) AS sales
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
       p.model_year
	)
SELECT
    brand,
    category,
    SUM (sales) sales
FROM
    sales_sales_summary
GROUP BY
    CUBE(brand, category);
```

以下示例使用兩個 CTE cte_category_counts 和 cte_category_sales 返回每個產品類別的產品數量和銷售額。
```SQL
WITH cte_category_counts (
    category_id, 
    category_name, 
    product_count
)
AS (
    SELECT 
        c.category_id, 
        c.category_name, 
        COUNT(p.product_id)
    FROM 
        production_products p
        INNER JOIN production_categories c 
            ON c.category_id = p.category_id
    GROUP BY 
        c.category_id, 
        c.category_name
),
cte_category_sales(category_id, sales) AS (
    SELECT    
        p.category_id, 
        SUM(i.quantity * i.list_price * (1 - i.discount))
    FROM    
        sales_order_items i
        INNER JOIN production_products p 
            ON p.product_id = i.product_id
        INNER JOIN sales_orders o 
            ON o.order_id = i.order_id
    WHERE order_status = 4 -- completed
    GROUP BY 
        p.category_id
) 
SELECT 
    c.category_id, 
    c.category_name, 
    c.product_count, 
    s.sales
FROM
    cte_category_counts c
    INNER JOIN cte_category_sales s 
        ON s.category_id = c.category_id
ORDER BY 
    c.category_name;
```

### recursive CTE
https://www.sqlservertutorial.net/sql-server-basics/sql-server-recursive-cte/  

遞歸 CTE 是引用自身的 CTE。  CTE 會重複執行、返回數據子集，直到返回完整的結果集。

語法如下
```SQL
WITH expression_name (column_list)
AS
(
    -- 初始查詢
    initial_query  
    UNION ALL
    -- 引用 expression_name 的遞歸查詢。
    recursive_query  
)
SELECT *
FROM   expression_name
```

由最高階管理者（其manager_id為NULL）,查詢其直接及間接管理的所有員工
```SQL
-- WITH RECURSIVE cte_org AS ( -- PostgresSQL
WITH cte_org AS (              --SQLite
    SELECT       
        staff_id, 
        first_name,
        manager_id      
    FROM       
        sales_staffs
    WHERE manager_id IS NULL
    UNION ALL
    SELECT 
        e.staff_id, 
        e.first_name,
        e.manager_id
    FROM 
        sales_staffs e
        INNER JOIN cte_org o 
            ON o.staff_id = e.manager_id
)
SELECT * FROM cte_org;
```

## PIVOT
樞紐分析是分組統計＋行轉列。
原SQL Server中，可使用 PIVOT指令。但SQLite及

### 使用 FILTER() 製作 pivot table
https://antonz.org/sqlite-pivot-table/  
https://modern-sql.com/use-case/pivot  
這種方式適用於SQLite 及 PostgesSQL

```SQL
SELECT 
    category_name, 
    COUNT(product_id) product_count
FROM 
    production_products p
    INNER JOIN production_categories c 
        ON c.category_id = p.category_id
GROUP BY 
    category_name;
```

|  category_name      | product_count  |
|         ----        |       ----:    |
| Children Bicycles   |	      59       |
| Comfort Bicycles    |	      30       |
| Cruisers Bicycles   |	      78       |
| Cyclocross Bicycles |       10       |
| Electric Bikes	  |       24       |
| Mountain Bikes	  |       60       |
| Road Bikes	      |       60       |


```SQL
SELECT 
    COUNT(product_id) filter(where category_name = 'Children Bicycles' ) as "Children Bicycles", 
	COUNT(product_id) filter(where category_name = 'Comfort Bicycles' ) as "Comfort Bicycles",
	COUNT(product_id) filter(where category_name = 'Cruisers Bicycles' ) as "Cruisers Bicycles",
	COUNT(product_id) filter(where category_name = 'Cyclocross Bicycles' ) as "Cyclocross Bicycles",
	COUNT(product_id) filter(where category_name = 'Electric Bikes' ) as "Electric Bikes",
	COUNT(product_id) filter(where category_name = 'Mountain Bikes' ) as "Mountain Bikes",
	COUNT(product_id) filter(where category_name = 'Road Bikes' ) as "Road Bikes"
FROM 
    production_products p
    INNER JOIN production_categories c 
        ON c.category_id = p.category_id;
```

 GROUP BY 替帶為多個 FILTER 達到轉軸效果。但必須手動輸入各篩選條件。
 
 ```SQL
SELECT 
    model_year,
    COUNT(product_id) filter(where category_name = 'Children Bicycles' ) as "Children Bicycles", 
	COUNT(product_id) filter(where category_name = 'Comfort Bicycles' ) as "Comfort Bicycles",
	COUNT(product_id) filter(where category_name = 'Cruisers Bicycles' ) as "Cruisers Bicycles",
	COUNT(product_id) filter(where category_name = 'Cyclocross Bicycles' ) as "Cyclocross Bicycles",
	COUNT(product_id) filter(where category_name = 'Electric Bikes' ) as "Electric Bikes",
	COUNT(product_id) filter(where category_name = 'Mountain Bikes' ) as "Mountain Bikes",
	COUNT(product_id) filter(where category_name = 'Road Bikes' ) as "Road Bikes"
FROM 
    production_products p
    INNER JOIN production_categories c 
        ON c.category_id = p.category_id
GROUP BY model_year;		
```

|Children Bicycles |Comfort Bicycles|Cruisers Bicycles|Cyclocross Bicycles|Electric Bikes|Mountain Bikes|Road Bikes|
|----:|----:|----:|----:|----:|----:|----:|----:|
|59|30|78	|10|24 |60 |60|

### 使用 CASE() 製作 pivot table
這種方式適用於大多數資料庫

GROUP BY 替帶為多個 CASE 達到轉軸效果。但必須手動輸入各篩選條件。

```SQL
SELECT 
    COUNT(CASE WHEN category_name = 'Children Bicycles' THEN product_id END ) AS "Children Bicycles",
	COUNT(CASE WHEN category_name = 'Comfort Bicycles' THEN product_id END ) AS "Comfort Bicycles",
	COUNT(CASE WHEN category_name = 'Cruisers Bicycles' THEN product_id END ) AS "Cruisers Bicycles",
	COUNT(CASE WHEN category_name = 'Cyclocross Bicycles' THEN product_id END ) AS "Cyclocross Bicycles",
	COUNT(CASE WHEN category_name = 'Electric Bikes' THEN product_id END ) AS "Electric Bikes",
	COUNT(CASE WHEN category_name = 'Mountain Bikes' THEN product_id END ) AS "Mountain Bikes",
	COUNT(CASE WHEN category_name = 'Road Bikes' THEN product_id END ) AS "Road Bikes"
FROM (
    SELECT *
    FROM
    production_products p
    INNER JOIN production_categories c 
        ON c.category_id = p.category_id
	) j;
```

分不同年代的車型的數量

```SQL
SELECT 
    model_year,
    COUNT(CASE WHEN category_name = 'Children Bicycles' THEN product_id END ) AS "Children Bicycles",
	COUNT(CASE WHEN category_name = 'Comfort Bicycles' THEN product_id END ) AS "Comfort Bicycles",
	COUNT(CASE WHEN category_name = 'Cruisers Bicycles' THEN product_id END ) AS "Cruisers Bicycles",
	COUNT(CASE WHEN category_name = 'Cyclocross Bicycles' THEN product_id END ) AS "Cyclocross Bicycles",
	COUNT(CASE WHEN category_name = 'Electric Bikes' THEN product_id END ) AS "Electric Bikes",
	COUNT(CASE WHEN category_name = 'Mountain Bikes' THEN product_id END ) AS "Mountain Bikes",
	COUNT(CASE WHEN category_name = 'Road Bikes' THEN product_id END ) AS "Road Bikes"
FROM (
    SELECT *
    FROM
    production_products p
    INNER JOIN production_categories c 
        ON c.category_id = p.category_id
	) j
GROUP BY model_year;	
```

