Step 1: Problem Definition
*Business context
A retail sales company operating in the customer goods industry wanrs to evaluate perfomance within its sales and marketing departments across multiplr regions.
*Data challenge
Customer,product and sales transaction data are stored in separated tables making it difficult to analyze overall sales perfomance(data duplication)
*Expected outcome
Identify top perfoming products per region,Analyze customer purchasing frequency and monitor sales growth trends

Step 2: Success Criteria
1.Identify the top 5 products per region
Use the RANK() window function to rank products based on total sales within each region.
2.Calculate running monthly sales totals
Use SUM() OVER() ordered by month to compute cumulative sales over time Grouped by month,Ordered by month.
3.Measure month-over-month sales growth
Use the LAG() window function to compare current month sales with the previous month to measure the increase or declines in perfomance.
4.Segment customers into quartiles based on spending
Use NTILE(4) to divide customers into four spending groups for targeted marketing analysis.
5.Compute three-month moving average sales
Use AVG() OVER() with a rolling window frame to smooth short-term fluctuations and identify sales trends.

Step 3: Database schema design
I.Relational tables
      
a.Customer table
Customer_id(PK)	Customer_name	region

b.Product Table
Product_id(PK)	Product_name	price

c.Sales Table
Sale_id(PK)	Customer_id(FK)	Product_id(FK)	Sale_date	quantity	Total_amount
Step 4: Part A — SQL JOINs Implementation

1. INNER JOIN
(Retrieve sales that have matching customers and products)
SELECT s.sale_id,c.customer_name,p.product_name,s.total_amount FROM sales s INNER JOIN customers c ON s.customer_id = c.customer_id INNER JOIN products p ON s.product_id = p.product_id;

Business Interpretation:
This query returns only completed sales that are linked to existing customers and products.

2. LEFT JOIN

( Find customers with no sales records)
SELECT c.customer_id,c.customer_name FROM customers c LEFT JOIN sales s ON c.customer_id = s.customer_id WHERE s.sale_id IS NULL;

 Interpretation:
Customers returned by this query have never made a purchase.


3. RIGHT JOIN
(Identify products that have never been sold)
SELECT p.product_id,p.product_name FROM sales s RIGHT JOIN products p ON s.product_id = p.product_id WHERE s.sale_id IS NULL;

 Interpretation:
Products with no associated sales are identified.In other word products which are not sold.

4. FULL OUTER JOIN 
(Compare customers and products including unmatched records)
SELECT c.customer_id,c.customer_name,p.product_id,p.product_name,s.sale_id FROM customers c FULL OUTER JOIN sales s ON c.customer_id = s.customer_id FULL OUTER JOIN products p ON s.product_id = p.product_id;

Interpretation: This query compares customers and products across all sales records,including unmatched entries.

5. SELF JOIN
(Compare customers within the same region or transactions within the same time period)

SELECT s1.sale_id AS sale_1,s2.sale_id AS sale_2,s1.sale_date FROM sales s1 JOIN sales s2 ON s1.sale_date = s2.sale_date AND s1.sale_id < s2.sale_id;  
