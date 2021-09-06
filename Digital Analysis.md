# Online Store: Clique Bait

## Digital Analysis

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
SELECT event_type, COUNT(event_type) AS count_event
  FROM clique_bait.events 
 GROUP BY event_type
 ORDER BY event_type;
````

**Answer:**

![image](https://user-images.githubusercontent.com/61902789/132166788-54649245-114a-4afc-a839-ebd36e339092.png)

***
