1. Creating and Populating Tables

To create a table named Employee,

    use presidio_task;

    create table employee (
        EmployeeId int Primary key auto_increment,
        FirstName varchar(32),
        LastName varchar(32),
        Age int,
        Department varchar(32),
        Salary decimal(10,0)
    );

To insert values,

    INSERT INTO Employee (FirstName, LastName, Age, Department, Salary) VALUES
    ('chowdri', 'sakthivel', 22, 'IT', 75000 ),
    ('koushik', 'd', 26, 'HR', 55000),
    ('arun', 'balaji', 23, 'IT', 80000),
    ('kaviraj', 'a', 25, 'Sales', 60000);

To view all fields of the table,

    select * from Employee;

2. Basic Filtering and Sorting

To filter records based on a condition,
 
    select * from employee where Department like "IT";

To order the results in descending order,

    select * from employee where Department like "IT" order by Salary desc;

Use of Logical operators,

    select * from employee where Department like "IT" and Salary > 40000;

3. Simple Aggregation and Grouping

Use of aggregate functions,

    select count(*) as total_employees from employe;

Use of aggregate functions with Group by,

    select Department, count(*) as total_employee , avg(Salary) as average_salary from employee Group by Department;

Use of Having,

    select department, avg(Salary) from department Group by department having avg(Salary) >= 60000;

4. Multi-Table JOINs

Create two tables with foreign key relationship,

    create table customer (
        customer_id int Primary key auto_increment,
        customer_name varchar(32),
        age int,
        phone_no varchar(10),
        email varchar(16)
    )

    create table orders (
        order_id int Primary key auto_increment,
        customer_id int,
        tot_price decimal(10,2) not null,
        order_date date
        foreign key (customer_id) references customer(customer_id)
    )

Write an INNER JOIN query to retrieve combined information,

    select * from customer inner join orders on customer.customer_id = orders.customer_id;

Other join,

    select * from customer left join orders on customer.customer_id = orders.customer_id;
    select * from customer right join orders on customer.customer_id = orders.customer_id;
    select * from customer natural join orders;

5. Subqueries and Nested Queries

Use a subquery in the WHERE clause,

    select * from employee where (
        select avg(Salary) from employee
    );

Use subqueries in the SELECT list to compute dynamic columns,

    select FirstName, LastName, department, ( 
        select avg(Salary) from employee where department = emp.department
        ) 
    from employee emp;

6. Date and Time Functions

Use built-in date functions,

    select * from orders where datediff(curdate(),order_date) < 7;
    select dateadd(curdate(), interval 1 day);
    select datesub(curdate(), interval 1 day);

Query to filter records based on date ranges    

    select * from orders where datediff(curdate(),order_date) < 31;

Formate the dates,

    select date_formate(order_date, '%M %d, %Y') AS formatted_date from orders;
    select to_char(order_date, 'mon-dd-yyyy') AS formatted_date from orders;



