 Subject: INSY 8311 SQL 
 Assignment I 
 Teta_Nzaramba_Cynthia 
 Group A


Step 1: Problem Definition

*Business context;
A retail sales company operating in the customer goods industry wanrs to evaluate perfomance within its sales and marketing departments across multiple regions.

*Data challenge;
Customer,product and sales transaction data are stored in separated tables making it difficult to analyze overall sales perfomance(data duplication)

*Expected outcome;
Identify top perfoming products per region,Analyze customer purchasing frequency and monitor sales growth trends

Step 2: Success Criteria
a.Identify the top 5 products per region;
Use the RANK() window function to rank products based on total sales within each region.
b.Calculate running monthly sales totals;
Use SUM() OVER() ordered by month to compute cumulative sales over time Grouped by month,Ordered by month.
c.Measure month-over-month sales growth;
Use the LAG() window function to compare current month sales with the previous month to measure the increase or declines in perfomance.
d.Segment customers into quartiles based on spending;
Use NTILE(4) to divide customers into four spending groups for targeted marketing analysis.
e.Compute three-month moving average sales;
Use AVG() OVER() with a rolling window frame to smooth short-term fluctuations and identify sales trends.

Step 3: Database schema design
I.Relational tables
      
a.Customer table;
Customer_id(PK)	Customer_name	region

b.Product Table;
Product_id(PK)	Product_name	price

c.Sales Table;
Sale_id(PK)	Customer_id(FK)	Product_id(FK)	Sale_date	quantity	Total_amount

![ER Diagram](/Screenshot/Picture1.png)
![ER Diagram](/Screenshot/Picture2.png)
![ER Diagram](/Screenshot/Picture3.png)

II.ERD DIAGRAM;
![ER Diagram](/Screenshot/ER.drawio.png)

Step 4: Part A — SQL JOINs Implementation

1. INNER JOIN
(Retrieve sales that have matching customers and products)
SELECT s.sale_id,c.customer_name,p.product_name,s.total_amount FROM sales s INNER JOIN customer c ON s.customer_id = c.customer_id INNER JOIN product p ON s.product_id = p.product_id;

![ER Diagram](/Screenshot/Picture4.png)

Interpretation:
This query returns only completed sales that are linked to existing customers and products.

2. LEFT JOIN

( Find customers with no sales records)
SELECT c.customer_id,c.customer_name FROM customer c LEFT JOIN sales s ON c.customer_id = s.customer_id WHERE s.sale_id IS NULL;

![ER Diagram](/Screenshot/Picture5.png)

Interpretation:
Customers returned by this query have never made a purchase.


3. RIGHT JOIN
(Identify products that have never been sold)
SELECT p.product_id,p.product_name FROM sales s RIGHT JOIN product p ON s.product_id = p.product_id WHERE s.sale_id IS NULL;

![ER Diagram](/Screenshot/Picture6.png)

Interpretation:
Products with no associated sales are identified.In other word products which are not sold.

4. FULL OUTER JOIN 
(Compare customers and products including unmatched records)
SELECT c.customer_id,c.customer_name,p.product_id,p.product_name,s.sale_id FROM customer c FULL OUTER JOIN sales s ON c.customer_id = s.customer_id FULL OUTER JOIN product p ON s.product_id = p.product_id;

![ER Diagram](/Screenshot/Picture7.png)

Interpretation: This query compares customers and products across all sales records,including unmatched entries.

5. SELF JOIN
(Compare customers within the same region or transactions within the same time period)

SELECT s1.sale_id AS sale_1,s2.sale_id AS sale_2,s1.sale_date FROM sales s1 JOIN sales s2 ON s1.sale_date = s2.sale_date AND s1.sale_id < s2.sale_id; 

![ER Diagram](/Screenshot/Picture8.png)

Interpretation: This query shows all sales happening on the same day.

Step 5: Part B — Window Functions Implementation

1. Ranking Functions

Top N customers by revenue

SELECT customer_id,
       SUM(total_amount) AS revenue,
       RANK() OVER (ORDER BY SUM(total_amount) DESC) AS revenue_rank
FROM sales
GROUP BY customer_id;

![ER Diagram](/Screenshot/picture9.png)

Interpretation:
Ranks customers based on total revenue contribution, helping identify high-value customers.

2. Aggregate Window Functions

Running monthly sales total
SELECT sale_date,total_amount,SUM(total_amount) OVER (ORDER BY sale_date) AS running_total FROM sales;

![ER Diagram](/Screenshot/Picture10.png)

Interpretation:
Shows cumulative sales growth over time, supporting trend analysis.

3. Navigation Functions

Month-over-month growth

SELECT sale_date,
       SUM(total_amount) AS monthly_sales,
       LAG(SUM(total_amount)) OVER (ORDER BY sale_date) AS previous_month
FROM sales
GROUP BY sale_date;

![ER Diagram](/Screenshot/Picture11.png)

Interpretation:
Compares current sales with the previous period to measure growth or decline.

4. Distribution Functions

Customer quartile segmentation

SELECT customer_id,
       SUM(total_amount) AS total_spent,
       NTILE(4) OVER (ORDER BY SUM(total_amount) DESC) AS spending_quartile
FROM sales
GROUP BY customer_id;

![ER Diagram](/Screenshot/Picture12.png)

Interpretation:
Segments customers into four groups, enabling targeted marketing strategies.


REFERENCES;
source:Notes of DBMS from TEMITOPE OGUNTADE
ERD:DRAWIO
DBMS TOOL:Postgresql for running queries evidences are screenshots in this document
