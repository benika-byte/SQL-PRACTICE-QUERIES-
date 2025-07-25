--Get employee names along with their department names
SELECT  e.name , d.department_name
FROM employees as e
join departments as d
on e.department_id = d.department_id

--List all employees and their assigned projects (even if not assigned)
SELECT e.name , p.project_name
from employees as e 
join employee_projects as ep
on e.emp_id = ep.emp_id
left join projects as p on p.project_id = p.project_id

--Show all departments with no employees
select  d.department_name , e.emp_id
from departments as d
join employees  as e 
on d.department_id = e.department_id
WHERE e.emp_id is null 

--Fetch projects and names of employees assigned to them
select  p.project_name , e.name
from employees as e 
join employee_projects as ep
on e.emp_id = ep.emp_id
join projects as p on ep.project_id = p.project_id

--List employees with their department and number of projects
select e.name,  d.department_name , COUNT(ep.project_id) as project_assigned_count
FROM employees as e
join employee_projects as ep
on e.emp_id = ep.emp_id
join departments as d on d.department_id = d.department_id
group by e.name , d.department_name

---Retrieve all sales with employee and department details
SELECT s.sale_amount ,s.sale_id , s.region , s.sale_date ,  e.emp_id , e.name , d.department_id , d.department_name
from sales as s
join employees as e
on s.emp_id = e.emp_id
left join departments as d 
on d.department_id = e.department_Id

--Get count of employees in each department (even if 0)
select count(emp_id) as employees_count , d.department_name
from employees as e 
join departments as d 
on e.department_id = d.department_id
group by emp_id , d.department_name

---show all employees and the regions they’ve sold in
select e.name , s.region 
from employees as e
join sales as s
on e.emp_id = s.emp_id


--list all employees with total sales (include those with 0 sales)
select e.name , sum(s.sale_amount)
from employees as e 
join sales as s
on e.emp_id = s.emp_id
group by e.name

--Group employees by department and find average salary
select  d.department_name , round(avg(e.salary),0) as average_salary
from employees as e
join departments as d 
on e.department_id = d.department_id
group by d.department_name

--Show departments having more than 3 employees
select department_name , count(emp_id) as employee_count
from departments as d 
join employees as e
on d.department_id = e.department_id
group by department_name 
having count(emp_id) >3 

--List regions where total sales > ₹1,00,000
select region , sum(sale_amount)
from sales 
group by region 
having sum(sale_amount) > 100000;

--Count employees by gender
select gender , count(*) as counts 
from employees 
group by gender;

--Show project IDs with more than 2 employees assigned
select project_id , count(emp_id) emp_count
from employee_projects 
group by project_id
having  count(emp_id) > 2

-- Find employees grouped by hire year and count them
select count(emp_id) , extract (year from hire_date ) as hire_year
from employees 
group by hire_year
order by hire_year


--Group sales by region and month
select  extract (month from sale_date) as sale_month, region , sum(sale_amount) as total_sales
from sales 
group by region , sale_month;

---Departments with avg salary > ₹70,000
select d.department_name , round( avg(e.salary),0) as average_salary 
from departments as d
join employees as e
on d.department_id = e.department_id 
group by d.department_name 
having avg(e.salary) > 70000


--- Count employees in each department whose salary > ₹50,000
select count(e.name) as counts , department_name 
from employees as e
join departments as d
on d.department_id = e.department_id
where e.salary > 50000
group by department_name 

--Show employees who sold more than ₹50,000 in total
select e.name , sale_amount as sales_by_employee
from employees as e 
join sales as s 
on e.emp_id = s.emp_id
group by e.name, sale_amount
having sale_amount > 50000

--Rank employees by salary within each department
SELECT name, department_id, salary,
       RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS salary_rank
FROM employees;

--Find top 3 earners in each department
SELECT name, department_id, salary,
       RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS top_earner
FROM employees
limit 3 

--Show cumulative sales by employee over time
SELECT emp_id, sale_date , sale_amount,
SUM(sale_amount) OVER (PARTITION BY emp_id ORDER BY sale_date) as cumulative_sales
from sales 

--Add a row number for each employee ordered by hire date
SELECT *,
ROW_NUMBER() OVER (ORDER BY hire_date) AS row_num
FROM employees;

--Find salary difference from department average
SELECT name, salary , department_id , 
salary - AVG(salary) OVER (PARTITION BY department_id ) AS  avg_sal_dif
FROM employees; 

--Running total of sales per region
select sale_amount , region , sale_date,
SUM(sale_amount) OVER (PARTITION BY region ORDER BY sale_date) as runnning_total
FROM sales;

-- Lead and lag salary in each department
SELECT name, department_id , salary, 
LAG(salary) OVER (PARTITION BY department_id ORDER BY salary) as prev_salary,
LEAD(salary) OVER (PARTITION BY department_id ORDER BY salary) as next_salary
FROM employees; 

--First and last sale date per employee
SELECT s.emp_id ,
MIN(sale_date) as first_sal_date,
MAX(sale_date) as last_sal_date
from sales as s
GROUP BY s.emp_id;
