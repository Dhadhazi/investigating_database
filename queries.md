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
> Now we need to know how the length of rental duration of these family-friendly movies compares to the duration that all movies are rented for. Can you provide a table with the movie titles and divide them into 4 levels (first_quarter, second_quarter, third_quarter, and final_quarter) based on the quartiles (25%, 50%, 75%) of the rental duration for movies across all categories? Make sure to also indicate the category that these family-friendly movies fall into.

```sql
SELECT 
      f.title, 
      c.name, 
      f.rental_duration, 
      NTILE(4) OVER (ORDER BY f.rental_duration) AS standard_quartile
FROM film_category fc
JOIN category c
ON c.category_id = fc.category_id
JOIN film f
ON f.film_id = fc.film_id
WHERE c.name IN ('Animation', 'Children', 'Classics', 'Comedy', 'Family', 'Music')
ORDER BY 3
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