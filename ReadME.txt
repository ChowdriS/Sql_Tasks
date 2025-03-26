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

    select * from employee where salary > (
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

7. Window Functions and Ranking

With window functions and partition,
    select emp_id, emp_name, department, salary,
        row_number() over (partition by department order by salary desc) as row_num,
        rank() over (partition by department order by salary desc) as rank_num,
        dense_rank() over (partition by department order by salary desc) as dense_rank_num
    from employees;

With Lead and Lag,
    select emp_id, emp_name, department, salary,
       lag(salary) over (partition by department order by salary desc) as prev_salary,
       lead(salary) over (partition by department order by salary desc) as next_salary
    from employees;

8. Common Table Expressions (CTEs) and Recursive Queries

An Non-Recursive cte,

    with salarysummary as (
        select department, avg(salary) as avg_salary
        from employees
        group by department
    )
    select e.emp_name, e.salary, s.avg_salary
    from employees e
    join salarysummary s on e.department = s.department;


An Recursive cte,

   with recursive orgchart as (
        -- base case
        select emp_id, emp_name, manager_id, 1 as depth
        from employeehierarchy
        where manager_id is null  

        union all

        -- recursive case
        select e.emp_id, e.emp_name, e.manager_id, o.depth + 1
        from employeehierarchy e
        inner join orgchart o on e.manager_id = o.emp_id
    )
    select * from orgchart;

To Stop Recursion,
    limit recursion using level depth in the query

    with recursive orgchart as (
        -- base case
        select emp_id, emp_name, manager_id, 1 as depth
        from employeehierarchy
        where manager_id is null  

        union all

        -- recursive case
        select e.emp_id, e.emp_name, e.manager_id, o.depth + 1
        from employeehierarchy e
        inner join orgchart o on e.manager_id = o.emp_id where o.depth < 5
    )
    select * from orgchart;

9. Stored Procedures and User-Defined Functions

To create Stored Procedures,

    delimiter $$

    create procedure gettotalsales(in start_date date, in end_date date, out total_sales decimal(10,2))
    begin
        select sum(tot_price) into total_sales
        from orders
        where order_date between start_date and end_date;
    end ;

    call gettotalsales('2024-01-01', '2024-12-31', @sales);
    select @sales $$

    delimiter ;


To create UDF,

    delimiter $$

    create function getdiscountedprice(original_price decimal(10,2), discount_percentage int) 
    returns decimal(10,2) deterministic
    begin
        declare discounted_price decimal(10,2);
        set discounted_price = original_price - (original_price * discount_percentage / 100);
        return discounted_price;
    end $$

    delimiter ;

10. Comprehensive Database Design, Optimization, and Advanced FeaturesObjective:Requirements

CREATE TABLE Customers (
    customer_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    phone VARCHAR(15) UNIQUE NOT NULL
);

CREATE TABLE Products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) NOT NULL CHECK (price > 0),
    stock INT NOT NULL CHECK (stock >= 0)
);

CREATE TABLE Orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_id INT NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    total_price DECIMAL(10,2) DEFAULT 0,
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id) ON DELETE CASCADE
);

CREATE TABLE OrderDetails (
    order_detail_id INT PRIMARY KEY AUTO_INCREMENT,
    order_id INT NOT NULL,
    product_id INT NOT NULL,
    quantity INT NOT NULL CHECK (quantity > 0),
    price DECIMAL(10,2) NOT NULL,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id) ON DELETE CASCADE,
    FOREIGN KEY (product_id) REFERENCES Products(product_id) ON DELETE CASCADE
);


CREATE INDEX idx_customer_email ON customers(email);
CREATE INDEX idx_customer_email ON orders(order_date);
CREATE INDEX idx_customer_email ON products(name);

DELIMITER $$
CREATE TRIGGER update_stock 
	AFTER INSERT ON OrderDetails
		FOR EACH ROW
BEGIN
    UPDATE products 
    SET stock = stock - NEW.quantity
    WHERE product_id = NEW.product_id;
END $$
DELIMITER ;


DELIMITER $$

CREATE PROCEDURE PlaceSimpleOrder(IN cust_id INT, IN prod_id INT, IN qty INT)
BEGIN
    DECLARE prod_price DECIMAL(10,2);
    DECLARE stock_available INT;
    DECLARE order_total DECIMAL(10,2);
    
    -- Start transaction
    START TRANSACTION;
    
    -- Get product price and stock
    SELECT price, stock INTO prod_price, stock_available 
    FROM Products 
    WHERE product_id = prod_id;
    
    -- Check stock availability
    IF stock_available < qty THEN
        ROLLBACK;
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Insufficient stock!';
    ELSE
        -- Calculate total price
        SET order_total = prod_price * qty;
        
        -- Insert order
        INSERT INTO Orders (customer_id, total_price) VALUES (cust_id, order_total);
        
        -- Insert order details
        INSERT INTO OrderDetails (order_id, product_id, quantity, price)
        VALUES (LAST_INSERT_ID(), prod_id, qty, prod_price);
        
        -- Update stock
        UPDATE Products SET stock = stock - qty WHERE product_id = prod_id;
        
        -- Commit transaction
        COMMIT;
    END IF;
END $$

DELIMITER ;


DELIMITER ;

CALL PlaceSimpleOrder(1, 2, 3);