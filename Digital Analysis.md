# Case Study #6: Clique Bait

The case study and datasets are from [8 Week SQL Challenge](https://8weeksqlchallenge.com/case-study-6/) written by Danny Ma.

<img width="500" alt="image" src="https://user-images.githubusercontent.com/61902789/132230660-4395d821-90fa-4733-9dba-87c101f77e60.png">

## Introduction

Clique Bait is not like your regular online seafood store - the founder and CEO Danny, was also a part of a digital data analytics team and wanted to expand his knowledge into the seafood industry!

In this case study - you are required to support Danny’s vision and analyse his dataset and come up with creative solutions to calculate funnel fallout rates for the Clique Bait online store.

## Available Data
For this case study there is a total of 5 datasets which you will need to combine to solve all of the questions.<br>

Data at a glance:<br>

**1. Users:** Customers who visit the Clique Bait website are tagged via their cookie_id.

<img width="350" alt="image" src="https://user-images.githubusercontent.com/61902789/132231369-646cd31a-c71a-4fb0-ac51-055d9ce4dfdd.png">

**2. Events:** Customer visits are logged in this events table at a cookie_id level and the event_type and page_id values can be used to join onto relevant satellite tables to obtain further information about each event.

The sequence_number is used to order the events within each visit.

<img width="750" alt="image" src="https://user-images.githubusercontent.com/61902789/132232941-c08fac92-b725-4897-9377-9c0bfc7fd810.png">

**3. Event Identifier:** The event_identifier table shows the types of events.

<img width="250" alt="image" src="https://user-images.githubusercontent.com/61902789/132233445-c81d7b24-488b-4edb-83fe-2c8c9a585dde.png">


**4. Campaign Identifier:** This table shows information for the 3 campaigns that Clique Bait has ran on their website so far in 2020.

<img width="900" alt="image" src="https://user-images.githubusercontent.com/61902789/132233960-fc234a50-c264-45e9-85f0-46a1b625ce5e.png">

**5. Page Hierarchy:** This table lists all of the pages on the Clique Bait website which are tagged and have data passing through from user interaction events.

<img width="400" alt="image" src="https://user-images.githubusercontent.com/61902789/132234402-36efbe5f-b478-4435-94dd-a5324e94a0bf.png">
![image](https://user-images.githubusercontent.com/61902789/132234402-36efbe5f-b478-4435-94dd-a5324e94a0bf.png)

## Digital Analysis

Using the available datasets - answer the following questions using a single query for each one:

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

 Step 1: we use the DISTINCT function to obtain unique user_id. <br>
 Step 2: use the COUNT function to calculate the total number of users from the "users" table. <br>

````sql
SELECT COUNT(DISTINCT user_id)
  FROM clique_bait.users;
````

**Answer:**

<img width="85" alt="image" src="https://user-images.githubusercontent.com/61902789/132160329-ec3c10f3-29b1-4cba-89f9-0f5f8bc496f9.png">

 -  There are 500 unique users.

***

**2. How many cookies does each user have on average?** 

 Step 1: use the COUNT and GROUP BY function to find out how many cookies each user has (inner query). <br>
 Step 2: use the AGV function to calculate average cookies each user has(outer query).<br>
 Step 3: use the ROUND function to round the number to 3 decimal places.<br>
 
````sql
SELECT ROUND(AVG(count_cookies),3) AS average_cookies
FROM
(SELECT user_id, COUNT(cookie_id) AS count_cookies
  FROM clique_bait.users 
  GROUP BY user_id
  )sub;
````

**Answer:**

<img width="130" alt="image" src="https://user-images.githubusercontent.com/61902789/132160461-32b740ba-f1de-443f-b6b3-906bd7ad9b3f.png">

 - Each user has 3.564 cookies on average.

***

**3. What is the unique number of visits by all users per month?**

 Step 1: use the "events" table because we want to know the number of visits. <br>
 Step 2: use the To_CHAR function to transform date type into 'YYYY-MM' so we can group data by month. <br>
 Step 3: use the COUNT and GROUP BY function to calculate unique number of visits by all users per month.<br>
 Step 4: use the ORDER BY function to have a clear view with the order of month.<br>
 
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

 Step 1: use the COUNT and GROUP BY function to calculate the number of events for each event type from "events" table. <br>
 Step 2: use the JOIN function to combine "events" and "event_identifier" tables so we can get the event_name for each event_type.  <br>
 Step 3: ORDER BY event_type to have a clear view. <br>
 
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
    SUM(CASE WHEN event_name = 'Purchase' THEN count_event ELSE 0 END) /         
    SUM(CASE WHEN event_name = 'Page View' THEN count_event ELSE 0 END) *100  AS percentage        
   FROM CTE_new_event;
````

**Answer:**

<img width="200" alt="image" src="https://user-images.githubusercontent.com/61902789/132178624-3f28090a-2609-4d28-8aae-13d0bd43d8a7.png">

 - 8.49% of visits which have a purchase event.
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
