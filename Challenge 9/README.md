# Challenge: Department Top Three Salaries
**Goal**: Write a solution to find the employees who are high earners in each of the departments. A high earner is someone whose salary is in the top 3 salaries for their department.\
**Source**: The challenge can be found [here](https://leetcode.com/problems/department-top-three-salaries/description/).

### Table: Employee

| Column Name  | Type    |
|--------------|---------|
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |

id is the primary key (column with unique values) for this table.\
departmentId is a foreign key (reference column) of the ID from the Department table.\
Each row of this table indicates the ID, name, and salary of an employee. It also contains the ID of their department.

### Table: Department

| Column Name | Type    |
|-------------|---------|
| id          | int     |
| name        | varchar |

id is the primary key (column with unique values) for this table.
Each row of this table indicates the ID of a department and its name.


## Example
Below is an example of the expected output for the corresponding input.

**Table: Employee**

| id | name  | salary | departmentId |
|----|-------|--------|--------------|
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |

**Table: Department**

| id | name  |
|----|-------|
| 1  | IT    |
| 2  | Sales |

**Output:**

| Department | Employee | Salary |
|------------|----------|--------|
| IT         | Max      | 90000  |
| IT         | Joe      | 85000  |
| IT         | Randy    | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |

**Explanation:**

In the IT department:
- Max earns the highest unique salary
- Both Randy and Joe earn the second-highest unique salary
- Will earns the third-highest unique salary

In the Sales department:
- Henry earns the highest salary
- Sam earns the second-highest salary
- There is no third-highest salary as there are only two employees


## My Solution
For this challenge, I have two solutions!

During this challenge, I had my first encounter with window functions and `DENSE_RANK()`.

As a result, my first solution is one that gets the job done, and the second is a tidier version.

### Solution 1 - It Just Works
```sql
WITH
-- Calculate Top 3 Salaries per Department
TopSalaries AS (
    SELECT 
        salary, 
        id 
    FROM (
        SELECT DISTINCT
            e.salary,
            d.id,
            DENSE_RANK() OVER (PARTITION BY d.id ORDER BY salary desc) as rnk
        FROM Employee e
        LEFT JOIN Department d on e.departmentId = d.id
    ) as _
    WHERE rnk <= 3
)
SELECT
    d.name as Department, 
    e.name as Employee,
    e.salary as Salary
FROM Employee e
LEFT JOIN Department d on departmentId = d.id
LEFT JOIN TopSalaries ts on ts.id = d.id 
WHERE 
    departmentId = ts.id
    AND
    e.salary = ts.salary 
```
**Step 1: Isolate top 3 salaries per dept.**\
This is done in the `TopSalaries` table by ranking the salaries using the `DENSE_RANK()` window function and keeping the salaries where `rank <= 3`.

**Step 2: Get details**\
With step one done, all we need to do is retrieve the required information (after a couple of joins), making sure that the salary of the employee is in the `TopSalaries` table (i.e. it's a top 3 salary in the department) in the `WHERE` clause.

### Solution 2 - With a Little Finesse
```sql
SELECT 
    Department, 
    Employee, 
    Salary
FROM (
    SELECT 
        d.name as Department, 
        e.name as Employee, 
        e.salary as Salary,
        DENSE_RANK() OVER (PARTITION BY d.name ORDER BY Salary desc) as rnk
    FROM Employee e
    JOIN Department d on e.departmentId = d.id
) as _
WHERE 
    rnk <= 3
```
This solution is effectively a condensed version of the first solution.
First, the two tables are joined (in the `FROM` clause) and an extra `rnk` column is added to rank the salaries according to the window function.

Finally, we get all the salaries where their rank is 3 or less for their department.