# Case Study 3 Foodie Fi

<img src='https://img.shields.io/badge/Microsoft%20SQL%20Server-CC2927?style=for-the-badge&logo=microsoft%20sql%20server&logoColor=white)'/>

The following are my solutions to the Case Study 3 Foodie Fie questions in 
[Danny Ma's Serious SQL course](https://www.datawithdanny.com/ "Data With Danny")
<br/>
<br/>
Danny has shared with you 2 key datasets for this case study :
[Data Set](https://github.com/Shailesh-python/Case_Study_3_Pizza_Runner/blob/main/Datasets%20and%20Tables)
<br/>
- `plans`
- `subscriptions`
<br/>

## A. Customer Journey

Based off the 8 sample customers provided in the sample from the subscriptions table, write a brief description about each customer’s onboarding journey.

Try to keep it as short as possible - you may also want to run some sort of join to make your explanations a bit easier!

## B. Data Analysis Questions

## [Question #1](#case-study-questions)
> How many customers has Foodie-Fi ever had?

## [Question #2](#case-study-questions)
> What is the monthly distribution of trial plan start_date values for our dataset - use the start of the month as the group by value.

## [Question #3](#case-study-questions)
> What plan start_date values occur after the year 2020 for our dataset? Show the breakdown by count of events for each plan_name.

## [Question #4](#case-study-questions)
> What is the customer count and percentage of customers who have churned rounded to 1 decimal place?

## [Question #5](#case-study-questions)
> How many customers have churned straight after their initial free trial - what percentage is this rounded to the nearest whole number?

## [Question #6](#case-study-questions)
> What is the number and percentage of customer plans after their initial free trial?

## [Question #7](#case-study-questions)
> What is the customer count and percentage breakdown of all 5 plan_name values at 2020-12-31?

## [Question #8](#case-study-questions)
> How many customers have upgraded to an annual plan in 2020?

## [Question #9](#case-study-questions)
> How many days on average does it take for a customer to an annual plan from the day they join Foodie-Fi?
> 
## [Question #10](#case-study-questions)
> Can you further breakdown this average value into 30 day periods (i.e. 0-30 days, 31-60 days etc)
> 
## [Question #11](#case-study-questions)
> How many customers downgraded from a pro monthly to a basic monthly plan in 2020?

## C. Challenge Payment Question

The Foodie-Fi team wants you to create a new payments table for the year 2020 that includes amounts paid by each customer in the subscriptions table with the following requirements:
- monthly payments always occur on the same day of month as the original start_date of any monthly paid plan
- upgrades from basic to monthly or pro plans are reduced by the current paid amount in that month and start immediately
- upgrades from pro monthly to pro annual are paid at the end of the current billing period and also starts at the end of the month period
- once a customer churns they will no longer make payments

## D. Outside The Box Questions














