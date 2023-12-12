
Question 1: How many total listings are there in the Capetown AirBnB data? Would you say there are inactive listings in the data? If yes, What percentage of total listing is active or inactive?

SQL Queries:

-- Total number of listings
SELECT COUNT(*) AS total_listings
FROM capetown_airbnb_listings;

-- Number of active and inactive listings
SELECT
    COUNT(*) FILTER (WHERE has_availability = TRUE) AS active_listings,
    COUNT(*) FILTER (WHERE has_availability = FALSE) AS inactive_listings
FROM
    capetown_airbnb_listings;


Percentage:


SELECT SUM(HOST_LISTINGS_COUNT) as Active_listings,
		SUM(HOST_TOTAL_LISTINGS_COUNT) as Total_listings,
(
	SELECT SUM(HOST_TOTAL_LISTINGS_COUNT) - SUM(HOST_LISTINGS_COUNT) AS INACTIVE_LISTINGS),
	(
		SELECT CONCAT(ROUND(((SUM(HOST_TOTAL_LISTINGS_COUNT) -  SUM(HOST_LISTINGS_COUNT)) :: numeric 
							 / SUM(HOST_TOTAL_LISTINGS_COUNT)::numeric)* 100,2),'%')) as percent_of_Inactive_listings,
		(
			SELECT CONCAT(ROUND(((SUM(HOST_LISTINGS_COUNT)) :: numeric 
							 / SUM(HOST_TOTAL_LISTINGS_COUNT)::numeric)* 100,2),'%')) as percent_of_Active_listings	 
FROM CAPETOWN_AIRBNB_LISTINGS;


Answer:

Percentage: 

![image](https://github.com/Alice-Mbaluka/SQL_project-MWC/assets/79568950/7fe401a7-4d4a-4338-bcf9-3b0216ebef29)



Question 2: How many distinct hosts are present in the CapeTown AirBnB listing? How many listings does each of this distinct hosts have? Which host have the most listings

SQL Queries:
-- Distinct hosts in the Capetown Airbnb listing
SELECT COUNT(DISTINCT host_name) AS distinct_hosts_count
FROM capetown_airbnb_listings;

-- Number of listings for each distinct host
SELECT host_name, COUNT(*) AS listings_count
FROM capetown_airbnb_listings
GROUP BY host_name
ORDER BY listings_count DESC;

-- Host with the most listings
SELECT host_name, COUNT(*) AS listings_count
FROM capetown_airbnb_listings
GROUP BY host_name
ORDER BY listings_count DESC;


Answer:
Distinct hosts in the Capetown Airbnb listing = 4627
Listings of the distinct hosts: 
![image](https://github.com/Alice-Mbaluka/SQL_project-MWC/assets/79568950/49c626a4-1220-4f83-a249-b7fe446b767b)


Host with the most listings: 
Propr (462 listings total)

Question 3: On Availability, What is the total number of listings available for more than 90 days a year? How many listings have availability for less than 30 days a year? What percentage of listings are available for instant booking?

SQL Queries:
-- Total number of listings available for more than 90 days a year
SELECT COUNT(*) AS total_listings
FROM capetown_airbnb_listings
WHERE availability_365 > 90;


-- Total number of listings available for less than 30 days a year
SELECT COUNT(*) AS total_listings30
FROM capetown_airbnb_listings
WHERE availability_365 <30;

-- Percentage of listings available for instant booking
SELECT CONCAT(ROUND(
		(SELECT COUNT(INSTANT_BOOKABLE)
			FROM capetown_airbnb_listings
			WHERE INSTANT_BOOKABLE = 'true')::NUMERIC /
		(SELECT COUNT(*)
			FROM capetown_airbnb_listings)::NUMERIC * 100,2),'%') AS PERCENTAGE_AVAILABLE_FOR_INSTANT_BOOKING;


Answer:
Total number of listings available for more than 90 days a year: 14998
Total number of listings available for less than 30 days a year: 3842
Percentage of listings available for instant booking: 27.81%


Question 4: What are the different types of properties available and their counts? Which property type has the highest average price? Write a query to show the correlation between review and pricing.

SQL Queries:
-- Types of properties and their counts
SELECT property_type, COUNT(*) AS property_count
FROM capetown_airbnb_listings
GROUP BY property_type
ORDER BY property_count DESC;

-- Property type with the highest average price
SELECT
    property_type,
   
   AVG(REPLACE( REPLACE(TRIM (BOTH ' ' FROM price),'$',''),',','') ::numeric )AS average_price
	
FROM
    capetown_airbnb_listings
WHERE
    price IS NOT NULL
GROUP BY
    property_type
ORDER BY
    average_price DESC
LIMIT 1;


-- Correlation between review scores and pricing
SELECT
    CORR(
        review_score_rating,
        REPLACE(REPLACE(TRIM(BOTH ' ' FROM price),'$',  '' ), ',', '' )::numeric ) AS correlation
    
FROM
    capetown_airbnb_listings
WHERE
    review_score_rating IS NOT NULL
    AND price IS NOT NULL;


Answer:
a)

![image](https://github.com/Alice-Mbaluka/SQL_project-MWC/assets/79568950/48b2f221-3be1-44b6-8454-98a8d5506fc2)

b) Entire villa: 13742.75 (Highest average price)

c) Corr = 0.01; conclusion is reviews and price have little or no relationship.

Question 5: How do individual listings or hosts perform compared to the average or top-performing ones in terms of ratings, pricing, and booking frequency?

SQL Queries:
Ratings comparison:
SELECT AVG(review_score_rating) AS average_rating
FROM capetown_airbnb_listings;
SELECT
    id,
    name,
    host_name,
    review_score_rating
FROM
    capetown_airbnb_listings

Pricing Comparison:

SELECT 
AVG(CAST(REPLACE(TRIM(BOTH ' ' FROM REPLACE(price, ',', '')), '$', '') AS numeric)) AS average_price

FROM capetown_airbnb_listings
WHERE price IS NOT NULL;

SELECT
    id,
    name,
    host_name,
    CAST(REPLACE(TRIM(BOTH ' ' FROM REPLACE(price, ',', '')), '$', '') AS numeric) AS listing_price,
    CASE
        WHEN CAST(REPLACE(TRIM(BOTH ' ' FROM REPLACE(price, ',', '')), '$', '') AS numeric) > (
            SELECT AVG(CAST(REPLACE(TRIM(BOTH ' ' FROM REPLACE(price, ',', '')), '$', '') AS numeric))
            FROM capetown_airbnb_listings
            WHERE price IS NOT NULL
        )
        THEN 'Above Average'
        WHEN CAST(REPLACE(TRIM(BOTH ' ' FROM REPLACE(price, ',', '')), '$', '') AS numeric) < (
            SELECT AVG(CAST(REPLACE(TRIM(BOTH ' ' FROM REPLACE(price, ',', '')), '$', '') AS numeric))
            FROM capetown_airbnb_listings
            WHERE price IS NOT NULL
        )
        THEN 'Below Average'
        ELSE 'Average'
    END AS price_comparison
FROM
    capetown_airbnb_listings
WHERE
    price IS NOT NULL;


Booking Frequency Comparison:

SELECT AVG(availability_365) AS average_booking_frequency
FROM capetown_airbnb_listings;
SELECT
    id,
    name,
    host_name,
    availability_365 AS booking_frequency
FROM
    capetown_airbnb_listings


![image](https://github.com/Alice-Mbaluka/SQL_project-MWC/assets/79568950/bcc732b7-55b7-4928-b64d-937ee6c1e01f)



Answer:

![image](https://github.com/Alice-Mbaluka/SQL_project-MWC/assets/79568950/6e51d607-4ed5-435a-802c-b3e139f779b3)

