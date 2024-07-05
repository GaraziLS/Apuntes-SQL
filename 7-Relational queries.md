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

