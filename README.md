# SQL-Zero-to-Hero-Notes
A log of my work during the Udemy SQL Bootcamp: Go from Zero to Hero by Jose Portilla
## Udemy Course Assessments and Challenges
To further my skills and test my SQL knowledge I completed the Udemy SQL Bootcamp: Go from Zero to Hero by Jose Portilla.

The course used both a dvdrental database and exercises database for example data and presented different business questions throughout the course that mimic things that would be encountered in the day to day work of a data analyst. Toward the end of the course I also learned to create my own database, school, to practice creating and modifying tables.

I documented my work that I did in pgAdmin 4 throughout the course during the challenges and assessment tests and compiled it into this notebook.

## Assessment Test 1
#### Question 1
Return the customer IDs of customers who have spent at least $110 with staff member who has an ID of 2: 
```
SELECT customer_id, SUM(amount)
FROM payment
WHERE staff_id = 2
GROUP BY customer_id
HAVING SUM(amount) >= 110;
```
#### Question 2
How many films begin with the letter J?
```
SELECT COUNT(*)
FROM film
WHERE title iLIKE 'j%';
```
Note that I used * in my COUNT call but the query would return the same result if I had called for any column in the table. COUNT is not dependent on the column name because it counts the number of rows in the table that meet the condition(s) in the query. Using title in place of * would be helpful if I ever return to this query at a later time as it may help remind me of the reason for running the query.

Also note that I used iLIKE instead of LIKE so I did not need to worry about J being case sensitive.

#### Question 3
What customer has the highest customer ID number whose name starts with an 'E' and has an address ID lower than 500?
```
SELECT first_name, last_name
FROM customer
WHERE first_name iLIKE 'e%' AND address_id < 500
ORDER BY customer_id DESC
LIMIT 1;
```
## JOIN Challenge
#### Question 1
California sales tax laws have changed and we need to alert our customers to this through email. What are the emails of the customers who live in California?
```
SELECT email,district
FROM customer
INNER JOIN address
ON customer.address_id = address.address_id
WHERE district = 'California';
```
#### Question 2
A customer walks in and is a huge Nick Wahlberg fan and wants to know which movies he's in - get a list of all the movies Nick Wahlberg has been in.
```
SELECT title, first_name, last_name
FROM actor
INNER JOIN film_actor
ON actor.actor_id = film_actor.actor_id
INNER JOIN film
ON film_actor.film_id = film.film_id
WHERE actor.actor_id = 2;
```
Note that the WHERE statement can also be filtered by:
WHERE first_name = 'Nick' AND last_name = 'Wahlberg';

## Assessment Test 2
#### Question 1
How can you retrieve all the information from the cd.facilities table?
```
SELECT *
FROM cd.facilities;
```
#### Question 2
You want to print out a list of all of the facilities and their cost to members. How would you retrieve a list of only facility names and costs?
```
SELECT name,membercost
FROM cd.facilities;
```
#### Question 3
How can you produce a list of facilities that charge a fee to members?
```
SELECT name,membercost
FROM cd.facilities
WHERE membercost > 0;
```
#### Question 4
How can you produce a list of facilities that charge a fee to members, and that fee is less than 1/50th of the monthly maintenance cost? Return the facid, facility name, member cost, and monthly maintenance of the facilities in question.
```
SELECT facid,name,membercost,monthlymaintenance
FROM cd.facilities
WHERE membercost > 0 AND membercost < (monthlymaintenance/50.0);
```
#### Question 5
How can you produce a list of all facilities with the word 'Tennis' in their name?
```
SELECT *
FROM cd.facilities
WHERE name iLIKE '%tennis%';
```
#### Question 6
How can you retrieve the details of facilities with ID 1 and 5? Try to do it without using the OR operator.
```
SELECT *
FROM cd.facilities
WHERE facid IN (1,5);
```
At first when solving this question I used UNION. My first attempt (shown directly below) does return the same results, but it's best practice to be as succinct as possible and using the IN operator leaves less room for error. It's also possible to use OR in the WHERE statement ("WHERE facid = 1 OR facid = 5") but IN is still preferred for the same reason.
```
SELECT *
FROM cd.facilities
WHERE facid = 1
UNION
SELECT *
FROM cd.facilities
WHERE facid = 5;
```
#### Question 7
How can you produce a list of members who joined after the start of September 2012? Return the memid, surname, firstname, and joindate of the members in question.
```
SELECT memid,surname,firstname,joindate
FROM cd.members
WHERE joindate >= '2012-09-01';
```
#### Question 8
How can you produce an ordered list of the first 10 surnames in the members table? The list must not contain duplicates.
```
SELECT DISTINCT surname
FROM cd.members
ORDER BY surname
LIMIT 10;
```
#### Question 9
You'd like to get the signup date of your last member. How can you retrieve this information?
```
SELECT joindate
FROM cd.members
ORDER BY joindate DESC
LIMIT 1;
```
Another option is to use the MAX function (shown directly below) but when I initially sovled this query I ORDER BY DESC and LIMIT 1.
```
SELECT MAX(joindate)
FROM cd.members;
```
#### Question 10
Produce a count of the number of facilities that have a cost to guests of 10 or more.
```
SELECT COUNT(facid)
FROM cd.facilities
WHERE guestcost >= 10;
```
#### Question 11
Produce a list of the total number of slots booked per facility in the month of September 2012. Produce an output table consisting of facility id and slots, sorted by the number of slots.
```
SELECT facid,SUM(slots) AS total_slots
FROM cd.bookings
WHERE starttime >= '2012-09-01' AND starttime < '2012-10-01'
GROUP BY facid
ORDER BY SUM(slots);
```
#### Question 12
Produce a list of facilities with more than 1000 slots booked. Produce an output table consisting of facility id and total slots, sorted by facility id.
```
SELECT facid,SUM(slots) AS total_slots
FROM cd.bookings
GROUP BY facid
HAVING SUM(slots) > 1000
ORDER BY facid;
```
#### Question 13
How can you produce a list of the start times for bookings for tennis courts, for the date '2012-09-21'? Return a list of start time and facility name pairings, ordered by the time.

Note that because starttime and name are unique to only one table I did not need to specify cd.bookings.starttime and cd.facilities.name:
```
SELECT starttime,name
FROM cd.bookings
INNER JOIN cd.facilities ON
cd.bookings.facid = cd.facilities.facid
WHERE starttime >= '2012-09-21' AND starttime < '2012-09-22'
AND name iLIKE '%tennis court%'
ORDER BY starttime;
```
I also could have used IN instead of iLIKE for facid or name:
```
SELECT starttime,name
FROM cd.bookings
INNER JOIN cd.facilities ON
cd.bookings.facid = cd.facilities.facid
WHERE starttime >= '2012-09-21' AND starttime < '2012-09-22'
AND cd.bookings.facid IN (0,1)
ORDER BY starttime;
```
```
SELECT starttime,name
FROM cd.bookings
INNER JOIN cd.facilities ON
cd.bookings.facid = cd.facilities.facid
WHERE starttime >= '2012-09-21' AND starttime < '2012-09-22'
AND cd.facilities.name IN ('Tennis Court 1','Tennis Court 2')
ORDER BY starttime;
```
#### Question 14
How can you produce a list of the start times for bookings by members named 'David Farrell'?
```
SELECT starttime,firstname,surname
FROM cd.bookings
INNER JOIN cd.members ON
cd.bookings.memid = cd.members.memid
WHERE firstname = 'David' AND surname = 'Farrell';
```
## Assessment Test 3
Directions for the task:
Create a new database called "School" this database should have two tables: teachers and students. The students table should have columns for student_id, first_name,last_name, homeroom_number, phone, email, and graduation year. The teachers table should have columns for teacher_id, first_name, last_name, homeroom_number, department, email, and phone. The constraints are mostly up to you, but your table constraints do have to consider the following:
* We must have a phone number to contact students in case of an emergency.
* We must have ids as the primary key of the tables
* Phone numbers and emails must be unique to the individual.

Step one was to create the students table, set appropriate data types, and indicate any necessary constraints:

Note that the student_id is the primary key and contraints were set so that first_name, last_name, and phone can not be null, and phone number and email must be unique. For the graduation_date I chose DATE instead of TIMESTAMP as I really only am concerned with year for graduation and not what time that data was collected.
```
CREATE TABLE students(
	student_id SERIAL PRIMARY KEY,
	first_name VARCHAR(50) NOT NULL,
	last_name VARCHAR(50) NOT NULL,
	homeroom_number SMALLINT,
	phone VARCHAR(20) NOT NULL UNIQUE,
	email VARCHAR(100) UNIQUE,
	graduation_year DATE
);
```
Note that I could have also selected INTEGER for the data type because only the year (no day or month) is given in a later task to insert into the table. However, I selected DATE for now in case we ever decide that we want to add month/day to the data.

Step two was to create the teachers table:
```
CREATE TABLE teachers(
	teacher_id SERIAL PRIMARY KEY,
	first_name VARCHAR(50) NOT NULL,
	last_name VARCHAR(50) NOT NULL,
	homeroom_number SMALLINT,
	department VARCHAR(50),
	email VARCHAR(100) UNIQUE,
	phone VARCHAR(20) UNIQUE
);
```
Additional task directions: Once you've made the tables, insert a student named Mark Watney (student_id=1) who has a phone number of 777-555-1234 and doesn't have an email. He graduates in 2035 and has 5 as a homeroom number.
```
INSERT INTO
students(first_name,last_name,homeroom_number,phone,graduation_year)
VALUES ('Mark','Watney',5,'7775551234','2035-01-01');
```
Note that since student_id is set as a serial data type I did not have to enter any information when inserting the requested data into the table, PostgreSQL will autogenerate an integer as student_id.

Final task directions: Then insert a teacher named Jonas Salk (teacher_id = 1) who as a homeroom number of 5 and is from the Biology department. His contact info is: jsalk@school.org and a phone number of 777-555-4321.
```
INSERT INTO teachers(first_name,last_name,homeroom_number,department,email,phone)
VALUES ('Jonas','Salk',5,'Biology','jsalk@school.org','7775554321');
```
## CASE Challenge
Use CASE and the dvdrental database to create a list of the number of movies that have R, PG, and PG-13 ratings:
```
SELECT
SUM(CASE rating
	WHEN 'R' THEN 1
	ELSE 0
END) AS r,
SUM(CASE rating
	WHEN 'PG' THEN 1
	ELSE 0
END) AS pg,
SUM(CASE rating
	WHEN 'PG-13' THEN 1
	ELSE 0
END) AS pg13
FROM film;
```
# Other Tasks and Notes
Everything remaining in this notebook reflects notes that were taken from example queries presented by the instructor during this course.

## Timestamps and TO_CHAR.
TO_CHAR converts a timestamp to a string data type. In the following example I chose to convert to US standard date format of MM-DD-YYYY, however the string can be adjusted to reflect whatever is most appropriate for the query. If I wanted European date format I could have entered DD-MM-YYYY.

It's also worth noting that with TO_CHAR you are converting into a string data type and can choose for the output to return however you like, so I could have used / instead of - to separate month-day-year.
```
SELECT TO_CHAR(payment_date,'MM-dd-YYYY')
FROM payment;
```
## Timestamps and EXTRACT
#### Question 1

During which months did payment occur? Format answer to return back the full month name.
```
SELECT DISTINCT(TO_CHAR(payment_date,'Month'))
FROM payment;
```
#### Question 2

How many payments occured on a Monday?

I got answer 2948 by using the below query:
```
SELECT COUNT(TO_CHAR(payment_date,'Day'))
FROM payment
WHERE TO_CHAR(payment_date,'Day') iLIKE 'monday%';
```
The same result can also be found using the below query and using a format modifier on Day and using = 'Monday' instead of using iLIKE:
```
SELECT COUNT(TO_CHAR(payment_date,'Day'))
FROM payment
WHERE TO_CHAR(payment_date,'fmDay')='Monday';
```
The same result can also be found using EXTRACT instead of TO_CHAR using the below query:

Note that PostgreSQL recognizes Sunday as 0 in regards to DOW.
```
SELECT COUNT(EXTRACT(DOW FROM payment_date))
FROM payment
WHERE EXTRACT(DOW FROM payment_date)=1;
```
## Mathematics and Calculations
Let the query do your math for you - you can calculate percentages, round, etc. all within a query.
The below query calculates the percentage of rental rate to replacement cost for the films and rounds it to 4 decimal places:
```
SELECT ROUND(rental_rate/replacement_cost,4)*100 AS percent_cost
FROM film;
```
If the movie rental store wanted to require a deposit be left on each film rented out, an analyst could use calculations within a query to do so. To create a deposit amount that is 10% of the percent_cost calculated above the following can be run:
```
SELECT ROUND(0.1*replacement_cost,2) AS deposit
FROM film;
```
## Concatenation

Use || to concatenate columns.

If a database has first and last names stored in separate columns and they need to be combined, concatenation is the best way to do so:
```
SELECT first_name || ' ' || last_name AS full_name
FROM customer;
```
It can also be used to create new columns with information that will be helpful to the analyst - for example if there are employee's first and last names stored in a database and an analyst wants to create emails for each employee based on names.
```
SELECT LOWER(LEFT(first_name,1)) || LOWER(last_name) || '@gmail.com' AS custom_email
FROM customer;
```
Note that in the above example query LOWER is used to make sure everything appears in lower case and LEFT is used to retrieve only the first letter of the first name and combine it with the entire last name to generate a gmail for each employee based on name.

## Subqueries

A subquery is **always** executed first. If a subquery returns a single value then a comparison operator can be used on it (>,<,=).

If an analyst wanted to return a list of all movies with a rental rate that is higher than the average rental rate of all films, a comparison operator and subquery can be used:
```
SELECT title,rental_rate
FROM film
WHERE rental_rate >
(SELECT AVG(rental_rate) FROM film);
```
Note that if a subquery returns multiple values, the IN operator must be used. 

Below is an example where 2 tables were joined so that film id and titles were shown for any movie that was returned between May 29 and May 30 of 2005. Since the subquery yields more than one result, IN was used in the WHERE statement to select certain fields that satisfy the results of the subquery.

We also ordered results by title.
```
SELECT film_id,title
FROM film
WHERE film_id IN
(SELECT inventory.film_id
FROM rental
INNER JOIN inventory ON inventory.inventory_id = rental.inventory_id
WHERE return_date BETWEEN '2005-05-29' AND '2005-05-30')
ORDER BY title;
```
## EXISTS 
EXISTS checks to see if any rows are returned in a subquery.
NOT EXISTS can be used to return anything that is not found in a subquery.

Below is an example of a query that returns results for customers who have made at lease one payment greater than $11.
```
SELECT first_name,last_name
FROM customer AS c
WHERE EXISTS
(SELECT * FROM payment AS p
 WHERE p.customer_id = c.customer_id
 AND p.amount > 11);
```
## Self JOIN
Self JOINs are helpful when comparing heirarchical data or when comparing rows within one table. 

The following example uses Self JOIN on the film table from the dvdrental database to create a list of films that all have the same lengths:
```
SELECT f1.title, f2.title, f1.length
FROM film AS f1
INNER JOIN film AS f2 ON
f1.film_id != f2.film_id
AND f1.length = f2.length;
```
