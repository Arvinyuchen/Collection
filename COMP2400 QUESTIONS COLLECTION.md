# *COMP2400 QUESTIONS COLLECTION* 

## ***Lab3***

<img src="/Users/arvinyan/Library/Application Support/typora-user-images/Screenshot 2023-08-15 at 5.09.11 pm.png" alt="Screenshot 2023-08-15 at 5.09.11 pm" style="zoom:45%;" />

<img src="/Users/arvinyan/Library/Application Support/typora-user-images/Screenshot 2023-08-15 at 5.08.58 pm.png" alt="Screenshot 2023-08-15 at 5.08.58 pm" style="zoom:45%;" />

1.*Show the project numbers, names and total hours for the projects if their total hours are larger than 200* *hours.*

1. Since the desired data are seperately stored in two relations.We want to combine two relatiosn together in order to get all the data we want. 

   1. 1st Method: ***Cartesian prodcuct and Primary keys***

      - ```postgresql
        SELECT *
        FROM works_on,project 
        WHERE works_on.pno = project.pnumber;
        ```

        <img src="/Users/arvinyan/Library/Application Support/typora-user-images/Screenshot 2023-08-15 at 5.17.58 pm.png" alt="Screenshot 2023-08-15 at 5.17.58 pm" style="zoom:50%;" />

   2. 2nd Method: ***INNER JOIN***

      - ```
        SELECT *
        FROM project INNER JOIN works_on ON project.pnumber = works_on.pno
        ```

        <img src="/Users/arvinyan/Library/Application Support/typora-user-images/Screenshot 2023-08-15 at 5.18.33 pm.png" alt="Screenshot 2023-08-15 at 5.18.33 pm" style="zoom:50%;" />	

2. SInce we want to acquire the total hours of the projects if the total hours are larger than 200 hours which has conditions total  hours$>$200. Therefore, we want to restrcit the relation of our current relations. 

   1. the first Action is to achieve group them together, to apply the aggregation functions 

      1. For both approaches we use key word *GROUP BY* 

         ```postgresql
         GROUP BY pno,pnumber
         ```

         *NOTE:* ***be really careful with the attributes of group by, since we already equaled the two attributes together, we should treat them as whole, else error message would pump up***  

   2. restrict the total value that is above 200 

      1. For both approches 

         ***apply HAVING SUM(hours) >200***

         ```postgresql
         HAVING SUM(hours)>200
         ```

3.  Select the attributes we want to display from the question, we are acquiring for project number,project names and total hours.

   1. for both approaches 

      ```postgresql
       SELECT pno, pname, SUM(hours)
      ```



2. ***DISTINCT*** 

   1. *List the projects which at least on employee works on.*

      ```postgresql
      SELECT DISTINCT project.*
      FROM project INNER JOIN works_on ON project.pnumber = works_on.pno;
      ```

      Each tuple in the project relation won't be duplicated.

      <img src="/Users/arvinyan/Library/Application Support/typora-user-images/Screenshot 2023-08-15 at 6.21.36 pm.png" alt="Screenshot 2023-08-15 at 6.21.36 pm" style="zoom:50%;" />

      However, there might be more than one person are working on the one projects. Therefore, we have to use *DISTINCT*.

3. ***Subquery*** 

   *Find the highest paid employee of each department, and show their first and last names, department numbers and salaries.*

   1. ***Construct the subquery***		

      <img src="/Users/arvinyan/Desktop/Screenshot 2023-08-16 at 12.20.51 am.png" alt="Screenshot 2023-08-16 at 12.20.51 am" style="zoom:50%;" />

      ```postgresql
      (SELECT dno, MAX(salary) AS max_salary
                            FROM employee
                            GROUP BY dno) AS h
      ```

      Constrcut a subquery that finds the maximum salaries of each department.

   2. ***Create the new Cartesian Product Relation***

      Since we are selecting the $fname, lname,h.dno,max\_salary$, the max_salary is obvious does not provide the sufficient data. This is just a helper query to get maximum salaries in each department. We wanna combine $employee$ and relation $h$ together by Cartesian Prodcut. However, only limited information is correct. 

      ​	

      <u>***NOTE: max_salary is a new attribute***</u> 

      ```postgresql
      SELECT fname, lname, h.dno, max_salary
      FROM employee, (SELECT dno, MAX(salary) AS max_salary
                      // AS max_salary gives us a new attribute 
                            FROM employee
                            GROUP BY dno) AS h
      ```

      <img src="/Users/arvinyan/Library/Application Support/typora-user-images/Screenshot 2023-08-16 at 12.22.36 am.png" alt="Screenshot 2023-08-16 at 12.22.36 am" style="zoom:50%;" />

   3. ***Matching and get Correct Information*** 

      we have to use the primary keys to match the values, the cartesian product relation just provide a congragated relations

      ```postgresql
      WHERE employee.salary = h.max_salary
            AND employee.dno = h.dno
      ```

      these two lines, matches all the information and a correct relation is constructed. 

      <img src="/Users/arvinyan/Library/Application Support/typora-user-images/Screenshot 2023-08-16 at 12.23.26 am.png" alt="Screenshot 2023-08-16 at 12.23.26 am" style="zoom:50%;" />

      ***Complete*** :

      ```postgresql
      SELECT fname, lname, h.dno, max_salary
      FROM employee, (SELECT dno, MAX(salary) AS max_salary
                            FROM employee
                            GROUP BY dno) AS h
            WHERE employee.salary = h.max_salary
            AND employee.dno = h.dno;
      ```

*Q3: How many directors are there in this database who have never written a movie?*

Since the id is the primary key, it is capable to identify each tuple. Therefore, the set of directors who have wrote movie is disjoint with the set of directors who have never written a movie. Moreover, we ca use the except as the operator between two relations. 

#### ***Directly Modify the Column Selection***
```postgresql
SELECT name, 
	CASE WHEN (monthlymaintenance > 100) THEN
		'expensive'
	ELSE
		'cheap'
	END AS cost
	FROM cd.facilities; 
```
- This includes the computation in the area of the query between SELECT and FROM.
- CASE statement
  To add a 'middling' option, we would simply insert another *WHEN...THEN* section.
- *AS* operator
  This is simply used to label columns or expressions, to make them display more nicely or to make easier to reference when used as part of subquery.

#### ***LIMIT***
keyword *LIMIT* should in the end of query
```postgresql
SELECT DISTINCT surname
FROM cd.members
ORDER BY surname
LIMIT 10;
```
#### ***Rename the Colunmns Name***
Add the *AS name* after iundicating the colunms
```postgresql
SELECT bks.starttime AS start, facs.name AS name
	FROM 
		cd.facilities facs
		INNER JOIN cd.bookings bks
			ON facs.facid = bks.facid
	WHERE 
		facs.name in ('Tennis Court 2','Tennis Court 1') AND 
		bks.starttime >= '2012-09-21' AND 
		bks.starttime < '2012-09-22'
ORDER BY bks.starttime;
```

### ***The application of || in sql***
```postgresql
select distinct mems.firstname || ' ' || mems.surname as member, facs.name as facility
	from 
		cd.members mems
		inner join cd.bookings bks
			on mems.memid = bks.memid
		inner join cd.facilities facs
			on bks.facid = facs.facid
	where
		facs.name in ('Tennis Court 2','Tennis Court 1')
order by member, facility          
```
*NOTE: the || operator is used to concatenate strings.*

### ***Is not Null and the GROUP BY***
```postgresql
select recommendedby, count(*) 
	from cd.members
	where recommendedby is not null
	group by recommendedby
order by recommendedby;       
```
***NOTE:Using the GROUP BY the count or aggregate functions after the SELECT statement before the FROM statement is manipulating on the grouped cluster not the whole relation***

### ***The Application of Subqueries***
```postgresql
select name, revenue from (
select f.name,sum(slots * case when b.memid =0 
from cd.facilities f inner join cd.bookings b
on f.facid = b.facid
group by f.name) as agg
where revenue < 1000
order by revenue;
```
We used the subquery to produce a relation with name of facilities and 
the sum of revenue. Since we sql does not support us to putting column names in the HAVING clause which throw out the error message: "ERROR: column "revenue" does not exist. ". Therefore, we need to use the outer query to filter and order the rows. 

### ***The Introduction of With***
```postgresql
select facid, sum(slots) as totalslots
	from cd.bookings
	group by facid
	having sum(slots) = (select max(sum2.totalslots) from
		(select sum(slots) as totalslots
		from cd.bookings
		group by facid
		) as sum2);
```
In this example, we could also use the HAVING. However, using the HAVING clause consequences the ugly and untidy query.

### ***The correlated Subqeury***
The main purpose of correlated subquery is to use two relations and connect them with the primary-key trying to find information that is located in other relations which couldn't be found in current relation. 
```postgresql
SELECT dname
FROM department AS d 
WHERE EXISTS (SELECT * FROM employee AS e 
              WHERE d.dnumber = e.dno AND e.salary < 50000);
```

### ***The introduction of Window Function***
```postgresql 
select column1 column2 windowfunction() OVER(partition by patition.expression
ORDER BY sort_expression [Windoe Frame]) AS calculated_column 
FROM 
   table_name
```
Window functions operate on the result set of (sub)-query, after the WHERE clause and all standard aggregation. They operate on a window of data.
- column1, column2, etc.: These are the columns you want to retrieve in the result set.
window_function(): The specific window function you're using (e.g., ROW_NUMBER(), SUM(), etc.).
- PARTITION BY: Divides the result set into partitions based on specific columns. The window function operates separately within each partition.
- ORDER BY: Specifies the order of rows within each partition.
- ROWS/RANGE window_frame: Defines the range of rows that the window function should consider for calculations.

### ***Comparison between CTEs and Windows Functions***
#### *Window Function*
```postgresql
select facid, total from(
   select facid,sum(slots) total, rank() over(order by sum(slots) desc) rank 
         from cd.bookings
         group  y facid) as ranked 
         where rank = 1
```
#### *CTEs/ WITH*
```postgresql 
with whole as (select facid, sum(slots) as totalslots
      from cd.bookings
      group by facid)

select facid, totalslots 
from whole 
where totalslots = (select max(totalslots) from whole);
```

The main difference is the method of acquiring the relation with relation schema 
with facid and sum of slots. We can do more manipulation on data if we use window 
function.