# Challenge: Confirmation Rate
**Goal**: Identify the confirmation rate of each user.\
**Source**: The challenge can be found [here](https://leetcode.com/problems/confirmation-rate/description/?envType=problem-list-v2&envId=mei43yec).

### Table: Signups

| Column Name | Type     |
|-------------|----------|
| user_id     | int      |
| time_stamp  | datetime |

`user_id` is the column of unique values for this table.  
Each row contains information about the signup time for the user with ID `user_id`.

### Table: Confirmations

| Column Name | Type     |
|-------------|----------|
| user_id     | int      |
| time_stamp  | datetime |
| action      | ENUM     |

`(user_id, time_stamp)` is the primary key (combination of columns with unique values) for this table.  
`user_id` is a foreign key (reference column) to the Signups table.  
`action` is an ENUM (category) of the type ('confirmed', 'timeout').

Each row of this table indicates that the user with ID `user_id` requested a confirmation message at `time_stamp` and that confirmation message was either confirmed ('confirmed') or expired without confirming ('timeout').

The confirmation rate of a user is the number of 'confirmed' messages divided by the total number of requested confirmation messages. The confirmation rate of a user that did not request any confirmation messages is 0. Round the confirmation rate to two decimal places.

## Example
Below is an example of the expected output for the corresponding input.

**Table: Signups:**

| user_id | time_stamp          |
|---------|---------------------|
| 3       | 2020-03-21 10:16:13 |
| 7       | 2020-01-04 13:57:59 |
| 2       | 2020-07-29 23:09:44 |
| 6       | 2020-12-09 10:39:37 |

**Table: Confirmations:**

| user_id | time_stamp          | action    |
|---------|---------------------|-----------|
| 3       | 2021-01-06 03:30:46 | timeout   |
| 3       | 2021-07-14 14:00:00 | timeout   |
| 7       | 2021-06-12 11:57:29 | confirmed |
| 7       | 2021-06-13 12:58:28 | confirmed |
| 7       | 2021-06-14 13:59:27 | confirmed |
| 2       | 2021-01-22 00:00:00 | confirmed |
| 2       | 2021-02-28 23:59:59 | timeout   |

**Output:**

| user_id | confirmation_rate |
|---------|-------------------|
| 6       | 0.00              |
| 3       | 0.00              |
| 7       | 1.00              |
| 2       | 0.50              |

## My Solution
My solution for this challenge is as shown below:
```sql
SELECT 
    s.user_id, 
    IFNULL(ROUND((SUM(if(action = 'confirmed', 1, 0))) / COUNT(*), 2),0) as confirmation_rate
FROM 
    Signups s
LEFT JOIN Confirmations c on s.user_id = c.user_id
GROUP BY 
    c.user_id
```

With `user_id` as a shared key across the two tables, a `LEFT JOIN` is computed with the `Confirmations` table using this key.

The calculation rate is computed (line 2 of the select statement) by dividing the number of 'confirmed' actions by all of the actions taken by the user. 

To enable the use of such aggregate functions, a `GROUP BY` is applied to `c.user_id`.