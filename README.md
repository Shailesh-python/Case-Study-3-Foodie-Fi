# Case Study #3 Foodie Fi

<img src='https://img.shields.io/badge/Microsoft%20SQL%20Server-CC2927?style=for-the-badge&logo=microsoft%20sql%20server&logoColor=white)'/>

The following are my solutions to the Case Study 3 Foodie Fie questions in 
[Danny Ma's Serious SQL course](https://www.datawithdanny.com/ "Data With Danny")
<br/>
<br/>
Danny has shared with you 2 key datasets for this case study :
[Data Set](https://github.com/Shailesh-python/Case-Study-3-Foodie-Fi/blob/main/Datasets%20%26%20Tables)
<br/>
- `plans`
- `subscriptions`
<br/>

## Data Analysis Questions

## [Question #1](#case-study-questions)
> How many customers has Foodie-Fi ever had?
```sql
SELECT
	COUNT( DISTINCT S.customer_id) AS total_customers
FROM foodie_fi.subscriptions S
```
| total_customers |
|-----------------|
|      1000       |

## [Question #2](#case-study-questions)
> What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value.
```sql
SELECT
	YEAR(S.start_date) as Year_Startdate,
	Month(S.start_date) as Month_Startdate,
	COUNT(*) AS trial_customers
FROM foodie_fi.subscriptions S
INNER JOIN foodie_fi.plans P
	ON S.plan_id = P.plan_id
WHERE P.plan_name = 'trial'
GROUP BY YEAR(S.start_date),Month(S.start_date)
```
![image](https://github.com/Shailesh-python/Case-Study-3-Foodie-Fi/blob/main/Question_2.jpg)

## [Question #3](#case-study-questions)
> What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name.
```sql
SELECT
	p.plan_id,
	p.plan_name,
	COUNT(*) AS [events]
FROM foodie_fi.subscriptions S
INNER JOIN foodie_fi.plans P
	ON S.plan_id = P.plan_id
WHERE s.start_date >= '2020-12-31'
GROUP BY P.plan_id, P.plan_name
ORDER BY P.plan_id
```
| plan_id | plan_name   | events |
|---------|-------------|--------|
|   1     |basic monthly|   8    |
|   2     |pro monthly  |   60   |
|   3     |pro annual   |   63   |
|   4     |churn        |   72   |

## [Question #4](#case-study-questions)
> What is the customer count and percentage of customers who have churned rounded to 1 decimal place?
```sql
WITH CTE AS
(
SELECT
	COUNT(
	DISTINCT
	CASE WHEN P.plan_name = 'churn' THEN S.customer_id ELSE NULL END
	) AS churned_customers,
	COUNT(DISTINCT S.customer_id) AS total_customers
FROM foodie_fi.subscriptions S
INNER JOIN foodie_fi.plans P
	ON S.plan_id = P.plan_id
)
	SELECT
		CTE.churned_customers,
		CAST((100.0 * CTE.churned_customers)/CTE.total_customers AS DECIMAL(5,1)) AS churned_percentage
	FROM CTE
```
| churned_customers | churned_percent |
|-------------------|-----------------|
|      307          |      30.7       |

## [Question #5](#case-study-questions)
> How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?
```sql
;WITH CTE AS
(
SELECT
	S.customer_id,
	P.plan_id,
	P.plan_name,
	ROW_NUMBER() OVER (PARTITION BY S.customer_id ORDER BY P.plan_id) AS RN
FROM foodie_fi.subscriptions S
INNER JOIN foodie_fi.plans P
	ON S.plan_id = P.plan_id
)
	SELECT 
	    COUNT(DISTINCT CTE.customer_id) as churned_customers,
	    CAST(
		  (100.0 * COUNT(DISTINCT CTE.customer_id)/
		  (SELECT COUNT(DISTINCT SS.customer_id) FROM foodie_fi.subscriptions SS))
		  AS DECIMAL(5,1)
		) AS immediatelyChurned_customers
	FROM CTE 
	WHERE CTE.plan_id = 4
		AND CTE.RN = 2
```
| churned_customers | immediatleyChurned_percent |
|-------------------|----------------------------|
|      92           |      9.2                   |

## [Question #6](#case-study-questions)
> What is the number and percentage of customer plans after their initial free trial?
```sql
;WITH CTE AS
(
SELECT
	S.customer_id,
	P.plan_id,
	P.plan_name,
	LEAD(S.plan_id, 1) OVER (PARTITION BY S.customer_id ORDER BY P.plan_id ASC) AS next_plan
FROM foodie_fi.subscriptions S
INNER JOIN foodie_fi.plans P
	ON S.plan_id = P.plan_id
)
	SELECT 
		CTE.next_plan,
	COUNT(DISTINCT CTE.customer_id) as churned_customers,
	CAST(
		(100.0 * COUNT(DISTINCT CTE.customer_id)/
		(SELECT COUNT(DISTINCT SS.customer_id) FROM foodie_fi.subscriptions SS))
		AS DECIMAL(5,1)
		) AS immediatelyChurned_customers
	FROM CTE 
	WHERE CTE.next_plan IS NOT NULL
		AND CTE.plan_id = 0
	GROUP BY CTE.next_plan
```
| next_plan | after_trial_Conversions   | after_trial_Conversions_percent |
|-----------|---------------------------|---------------------------------|
|   1       |546                        |             54.6                |
|   2       |325                        |             32.5                |
|   3       |37                         |              3.7                |
|   4       |92                         |              9.2                |

## [Question #7](#case-study-questions)
> What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?
```sql
-- Retrieve next plan's start date located in the next row based on current row
;WITH next_plan AS(
SELECT 
  customer_id, 
  plan_id, 
  start_date,
  LEAD(start_date, 1) OVER(PARTITION BY customer_id ORDER BY start_date) as next_date
FROM foodie_fi.subscriptions
WHERE start_date <= '2020-12-31'
),
-- Find customer breakdown with existing plans on or after 31 Dec 2020
customer_breakdown AS (
  SELECT 
    plan_id, 
    COUNT(DISTINCT customer_id) AS customers
  FROM next_plan
  WHERE 
    (next_date IS NOT NULL AND (start_date < '2020-12-31' 
      AND next_date > '2020-12-31'))
    OR (next_date IS NULL AND start_date < '2020-12-31')
  GROUP BY plan_id)

SELECT plan_id, customers, 
  ROUND(100 * CAST(customers AS INT)/ (
    SELECT COUNT(DISTINCT customer_id) 
    FROM foodie_fi.subscriptions),1) AS percentage
FROM customer_breakdown
GROUP BY plan_id, customers
ORDER BY plan_id;
```
| plan_id | customers | percentage |
|---------|-----------|------------|
|   0     |19         |           1|
|   1     |224        |          22|
|   2     |326        |          32|
|   3     |195        |          19|
|   4     |235        |          23|


## [Question #8](#case-study-questions)
> How many customers have upgraded to an annual plan in 2020?
```SQL
SELECT 
  COUNT(DISTINCT customer_id) AS unique_customer
FROM foodie_fi.subscriptions
WHERE plan_id = 3
  AND start_date <= '2020-12-31';
```
| unique_customers|
|-----------------|
|      195        |

## [Question #9](#case-study-questions)
> How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
```sql
SELECT
	AVG(
		DATEDIFF(DD,s_start.start_date,s_annual.start_date)
	) AS avg_days
FROM foodie_fi.subscriptions s_start
LEFT JOIN foodie_fi.subscriptions s_annual
	ON s_start.customer_id = s_annual.customer_id
WHERE s_annual.plan_id = 3
	AND s_start.plan_id = 0
```
| avg_days |
|----------|
|    105   |


## [Question #10](#case-study-questions)
> Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
> 
## [Question #11](#case-study-questions)
> How many customers downgraded from a pro monthly to a basic monthly plan in 2020?
```sql
SELECT
	COUNT(DISTINCT B.customer_id) AS Customers
FROM foodie_fi.subscriptions p
LEFT JOIN foodie_fi.subscriptions b
	ON p.customer_id = b.customer_id
WHERE p.plan_id = 3
	AND b.plan_id = 2
	AND YEAR(B.start_date) = 2020
	AND B.start_date >= P.start_date
```
No customer has downgraded from pro monthly to basic monthly in 2020.
| customers|
|----------|
|      0   |
