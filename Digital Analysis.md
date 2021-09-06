# Online Store: Clique Bait

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


**1. How many users are there?**

````sql
SELECT COUNT(DISTINCT user_id)
  FROM clique_bait.users LIMIT 10;
````

**Answer:**

<img width="85" alt="image" src="https://user-images.githubusercontent.com/61902789/132160329-ec3c10f3-29b1-4cba-89f9-0f5f8bc496f9.png">

 -  There are 500 unique users.

***

**2. How many cookies does each user have on average?**

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

````sql
  SELECT To_CHAR(start_date,'YYYY-MM') AS month, 
  		   COUNT(cookie_id) 
    FROM clique_bait.users 
   GROUP BY month
   ORDER BY month;
````

**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132160147-3281d7b9-8a2d-472c-ac4f-fbc73457870c.png)

***


**4. What is the number of events for each event type?**

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
    SUM(CASE WHEN event_name = 'Purchase' THEN count_event 
        ELSE 0 END) / 
    SUM(CASE WHEN event_name = 'Page View' THEN count_event 
        ELSE 0 END) *100  AS percentage
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
   (1-SUM(CASE WHEN event_name = 'Purchase' THEN count_event 
        ELSE 0 END) / 
    SUM(CASE WHEN event_name = 'Add to Cart' THEN count_event 
        ELSE 0 END)) *100  AS percentage
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

 - All Products, Lobster and Crab .
***
