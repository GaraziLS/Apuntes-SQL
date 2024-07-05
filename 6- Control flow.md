This section of the course covers control flow in MySql, in other words, conditionals.

## Defining Aliases in SQL Queries and Exporting Data to CSV

We can give aliases to columns by using the AS command.

```
SELECT
addresses_street_one AS 'Street',
addresses_street_two AS 'Street 2',
addresses_city AS 'City',
addresses_state AS 'State',
addresses_postal_code AS 'Postal Code'
FROM addresses;
```

This would rename the columns.

## Adding Custom Row and Cell Names in SQL

You can use name rows to display data better, especially when large quantities of data are involved.

This code:

```
SELECT 'Email:', users_email, 'Name:', users_name
FROM users;
```

Gives this:

![Custom names](https://s3-us-west-2.amazonaws.com/devcamp-pictures/SQL+images/Adding+Custom+Row+and+Cell+Names+in+SQL+%23+1025/Screen+Shot+2017-10-26+at+9.58.09+AM.png)

## How to Alias Table Names in SQL

Greater and Lesser than signs can be used with where clauses to select items.

```
SELECT guides_title, guides_revenue
FROM guides
WHERE guides_revenue > 600;
```

What happens when we start working with data coming from multiple tables on the same query? That's where table aliases are handy. We're going to grab the same code as before.

```
SELECT alias.guides_title, alias.guides_revenue
FROM guides
WHERE guides_revenue > 600;
```

This becomes handy when joining tables together.

## How to Use Case Statements in SQL Queries to Implement Conditional Logic

We're going to implement a case statememt to implement conditional logic.

```
SELECT 
	guides_title,
    CASE
		WHEN guides_revenue > 1000 THEN 'Best seller'
        WHEN guides_revenue < 600 THEN 'Not displayed'
        ELSE 'Average sellers'
	END AS 'Status'
    FROM guides;
```

Here we're selecting a column and opening a case statement to write the conditional. The then portion labels the column. Then, we end the clause with the END statement having an alias and we finally select the table.

The code above is the same as this:

```
if (guides_revenue > 1000) {
    'Best Seller';
} else if (guides_revenue < 600) {
    'Not Displayed';
} else {
    'Average Sellers';
}

(...)

if guides_revenue > 1000:
    "Best Seller"
elif guides_revenue < 600:
    "Not Displayed"
else:
    "Average Sellers"
```

Finally, we get this:

```
guides_title	status
My Blog	        Not Displayed
Something Else	Best Seller
My Great Post	Average Sellers
My Blog	Average Sellers
My Blog	Average Sellers
Another One of My Posts	Best Seller
Guide by Jon	Not Displayed
```


All of this isn't in the table, but inside the query.