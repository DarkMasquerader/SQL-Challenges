# Challenge: Customer Who Visited but Did Not Make Any Transactions
**Goal**: Isolate customers in the database who have visited, but do not have any transactions.\
**Source**: The challenge can be found [here](https://leetcode.com/problems/customer-who-visited-but-did-not-make-any-transactions/description/?envType=problem-list-v2&envId=mei43yec).

### Table: Visits

| Column Name | Type |
|-------------|------|
| visit_id    | int  |
| customer_id | int  |

`visit_id` is the column with unique values for this table.  
This table contains information about the customers who visited the mall.

### Table: Transactions

| Column Name    | Type |
|----------------|------|
| transaction_id | int  |
| visit_id       | int  |
| amount         | int  |

`transaction_id` is the column with unique values for this table.  
This table contains information about the transactions made during the `visit_id`.

## Example
Below is an example of the expected output for the corresponding input.

**Table: Visits**

| visit_id | customer_id |
|----------|-------------|
| 1        | 23          |
| 2        | 9           |
| 4        | 30          |
| 5        | 54          |
| 6        | 96          |
| 7        | 54          |
| 8        | 54          |

**Table: Transactions**

| transaction_id | visit_id | amount |
|----------------|----------|--------|
| 2              | 5        | 310    |
| 3              | 5        | 300    |
| 9              | 5        | 200    |
| 12             | 1        | 910    |
| 13             | 2        | 970    |

**Output:**

| customer_id | count_no_trans |
|-------------|----------------|
| 54          | 2              |
| 30          | 1              |
| 96          | 1              |


## My Solution
My solution for this challenge is as shown below:
```sql
SELECT 
    customer_id, 
    COUNT(*) as count_no_trans
FROM 
    Visits v
LEFT JOIN 
    Transactions t on v.visit_id = t.visit_id
WHERE 
    transaction_id is null
GROUP BY 
    v.customer_id

```
With `visit_id` as the shared key across the two tables, the tables are joined on this key.

A `LEFT JOIN` on the `Transactions` table is performed as all the entries in the `Visits` table are required and NULL values are acceptable/expected in the `Transactions` table.

As we are looking for the number of visits customers made **without** making a purchase, we have the condition `transaction_id is null`.

The results are grouped by `v.customer_id` to enable the use of the `COUNT()` aggregate function in the select statement.

## Extension
If we are interested in finding the number of visits from customers who have **never** made a single transaction, the solution is as such:

```sql
WITH cwt as (
    SELECT DISTINCT customer_id
    FROM Visits v
    LEFT JOIN Transactions t on v.visit_id = t.visit_id
    WHERE t.visit_id is not NULL
) 

SELECT 
    customer_id, 
    COUNT(*) as count_no_trans
FROM 
    Visits v
LEFT JOIN 
    Transactions t on v.visit_id = t.visit_id
WHERE 
    transaction_id is null
    and 
    customer_id NOT IN (
        SELECT customer_id FROM cwt
        )
GROUP BY 
    v.customer_id
```
To achieve this, a temporary table `cwt` is created above which contains a list of all customers with at least one transaction.

The previous SQL query remains the same, except with the addition of the second condition in the `WHERE` clause.

























