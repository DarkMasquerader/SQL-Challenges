# Challenge: Students and Examinations
**Goal**: Find the number of times each student attended each exam, ordered by `student_id` and `subject_name`.\
**Source**: The challenge can be found [here](https://leetcode.com/problems/students-and-examinations/description/?envType=problem-list-v2&envId=mei43yec).

### Table: Students

| Column Name  | Type     |
|--------------|----------|
| student_id   | int      | (primary key)
| student_name | varchar  |

Each row in the `Students` table provides the ID and name of a student at the school.

### Table: Subjects

| Column Name  | Type     |
|--------------|----------|
| subject_name | varchar  | (primary key)

Each row in the `Subjects` table lists the name of a subject taught at the school.

### Table: Examinations

| Column Name  | Type     |
|--------------|----------|
| student_id   | int      |
| subject_name | varchar  |

This table lacks a primary key and may contain duplicate rows.
Each row records a student's attendance at an exam for a specific subject listed in the `Subjects` table.

## My Solution
My solution for this challenge is as shown below:
```sql
SELECT
    s.student_id,
    s.student_name,
    sub.subject_name,
    SUM(if(e.student_id is not null, 1, 0)) as attended_exams
FROM Students s
JOIN Subjects sub 
LEFT JOIN Examinations e on
    s.student_id = e.student_id
    and
    sub.subject_name = e.subject_name
GROUP BY 
    s.student_id, sub.subject_name
ORDER BY
    s.student_id, sub.subject_name
```

As we are counting the number of times each student attended each exam (including when they **did not** attend the exam), a `CROSS JOIN` is performed on the `Students` and `Subjects` tables to assign each student to each of the available subjects.

A `LEFT JOIN` is then performed on the table to match all the students to the exams that they took. A left join was chosen instead of an inner join as we still want to know if a student did not attend an exam (by setting the count to '0') rather than removing the entry altogether.

Lastly, the `SUM()` aggregate function is used alongside an if-statement to tally the number of exams.
This solution is necessary as '0' is a possible output for the `attended_exams` column and `COUNT()` would not enable this. 

































