Question 1:

What is the correlation between the number of reviews and the review score rate

SQL Queries:
-- Correlation between the number of reviews and review scores
SELECT
  CORR(review_score_rating, number_of_reviews) AS correlation
FROM
  capetown_airbnb_listings
WHERE
  review_score_rating IS NOT NULL
  AND number_of_reviews IS NOT NULL;

Answer: Correlation coeff: 0.11, tehrefore no correlation, number of reviews does not exactly impact the review score rating.

Question 2:

What is the distribution of review scores across different property types, and do certain property types consistently receive higher ratings?

SQL Queries:

-- Distribution of review scores across different property types
SELECT
  property_type,
  COUNT(*) AS property_count,
  ROUND(AVG(review_score_rating), 2) AS avg_rating,
  MIN(review_score_rating) AS min_rating,
  MAX(review_score_rating) AS max_rating
FROM
  capetown_airbnb_listings
WHERE
  review_score_rating IS NOT NULL
GROUP BY
  property_type
ORDER BY
  avg_rating DESC;

Answer:

![image](https://github.com/Alice-Mbaluka/SQL_project-MWC/assets/79568950/af4c38a2-4364-465e-bc54-5d27db7b0fe4)


Question 3:
What is the distribution of host response times, and do hosts with faster response times tend to have higher-rated properties?

SQL Queries:

-- Distribution of host response times and their impact on average review scores 

SELECT
  host_response_time,
  ROUND(AVG(review_score_rating), 2) AS avg_rating
FROM
  capetown_airbnb_listings
WHERE
  host_response_time IS NOT NULL
  AND host_response_time <> 'N/A'
  AND review_score_rating IS NOT NULL
GROUP BY
  host_response_time
ORDER BY
  avg_rating DESC;
  
Answer:

![image](https://github.com/Alice-Mbaluka/SQL_project-MWC/assets/79568950/20b78dc4-ee8e-4d8d-87aa-754aed827e37)


Question 4:
How does the availability of instant booking affect the average review score for different property types?

SQL Queries:

SELECT
  property_type,
  instant_bookable,
  COUNT(*) AS property_count,
  ROUND(AVG(review_score_rating), 2) AS avg_rating
FROM
  capetown_airbnb_listings
WHERE
  instant_bookable IN ('t', 'f')
  AND review_score_rating IS NOT NULL
GROUP BY
  property_type, instant_bookable
ORDER BY
  property_type, avg_rating DESC;

Question 5:
What is the min and max price for the properties

SQL Queries:

Select min (price) As minimum_price From capetown_airbnb_listings 
Select max (price) As maximum_price From capetown_airbnb_listings
