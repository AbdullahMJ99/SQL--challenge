# SQL--challenge
Background
It’s been two weeks since you were hired as a new data engineer at Pewlett Hackard (a fictional company). Your first major task is to do a research project about people whom the company employed during the 1980s and 1990s. All that remains of the employee database from that period are six CSV files.

For this project, you’ll design the tables to hold the data from the CSV files, import the CSV files into a SQL database, and then answer questions about the data. That is, you’ll perform data modeling, data engineering, and data analysis, respectively.


![image](https://github.com/AbdullahMJ99/SQL--challenge/assets/135548699/881aa3b9-985b-40e1-9280-3b9a1d87a1f4)

**Creating Tables**

CREATE TABLE departments (
	dept_no VARCHAR(4) PRIMARY KEY,
	dept_name VARCHAR(50) NOT NULL UNIQUE
);

CREATE TABLE titles(
	title_id VARCHAR(5) PRIMARY KEY,
	title VARCHAR (50) NOT NULL UNIQUE  
);

CREATE TABLE employees (
	emp_no INT PRIMARY KEY,
	emp_title_id VARCHAR(5) NOT NULL,
	birth_date DATE NOT NULL,
	first_name VARCHAR(50) NOT NULL,
	last_name VARCHAR(50) NOT NULL,
	sex CHAR,
	hire_date DATE NOT NULL,
	FOREIGN KEY (emp_title_id) REFERENCES titles (title_id)
);

CREATE TABLE dept_emp(
	emp_no INT NOT NULL, 
	dept_no VARCHAR(4) NOT NULL,
	PRIMARY KEY (emp_no, dept_no),
	FOREIGN KEY (emp_no) REFERENCES employees (emp_no),
	FOREIGN KEY (dept_no) REFERENCES departments (dept_no)
);

CREATE TABLE dept_manager (
	dept_no VARCHAR(4)  NOT NULL,
	emp_no INT NOT NULL,
	PRIMARY KEY (emp_no, dept_no),
	FOREIGN KEY (dept_no) REFERENCES departments (dept_no),
	FOREIGN KEY (emp_no) REFERENCES employees (emp_no)
);

CREATE TABLE salaries (
	emp_no INT primary key,
	salary INT NOT NULL,
	FOREIGN KEY (emp_no) REFERENCES employees(emp_no)
);


**Analysis:**


--List the employee number, last name, first name, sex, and salary of each employee
SELECT e.emp_no,last_name,first_name,sex,salary 
FROM employees e, salaries s
WHERE s.emp_no=e.emp_no;

--List the first name, last name, and hire date for the employees who were hired in 1986
SELECT first_name,last_name, hire_date FROM employees
WHERE EXTRACT (YEAR FROM hire_date)='1986';

--List the manager of each department along with their department number, department name, employee number, last name, and first name
SELECT d.dept_no,dept_name,e.emp_no,last_name,first_name
FROM departments d, employees e, dept_manager dm
WHERE (d.dept_no=dm.dept_no)AND (e.emp_no=dm.emp_no);

--List the department number for each employee along with that employee’s number, last name, first name, and department name
SELECT d.dept_no,e.emp_no,last_name,first_name,dept_name 
FROM departments d, employees e, dept_emp de
WHERE (d.dept_no=de.dept_no)and (e.emp_no=de.emp_no);

--List first name, last name, and sex of each employee whose first name is Hercules and whose last name begins with the letter B
SELECT first_name,last_name,sex 
FROM employees
WHERE (first_name='Hercules') AND (last_name LIKE 'B%');

--List each employee in the Sales department, including their employee number, last name, and first name
SELECT e.emp_no,last_name,first_name
FROM departments d, employees e, dept_emp de
WHERE (d.dept_no=de.dept_no)AND (e.emp_no=de.emp_no)AND (dept_name='Sales');

--List each employee in the Sales and Development departments, including their employee number, last name, first name, and department name
SELECT e.emp_no,last_name,first_name,dept_name
FROM departments d, employees e, dept_emp de
WHERE (d.dept_no=de.dept_no)AND (e.emp_no=de.emp_no)AND ((dept_name='Sales')OR(dept_name='Development'));

--List the frequency counts, in descending order, of all the employee last names (that is, how many employees share each last name)
SELECT last_name, COUNT(last_name) FROM employees 
GROUP BY last_name
ORDER BY last_name;



