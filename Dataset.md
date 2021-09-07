## Available Data
For this case study there is a total of 5 datasets which we will need to combine to solve all of the questions.<br>
To obtain the complete data, click [here](https://www.db-fiddle.com/f/jmnwogTsUE8hGqkZv9H7E8/17)!<br>

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

<img width="500" alt="image" src="https://user-images.githubusercontent.com/61902789/132234402-36efbe5f-b478-4435-94dd-a5324e94a0bf.png">
