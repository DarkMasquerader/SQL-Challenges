# Challenge: Biggest Single Number 
**Goal**: Identify the biggest number that only appears once in the table\
**Source**: The challenge can be found [here](https://leetcode.com/problems/biggest-single-number/description/?envType=problem-list-v2&envId=mribl50j).

### Table: MyNumbers

| Column Name | Type |
|-------------|------|
| num         | int  |

This table may contain duplicates (In other words, there is no primary key for this table in SQL).  

A single number is a number that appeared only once in the MyNumbers table.

Find the largest single number. If there is no single number, report null.

## Example
Below is an example of the expected output for the corresponding input.

**Table: MyNumbers:**

| num |
|-----|
| 8   |
| 8   |
| 3   |
| 3   |
| 1   |
| 4   |
| 5   |
| 6   |

**Output:**

| num |
|-----|
| 6   |

## My Solution
My solution for this challenge is as shown below:
```sql
SELECT MAX(num) as num
FROM (
    SELECT num 
    FROM MyNumbers
    GROUP BY num
    HAVING count(*) = 1
) as _

```
The first step taken is to create a sub-query isolating the numbers which only appear once.

The second step is to select the largest number using `MAX()`, which also allows for 'null' to be reported, as per the requirements of the challenge.