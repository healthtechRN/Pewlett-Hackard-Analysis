# Pewlett-Hackard-Analysis
## Project Overview
### Using PostgreSQL to 
- Design an Entity Relationship Diagram (ERD) that will apply to the data
- Create and use a SQL database
- Import and export large CSV datasets into pgAdmin
- Practice using different joins to create new tables in pgAdmin
- Write basic- to intermediate-level SQL statements

#### The following SQL analysis of employee data was used to:
- Determine the number of retiring employees per title for fictional company Pewlett Hackard, and identify employees who are eligible to participate in a mentorship program. There were 300,024 employees in the data set

### Resources
- Data Source: (1) departments.csv, (2) dept_emp.csv, (3) dept_manager.csv, (4) employees.csv, (5) salaries.csv, and (6) titles.csv
- Relational database system: PostgreSQL 13.5
- Software: pgAdmin 4
### ERD
![ERD](Resources/EmployeeDB.png)

## Results 
- ### Deliverable 1: The Number of Retiring Employees by Title
  - Senior Engineers is the job title with the most employees retiring = 25,916
  - Managers is the job title with the least = 2

![Retiring Employee Count by Title](Resources/count_by_title.png)
- ### Deliverable 2: The Employees Eligible for the Mentorship Program 
  - There were 1,549 employees eligible for the Mentorship Program that met the criteria "current employees who were born between January 1, 1965 and December 31, 1965"
  - The job title with the most mentorship program eligibility was Senior Staff = 569

## Summary
- ### How many roles will need to be filled as the "silver tsunami" begins to make an impact?
  - A grand total of 72,458 roles will need to be filled 
- #### Query to get the the number of retiring employees per title
```SQL
-- The Number of Retiring Employees by Title
SELECT e.emp_no,
    e.first_name,
e.last_name,
    t.title,
    t.from_date,
	t.to_date
INTO retirement_titles
FROM employees as e
INNER JOIN titles as t
ON (e.emp_no = t.emp_no)
WHERE (e.birth_date BETWEEN '1952-01-01' AND '1955-12-31')
ORDER BY e.emp_no, t.emp_no ASC;

SELECT * FROM retirement_titles;

-- Use Distinct with Orderby to remove duplicate rows
SELECT DISTINCT ON (rt.emp_no) rt.emp_no,
rt.first_name, 
rt.last_name, 
rt.title
INTO unique_titles
FROM retirement_titles as rt
WHERE rt.to_date = ('9999-01-01')
ORDER BY rt.emp_no, rt.to_date DESC;

-- Employee count by most recent job title who are about to retire
SELECT COUNT(ut.emp_no), ut.title
-- INTO retiring_titles
FROM unique_titles as ut
GROUP BY ut.title;
```
- ### Are there enough qualified, retirement-ready employees in the departments to mentor the next generation of Pewlett Hackard employees?
  - There are only 1,549 qualified mentors using the current criteria which do not reflect enough qualified, retirement-ready employees to mentor the next generation of Pewlett Hackard employees which needs to fill 72,458 roles. Each mentor would have to mentor about 47 mentees, which is not possible to mentor.
  - #### Query to create a mentorship-eligibility table that holds the current employees who were born between January 1, 1965 and December 31, 1965.
```SQL
-- The Employees Eligible for the Mentorship Program 
SELECT DISTINCT ON (e.emp_no) e.emp_no,
    e.first_name,
e.last_name,
e.birth_date,
    de.from_date,
	de.to_date,
	t.title
-- INTO mentorship_eligibilty
FROM employees as e
INNER JOIN dept_emp as de
ON (e.emp_no = de.emp_no)
INNER JOIN titles as t
ON (e.emp_no = t.emp_no)
WHERE (e.birth_date BETWEEN '1965-01-01' AND '1965-12-31')
     AND de.to_date = ('9999-01-01')
ORDER BY e.emp_no, de.emp_no ASC;
```
