# Challenge: Trips and Users
**Goal**: Identify the cancellation rate from unbanned users who are not partners.\
**Source**: The challenge can be found [here](https://leetcode.com/problems/trips-and-users/description/).

### Table: Trips

| Column Name | Type     |
|-------------|----------|
| id          | int      |
| client_id   | int      |
| driver_id   | int      |
| city_id     | int      |
| status      | enum     |
| request_at  | date     |

`id` is the primary key (column with unique values) for this table.  
The table holds all taxi trips. Each trip has a unique `id`, while `client_id` and `driver_id` are foreign keys to the `users_id` in the Users table.  
`status` is an ENUM (category) type of ('completed', 'cancelled_by_driver', 'cancelled_by_client').

### Table: Users

| Column Name | Type     |
|-------------|----------|
| users_id    | int      |
| banned      | enum     |
| role        | enum     |

`users_id` is the primary key (column with unique values) for this table.  
The table holds all users. Each user has a unique `users_id`, and `role` is an ENUM type of ('client', 'driver', 'partner').  
`banned` is an ENUM (category) type of ('Yes', 'No').

The cancellation rate is computed by dividing the number of canceled (by client or driver) requests with unbanned users by the total number of requests with unbanned users on that day.

Write a solution to find the cancellation rate of requests with unbanned users (both client and driver must not be banned) each day between "2013-10-01" and "2013-10-03". Round Cancellation Rate to two decimal points.

## Example
Below is an example of the expected output for the corresponding input.

**Table: Trips:**

| id  | client_id | driver_id | city_id | status              | request_at |
|-----|-----------|-----------|---------|---------------------|------------|
| 1   | 1         | 10        | 1       | completed           | 2013-10-01 |
| 2   | 2         | 11        | 1       | cancelled_by_driver | 2013-10-01 |
| 3   | 3         | 12        | 6       | completed           | 2013-10-01 |
| 4   | 4         | 13        | 6       | cancelled_by_client | 2013-10-01 |
| 5   | 1         | 10        | 1       | completed           | 2013-10-02 |
| 6   | 2         | 11        | 6       | completed           | 2013-10-02 |
| 7   | 3         | 12        | 6       | completed           | 2013-10-02 |
| 8   | 2         | 12        | 12      | completed           | 2013-10-03 |
| 9   | 3         | 10        | 12      | completed           | 2013-10-03 |
| 10  | 4         | 13        | 12      | cancelled_by_driver | 2013-10-03 |

**Table: Users:**

| users_id | banned | role   |
|----------|--------|--------|
| 1        | No     | client |
| 2        | Yes    | client |
| 3        | No     | client |
| 4        | No     | client |
| 10       | No     | driver |
| 11       | No     | driver |
| 12       | No     | driver |
| 13       | No     | driver |

**Output:**

| Day        | Cancellation Rate |
|------------|-------------------|
| 2013-10-01 | 0.33              |
| 2013-10-02 | 0.00              |
| 2013-10-03 | 0.50              |

## My Solution
My solution for this challenge is as shown below:
```sql
WITH banned_users as (
    SELECT DISTINCT users_id
    from Users
    WHERE banned = 'Yes'
)

SELECT 
    request_at as Day, 
    ROUND(
        SUM(
            if(
                status LIKE 'cancelled%',
                1.00, 
                0
            )
        ) 
        / 
        COUNT(*), 2) as 'Cancellation Rate'
FROM 
    Trips t
WHERE
    client_id NOT IN (SELECT users_id from banned_users)
    and
    driver_id NOT IN (SELECT users_id from banned_users)
    and
    request_at BETWEEN '2013-10-01' and '2013-10-03'
GROUP BY
    Day
```

As we want to exclude the banned users from our count, a temporary table `banned_users` containing the IDs of banned users is created at the start of the statement.

The first two conditions of the `WHERE` clause check to see if the current user is banned (both client_id and driver_id are foreign keys under 'users_id' in the Users table).

The third condition of the `WHERE` ensures we stay within the pre-defined range of dates.

Lastly, the cancellation rate is calculated using the `SUM()` aggregate function, where we count a row (i.e. return a value of 1.00) if the status of the row starts with 'cancelled'.

