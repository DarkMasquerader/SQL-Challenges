# Challenge: Rising Temperature
**Goal**: Find all date ID's with a higher temperature compared to the previous day.\
**Source**: The challenge can be found [here](https://leetcode.com/problems/rising-temperature/description/?envType=problem-list-v2&envId=mei43yec).

### Table: Weather

| Column Name | Type   |
|-------------|--------|
| id          | int    |
| recordDate  | date   |
| temperature | int    |

`id` is the column with unique values for this table.  
There are no different rows with the same `recordDate`.  
This table contains information about the temperature on a certain day.

## Example

**Table: Weather**

| id | recordDate | temperature |
|----|------------|-------------|
| 1  | 2015-01-01 | 10          |
| 2  | 2015-01-02 | 25          |
| 3  | 2015-01-03 | 20          |
| 4  | 2015-01-04 | 30          |

**Output:**

| id |
|----|
| 2  |
| 4  |

## My Solution
My solution for this challenge is as shown below:
```sql
SELECT 
    w1.id 
FROM 
    weather w1
JOIN 
    weather w2
WHERE
    DATEDIFF(w1.recordDate, w2.recordDate) = 1
    and
    w1.temperature > w2.temperature
```

As only one table is given, a `CROSS JOIN` is performed on the table to generate all possible combinations of the columns.

To isolate the rows of interest, the `WHERE` clause is included to isolate the ID's of the dates where the temperature is higher than the previous day.




















