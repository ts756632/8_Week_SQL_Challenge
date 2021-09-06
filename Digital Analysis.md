# Online Store: Clique Bait

## Digital Analysis

**1. How many users are there?**

````sql
SELECT COUNT(DISTINCT user_id)
  FROM clique_bait.users LIMIT 10;
````

**Answer: **

![users](https://github.com/ts756632/Online_Store_Analysis/blob/main/image/users.PNG)

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

**Answer: **

![](https://github.com/ts756632/Online_Store_Analysis/blob/main/image/cookies.PNG)

- Each user has 3.564 cookies on average.

***
