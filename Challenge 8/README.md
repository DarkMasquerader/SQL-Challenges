# Challenge: Human Traffic of Stadium
**Goal**: Find the records with three or more rows with consecutive `id`'s, and the number of people is greater than or equal to 100 for each.\
**Source**: The challenge can be found [here](https://leetcode.com/problems/human-traffic-of-stadium/description/?source=submission-ac).

### Table: Stadium

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| visit_date  | date    |
| people      | int     |

`visit_date` is the column with unique values for this table.  
Each row of this table contains the visit date and visit id to the stadium with the number of people during the visit.  
As the `id` increases, the `visit_date` increases as well.

Return the result table ordered by `visit_date` in ascending order.

Example:
Below is an example of the expected output for the corresponding input.

**Table: Stadium:**

| id | visit_date | people |
|----|------------|--------|
| 1  | 2017-01-01 | 10     |
| 2  | 2017-01-02 | 109    |
| 3  | 2017-01-03 | 150    |
| 4  | 2017-01-04 | 99     |
| 5  | 2017-01-05 | 145    |
| 6  | 2017-01-06 | 1455   |
| 7  | 2017-01-07 | 199    |
| 8  | 2017-01-09 | 188    |

**Output:**

| id | visit_date | people |
|----|------------|--------|
| 5  | 2017-01-05 | 145    |
| 6  | 2017-01-06 | 1455   |
| 7  | 2017-01-07 | 199    |
| 8  | 2017-01-09 | 188    |

**Explanation:**

- The four rows with ids 5, 6, 7, and 8 have consecutive ids and each of them has >= 100 people attended.
- Note that row 8 was included even though the visit_date was not the next day after row 7.
- The rows with ids 2 and 3 are not included because we need at least three consecutive ids.


## My Solution
My solution for this challenge is as shown below:
```sql
# IDs with >= 100 people
WITH id_100 as ( 
    SELECT
        *
    FROM 
        Stadium
    WHERE 
        people >= 100
) 

SELECT DISTINCT
    s1.id, 
    s1.visit_date, 
    s1.people
FROM id_100 s1
JOIN id_100 s2 
JOIN id_100 s3 
WHERE
    # Following IDs are consecutively larger
    (s2.id - s1.id = 1 AND s3.id - s2.id = 1) 
    OR

    # Following IDs are consecutively smaller
    (s1.id - s2.id = 1 AND s2.id - s3.id = 1)
    OR 

    # One ID either side is bigger and the other smaller 
    (s2.id - s1.id = 1 AND s1.id - s3.id = 1) 
ORDER BY
    s1.visit_date
```

This one was quite the complex one and very fun to solve!

The first step is to isolate the IDs where `people >= 100` as these are the only IDs we are interested in and store them in the temporary table `id_100`.

The next step is to `CROSS JOIN` this table with itself twice providing every possible 3-step combination of IDs, as we are looking for a minimum of three consecutive rows of IDs.

This will produce something similar to the table below:

| s1.id | s2.id | s3.id |
| -- | -- | -- |
| 5  | 6  | 7  |
| 6  | 7  | 5  |
| 6  | 7  | 8  |
| 7  | 6  | 5  |
| 7  | 8  | 6  |
| 8  | 7  | 6  |

(**NOTE:** Unnecessary columns have been removed for ease of understanding.)

As we take our unique IDs from `s1.id` \*only\*, the table needs to be configured such that s1 will contain all of the IDs that meet the condition of the challenge.

As a result of this 'constraint' requiring our IDs to be in table `s1`, there are three possible scenarios we can encounter which are handled in the `WHERE` clause:
1. The values of the IDs increase as we go to the right (row 1)
1. The values of the IDs decrease as we go to the right (row 4)
1. One of the values to the right is larger than the first and the other values are smaller than the first (row 5)

The third case is required in edge cases where there are only 3 valid IDs.

With the above established, all that remains is to select the distinct columns from `s1` and order them appropriately.

## Alternative Solution 
Given the amount of fun I had with this challenge, I put together an easier-to-read, yet more verbose solution:
```sql
WITH 

# IDs with >= 100 people
id_100 as ( 
    SELECT
        *
    FROM 
        Stadium
    WHERE 
        people >= 100
),

# Subsequent IDs are consecutively larger
consecutive_larger as (
    SELECT DISTINCT s1.id, s1.visit_date, s1.people
    FROM id_100 s1
    JOIN id_100 s2 on s2.id - s1.id = 1
    JOIN id_100 s3 on s3.id - s2.id = 1
), 

# Subsequent IDs are consecutively smaller
consecutive_smaller as (
    SELECT DISTINCT s1.id, s1.visit_date, s1.people
    FROM id_100 s1
    JOIN id_100 s2 on s1.id - s2.id = 1
    JOIN id_100 s3 on s2.id - s3.id = 1
),

# One subsequent ID is bigger and the other smaller
varying_neighbours as (
    SELECT DISTINCT s1.id, s1.visit_date, s1.people
    FROM id_100 s1
    JOIN id_100 s2 
    JOIN id_100 s3
    WHERE 
        s2.id - s1.id = 1
        AND
        s1.id - s3.id = 1
),

# Combined results
master_table as (
    SELECT * FROM consecutive_larger
    UNION
    SELECT * FROM consecutive_smaller
    UNION
    SELECT * FROM varying_neighbours
)

SELECT DISTINCT
    id, 
    visit_date, 
    people
FROM master_table
ORDER BY visit_date
```































