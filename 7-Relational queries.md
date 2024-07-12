SQL is a relational data management system. Each table can reference another table. In a social network a user could be connected to a post, for example. We'll also run queries that connect those tables together.

We're also going to talk about inner joins, and outer joins, that are divided between right outer joins and left outer joins.

## Guide to SQL Inner Joins

We need to join tables quite often. The most basic JOIN is an inner join.

```
SELECT * 
FROM guides
INNER JOIN users;
ON guides.guides_users_id = users.users_id;
```

This means that we want to join the users table to the guides one, by using the foreign key of the guides table and referring to the column it's tied to, users_id (first, the two tables are selected, and then the two columns).

Now the two tables are joined.

> Instead of INNER JOIN you can say just JOIN and it will also work.

However, there are columns that we don't need, so we can left them out of the selection.

```
SELECT guides_title, guides_revenue, users_name, users_email;
```

Earlier we said that aliases are useful when joining tables. We'll do that now. When you have a lot of data, put every item in a separate line. So pass the params with the ``alias.column`` format, and then select the table, space, pass the alias in (in this case, just g). Join that to the users table and pass its alias as well, then repeat the process from above (we want to join the users table to the guides one, by using the foreign key of the guides table and referring to the column it's tied to, users_id (first, the two tables are selected, and then the two columns. The columns carry the table aliases).

```
SELECT 
g.guides_title, 
g.guides_revenue, 
u.users_name, 
u.users_email
FROM guides g
JOIN users u
ON g.guides_users_id = u.users_id
ORDER BY g.guides_revenue DESC;
```

We can also use other functions to order the data.

## How to Add Multiple Conditionals to an Inner Join Query in SQL

In this guide we'll walk through how to implement multiple conditionals from various tables to build an inner join based result set, and how we can leverage a WHERE clause inside of a join statement. I am going to say select all from guides g.

```
SELECT *
FROM guides g
JOIN users u
ON g.guides_users_id = u.users_id
WHERE u.users_name = "Tiffany";
```

This code is going to find all the guides where the username is Tiffany, and also joins the two tables. This is the result:

We can change the search criteria too:

```
SELECT *
FROM guides g
JOIN users u
ON g.guides_users_id = u.users_id
WHERE g.guides_revenue > 700
AND u.users_name = "Tiffany"
OR u.users_name = "Kristine";
```

## How to Join 3 Tables Together with an Inner Join in SQL

This guide provides a walkthrough for how to join three database tables together using a SQL inner join. Additionally, we'll examine the possible pitfalls related to running inner join queries with more than two tables.

In order to join more than 2 tables, we'll join two first, and then the other one:

```
SELECT *
FROM users u
JOIN guides g
ON g.guides_users_id = u.users_id
JOIN addresses a
ON a.addresses_users_id = u.users_id
ORDER BY g.guides_revenue DESC;
```

## Inner vs outer joins

```
SELECT *
FROM guides g
JOIN users u
ON g.guides_users_id = u.users_id;
```

Given that base code, it's important to know that inner joins won't bring users that didn't write any guides. In other words, inner joins won't join null values. Outer joins will, though.

In order to use outer joins, we'll write:

```
SELECT *
FROM guides g
RIGHT JOIN users u
ON g.guides_users_id = u.users_id;
```

There are also left joins. The order of joined tables matter, so the result is different.

* (INNER) JOIN: Returns records that have matching values in both tables.


![inner join](https://learnsql.es/blog/explicacion-de-los-tipos-de-join-en-sql/1-big.webp)

* LEFT (OUTER) JOIN: Returns all records from the left table, and the matched records from the right table.

![left outer joim](https://learnsql.es/blog/explicacion-de-los-tipos-de-join-en-sql/3-big.webp)

* RIGHT (OUTER) JOIN: Returns all records from the right table, and the matched records from the left table.

![right outer join](https://learnsql.es/blog/explicacion-de-los-tipos-de-join-en-sql/4-big.webp)

* FULL (OUTER) JOIN: Returns all records when there is a match in either left or right table.

![full outer join](https://learnsql.es/blog/explicacion-de-los-tipos-de-join-en-sql/5-big.webp)



> Right and left outer joins can be done also by changing the order of the tables. Thus, we'd only have to use one type.

```
SELECT *
FROM users u
LEFT JOIN guides g
ON g.guides_users_id = u.users_id;
```

This code will show the users in the left side and the guides on the right. It also displays null values in the right guide,because we're prioritizing the left side.

```
SELECT *
FROM guides g 
LEFT JOIN users u
ON g.guides_users_id = u.users_id;			
```

This code, however, will put the guides in the left and the users in the right. It doesn't display null values because all the rows in the left (guides) have a user. 

> Deoending on which side we're choosing, null values will display (or not) because the main table varies. If a table of users is in the left and we're using a left join, null values will display because we're selecting everything. If we change the order of the tables or use a right join, null values won't display because the main table is changed and it only shows coincidences on the right side.

## How to Build a Summary Report of Data from 3 Tables in SQL

This comprehensive guide walks through how to join three tables and utilize the COALESCE function in order to accurately count records in each table and format the data for users.

```
SELECT *
FROM users u
JOIN addresses a
ON addresses_users_id = u.users_id
JOIN guides g
ON g.guides_users_id = u.users_id
```

You can also select multiple columns and even perform calculations from different tables if you join them later. Remember that grouping and ordering items come last:

```
SELECT 
	c.course_name,
    avg(grade_value) as grade_avg
FROM project_grades g
JOIN project_courses c on c.course_grades_id = g.grade_id
GROUP BY course_name
ORDER BY grade_avg asc
```

Given this code, we get duplicate users. We don't want that. So, for example, we only have two users that were shown and that is because our other sample users either only have addresses associated with their names or they only have guides but they don't have both and the only users that will show up in a multiple join inner join type table query are going to be the users that have data and a reference in all of the tables. It shows if there's data, regardless of it's duplicated or not.

> We'll create a summary report with all of these tables. First, we'll select the users_email, and then we'll use a function called COALESCE. It's similar to the count function, but won't count null values.

```
SELECT
  u.users_email AS 'Email',
  COALESCE(g.guide_count, 0) AS guide_count,
  COALESCE(a.address_count, 0) AS address_count
FROM users u
  LEFT JOIN (
    SELECT COUNT(*) AS guide_count, guides_users_id
    FROM guides
    GROUP BY guides_users_id
  ) AS g
  ON g.guides_users_id = u.users_id
  LEFT JOIN (
    SELECT COUNT(*) AS address_count, addresses_users_id
    FROM addresses
    GROUP BY addresses_users_id
  ) AS a
  ON a.addresses_users_id = u.users_id
ORDER BY u.users_email;
```

COALESCE takes two arguments. The first is the thing that will be counted and that appears in subqueries because it's what is selected, the second param is the value that null will take. Then we select the main table, users ( alias u).

Then we proceed to do an outer join where we select and count the guide_count (of the coalesce function) and the foreign key, then we select the secondary table (the table that holds the data we're querying) and group by the foreign key. Then we alias the outer joins and join everything to the foreign key. Finally, we order the data.