# Challenge: Monthly Transactions
**Goal**: For each month and country, identify the number of transactions and their total amount, the number of approved transactions and their total amount.\
**Source**: The challenge can be found [here](https://leetcode.com/problems/monthly-transactions-i/description/?envType=problem-list-v2&envId=mei43yec).

### Table: Transactions

| Column Name | Type    |
|-------------|---------|
| id          | int     | (primary key)
| country     | varchar |
| state       | enum    |
| amount      | int     |
| trans_date  | date    |

`id` is the primary key of this table.  
The table contains information about incoming transactions.  
The `state` column is an enum of type ["approved", "declined"].

## Example
Below is an example of the expected output for the corresponding input.

**Table: Transactions**

| id   | country | state    | amount | trans_date |
|------|---------|----------|--------|------------|
| 121  | US      | approved | 1000   | 2018-12-18 |
| 122  | US      | declined | 2000   | 2018-12-19 |
| 123  | US      | approved | 2000   | 2019-01-01 |
| 124  | DE      | approved | 2000   | 2019-01-07 |

**Output:**

| month    | country | trans_count | approved_count | trans_total_amount | approved_total_amount |
|----------|---------|-------------|----------------|--------------------|-----------------------|
| 2018-12  | US      | 2           | 1              | 3000               | 1000                  |
| 2019-01  | US      | 1           | 1              | 2000               | 2000                  |
| 2019-01  | DE      | 1           | 1              | 2000               | 2000                  |


## My Solution
My solution for this challenge is as shown below:
```sql
SELECT 
    DATE_FORMAT(trans_date, '%Y-%m') as month, 
    country, 
    COUNT(*) as trans_count, 
    SUM(if(state = 'approved', 1, 0)) as approved_count, 
    SUM(amount) as trans_total_amount, 
    SUM(if(state = 'approved', amount, 0)) as approved_total_amount
FROM 
    Transactions t
GROUP BY 
    DATE_FORMAT(trans_date, '%Y-%m'), 
    country
```

The first step to solving this solution is to get the date in the correct format using the `DATE_FORMAT()` function, then grouping by the date and country. Once this is done, the solution is quite simple!