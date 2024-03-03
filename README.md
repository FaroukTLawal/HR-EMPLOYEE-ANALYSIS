# HR_EMPLOYEE_ANALYSIS

### An analysis of a company's employee distribution
![Screenshot (46)](https://github.com/FaroukTLawal/HR-EMPLOYEE-ANALYSIS/assets/153385880/7a11e6a8-1a04-4c76-a0eb-d8e0db84a949)

## Table Of Content

- [Data Used](#Data-used)
- [Questions](#Questions)
- [Some SQL codes Used in analysis](#Some-SQL-codes-used-in-analysis)
- [Summary Of Findings](#Summary-Of-Findings)
- [Limitations](#Limitations)
  
## Data Used

* Data - HR Data with over 22000 rows from the year 2000 to 2020
* Data Cleaning & Analysis - MySQL
* Data Visualization - PowerBI
  
## Questions
  
  1. What is the Gender breakdown of employees in the company ?
  2. what is the race/ethnicity breakdown of employees in the company?
  3. What is the age distribution of employees in the company?
  4. How many employees work at headquarters versus renote locations?
  5. What is the average length of employment for employees who have been terminated?
  6. How does the gender distribution vary across departments and job titles?
  7. What is the distribution of job titles across the company?
  8. which department has the highest turnover rate ?
  9. what is the distribution of employees across locations by state?
  10. How has the company's employee count changed over time based on the hire and term dates?
  11. what is the tenure distribution for each department?

 # Some SQL codes used in analysis

 ``` sql
select gender, count(*) as count from dbo.[HR PROJECT CLEANED] where age > 18 and termdate = '00/00/0000' group by gender
select race, count(*) as count from dbo.[HR PROJECT CLEANED] where age > 18 and termdate = '00/00/0000' group by race order by count desc
select min(age) as yougest, max(age) as oldest from dbo.[HR PROJECT CLEANED] where age > 18 and termdate = '00/00/0000'
select case when age >= 18 and age <= 24 then '18-24'
 when age >= 25 and age <= 34 then '25-34'
 when age >= 35 and age <= 44 then '35-44'
 when age >= 45 and age <= 54 then '44-54' 
when age >= 55 and age <= 64 then '55-64' 
else '65+' end as age_group, 
count(*) as count from dbo.[HR PROJECT CLEANED] 
where age >= 18 and termdate = '00/00/0000'
group by age 
order by age_group
select location, count(*) as count from dbo.[HR PROJECT CLEANED] where age >= 18 and termdate = '00/00/0000' group by location
select avg(datediff(DAY, termdate, hire_date))/365 as avg_length_employment from dbo.[HR PROJECT CLEANED]
 where termdate <= getdate() and termdate <> '00/00/0000' and age>=18
select department, gender, count(*) as count from dbo.[HR PROJECT CLEANED]
 where age >= 18 and termdate = '00/00/0000' group by department, gender order by department

select department, total_count, terminated_count,CAST (terminated_count as decimal)/total_count as termination_rate
 from ( select department, count(*) as total_count, sum(case when termdate<> '00/00/0000' and 
termdate <= getdate() then 1 else 0 end) as terminated_count from dbo.[HR PROJECT CLEANED]
 where age >= 18 group by department) as subquery order by termination_rate desc
select location_state, count(*) as count from dbo.[HR PROJECT CLEANED] where age >= 18 and termdate= '00/00/0000' group by location_state order by count desc
select year, hires, terminations, hires - terminations as net_change, round(((hires - terminations) * 100.0)/ nullif (hires, 0), 2) as net_change_percent
from (select year(hire_date) as year, count(*) as hires, sum(case when termdate<> '00/00/0000' and termdate <= getdate() then 1 else 0 end) as terminations
from dbo.[HR PROJECT CLEANED] where age > 18 group by year(hire_date)) as subquery order by year asc
select department, round(avg(DATEDIFF(day,termdate, hire_date)/365),0) as avg_tenure
from dbo.[HR PROJECT CLEANED] where termdate <= GETDATE() and termdate<> '00/00/0000' and age>= 18 
group by department order by avg_tenure asc

select 
case when age >= 18 and age <= 24 then '18-24'
 when age >= 25 and age <= 34 then '25-34'
  when age >= 35 and age <= 44 then '35-44'
   when age >= 45 and age <= 54 then '44-54'
    when age >= 55 and age <= 64 then '55-64'
	else '65+' end as age_group, gender, count(*) as count  from dbo.[HR PROJECT CLEANED] where age>= 18 and termdate = '00/00/0000'
	group by age, gender
	order by age_group, gender
```
 
## Summary Of Findings

 * There are more male employees.
 * White race is the most dominant white native Hawaiin and American Indian are the last dominant?.
 * The youngest employee is 20 years old and the oldest is 57 years old.
 * 5 age groups were created (18-24,25-34, 35-44, 45-54, 55-64). A large number of employees were between 25-34 followed by 35-44 while the smallest group was 55-64.
 * A large number of employees work at the headquarters versus remotely.
 * The average length of employment for terminated employees is around 7 years.
 * The gender distribution across departments is fairly balanced but there are generally more male than female employees.
 * The Auditing department has the highest turnover rate followed by Legal. The least turnover rate are in the marketing and busiess developments departments.
 * A large number of employees come from the state of Ohio.
 * The net change in employees has increased over the years.
 * The average tenure for each department is about 8 years with Legal and auditing having the highest and services, sales an marketing having the lowest.

## Limitations

 * Some records had negative ages and these were excluded during quering(967 records). Ages used were 18 years and above.
 * Some termdates were far into the future and were not included in the analysis(1599 records). The only term dates used were those less than or equal to the current date. 
