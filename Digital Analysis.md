# Case Study #6: Clique Bait

I am interested in customer behavior in an online shopping website. Through data analysis we can understand customers buying habits, including product preference, frequency patterns, and background factors influencing their decision to buy something. <br>


The case study and datasets are from [8 Week SQL Challenge](https://8weeksqlchallenge.com/case-study-6/) created by Danny Ma. Thank you for the great case study!

<img width="500" alt="image" src="https://user-images.githubusercontent.com/61902789/132230660-4395d821-90fa-4733-9dba-87c101f77e60.png">


## Introduction

Clique Bait is not like regular online seafood store - the founder and CEO Danny, was also a part of a digital data analytics team and wanted to expand his knowledge into the seafood industry!

In this case study - we are required to support Danny’s vision and analyze his dataset and come up with creative solutions to calculate funnel fallout rates for the Clique Bait online store.

For this case study there is a total of 5 datasets which we will need to combine to solve all of the questions. Take a look at the data [here](https://github.com/ts756632/8_Week_SQL_Challenge/blob/main/Dataset.md)!


## Digital Analysis

Let's answer the following questions with the datasets:

1. How many users are there?
2. How many cookies does each user have on average?
3. What is the unique number of visits by all users per month?
4. What is the number of events for each event type?
5. What is the percentage of visits which have a purchase event?
6. What is the percentage of visits which view the checkout page but do not have a purchase event?
7. What are the top 3 pages by number of views?
8. What is the number of views and cart adds for each product category?
9. What are the top 3 products by purchases?

***

**1. How many users are there?**

 **Thinking Process**

 -  Find out unique user_id. 

````sql
SELECT COUNT(DISTINCT user_id)
  FROM clique_bait.users;
````
 **Why I use these functions?**

 -  We use the DISTINCT function to obtain unique user_id. 
 -  Use the COUNT function to calculate the total number of users from the "users" table. 
 
**Answer:**

<img width="85" alt="image" src="https://user-images.githubusercontent.com/61902789/132160329-ec3c10f3-29b1-4cba-89f9-0f5f8bc496f9.png">

 -  There are 500 unique users.

***

**2. How many cookies does each user have on average?** 
 
 **Thinking Process**

 -  Group data by user_id
 -  Count the number of cookies each user has. 
 -  Calculate the average number for all users.
 
````sql
SELECT ROUND(AVG(count_cookies),3) AS average_cookies
FROM
(SELECT user_id, COUNT(cookie_id) AS count_cookies
  FROM clique_bait.users 
  GROUP BY user_id
  )sub;
````
 **Why I use these functions?**

 -  Use the COUNT and GROUP BY functions to find out how many cookies each user has (inner query). 
 -  Use the AVG function to calculate average cookies each user has (outer query).
 -  Use the ROUND function to round the number to 3 decimal places.
 
**Answer:**

<img width="150" alt="image" src="https://user-images.githubusercontent.com/61902789/132160461-32b740ba-f1de-443f-b6b3-906bd7ad9b3f.png">

 - Each user has 3.564 cookies on average.

***

**3. What is the unique number of visits by all users per month?**

 **Thinking Process**

 -  Group data by month. 
 -  Calculate the unique number of visits for each month.
 
````sql
  SELECT To_CHAR(event_time,'YYYY-MM') AS month, 
  	 COUNT(DISTINCT visit_id) AS unique_visits
    FROM clique_bait.events 
   GROUP BY month
   ORDER BY month;
````
 **Why I use these functions?**

 -  Use the "events" table because we want to know the number of visits. 
 -  Use the To_CHAR function to transform date type into 'YYYY-MM' so we can group data by month. 
 -  Use the COUNT and GROUP BY functions to calculate the unique number of visits by all users per month.
 -  Use the ORDER BY function to have a clear look with the order of month.
 
**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132221884-325639aa-93a0-49b2-a04c-4ebd9ff32d97.png)


***


**4. What is the number of events for each event type?**

 **Thinking Process**

 -  Group data by event type.
 -  Count the numbers of events for each type. 
 
````sql
SELECT a.event_type, 
       b.event_name, 
       COUNT(a.event_type) AS count_event
  FROM clique_bait.events a 
  JOIN clique_bait.event_identifier b
    ON a.event_type = b.event_type
  GROUP BY a.event_type, b.event_name
  ORDER BY a.event_type;
````
 **Why I use these functions?**

 -  Use the COUNT and GROUP BY function to calculate the number of events for each event type from "events" table. 
 -  Use the JOIN function to combine "events" and "event_identifier" tables so we can get the event_name for each event_type.  
 -  ORDER BY event_type to have a clear view. 
 
**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132167785-9147d3af-faa9-44fe-8744-efefd2f9bcf7.png)

***

**5. What is the percentage of visits which have a purchase event?**

 **Thinking Process**
  
 - Count the totle number of purchase events. 
 - Count the unique number of visits.
 - Calculate the percentage of visits which have a purchase event.
 
````sql
WITH CTE_new AS(
	SELECT visit_id, 
	       MAX(CASE WHEN event_type = 3 THEN 1 ELSE 0 END) AS purchase
	  FROM clique_bait.events 
	 GROUP BY visit_id
	 )
 
 SELECT COUNT(*) AS unique_number_of_visits,
        SUM(purchase) AS number_of_purchase, 
        ROUND(CAST(CAST(SUM(purchase)AS float) / CAST(COUNT(*)AS float) *100 AS numeric) , 2) AS percentage
   FROM CTE_new;

````
 **Why I use these functions?**
 
 -  Create a CTE using WITH function to store a temporary result. 
 -  Use the CASE WHEN statement to determine whether a visit has a purchase or not. 
    If a single visit has a purchase event (event_type = 3), then mark "1," otherwise, "0." 
 -  Use the MAX function and GROUP BY visit_id to mark the visits which have a purchase event. 
    Each visit should have at most a purchase event. 
 -  Calculate the percentage of visits which have a purchase event by SUM(purchase)/COUNT(*). 
 -  Use the CAST function to transform SUM(purchase) and COUNT(*) into float type to avoid division returning zero. 
 -  Use the CAST function to transform the percentage into numeric type so the ROUND function works.
 
**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132284520-46f1e7ef-e343-4b93-8d18-910be7222af9.png)

 - 49.86% of visits which have a purchase event.
***

**6. What is the percentage of visits which view the checkout page but do not have a purchase event?**

 **Thinking Process**
  
 - Count the totle number of purchase events. 
 - Count the totle number of checkout page views.
 - Calculate the percentage of visits which view the checkout page but `do not` have a purchase event.
 
````sql
WITH CTE_new AS(
	SELECT visit_id, 
	       MAX(CASE WHEN page_id = 12 AND event_type = 1 THEN 1 ELSE 0 END) AS checkout,
	       MAX(CASE WHEN event_type = 3 THEN 1 ELSE 0 END) AS purchase  
	  FROM clique_bait.events 
	 GROUP BY visit_id
	 )
 
 SELECT SUM(checkout) AS number_of_checkout,
        SUM(purchase) AS number_of_purchase,
        ROUND(CAST( (1-CAST(SUM(purchase)AS float) / CAST(SUM(checkout) AS float)) *100 AS numeric) , 2) AS percentage
   FROM CTE_new;
````
**Why I use these functions?**

 -  Customers who have a puchase event must have viewed the checkout page.  
 -  Use the MAX function and CASE WHEN statement to determine whether a visit views the checkout page. 
    If a single visit views the checkout page (page_id = 12 AND event_type = 1), then mark "1," otherwise, "0."  
 -  Use the MAX function and CASE WHEN statement to determine whether a visit has a purchase or not. 
    If a single visit has a purchase event (event_type = 3), then mark "1," otherwise, "0." 
 -  Calculate the percentage of visits which view the checkout page but do not have a purchase event by 1- SUM(purchase)/SUM(checkout). 
 -  Use the CAST function to transform SUM(purchase) and COUNT(*) into float type to avoid division returning zero. 
 -  Use the CAST function to transform the percentage into numeric type so the ROUND function works.
 
**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132319139-e9413f3b-e418-41b4-9d08-5df22e492c3e.png)

 - 15.5 % of visits which view the checkout page but do not have a purchase event.
***

**7. What are the top 3 pages by number of views?**

 -  Use the JOIN function to combine "events" and "page_hierarchy" tables so we can get the page_name for each page_id. 
 -  With the conditional statement AND, we can filter data before the join occurs. <br> 
    We are interested in the data with page view event (vent_type = 1) only.  
 -  Use the COUNT and GROUP BY function to calculate the number of page view events for each page. 
 -  ORDER BY number_of_views DESC and LIMIT 3 to get the top 3 pages by number of views. 

````sql
SELECT a.page_id, 
       b.page_name, 
       COUNT(a.page_id) AS number_of_views
  FROM clique_bait.events a 
  JOIN clique_bait.page_hierarchy b
    ON a.page_id = b.page_id
   AND a.event_type = 1 -- a visit with a page view event
 GROUP BY a.page_id, b.page_name
 ORDER BY number_of_views DESC
 LIMIT 3
````

**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132328671-d5ddef95-d1f0-4610-ad31-860134f083ab.png)

 - All Products, Checkout and Home Page.
***

**8. What is the number of views and cart adds for each product category?**

 -  Use WHERE clause to filter data with Page View and Add to Cart event (event_type IN (1,2)) as a subquery
 -  Use the JOIN function to combine "events" and "page_hierarchy" tables and find out what do customers do on each product category. 
 -  With the conditional statement AND, we can filter data before the join occurs. <br> 
    We are interested in the data which product_category IS NOT NULL.  
 -  Use the SUM function and CASE WHEN statement to calculate the number of views and cart adds for each product category (GROUP BY product_category). 
 
````sql
SELECT c.product_category,
       SUM(CASE WHEN sub.event_type = 1 THEN 1 ELSE 0 END) AS page_views,
       SUM(CASE WHEN sub.event_type = 2 THEN 1 ELSE 0 END) AS car_adds
  FROM clique_bait.page_hierarchy c
  JOIN
    (SELECT page_id, 
            event_type     
       FROM clique_bait.events 
      WHERE event_type IN (1,2)
       ) sub 
    ON c.page_id = sub.page_id 
   AND c.product_category IS NOT NULL 
GROUP BY c.product_category;
````

**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132333338-8a794c5b-b43b-4c44-b45e-fc785bfb1699.png)

***

**9. What are the top 3 products by purchases?**

 **Thinking Process**

 -  Filter visit_id to find out customers who have purchase events.
 -  Based on the visit_id which has purchase event, list all products that are added to cart.
 -  Calculate numbers of purchase for each product.
 
````sql
WITH CTE_new AS (
  SELECT a.visit_id, 
  	 a.page_id
  FROM clique_bait.events a
  JOIN 
    (SELECT visit_id
       FROM clique_bait.events
      WHERE event_type = 3) sub  -- filter visit_id to find out customers who have purchase events
   ON a.visit_id = sub.visit_id 
  WHERE event_type = 2  -- list all products that are added to cart
  ORDER BY visit_id, page_id
  )
  
 SELECT c.product_id, 
 	c.page_name AS product_name,  
        COUNT(c.product_id) AS number_of_purchase
   FROM CTE_new b
   JOIN clique_bait.page_hierarchy c
     ON b.page_id = c.page_id
  GROUP BY c.product_id, c.page_name 
  ORDER BY number_of_purchase DESC
  LIMIT 3
````
 **Why I use these functions?**
 
 -  Find out visit_id with Purchase event (event_type = 3) using WHERE clause to filter data(inner query winthin CTE).
 -  SELF JOIN "events" table to get the data with Add to Cart event (WHERE event_type = 2) to find out what products do people purchase for each visit. 
 -  Create a CTE to store the temporary data.
 -  Combine "CTE_new" and "page_hierarchy" tables to find out product_name (i.e.page_name).
 -  COUNT purchases for each product.
 -  List top 3 products by purchase.
 
**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132368635-629a6a64-aa75-479a-b237-3699ddae78be.png)


***
