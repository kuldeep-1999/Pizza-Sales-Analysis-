# Pizza-Sales-Analysis

## Problem Statment 

The goal of this analysis is to provide insights into pizza sales performance for the client to improve business decisions. Specifically, we aim to identify:
- Peak sales days and times to optimize staffing and stock management.
- The most and least popular pizza categories, sizes and pizza to refine the menu and marketing strategies.
- Monthly sales trends to anticipate seasonality and adjust pricing or promotions.
- Key drivers of revenue to enhance profitability, such as bundling options, customer preferences, and optimal pricing models.
- Opportunities for enhancing customer satisfaction and increasing revenue by understanding order patterns, popular pizza combinations, and customer spending habits.
- This report will enable the client to make informed decisions for improving pizza sales, customer satisfaction, and operational efficiency.

## Features
The dataset contains details about pizza sales, with the following key columns:

- pizza_id: Unique identifier for each pizza.
- order_id: Identifier for each order.
- pizza_name_id: ID of the pizza name.
- quantity: Number of pizzas ordered.
- order_date: Date when the order was placed.
- days_of_week: Day of the week corresponding to the order date.
- months: Month corresponding to the order date.
- order_time: Time of the order.
- unit_price: Price per unit of pizza.
- total_price: Total price of the order.
- pizza_size: Size of the pizza (M for medium, L for large, etc.).
- pizza_category: Type of pizza (e.g., Classic, Supreme, Veggie).
- pizza_ingredients: Ingredients used in the pizza.
- pizza_name: Full name of the pizza.

## Initial Insights:

#### Total revenue :
SELECT 
    ROUND(SUM(total_price), 2) AS Total_Revenue
FROM
    pizza_sales;

#### Total Pizza Sold :
SELECT 
    SUM(quantity) AS Total_Pizza_Sold
FROM
    pizza_sales;
    
#Total Order:
SELECT 
    COUNT(DISTINCT (order_id)) AS Total_Orders
FROM
    pizza_sales;
    
#Average Order value
SELECT 
    ROUND(SUM(total_price) / COUNT(DISTINCT (order_id)),
            2) AS Average_order_Value
FROM
    pizza_sales;
    
# Average Pizza per Order :
SELECT 
    ROUND(SUM(quantity) / COUNT(DISTINCT (order_id)),
            2) AS Average_Pizza_Per_Order
FROM
    pizza_sales;

#Order by Day :
SELECT 
    days_of_week, COUNT(DISTINCT (order_id)) AS Total_Orders
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC;

#Monthl Order:
SELECT 
    months, COUNT(DISTINCT (order_id)) AS Total_Orders
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC;

#Hourly Order:
SELECT 
    HOUR(order_time) AS Hour,
    COUNT(DISTINCT (order_id)) AS Total_Orders
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC;

#percentage of sales by pizza catagory :
SELECT 
    pizza_category AS Category,
    ROUND(SUM(total_price), 2) AS Total_Sales,
    ROUND((SUM(total_price) * 100) / (SELECT 
                    SUM(total_price)
                FROM
                    pizza_sales),
            2) AS Total_sales_in_percentage
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC;

#Percentage of sales by pizza SIZE :
SELECT 
    pizza_size AS Size,
    CAST(SUM(total_price) AS DECIMAL (10 , 2 )) AS Total_Sales,
    CAST((SUM(total_price) * 100) / (SELECT 
                SUM(total_price)
            FROM
                pizza_sales)
        AS DECIMAL (10 , 2 )) AS Total_sales_in_percentage
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC;

#top 5 best sellers wrt Total Revenue :
SELECT 
    pizza_name, SUM(total_price) AS Total_Revenue
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5; 

#top 5 worst sellers wrt Total Revenue :
SELECT 
    pizza_name, SUM(total_price) AS Total_Revenue
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2
LIMIT 5; 

#top 5 best sellers wrt Total Quantity :
SELECT 
    pizza_name, SUM(quantity) AS Total_Quantity
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5; 

#top 5 worst sellers wrt Total Quantity :
SELECT 
    pizza_name, SUM(quantity) AS Total_Quantity
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2
LIMIT 5; 

#top 5 best sellers wrt Total Orders :
SELECT 
    pizza_name, COUNT(DISTINCT (order_id)) AS Total_Orders
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5; 

#top 5 worst sellers wrt Total Orders :
SELECT 
    pizza_name, COUNT(DISTINCT (order_id)) AS Total_Orders
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2
LIMIT 5;

#Pizzas frequently ordered together :
SELECT 
    a.pizza_name AS pizza1,
    b.pizza_name AS pizza2,
    COUNT(*) AS Order_Count
FROM
    pizza_sales a
        JOIN
    pizza_sales b ON a.order_id = b.order_id
        AND a.pizza_name < b.pizza_name
GROUP BY 1 , 2
ORDER BY 3 DESC
LIMIT 5;

#Order Frequency by Day of the Week:
SELECT days_of_week, pizza_name, Order_Count
FROM (
    SELECT days_of_week, pizza_name, COUNT(order_id) AS Order_Count,
           ROW_NUMBER() OVER (PARTITION BY days_of_week ORDER BY COUNT(order_id) DESC) AS rank1
    FROM pizza_sales
    GROUP BY days_of_week, pizza_name
) AS ranked_pizzas
WHERE rank1 = 1
ORDER BY 3;

#Order Patterns by Pizza Category and Size:
SELECT 
    pizza_category, pizza_size, COUNT(order_id) AS Order_Count
FROM
    pizza_sales
GROUP BY 1 , 2
ORDER BY 3 DESC
LIMIT 5;
