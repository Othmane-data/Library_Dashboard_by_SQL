# Library System Management Dashboard Data Analysis by SQL

![](library.jpg)
--

## Introduction

This project demonstrates the implementation of a Library Management System using SQL. It includes creating and managing tables, performing CRUD operations, and executing advanced SQL queries. The goal is to showcase skills in database design, manipulation, and querying.

## Objectives

1.	Set up the Library Management System Database: Create and populate the database with tables for branches, employees, members, books, issued status, and return status.
2.	CRUD Operations: Perform Create, Read, Update, and Delete operations on the data.
3.	CTAS (Create Table As Select): Utilize CTAS to create new tables based on query results.
4.	Advanced SQL Queries: Develop complex queries to analyze and retrieve specific data.

   
## Project Structure

### Database Setup

 ![](erd.database library.pgerd)
 
___Table Creation:___ Created tables for branches, employees, members, books, issued status, and return status. Each table includes relevant columns and relationships.

```sql
CREATE DATABASE library1;
-- Create table "Branch"

drop table if exists branch;
create table branch
(branch_id VARCHAR(15) primary key,
manager_id varchar(15),
branch_address varchar(55),
contact_no varchar(15)
);

-- Create table "Employee"

drop table if exists employees;
create table employees
(emp_id varchar(10) primary key,
emp_name varchar(25),
position varchar(15),
salary numeric,
branch_id varchar(25)

);

-- Create table "Books"	

drop table if exists books;
create table books
(isbn varchar(20) primary key,
book_title varchar(75),
category varchar(20),
rental_price FLOAT,
status varchar(15),
author varchar(35),
publisher varchar(55)
);

-- Create table "members"

drop table if exists members;
create table members
(member_id varchar(10) primary key,
member_name varchar(25),
member_address varchar(75),
reg_date DATE
);

-- Create table "issued_status"

drop table if exists issued_status;
create table issued_status
(
issued_id varchar(10) primary key,
issued_member_id varchar(10),
issued_book_name varchar(75),
issued_date DATE,
issued_book_isbn varchar(25),
issued_emp_id varchar(10) 
);

-- Create table "return_status"

drop table if exists return_status;
create table return_status
(return_id varchar(10)primary key,
issued_id varchar(10),
return_book_name varchar(75),
return_date DATE,
return_book_isbn varchar(20)
);
```

__Foreign key__

```sql
--Foreign key  "members"
alter table issued_status
add constraint fk_members
foreign key (issued_member_id)
references members (member_id);

--Foreign key  "books"
alter table issued_status
add constraint fk_books
foreign key (issued_book_isbn)
references books (isbn);

--Foreign key  "employees"
alter table issued_status
add constraint fk_employees
foreign key (issued_emp_id)
references employees (emp_id);

--Foreign key  "branch"
alter table employees
add constraint fk_branch
foreign key (branch_id)
references branch (branch_id);

--Foreign key  "employees"
alter table issued_status
add constraint fk_employees
foreign key (issued_emp_id)
references employees (emp_id);

--Foreign key  "issued_status"
alter table return_status
add constraint fk_issued_status
foreign key (issued_id)
references issued_status(issued_id);

--Foreign key  "books"
add constraint fk_books
foreign key (return_book_isbn)
references books(isbn));
--ALTER TABLE return_status
DROP CONSTRAINT fk_books;

```



___Task 1. Create a New Book Record___ -- "978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', 6.00, 'yes', 'Harper Lee', 'J.B. Lippincott & Co.')"

```sql
insert into books (isbn, book_title, category, rental_price, status, author, publisher)
values ('978-1-60129-456-2', 'To Kill a Mockingbird', 'Classic', '6.00', 'yes', 'Harper Lee', 'J.B. Lippincott & Co.');
select * from books
```

___Task 2. Update an Existing Member's Address___

```sql
update members
set member_address = '125 Main st'
where member_id = 'C101';
select * from members

```

___Task 3. Delete a Record from the Issued Status Table___

___Objective:___ Delete the record with issued_id = 'IS121' from the issued_status table.

```sql
delete from issued_status
where issued_id = 'IS121'
select * from issued_status
```

___Task 4. Retrieve All Books Issued by a Specific Employee___ 

___Objective:___ Select all books issued by the employee with emp_id = 'E101'.

```sql
select issued_emp_id,
emp_name,
issued_book_name
from employees
left join issued_status on employees.emp_id=issued_status.issued_emp_id
where employees.emp_id = 'E101'
```

___Task 5. List Members Who Have Issued More Than One Book___ 

___Objective:___ Use GROUP BY to find members who have issued more than one book.

```sql
select
member_name,
count(issued_member_id) as count_issued
from members
left join issued_status on issued_status.issued_member_id=members.member_id
group by member_name
having count(issued_member_id) > 1
order by 2 desc
```

#### CTAS (Create Table As Select)

___Task 6. Create Summary Tables: Used CTAS to generate new tables based on query results each book and total book_issued_cnt**__

```sql
create table book_issued_cnt as
select isbn, book_title, count(ist.issued_id) as issue_count
from issued_status as ist
join books as b
on 
ist.issued_book_isbn= b.isbn
group by b.isbn,b.book_title

```

#### Data Analysis & Findings

___The following SQL queries were used to address specific questions:___

___Task 7. Retrieve All Books in a Specific Category___

```sql
select * from books
where category = 'Classic'
```

___Task 8: Find Total Rental Income by Category:___

```sql
select b.category, 
sum(b.rental_price),
count(*)
from 
issued_status as ist
join books as b
on ist.issued_book_isbn= b.isbn
group by 1

select category, 
sum(rental_price),
count(*)
from books 
group by 1

```

___Task 9 :List Members Who Registered in the Last 1000 Days:___

```sql
select * 
from 
members
where 
reg_date >= current_date - interval '1000 days'

```

___Task 10 :List Employees with Their Branch Manager's Name and their branch details:___

```sql
select e1.*,
b.manager_id,
e2.emp_name as manager

from 
employees as e1
join 
branch as b 
on e1.branch_id=b.branch_id
join 
employees as e2
on e2.emp_id=b.manager_id

```

___Task 11. Create a Table of Books with Rental Price Above a Certain Threshold 7 dollars:___

```sql
create table rental_books_price as
(select * from books
where rental_price >7)

```

___Task 12. Retrieve the List of Books Not Yet Returned___

```sql
select *
 --distinct issued_book_name 
from 
issued_status as i
left join 
return_status as r
on i.issued_id=r.issued_id
--where return_id is null

```

#### Advanced SQL Operations

```sql
select * from books
select * from branch
select * from employees
select * from issued_status
select * from members
select * from return_status

```


___Task 13. Identify Members with Overdue Books___

__Write a query to identify members who have overdue books (assume a 30-day return period). 
___Display the member's_id, member's name, book title, issue date, and days overdue.

```sql
select 
iss.issued_member_id,
mb.member_name,
bk.book_title,
iss.issued_date,
current_date-(iss.issued_date) as days_overdue
from 
issued_status as iss
join
books as bk on 
bk.isbn=iss.issued_book_isbn
join
 members as mb on 
mb.member_id=iss.issued_member_id
left join
 return_status as rs on
 iss.issued_id=rs.issued_id
where 
rs.return_date is null
and
(current_date-(iss.issued_date))> 30

```

___Task 14. Branch Performance Report___

Create a query that generates a performance report for each branch, showing the number of books issued, the number of books returned, and the total revenue generated from book rentals.

```sql
create table performance_table as
select
 br.branch_id,
br.manager_id,
count(iss.issued_id) as count_book_id,
count(rs.return_id) as count_book_return,
sum(bk.rental_price) as sum_retal
from
 issued_status as iss
join
 employees as em
on iss.issued_emp_id=em.emp_id
join 
branch as br
on em.branch_id=br.branch_id
left join
 return_status as rs
on iss.issued_id=rs.issued_id
join 
books as bk
on iss.issued_book_isbn=bk.isbn
group by 1,2;

select * from performance_table

```

___Task 15. CTAS: Create a Table of Active Members___

Use the CREATE TABLE AS (CTAS) statement to create a new table active_members containing members who have issued at least one book in the last 10 months.

```sql
create table  active_members as 
select * 
from 
members
where 
member_id in (select distinct issued_member_id from issued_status
where issued_date >= current_date - interval '10 month')

select * from active_members

```

___Task 16. Find Employees with the Most Book Issues Processed___
Write a query to find the top 3 employees who have processed the most book issues. Display the employee name, number of books processed, and their branch.


```sql
select 
em.emp_name,
count(issued_emp_id) as number_of_book_proced,
br.*
from issued_status as iss
join 
employees as em
on
iss.issued_emp_id=em.emp_id
join 
branch as br
on 
em.branch_id=br.branch_id
group by 1,3
order by 2 desc
limit 3;

```

### Reports

•	Database Schema: Detailed table structures and relationships.
•	Data Analysis: Insights into book categories, employee salaries, member registration trends, and issued books.
•	Summary Reports: Aggregated data on high-demand books and employee performance.

### Conclusion

This project demonstrates the application of SQL skills in creating and managing a library management system. It includes database setup, data manipulation, and advanced querying, providing a solid foundation for data


