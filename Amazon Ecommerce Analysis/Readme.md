# Amazon Ecommerce Analysis
## Description
This project analyzes Amazon ecommerce data using SQL for data cleaning, transformation, and modelling, and Power BI for creating an interactive sales dashboard. The system tracks key metrics such as revenue, order quantity, top-selling products, customer purchases, and seller performance. The final dashboard provides clear insights into sales trends, category contributions, payment behaviors, and state-wise performance to support better business decision-making.

## Data Aggregation
### Create the Database
Query :
````sql
CREATE DATABASE Amazon_Analysis;
USE Amazon_Analysis;
````
### Import Datasets : Customers , Order_items , Orders , Products , Sellers & Analyze the Fact table and Dimension table. 

Query :
````sql
SELECT * FROM Order_items LIMIT 3 ;
````
Result:

<img width="430" height="81" alt="image" src="https://github.com/user-attachments/assets/55ed8c27-11b2-4425-b723-403ae15cbb47" />

Query :
````sql
SELECT * FROM Orders LIMIT 3 ;
````
Result:

<img width="487" height="83" alt="image" src="https://github.com/user-attachments/assets/2b0b47fc-8895-4660-bea6-aedd379054f8" />

Query :
````sql
SELECT * FROM Customers LIMIT 3 ;
````
Result:

<img width="540" height="82" alt="image" src="https://github.com/user-attachments/assets/30843c32-be3f-4dea-b68b-6a05b8513365" />

Query :
````sql
SELECT * FROM Products LIMIT 3 ;
````
Result:

<img width="392" height="84" alt="image" src="https://github.com/user-attachments/assets/c5104e42-d3b0-4079-842f-bda34909db14" />

Query :
````sql
SELECT * FROM Sellers LIMIT 3 ;
````
Result:

<img width="542" height="79" alt="image" src="https://github.com/user-attachments/assets/9c0c9377-9312-4fc7-ad44-99371817615b" />

### DATA CLEANING PROCESS
### 1. Check only primary key, foreign key column null values for all table.

#### ORDER_ITEMS TABLE

Query :
````sql
SELECT SUM(CASE WHEN ORDER_ITEM_ID IS NULL THEN 1 ELSE 0 END) AS NULL_ORDERITEM_ID ,
	   SUM(CASE WHEN ORDER_ID IS NULL THEN 1 ELSE 0 END) AS NULL_ORDER_ID ,
       SUM(CASE WHEN PRODUCT_ID IS NULL THEN 1 ELSE 0 END) AS NULL_PRODUCT_ID ,
       SUM(CASE WHEN SELLER_ID IS NULL THEN 1 ELSE 0 END) AS NULL_SELLER_ID
FROM ORDER_ITEMS;
````
Result:

<img width="472" height="47" alt="image" src="https://github.com/user-attachments/assets/fd2bd3ca-1040-488f-bf1e-f5e2b83a72fc" />

#### ORDERS TABLE

Query :
````sql
SELECT SUM(CASE WHEN ORDER_ID IS NULL THEN 1 ELSE 0 END) AS NULL_ORDER_ID ,
       SUM(CASE WHEN CUSTOMER_ID IS NULL THEN 1 ELSE 0 END) AS NULL_CUSTOMER_ID
FROM ORDERS;
````
Result:

<img width="257" height="50" alt="image" src="https://github.com/user-attachments/assets/eaddce7c-93ed-4f7f-849a-117a67ad23b3" />

#### CUSTOMERS TABLE

Query :
````sql
SELECT SUM(CASE WHEN CUSTOMER_ID IS NULL THEN 1 ELSE 0 END) AS NULL_CUSTOMER_ID 
FROM CUSTOMERS;
````
Result:

<img width="158" height="43" alt="image" src="https://github.com/user-attachments/assets/2effc2a9-860d-4b29-bc47-ca2c67b4ba1d" />

#### PRODUCTS TABLE

Query :
````sql
SELECT SUM(CASE WHEN PRODUCT_ID IS NULL THEN 1 ELSE 0 END) AS NULL_PRODUCT_ID 
FROM PRODUCTS;
````
Result:

<img width="148" height="46" alt="image" src="https://github.com/user-attachments/assets/471fc8c9-db79-4961-a151-42b18025d20c" />

#### SELLERS TABLE

Query :
````sql
SELECT SUM(CASE WHEN SELLER_ID IS NULL THEN 1 ELSE 0 END) AS NULL_SELLER_ID
FROM SELLERS;
````
Result:

<img width="136" height="44" alt="image" src="https://github.com/user-attachments/assets/39ee8022-b5ee-4b22-8106-8ee38e201522" />

### 2. Check Duplicates for main tables.

#### ORDER_ITEMS TABLE

Query :
````sql
SELECT DISTINCT ORDER_ITEM_ID , COUNT(*)
FROM ORDER_ITEMS
GROUP BY ORDER_ITEM_ID  
HAVING COUNT(*) > 1;
````
Result:

<img width="197" height="28" alt="image" src="https://github.com/user-attachments/assets/5d53e14b-d784-4366-a301-45e16ef4c0ac" />

#### ORDERS TABLE

Query :
````sql
SELECT DISTINCT ORDER_ID , COUNT(*)
FROM ORDERS
GROUP BY ORDER_ID  
HAVING COUNT(*) > 1;
````
Result:

<img width="165" height="27" alt="image" src="https://github.com/user-attachments/assets/c67f756b-3003-405d-8bda-adbef711238b" />

#### CUSTOMERS TABLE

Query :
````sql
SELECT DISTINCT CUSTOMER_ID , COUNT(*)
FROM CUSTOMERS
GROUP BY CUSTOMER_ID 
HAVING COUNT(*) > 1;
````
Result:

<img width="188" height="27" alt="image" src="https://github.com/user-attachments/assets/b6847fb2-8241-4b8c-b625-63b5f3a2b97e" />

### 3. Change the Order_Date datatype ( text -> date) in orders table [safely]

Query :
````sql
ALTER TABLE orders ADD COLUMN order_date_new DATE;

UPDATE orders
SET order_date_new = STR_TO_DATE(order_date, '%Y-%m-%d');

ALTER TABLE orders DROP COLUMN order_date;

ALTER TABLE orders
CHANGE order_date_new order_date DATE;

DESCRIBE ORDERS;
````
Result:

<img width="340" height="133" alt="image" src="https://github.com/user-attachments/assets/649eea73-e192-4df4-b95f-3b131d55e393" />

### 4. Check the Validate foreign keys

#### PRODUCT & ORDER_ITEMS

Query :
````sql
SELECT DISTINCT OI.PRODUCT_ID
FROM ORDER_ITEMS AS OI
LEFT JOIN PRODUCTS AS P ON OI.PRODUCT_ID = P.PRODUCT_ID
WHERE P.PRODUCT_ID IS NULL;
````
Result:

<img width="118" height="29" alt="image" src="https://github.com/user-attachments/assets/91889ee3-71dd-4bb2-aaec-c26e37049506" />

#### SELLERS & ORDER_ITEMS

Query :
````sql
SELECT DISTINCT OI.SELLER_ID
FROM ORDER_ITEMS AS OI
LEFT JOIN SELLERS AS S ON OI.SELLER_ID = S.SELLER_ID
WHERE S.SELLER_ID IS NULL;
````
Result:

<img width="103" height="26" alt="image" src="https://github.com/user-attachments/assets/5ca8f088-e1f0-4bba-8a98-26b05ef910c1" />

## ANALYSIS PROCESS

### Total revenue generated by each product category.

Query :
````sql
SELECT P.CATEGORY,
       SUM(OI.QUANTITY * OI.PRICE) AS TOTAL_REVENUE
FROM ORDER_ITEMS AS OI
JOIN PRODUCTS AS P ON OI.PRODUCT_ID = P.PRODUCT_ID
GROUP BY P.CATEGORY
ORDER BY TOTAL_REVENUE DESC;
````
Result:

<img width="241" height="156" alt="image" src="https://github.com/user-attachments/assets/ae9b971e-2bb2-47b2-8467-867c08192311" />

### Top 10 highest selling products based on quantity.

Query :
````sql
SELECT P.CATEGORY,
	   P.PRODUCT_NAME,
       SUM(OI.QUANTITY) AS TOTAL_QTY
FROM ORDER_ITEMS AS OI
JOIN PRODUCTS AS P ON OI.PRODUCT_ID = P.PRODUCT_ID
GROUP BY P.CATEGORY, P.PRODUCT_NAME
ORDER BY TOTAL_QTY DESC
LIMIT 10;
````
Result:

<img width="315" height="197" alt="image" src="https://github.com/user-attachments/assets/97bf0d66-26ca-4538-942a-b334bdc6ca3a" />

### Monthly sales 

Query :
````sql
SELECT DATE_FORMAT(O.ORDER_DATE, '%Y-%m') AS MONTH,
       SUM(O.ORDER_AMOUNT) AS MONTHLY_SALES
FROM ORDERS AS O
WHERE O.ORDER_STATUS = 'DELIVERED'
GROUP BY MONTH
ORDER BY MONTH;
````
Result:

<img width="190" height="298" alt="image" src="https://github.com/user-attachments/assets/8ce97fb3-fba8-4952-9b8c-795a3ec56fd9" />

### Top 10 High Revenue States

Query :
````sql
SELECT C.STATE,
       SUM(O.ORDER_AMOUNT) AS REVENUE
FROM ORDERS AS O
JOIN CUSTOMERS AS C ON O.CUSTOMER_ID = C.CUSTOMER_ID
WHERE O.ORDER_STATUS = 'DELIVERED'
GROUP BY C.STATE
ORDER BY REVENUE DESC
limit 10;
````
Result:

<img width="182" height="198" alt="image" src="https://github.com/user-attachments/assets/cfbbec54-4f35-48b6-a71d-0e0e4d7adf31" />

### TOP 10 Customers Lifetime Purchase

Query :
````sql
SELECT C.CUSTOMER_ID,
       C.CUSTOMER_NAME,
       SUM(O.ORDER_AMOUNT) AS LIFETIME_PURCHASE
FROM CUSTOMERS AS C
JOIN ORDERS AS O ON C.CUSTOMER_ID = O.CUSTOMER_ID
GROUP BY C.CUSTOMER_ID, C.CUSTOMER_NAME
ORDER BY LIFETIME_PURCHASE DESC
limit 10;
````
Result:

<img width="349" height="199" alt="image" src="https://github.com/user-attachments/assets/9d3d5e2b-f67d-4580-8cd4-4289482186ad" />

### Average order amount by payment type

Query :
````sql
SELECT PAYMENT_TYPE,
       ROUND(AVG(ORDER_AMOUNT)) AS AVG_AMOUNT
FROM ORDERS
GROUP BY PAYMENT_TYPE
ORDER BY AVG_AMOUNT DESC;
````
Result:

<img width="212" height="114" alt="image" src="https://github.com/user-attachments/assets/a42850be-1ce7-4cd7-8d6a-c0247661f14a" />

### Total no of customers by state

Query :
````sql
SELECT STATE, COUNT(*) AS TOTAL_CUSTOMERS
FROM CUSTOMERS
GROUP BY STATE
ORDER BY TOTAL_CUSTOMERS DESC;
````
Result:

<img width="240" height="279" alt="image" src="https://github.com/user-attachments/assets/cee028fe-edf4-44e8-8dfb-4f8ae5a2ba92" />

### Seller performance ranking

Query :
````sql
SELECT SELLER_ID,
       SUM(PRICE * QUANTITY) AS REVENUE,
       RANK() OVER (ORDER BY SUM(PRICE * QUANTITY) DESC) AS SELLER_RANK
FROM ORDER_ITEMS
GROUP BY SELLER_ID;
````
Result:

<img width="251" height="213" alt="image" src="https://github.com/user-attachments/assets/e2c00acd-c047-4188-8027-6d5ac764ede7" />

### Seller Revenue with Rating

Query :
````sql
SELECT 
    S.SELLER_ID,
    S.SELLER_NAME,
    S.SELLER_RATING,
    SUM(OI.QUANTITY * OI.PRICE) AS TOTAL_REVENUE
FROM SELLERS S
LEFT JOIN ORDER_ITEMS OI ON S.SELLER_ID = OI.SELLER_ID
GROUP BY S.SELLER_ID, S.SELLER_NAME, S.SELLER_RATING
ORDER BY TOTAL_REVENUE DESC;
````
Result:

<img width="471" height="209" alt="image" src="https://github.com/user-attachments/assets/55c2f892-a7c1-4ff1-9098-dcbd8f1e3ef8" />


### Running Total of Daily Sales

Query :
````sql
SELECT O.ORDER_DATE,
       SUM(O.ORDER_AMOUNT) AS DAILY_SALES,
       SUM(SUM(O.ORDER_AMOUNT)) OVER (ORDER BY O.ORDER_DATE) AS RUNNING_SALES
FROM ORDERS AS O
GROUP BY O.ORDER_DATE;
````
Result:

<img width="298" height="207" alt="image" src="https://github.com/user-attachments/assets/34ea7d8c-e2ab-4561-bb63-344045985ed7" />

### Category wise sales contribution percentage

Query :
````sql
WITH T AS (
  SELECT CATEGORY,
         SUM(OI.QUANTITY * OI.PRICE) AS REVENUE
  FROM PRODUCTS AS P
  JOIN ORDER_ITEMS AS OI ON P.PRODUCT_ID = OI.PRODUCT_ID
  GROUP BY CATEGORY
  ORDER BY REVENUE DESC
)
SELECT CATEGORY,
       REVENUE,
       ROUND(REVENUE * 100 / (SELECT SUM(REVENUE) FROM T), 1) AS CONTRIBUTION_PCT
FROM T;
````
Result:

<img width="323" height="150" alt="image" src="https://github.com/user-attachments/assets/3ddaa1d7-d422-4906-8800-5700bb01c1f3" />

### Stored Procedure -> Get customer order summary

Stored Procedure
````sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `CUSTOMERSUMMARY`(IN CUST_ID VARCHAR(10))
BEGIN
	 SELECT O.ORDER_ID,
           O.ORDER_DATE,
           O.ORDER_AMOUNT,
           O.ORDER_STATUS
    FROM ORDERS O
    WHERE O.CUSTOMER_ID = CUST_ID
    ORDER BY ORDER_DATE DESC;
END
````

Query :
````sql
CALL CUSTOMERSUMMARY('CUST_140');
````
Result:

<img width="386" height="101" alt="image" src="https://github.com/user-attachments/assets/345a8dab-99a6-43a6-a456-aae1dca718d3" />

## Real-Time Insights Through Direct SQL Integration Between MySQL and Power BI

This image shows the Power BI MySQL database connection window, where the server, database name, and SQL query are entered to load data directly from MySQL into Power BI.

<img width="861" height="589" alt="image" src="https://github.com/user-attachments/assets/d242ee97-bcc7-4aa5-9cb2-3a6e026c3c75" />

After importing the SQL query results into Power BI, the data was cleaned, modeled, and prepared for visualization. Various charts, KPIs, and filters were created to highlight key metrics such as revenue, sales trends, customer insights, and seller performance. These visuals were then combined into interactive dashboards that allow users to explore the data and understand business patterns easily. This end-to-end workflow enables clear, dynamic, and data-driven decision-making.

## Dashboard Visualization
### Amazon Ecommerce Sales Analysis

<img width="1173" height="662" alt="Amazon Ecommerce Sales Analysis" src="https://github.com/user-attachments/assets/d5db0d89-b932-4ca1-a96f-7360b723432b" />

### Dashboard Insights








