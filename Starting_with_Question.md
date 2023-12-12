
Question 1: How many total listings are there in the Capetown AirBnB data? Would you say there are inactive listings in the data? If yes, What percentage of total listing is active or inactive?

SQL Queries:
-- Total number of listings
SELECT COUNT(*) AS total_listings
FROM capetown_airbnb_listings;

-- Number of active listings
SELECT COUNT(*) AS active_listings
FROM capetown_airbnb_listings
WHERE has_availability = 't';

-- Number of inactive listings
SELECT COUNT(*) AS inactive_listings
FROM capetown_airbnb_listings
WHERE has_availability = 'f';

SELECT
    COUNT(*) FILTER (WHERE availability_365 > 0) AS active_listings,
    COUNT(*) FILTER (WHERE availability_365 = 0) AS inactive_listings
FROM
    capetown_airbnb_listings;

WITH total_counts AS (
    SELECT COUNT(*) AS total_listings
    FROM capetown_airbnb_listings
)

Percentage:
SELECT
    total_listings,
    active_listings,
    inactive_listings,
    (active_listings * 100.0 / total_listings) AS active_percentage,
    (inactive_listings * 100.0 / total_listings) AS inactive_percentage
FROM
    total_counts,
    (
        SELECT
            COUNT(*) FILTER (WHERE availability_365 > 0) AS active_listings,
            COUNT(*) FILTER (WHERE availability_365 = 0) AS inactive_listings
        FROM
            capetown_airbnb_listings
    ) AS availability_counts;


Answer:
Total listings are 21120.
Active listings are 19595.
Yes, there are inactive listings in the data, inactive = 1525


Question 2: How many distinct hosts are present in the CapeTown AirBnB listing? How many listings does each of this distinct hosts have? Which host have the most listings

SQL Queries:
-- Distinct hosts in the Capetown Airbnb listing
SELECT COUNT(DISTINCT host_id) AS distinct_hosts_count
FROM capetown_airbnb_listings;

-- Number of listings for each distinct host
SELECT host_id, COUNT(*) AS listings_count
FROM capetown_airbnb_listings
GROUP BY host_id
ORDER BY listings_count DESC;

-- Host with the most listings
SELECT host_id, COUNT(*) AS listings_count
FROM capetown_airbnb_listings
GROUP BY host_id
ORDER BY listings_count DESC
LIMIT 1;


Answer:
Distinct hosts in the Capetown Airbnb listing = 11303
Listings of the distinct hosts: excel download file

Host with the most listings: "host_id"
487012580 (155 listings total)

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
SELECT
    (COUNT(*) FILTER (WHERE instant_bookable = TRUE) * 100.0 / COUNT(*)) AS instant_booking_percentage
FROM
    capetown_airbnb_listings;


Answer:
Total number of listings available for more than 90 days a year:
Total number of listings available for less than 30 days a year:
Total listings available for instant booking: 5874


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




Answer:
