# Challenge: Percentage of Users Attended a Contest
**Goal**: Identify the percentage of students who attended a given contest, rounded to 2dp and ordered by the percentage in descending order (contest_id in ascending order if tie).\
**Source**: The challenge can be found [here](https://leetcode.com/problems/percentage-of-users-attended-a-contest/description/?envType=problem-list-v2&envId=mei43yec).

### Table: Users

| Column Name | Type    |
|-------------|---------|
| user_id     | int     | (primary key)
| user_name   | varchar |

Each row in the `Users` table contains the name and ID of a user.

### Table: Register

| Column Name | Type    |
|-------------|---------|
| contest_id  | int     |
| user_id     | int     |

`(contest_id, user_id)` is the primary key (combination of columns with unique values) for this table.  
Each row in the `Register` table indicates that a user with `user_id` registered for a contest with `contest_id`.

## Example
Below is an example of the expected output for the corresponding input.

**Users table:**

| user_id | user_name |
|---------|-----------|
| 6       | Alice     |
| 2       | Bob       |
| 7       | Alex      |

**Register table:**

| contest_id | user_id |
|------------|---------|
| 215        | 6       |
| 209        | 2       |
| 208        | 2       |
| 210        | 6       |
| 208        | 6       |
| 209        | 7       |
| 209        | 6       |
| 215        | 7       |
| 208        | 7       |
| 210        | 2       |
| 207        | 2       |
| 210        | 7       |

**Output:**

| contest_id | percentage |
|------------|------------|
| 208        | 100.0      |
| 209        | 100.0      |
| 210        | 100.0      |
| 215        | 66.67      |
| 207        | 33.33      |

## My Solution
My solution for this challenge is as shown below:
```sql
SELECT
    contest_id, 
    ROUND(COUNT(*) / (SELECT COUNT(DISTINCT user_id) from Users) * 100, 2) as percentage
FROM 
    Register r
GROUP BY 
    contest_id
ORDER BY
    percentage desc, 
    contest_id asc
```

As we want to find the percentage of students that attended the course, we first need to identify the *total number of students* in the dataset. 
This is achieved with the following sub-query: 
`(SELECT COUNT(DISTINCT user_id) from Users) * 100, 2)`.

In order to use aggregate functions to calculate the percentage of students that attended the contest, the `Register` table is grouped by `contest_id`.