# Restaurant-Order-Analysis

SELECT * FROM Restaurant r;
SELECT * FROM "Order" o ; 

-- 1. Display all orders from restaurants in Atlanta.
SELECT * FROM "Order" o 
INNER JOIN Restaurant r ON o.restaurantId = r.restaurantId 
WHERE city LIKE '%Atlanta%';

-- 2. Find orders with 'Burger' on the menu name.
SELECT * FROM "Order" o 
WHERE "menus.name" LIKE '%Burger%';

--- 3. List orders by dateAdded in ascending order.
SELECT orderId FROM "Order" o 
ORDER BY (o.dateAdded) ASC;

-- 4. Count the number of orders for each restaurant. Display the 10 restaurants with the highest number of orders, along with the city where they are located.
SELECT r.name, r.city, COUNT(o.orderId) AS number_of_orders
FROM "Order" o 
INNER JOIN Restaurant r ON o.restaurantId = r.restaurantId 
GROUP BY r.name, r.city
ORDER BY number_of_orders DESC   
LIMIT 10;

--5. List all restaurants with more than 20 orders and the city where they are located.
SELECT r.name, r.city FROM Restaurant r 
INNER JOIN "Order" o ON r.restaurantId = o.restaurantId 
GROUP BY r.name, r.city
HAVING COUNT(orderId) > 20;

--6. Retrieve all information from the orders table along with restaurant details.
SELECT o.*, r.* FROM "Order" o 
INNER JOIN Restaurant r ON o.restaurantId = r.restaurantId;

--7. Categorize restaurants based on menu price range.
SELECT r.restaurantId, r.name, r.city,
CASE
	WHEN priceRangeMax < 15 THEN 'Low Price'
	WHEN priceRangeMax BETWEEN 15 AND 30 THEN 'Medium Price'
	WHEN priceRangeMax > 30 THEN 'High Price'
	ELSE 'Unknown'
	END AS price_category 
	FROM "Order" o 
	INNER JOIN Restaurant r ON r.restaurantId = o.restaurantId
	
--8. Find orders where the restaurant is in the same city as ‘Wildwood Pizza’.
SELECT * FROM "Order" o 
INNER JOIN Restaurant r ON o.restaurantId = r.restaurantId
WHERE r.city = 
	(SELECT city FROM Restaurant r
	WHERE name = 'Wildwood Pizza');

--9. Calculate the average time between dateAdded and dateUpdated in days.
SELECT AVG(JULIANDAY(dateUpdated) - JULIANDAY(dateAdded)) AS avg_days
FROM "Order" o ;

--10. Display information of restaurants that have Karaoke and sort them by dateSeen. Include full address in one single column, category, menus, and average menu price. (Use the maximum menu price).
CREATE VIEW KaraokeRestaurants AS
SELECT
    r.name AS Resturant_Name,
    r.address  || ' ' || r.city || ' ' || r.province || ' ' || r.postalCode || ' ' ||  r.country AS FullAddress,
    o.categories AS Categorized_By,
    o."menus.name" AS Menu_Name,
    o."menus.amountMax" AS AvgMenuPrice
FROM "Order" o 
INNER JOIN Restaurant r ON o.restaurantId = r.restaurantId
WHERE Categorized_By LIKE '%Karaoke%'
GROUP BY Resturant_Name, FullAddress, Categorized_By, Menu_Name, AvgMenuPrice
ORDER BY o."menus.dateSeen";

SELECT * FROM KaraokeRestaurants;

--11. List the names of restaurants, their state/province location, along with the number of orders they received. Display the restaurants with the largest number of orders first.
SELECT
	r.name AS restaurant_name, r.province AS state_province, COUNT(o.orderId) AS orders_recieved FROM Restaurant r 
JOIN "Order" o ON r.restaurantId = o.restaurantId 
GROUP BY r.name, r.province 
ORDER BY orders_recieved DESC; 

--12. Show the name of restaurants where offers vegetarian or vegan pizzas and 
their menu descriptions.
SELECT 
r.name AS restaurant_name,
o."menus.name" AS menu_items,
o."menus.description" AS menu_description
FROM Restaurant r 
INNER JOIN "Order" o ON r.restaurantId = o.restaurantId 
WHERE o."menus.description" LIKE '%vegetarian%pizza%' OR '%vegan%pizza%';

