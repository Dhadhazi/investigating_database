## Question 1
> We want to understand more about the movies that families are watching. The following categories are considered family movies: Animation, Children, Classics, Comedy, Family and Music. Create a query that lists each movie, the film category it is classified in, and the number of times it has been rented out.

```sql
SELECT 
      f.title as film_title, 
      c.name as category_name, 
      COUNT(r.rental_id) as rental_count
FROM film_category fc
JOIN category c
ON c.category_id = fc.category_id
JOIN film f
ON f.film_id = fc.film_id
JOIN inventory i
ON i.film_id = f.film_id
JOIN rental r 
ON r.inventory_id = i.inventory_id
WHERE c.name IN ('Animation', 'Children', 'Classics', 'Comedy', 'Family', 'Music')
GROUP BY 1, 2
ORDER BY 2, 1
```

## Question 2
> Finally, provide a table with the family-friendly film category, each of the quartiles, and the corresponding count of movies within each combination of film category for each corresponding rental duration category. The resulting table should have three columns: Category, Rental length catehory, Count

```sql
SELECT 
      category, 
      standard_quartile,
      COUNT(*)
FROM
    (SELECT c.name category, f.rental_duration,
     NTILE(4) OVER (ORDER BY f.rental_duration) AS standard_quartile
     FROM category c
     JOIN film_category fc
     ON c.category_id = fc.category_id
     JOIN film f
     ON fc.film_id = f.film_id
     WHERE c.name IN ('Animation', 'Children', 'Classics', 'Comedy', 'Family', 'Music')) sub
GROUP BY 1, 2
ORDER BY 1, 2
```

## Question 3
> We want to find out how the two stores compare in their count of rental orders during every month for all the years we have data for. Write a query that returns the store ID for the store, the year and month and the number of rental orders each store has fulfilled for that month. Your table should include a column for each of the following: year, month, store ID and count of rental orders fulfilled during that month.

```sql
SELECT
      DATE_PART('month', r.rental_date) AS Rental_month,   
      DATE_PART('year', r.rental_date) AS Rental_year, 
      store.store_id AS Store_ID, 
      COUNT(*) AS Count_rentals
FROM store
JOIN staff
ON store.store_id = staff.store_id
JOIN rental r
ON r.staff_id = staff.staff_id
GROUP BY 1, 2, 3
ORDER BY count_rentals DESC
```

## Question 4
> We would like to know who were our top 10 paying customers, how many payments they made on a monthly basis during 2007, and what was the amount of the monthly payments. Can you write a query to capture the customer name, month and year of payment, and total payment amount for each month by these top 10 paying customers?

```sql
WITH sub AS (
  SELECT 
        c.customer_id, 
        SUM(p.amount) AS total
  FROM customer c
  JOIN payment p
  ON p.customer_id = c.customer_id
  GROUP BY c.customer_id
  ORDER BY total DESC
  LIMIT 10
)

SELECT 
      DATE_TRUNC('month', payment_date) AS pay_mon, 
      CONCAT(first_name, ' ', last_name) AS fullname, 
      COUNT(p.amount) AS pay_countpermon, 
      SUM(p.amount) AS pay_amount
FROM sub
JOIN customer c
ON sub.customer_id = c.customer_id
JOIN payment p
ON p.customer_id = c.customer_id
WHERE payment_date >= '2007-01-01' AND payment_date < '2008-01-01'
GROUP BY 1, 2
ORDER BY 2, 1
```