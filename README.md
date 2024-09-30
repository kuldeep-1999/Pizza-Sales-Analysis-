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
    
#### Total Order:
SELECT 
    COUNT(DISTINCT (order_id)) AS Total_Orders
FROM
    pizza_sales;
    
#### Average Order value
SELECT 
    ROUND(SUM(total_price) / COUNT(DISTINCT (order_id)),
            2) AS Average_order_Value
FROM
    pizza_sales;
    
#### Average Pizza per Order :
SELECT 
    ROUND(SUM(quantity) / COUNT(DISTINCT (order_id)),
            2) AS Average_Pizza_Per_Order
FROM
    pizza_sales;

#### Order by Day :
SELECT 
    days_of_week, COUNT(DISTINCT (order_id)) AS Total_Orders
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC;

#### Monthly Order:
SELECT 
    months, COUNT(DISTINCT (order_id)) AS Total_Orders
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC;

#### Hourly Order:
SELECT 
    HOUR(order_time) AS Hour,
    COUNT(DISTINCT (order_id)) AS Total_Orders
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC;

#### Percentage of sales by pizza catagory :
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

#### Percentage of sales by pizza size :
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

#### Top 5 best sellers wrt Total Revenue :
SELECT 
    pizza_name, SUM(total_price) AS Total_Revenue
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5; 

#### Top 5 worst sellers wrt Total Revenue :
SELECT 
    pizza_name, SUM(total_price) AS Total_Revenue
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2
LIMIT 5; 

#### Top 5 best sellers wrt Total Quantity :
SELECT 
    pizza_name, SUM(quantity) AS Total_Quantity
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5; 

#### Top 5 worst sellers wrt Total Quantity :
SELECT 
    pizza_name, SUM(quantity) AS Total_Quantity
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2
LIMIT 5; 

#### Top 5 best sellers wrt Total Orders :
SELECT 
    pizza_name, COUNT(DISTINCT (order_id)) AS Total_Orders
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5; 

#### Top 5 worst sellers wrt Total Orders :
SELECT 
    pizza_name, COUNT(DISTINCT (order_id)) AS Total_Orders
FROM
    pizza_sales
GROUP BY 1
ORDER BY 2
LIMIT 5;

#### Pizzas frequently ordered together :
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

#### Order Frequency by Day of the Week:
SELECT days_of_week, pizza_name, Order_Count
FROM (
    SELECT days_of_week, pizza_name, COUNT(order_id) AS Order_Count,
           ROW_NUMBER() OVER (PARTITION BY days_of_week ORDER BY COUNT(order_id) DESC) AS rank1
    FROM pizza_sales
    GROUP BY days_of_week, pizza_name
) AS ranked_pizzas
WHERE rank1 = 1
ORDER BY 3;

#### Order Patterns by Pizza Category and Size:
SELECT 
    pizza_category, pizza_size, COUNT(order_id) AS Order_Count
FROM
    pizza_sales
GROUP BY 1 , 2
ORDER BY 3 DESC
LIMIT 5;

## Conclusion 
The analysis of the pizza sales data has provided several valuable insights that can be used to improve business strategy, operations, and customer engagement:

#### Peak Sales Days: 
- **Friday** consistently generates the highest sales, indicating a strong demand for pizzas at the end of the workweek. This suggests an opportunity to further optimize promotions or discounts on Fridays to boost sales even more.

#### Top-Selling Pizza Categories:
- The **Classic** category is the most popular, followed closely by Supreme and Chicken pizzas. Menu offerings could be enhanced by promoting these best-selling categories while exploring ways to boost the less popular ones like Veggie pizzas.

#### Popular Pizza Sizes:
- **Large** pizzas are overwhelmingly the most popular, contributing the most to revenue. Marketing efforts could focus on promoting large pizzas through deals or bundles to encourage customers to order larger sizes. On the flip side, there is an opportunity to improve sales of smaller or extra-large sizes.

#### Monthly Trends:
- **July** is the best-performing month, with high pizza sales. This indicates seasonality in sales, which could be leveraged by launching seasonal offers or special promotions to take advantage of peak demand months. Analyzing why July outperforms could offer insights for replicating this success in other months.

#### Customer Preferences and Pizzas Frequently Ordered Together:
- Certain pizzas, such as The Classic Deluxe, Barbecue Chicken, and Hawaiian, are consistently top-sellers across all days. These pizzas could be promoted in bundles or meal deals, capitalizing on their popularity.
- Through combination analysis, specific pizzas that are frequently ordered together can be marketed as part of combo deals to increase the average order value.

#### Day-Specific Customer Preferences:
- Each day of the week shows varying preferences for pizzas. For instance, The Pepperoni Pizza might be the top seller on Fridays, and similar trends may be observed for other days. Understanding these preferences allows for tailored promotions (e.g., "Pizza of the Day" deals) that align with customer demand.

### Actionable Recommendations:
- **Promotions and Bundling:** Offer bundle deals or family-size packages for large pizzas, as they are the most popular and contribute the most to revenue. Additionally, combo deals with frequently ordered pizza pairs could boost sales.
- **Day-Specific Offers:** Tailor promotions based on the top pizza for each day of the week to drive sales and customer satisfaction.
- **Menu Optimization:** Focus on promoting the best-performing pizza categories, while considering revamping or better marketing strategies for less popular categories to maintain balance.
- **Seasonal Promotions:** Implement special deals or limited-time offers in peak months like July to sustain momentum and increase customer retention.

This data-driven approach will allow the business to better align its offerings with customer preferences, optimize promotions, and ultimately drive revenue growth.
