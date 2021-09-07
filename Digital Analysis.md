# Case Study #6: Clique Bait

I am interested in the customer behavior in an online shopping website. Through data analysis we can understand customers buying habits, including product preference, frequency patterns, and background factors influencing their decision to buy something. <br>

The case study and datasets are from [8 Week SQL Challenge](https://8weeksqlchallenge.com/case-study-6/) created by Danny Ma. Thank you for the great case study!

<img width="500" alt="image" src="https://user-images.githubusercontent.com/61902789/132230660-4395d821-90fa-4733-9dba-87c101f77e60.png">



## Introduction

Clique Bait is not like regular online seafood store - the founder and CEO Danny, was also a part of a digital data analytics team and wanted to expand his knowledge into the seafood industry!

In this case study - we are required to support Danny’s vision and analyze his dataset and come up with creative solutions to calculate funnel fallout rates for the Clique Bait online store.

For this case study there is a total of 5 datasets which you will need to combine to solve all of the questions. Take a look at the data [here](https://github.com/ts756632/8_Week_SQL_Challenge/blob/main/Dataset.md)!


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

 -  We use the DISTINCT function to obtain unique user_id. <br>
 -  Use the COUNT function to calculate the total number of users from the "users" table. <br>

````sql
SELECT COUNT(DISTINCT user_id)
  FROM clique_bait.users;
````

**Answer:**

<img width="85" alt="image" src="https://user-images.githubusercontent.com/61902789/132160329-ec3c10f3-29b1-4cba-89f9-0f5f8bc496f9.png">

 -  There are 500 unique users.

***

**2. How many cookies does each user have on average?** 

 -  Use the COUNT and GROUP BY function to find out how many cookies each user has (inner query). <br>
 -  Use the AVG function to calculate average cookies each user has(outer query).<br>
 -  Use the ROUND function to round the number to 3 decimal places.<br>
 
````sql
SELECT ROUND(AVG(count_cookies),3) AS average_cookies
FROM
(SELECT user_id, COUNT(cookie_id) AS count_cookies
  FROM clique_bait.users 
  GROUP BY user_id
  )sub;
````

**Answer:**

<img width="150" alt="image" src="https://user-images.githubusercontent.com/61902789/132160461-32b740ba-f1de-443f-b6b3-906bd7ad9b3f.png">

 - Each user has 3.564 cookies on average.

***

**3. What is the unique number of visits by all users per month?**

 -  Use the "events" table because we want to know the number of visits. <br>
 -  Use the To_CHAR function to transform date type into 'YYYY-MM' so we can group data by month. <br>
 -  Use the COUNT and GROUP BY function to calculate unique number of visits by all users per month.<br>
 -  Use the ORDER BY function to have a clear view with the order of month.<br>
 
````sql
  SELECT To_CHAR(event_time,'YYYY-MM') AS month, 
  	 COUNT(DISTINCT visit_id) AS unique_visits
    FROM clique_bait.events 
   GROUP BY month
   ORDER BY month;
````

**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132221884-325639aa-93a0-49b2-a04c-4ebd9ff32d97.png)


***


**4. What is the number of events for each event type?**

 -  Use the COUNT and GROUP BY function to calculate the number of events for each event type from "events" table. <br>
 -  Use the JOIN function to combine "events" and "event_identifier" tables so we can get the event_name for each event_type.  <br>
 -  ORDER BY event_type to have a clear view. <br>
 
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

**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132167785-9147d3af-faa9-44fe-8744-efefd2f9bcf7.png)

***

**5. What is the percentage of visits which have a purchase event?**

 -  Create a CTE using WITH function to store a temporary result. <br>
 -  Use the CASE WHEN statement to determine whether a visit have a purchase or not. <br>
    If a user has a purchase event (event_type = 3), then mark "1," otherwise, "0." <br> 
 -  Use the MAX function and GROUP BY visit_id to mark the visits which have a purchase event. <br>
    Each visit should have at most a purchase event. <br>
 -  Calculate the percentage of visits which have a purchase event by SUM(purchase)/COUNT(*). <br>
 -  Use the CAST function to transform SUM(purchase) and COUNT(*) into float type to avoid division returning zero. <br>
 -  Use the CAST function to transform the percentage into numeric type so the ROUND function works.<br>
 
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

**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132284520-46f1e7ef-e343-4b93-8d18-910be7222af9.png)

 - 49.86% of visits which have a purchase event.
***

**6. What is the percentage of visits which view the checkout page but do not have a purchase event?**

````sql
WITH CTE_new_event AS (
SELECT a.event_type, 
       b.event_name, 
       COUNT(a.event_type) AS count_event
  FROM clique_bait.events a 
  JOIN clique_bait.event_identifier b
    ON a.event_type = b.event_type 
 GROUP BY a.event_type, b.event_name
 ORDER BY a.event_type
  )
  
 SELECT  
   (1-SUM(CASE WHEN event_name = 'Purchase' THEN count_event ELSE 0 END) /         
    SUM(CASE WHEN event_name = 'Add to Cart' THEN count_event ELSE 0 END)) *100  AS percentage      
   FROM CTE_new_event;
````

**Answer:**

<img width="200" alt="image" src="https://user-images.githubusercontent.com/61902789/132182065-f2cba7a1-8397-4b82-a982-934a492c09e6.png">

 - 78.97 % of visits which view the checkout page but do not have a purchase event.
***

**7. What are the top 3 pages by number of views?**

````sql
SELECT a.page_id, 
       b.page_name, 
       COUNT(a.page_id) AS number_of_views
  FROM clique_bait.events a 
  JOIN clique_bait.page_hierarchy b
    ON a.page_id = b.page_id
 GROUP BY a.page_id, b.page_name
 ORDER BY number_of_views DESC
 LIMIT 3;
````

**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132184328-051de857-2026-437f-991b-e4ca37830575.png)

 - All Products, Lobster and Crab.
***

**8. What is the number of views and cart adds for each product category?**

````sql
SELECT c.product_category,
       SUM(CASE WHEN sub.event_type = 1 THEN 1 ELSE 0 
       END) AS page_views,
       SUM(CASE WHEN sub.event_type = 2 THEN 1 ELSE 0
       END) AS car_adds
FROM clique_bait.page_hierarchy c
JOIN
    (SELECT a.page_id, 
         b.event_type, 
           b.event_name  
      FROM clique_bait.events a 
      JOIN clique_bait.event_identifier b
        ON a.event_type = b.event_type
       AND a.event_type IN (1,2)
       ) sub 
     ON  c.page_id = sub.page_id  
GROUP BY c.product_category;
````

**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132196846-a23c580b-0a0f-4c87-ad00-08e08280d83f.png)

***

**Answer:**

<img width="200" alt="image" src="https://user-images.githubusercontent.com/61902789/132182065-f2cba7a1-8397-4b82-a982-934a492c09e6.png">

 - 78.97 % of visits which view the checkout page but do not have a purchase event.
***

**9. What are the top 3 products by purchases?**

````sql
SELECT DISTINCT product_id, sub.page_id
FROM clique_bait.page_hierarchy c
JOIN
(SELECT a.page_id, 
	   b.event_type, 
       b.event_name  
  FROM clique_bait.events a 
  JOIN clique_bait.event_identifier b
    ON a.event_type = b.event_type
   AND a.event_type = 3 
   ) sub 
 ON  c.page_id = sub.page_id
````

**Answer:**


***
