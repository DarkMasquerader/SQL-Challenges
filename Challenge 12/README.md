# Challenge: Immediate Food Delivery II
**Goal**: Find the number of customers who made an immediate order on their first order.\
**Source**: The challenge can be found [here](https://leetcode.com/problems/immediate-food-delivery-ii/description/?envType=problem-list-v2&envId=moxzxlls).

### Table: Delivery

| Column Name                 | Type    |
|-----------------------------|---------|
| delivery_id                 | int     |
| customer_id                 | int     |
| order_date                  | date    |
| customer_pref_delivery_date | date    |

`delivery_id` is the column of unique values of this table.
The table holds information about food delivery to customers that make orders at some date and specify a preferred delivery date (on the same order date or after it).

If the customer's preferred delivery date is the same as the order date, then the order is called immediate; otherwise, it is called scheduled.

The first order of a customer is the order with the earliest order date that the customer made. It is guaranteed that a customer has precisely one first order.

## Example
Below is an example of the expected output for the corresponding input.

**Table: Delivery:**

| delivery_id | customer_id | order_date | customer_pref_delivery_date |
|-------------|-------------|------------|-----------------------------|
| 1           | 1           | 2019-08-01 | 2019-08-02                  |
| 2           | 2           | 2019-08-02 | 2019-08-02                  |
| 3           | 1           | 2019-08-11 | 2019-08-12                  |
| 4           | 3           | 2019-08-24 | 2019-08-24                  |
| 5           | 3           | 2019-08-21 | 2019-08-22                  |
| 6           | 2           | 2019-08-11 | 2019-08-13                  |
| 7           | 4           | 2019-08-09 | 2019-08-09                  |

**Output:**

| immediate_percentage |
|----------------------|
| 50.00                |

**Explanation:**

The customer id 1 has a first order with delivery id 1 and it is scheduled.\
The customer id 2 has a first order with delivery id 2 and it is immediate.\
The customer id 3 has a first order with delivery id 5 and it is scheduled.\
The customer id 4 has a first order with delivery id 7 and it is immediate.\
Hence, half the customers have immediate first orders.


## My Solution
My solution for this challenge is as shown below:
```sql
WITH 
first_order as (
    SELECT
        customer_id, 
        MIN(order_date) as first_order_date
    FROM 
        delivery
    GROUP BY customer_id
)

SELECT
    ROUND(
        SUM(
            order_date = customer_pref_delivery_date
            AND
            order_date = first_order_date
        ) / COUNT(DISTINCT d.customer_id) * 100
    , 2) as immediate_percentage
FROM delivery d 
JOIN first_order f on d.customer_id = f.customer_id 
```
The first step taken is to isolate each customer's first order date (`first_order` table) and `JOIN` the table appropriately to the `Delivery` table.

With this done, we can now check to see if an order is immediate (`order_date = customer_pref_delivery_date`) and if it's the customer's first order (`order_date = first_order_date`).

## Alt. Solution 
I have written an alternative solution that foregoes the need for joins by using window functions:
```sql
SELECT 
    ROUND(
        SUM(
            order_date = customer_pref_delivery_date
            and 
            order_date = first_order_date
        ) / COUNT(DISTINCT customer_id) * 100
    , 2) as immediate_percentage
FROM (
    SELECT
        delivery_id, 
        customer_id, 
        order_date, 
        customer_pref_delivery_date, 
        MIN(order_date) OVER (PARTITION BY customer_id) as first_order_date
    FROM delivery 
) AS _
```