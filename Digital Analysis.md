# Online Store: Clique Bait

## Digital Analysis

**1. How many users are there?**

````sql
SELECT COUNT(DISTINCT user_id)
  FROM clique_bait.users LIMIT 10;
````

**Answer:**

<img width="85" alt="image" src="https://github.com/ts756632/Online_Store_Analysis/blob/main/image/users.PNG">
 - There are 500 unique users.

***

**2. How many cookies does each user have on average?**

````sql
SELECT AVG(count_cookies) AS average_cookies
FROM
(SELECT user_id, COUNT(cookie_id) AS count_cookies
  FROM clique_bait.users 
  GROUP BY user_id
  )sub;
````

**Answer:**

<img width="130" alt="image" src="https://github.com/ts756632/Online_Store_Analysis/blob/main/image/cookies.PNG">

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

![image](https://user-images.githubusercontent.com/61902789/132159951-a6863355-e070-43c4-9443-8ba873f86bc9.png)

<img width="130" alt="image" src="https://github.com/ts756632/Online_Store_Analysis/blob/main/image/cookies.PNG">

 - Each user has 3.564 cookies on average.

***
