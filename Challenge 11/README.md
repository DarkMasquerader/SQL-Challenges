# Challenge: Average Selling Price
**Goal**: Find the average selling price of each product.\
**Source**: The challenge can be found [here](https://leetcode.com/problems/average-selling-price/?envType=problem-list-v2&envId=me16nsyi).

### Table: Prices

| Column Name | Type |
|-------------|------|
| product_id  | int  |
| start_date  | date |
| end_date    | date |
| price       | int  |

(product_id, start_date, end_date) is the primary key (combination of columns with unique values) for this table.  
Each row of this table indicates the price of the product_id in the period from start_date to end_date.  
For each product_id, there will be no two overlapping periods. That means there will be no two intersecting periods for the same product_id.

### Table: UnitsSold

| Column Name   | Type |
|---------------|------|
| product_id    | int  |
| purchase_date | date |
| units         | int  |

This table may contain duplicate rows.  
Each row of this table indicates the date, units, and product_id of each product sold.

## Example
Below is an example of the expected output for the corresponding input.


**Table: Prices**

| product_id | start_date | end_date   | price |
|------------|------------|------------|-------|
| 1          | 2019-02-17 | 2019-02-28 | 5     |
| 1          | 2019-03-01 | 2019-03-22 | 20    |
| 2          | 2019-02-01 | 2019-02-20 | 15    |
| 2          | 2019-02-21 | 2019-03-31 | 30    |

**Table: UnitsSold**

| product_id | purchase_date | units |
|------------|---------------|-------|
| 1          | 2019-02-25    | 100   |
| 1          | 2019-03-01    | 15    |
| 2          | 2019-02-10    | 200   |
| 2          | 2019-03-22    | 30    |

**Output:**

| product_id | average_price |
|------------|---------------|
| 1          | 6.96          |
| 2          | 16.96         |

**Explanation:**  
Average selling price = Total Price of Product / Number of products sold.  
Average selling price for product 1 = ((100 * 5) + (15 * 20)) / 115 = 6.96  
Average selling price for product 2 = ((200 * 15) + (30 * 30)) / 230 = 16.96

## My Solution
My solution for this challenge is as shown below:
```sql
SELECT
    p.product_id, 
    IFNULL(ROUND(
        SUM(
            price * units
        ) / SUM(units)
        , 2), 0) as average_price
FROM prices p
LEFT JOIN unitssold s on 
    p.product_id = s.product_id
    AND
    purchase_date BETWEEN start_date and end_date 
GROUP BY p.product_id
```

The first step is to append the `units` column from the `UnitsSold` table to the `Prices` table, on the appropriate rows (where the ID's and dates line up). This is handled by the `LEFT JOIN` creating a table as shown below: 
| Column Name | Type |
|-------------|------|
| product_id  | int  |
| start_date  | date |
| end_date    | date |
| price       | int  |
| units       | int  |

Following this, we need to group by the `product_id` (as we are interested in finding the average selling price for each product) and perform the calculations shown in the `SELECT` statement.

`IFNULL` is included to handle any edge cases where a product exists, yet has no sales.