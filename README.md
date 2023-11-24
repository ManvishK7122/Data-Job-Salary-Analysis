# Data Science Job Family Salary Analysis

## Introduction

### Background/Problem Statement

In 2021, Harvard Business Review published an article titled _Data Scientist: The Sexiest Job of the 21st Century_. Within this article, the main topics discussed were how Big data jobs and jobs within that job family will have a boom and increase in demand due to how corporations see them as valuable assets when it comes to making business decisions. Through reading this article, I wanted to conduct a personal study/project about the job family by using data of salaries given to the job family from 2020 to 2023 and seeing if the statement made by the Harvard article was true. Another objective of this study was deducing what kind of factors affected their salary(Examples: Company Size and Level of Remoteness). 

### Fields/Preview

Within the Kaggle dataset regarding Data Job Salaries from 2020-2023, my objective is to look into 3 specific roles within the Data Science Job Family, which are
1. Data Analyst or any non-lead/managerial analyst role(Quality Analyst, Financial Data Analyst, etc.)
2. Data Scientist or any non-lead/managerial scientist role (Machine Learning Scientist, Applied Scientist, etc.)
3. Data Engineer or any non-lead/managerial engineer role(BI Engineer, Analyst Engineer, etc.)

Along with the job roles that I will be analyzing, the fields within our dataset include:

1. Work Year: The year the salary was issued.
2. Experience Level: The experience level in the job during the hired year.(Examples: EN, MI, SE, etc.)
3. Employment Type: The type of employment for the role. (Examples: FT, PT, etc.)
4. Job Title: The role worked in during the year. (Example: Data Analyst, ML Engineer, etc.)
5. Salary: The total gross salary amount paid within the company currency.
6. Salary Currency: The currency of the salary paid. (ISO 4217 currency code)
7. Salary in USD: The salary in USD.
8. Employee Residence: The employee's country of residence during the work year (ISO 3166 country code)
9. Remote Ratio/Level of Remoteness: The overall amount of work done remotely(Examples: 0, 50, 100)
10. Company Location: The country of the employer's main office or contracting branch
11. Company Size: The median number of people that worked for the company during the year. Regarding company size, these are the rough amount of people working in these companies.
- Small: Around 100 employees
- Medium: Around 500 employees
- Large: 1000+ employees

The fields that are going to be examined and analyzed through Advanced SQL querying are
1. Salary in USD(KPI or metric of success)
2. Work Year
3. Experience Level
4. Job Title
5. Remote Ratio
6. Company Size

### Tools Used
- Excel: Data Entry
- MYSQL: Data Cleaning & Querying
- Tableau: Building Interactive Dashboard
  
### Skills Applied
- CTE
- Nest Queries
- Windows Function
- Aggregation
- SQL wildcard
- Dashboard/Visualization Creation

## Data Preparation/Cleaning

### Data Cleaning Methods

Although it wasn't relevant to the analysis of the dataset, I added a field/column in Excel numbering all the entries with id numbers. The reason behind me doing this was to be able to identify which entry in the datasets had particular values I wanted to look into, however, the entry of id numbers did not affect data analysis and queries. One of the primary concerns with the data was the readiblity of the data itself due to the abbreviation and confusing terminology without the context of the definition for the fields. To mitigate this issue, I changed the names in the field through the UPDATE function on SQL to make the data more readible to the audience. An examples of this query would include: 

```sql
UPDATE salary
SET remote_ratio = "Remote"
WHERE remote_ratio = "100";
```

```sql
UPDATE salary
SET experience_level = "Senior"
WHERE experience_level = "SE"
```
I proceeded to do this process for the experience_level, remote_ratio, employment_type, and company_size fields. Along with the renaming of values within the field, I also updated the name of the remote_ratio column into Remoteness. The query looked like this:

```sql
ALTER TABLE Salary
RENAME COLUMN remote_ratio TO Remoteness
```

- Note that all the clarification and information about company size and employment type is confirmed by the discussion posts within the Kaggle Dataset by the owner.

- Here is the dataset after all the data cleaning


## Data Analysis/Questions

### Questions/Exploratory Data Analysis
1. Across our 3 job families, what is the distribution of individuals that work in the different sizes of companies and the percent total of each company size?
2. When looking at the different levels of experience level within our job families, how many individuals fall under each experience level(Entry, Intermediate, Etc.)?
3. Throughout the years our dataset covers (2020-2023), What is the general trend in terms of demand and their respective pay across our 3 job families?
4. Within our dataset and job roles, what are the number of individuals under each of the different levels of remoteness (Remote, Hybrid, On-site)?
5. Across the different experience levels and job families, what conditions (Level of remoteness and Company Size) tend to have the highest salaries?
   - Example: For Entry Level Analyst, hybrid analyst roles at a smaller companies tends to issue the higher salaries on average than the other types of conditions?
      - This is done for the different levels of experience/job families (Intermediate Level Scientists, Senior Level Engineers, etc.)
6. In comparison to the average salary of the job role, does jobs that have higher than average salaries still have the same conditions that pay the higher salaries in the other roles and experience levels?
    - Does the level of remoteness and size of company change with these roles at higher experience levels?

### Interesting/Example Queries

Q1: Across our 3 job families, what is the distribution of individuals that work in the different sizes of companies and the percent total of each company size? 

- This query is specifically for the Analyst role.
  
```sql
SELECT company_size,
  COUNT(job_title) AS number_of_emp_company,
  SUM(COUNT(job_title)) OVER() AS total_of_emp,
  ROUND((COUNT(job_title)* 100.0 / SUM(COUNT(job_title)) OVER()),2) AS percent_total_of_emp
FROM salary
WHERE job_title LIKE "% Analyst" 
GROUP BY company_size
```

Q2:When looking at the different levels of experience level within our job families, how many individuals fall under each experience level(Entry, Intermediate, Etc.)?
- This query is done with the Scientist role
  
```sql
SELECT experience_level,
  COUNT(job_title) AS number_of_entries,
  SUM(COUNT(job_title)) OVER() AS total_of_entries,
  ROUND(COUNT(job_title)* 100.0/ SUM(COUNT(job_title)) OVER(),2) AS percent_total
FROM salary 
WHERE job_title LIKE "% Scientist" 
GROUP BY experience_level
```

Q3: Throughout the years our dataset covers, What is the general trend in terms of demand and their respective pay across our 3 job families?

- This query is done with the Engineer role.
```sql
SELECT DISTINCT work_year,
  ROUND(AVG(salary_in_usd) OVER(PARTITION by work_year),2) AS avg_sal_engineer,
	MAX(salary_in_usd) OVER(PARTITION BY work_year) AS max_sal_engineer,
  MIN(salary_in_usd) OVER(PARTITION BY work_year) AS min_sal_engineer
FROM salary
WHERE job_title LIKE "% Engineer"
```

Q4: Within our dataset, what are the number of individuals under each of the different levels of remoteness (Remote, Hybrid, On-site) at the different job roles?

```sql
SELECT DISTINCT Remoteness,
  COUNT(salary_in_usd) OVER(PARTITION BY Remoteness) AS number_of_entries,
  ROUND(AVG(salary_in_usd) OVER(PARTITION BY Remoteness),2) AS avg_salary_remoteness,
  MAX(salary_in_usd) OVER(PARTITION BY Remoteness) AS max_salary_remoteness,
  MIN(salary_in_usd) OVER(PARTITION BY Remoteness) AS min_salary_remoteness
FROM Salary
WHERE job_title LIKE "% Analyst"
ORDER BY avg_salary_remoteness DESC
```

Q5: Across the different experience levels and job families, what conditions (Level of remoteness and Company Size) tend to have the highest salaries?

```sql
SELECT DISTINCT Remoteness, company_size, 
    COUNT(salary_in_usd) OVER(PARTITION BY Remoteness,company_size) AS number_of_entries,
    ROUND(AVG(salary_in_usd) OVER(PARTITION BY Remoteness,company_size),2) AS avg_salary_remoteness,
    MAX(salary_in_usd) OVER(PARTITION BY Remoteness,company_size) AS max_salary_remoteness,
    MIN(salary_in_usd) OVER(PARTITION BY Remoteness,company_size) AS min_salary_remoteness
FROM Salary
WHERE job_title LIKE "% Scientist" AND experience_level = "Intermediate" 
ORDER BY avg_salary_remoteness DESC
```
Q6 In comparison to the average salary of the job role, does jobs that have higher than average salaries still have the same conditions that pay the higher salaries in the other roles and experience levels?

- This query is done with the Analyst role.
  
```sql
WITH analyst_sal AS (

SELECT DISTINCT Remoteness, experience_level,company_size,
    COUNT(salary_in_usd) OVER(PARTITION BY Remoteness,company_size, experience_level) AS number_of_entries,
    ROUND(AVG(salary_in_usd) OVER(PARTITION BY Remoteness,company_size, experience_level),2) AS avg_salary_remoteness,
    MAX(salary_in_usd) OVER(PARTITION BY Remoteness,company_size, experience_level) AS max_salary_remoteness,
    MIN(salary_in_usd) OVER(PARTITION BY Remoteness,company_size, experience_level) AS min_salary_remoteness
FROM Salary
WHERE job_title LIKE "% Analyst" 
ORDER BY avg_salary_remoteness DESC

)

SELECT *
FROM analyst_sal
WHERE avg_salary_remoteness >
  (SELECT ROUND(AVG (salary_in_usd) OVER(),2) AS avg_sal_ana
  FROM salary
  WHERE job_title LIKE "% Analyst")
																		
AND number_of_entries >= 3
```

## Final Thoughts/Conclusions

### Insights

- When looking at the distribution of employees at different sized companies across the job families, the majority of our entries in our dataset are skewed towards individuals working in medium-sized companies with each job family having roughly 80% of their entries in that company size.
  - Here are each of the distributions across company sizes
1. Analysts
  
| Company Size  | # of employees | Total employees | Percent Total |
| ------------- | ------------- | ------------- | ------------- |
| Large  | 70  | 684 | 10.23% |
| Medium  | 589  | 684 | 86.11% |
| Small  | 25  | 684 | 3.65% |

2. Scientists

| Company Size  | # of employees | Total employees | Percent Total |
| ------------- | ------------- | ------------- | ------------- |
| Large  | 176 | 1063 | 16.56% |
| Medium  | 837  | 1063 | 78.74% |
| Small  | 50  | 1063 | 4.70% |

3. Engineers

| Company Size  | # of employees | Total employees | Percent Total |
| ------------- | ------------- | ------------- | ------------- |
| Large  | 147 | 1640 | 8.96% |
| Medium  | 1437  | 1640 | 87.62% |
| Small  | 56  | 1640 | 3.41% |

- The distribution of employees across the experience levels is primarily senior level and partially at the intermiediate/mid experience level. Across these two levels of experience, the categories together total to around 80-90% of the distribution with the entry level roles being around 7-11% of the distribution and the rest being in the executive level of experience.
  - Here are the distributions across the experience levels
1. Analysts

| Experience Level  | # of employees | Total employees | Percent Total |
| ------------- | ------------- | ------------- | ------------- |
| Entry  | 81 | 684 | 11.84% |
| Executive  | 4  | 684 | 0.58% |
| Intermediate  | 197 | 684 | 28.80% |
| Senior  |  402 | 684 | 58.77% |

2. Scientists

| Experience Level  | # of employees | Total employees | Percent Total |
| ------------- | ------------- | ------------- | ------------- |
| Entry  | 88 | 1063 | 8.28% |
| Executive  | 17 | 1063 | 1.60% |
| Intermediate  | 213 | 1063 | 20.04% |
| Senior  |  745 | 1063 | 70.08% |

3. Engineers

| Experience Level  | # of employees | Total employees | Percent Total |
| ------------- | ------------- | ------------- | ------------- |
| Entry  | 121 | 1640 | 7.38% |
| Executive  | 60 | 1640 | 3.66% |
| Intermediate  | 336 | 1640 | 20.49% |
| Senior  |  1123 | 1640 | 68.48% |

- In terms of the trend in the salaries and demand for individuals in the big data job field from 2020 to 2023, the average salary and maximum salary has increased over time, which means that overall demand for data jobs are increasing. Although different trends are shown across the job families, the overall trend is the increase in demand for these data jobs.
  - Here are the average, maximum, and minimum salaries at each job role
1. Analysts

| Work Year  | Average Salary | Maximum Salary | Minimum Salary |
| ------------- | ------------- | ------------- | ------------- |
| 2020  | $56,107.00 | $135,000 | $6,072 |
| 2021  | $77,978.63 | $200,000 | $9,272 |
| 2022  | $103,922.50 | $430,967 | $5,723 |
| 2023  | $111,618.74 | $385,000 | $12,877 |

2. Scientists

| Work Year  | Average Salary | Maximum Salary | Minimum Salary |
| ------------- | ------------- | ------------- | ------------- |
| 2020  | $109,162.07 | $450,000 | $21,669 |
| 2021  | $90,994.04 | $423,000 | $5,679 |
| 2022  | $137,767.12 | $380,000 | $8,000 |
| 2023  | $161,823.07 | $423,834 | $9,727 |

-Something to note about the data scientist roles is that there is a slight decrease in the average salary in 2021, however, we still proceed to see the increase in average salary till 2023. 

3. Engineers

| Work Year  | Average Salary | Maximum Salary | Minimum Salary |
| ------------- | ------------- | ------------- | ------------- |
| 2020  | $85,916.26 | $250,000 | $21,669 |
| 2021  | $90,031.4 | $276,000 | $5,679 |
| 2022  | $139,642.03 | $375,000 | $8,000 |
| 2023  | $156,474.19 | $342,810 | $9,727 |

- When looking at the amount of employees that work at different levels of remoteness, the majority of the distribution are either on-site or fully remote roles across the different job families.
  - Here are the distributions of people working in each type of remoteness level

1. Analysts

| Remoteness  | # of employees | Average Salary | Maximum Salary | Minimum Salary |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| On-site  | 347 | $113,627.62 | $430,967 | $6,072 |
| Remote  | 307 | $101,50.99 | $250,000 | $5,723 |
| Hybrid  | 30 | $52,278.63 | $125,404 | $8,000 |

2. Scientists

| Remoteness  | # of employees | Average Salary | Maximum Salary | Minimum Salary |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| On-site  | 522 | $151,904.42 | $450,000 | $9,466 |
| Remote  | 471 | $146,520.79 | $416,000 | $5,679 |
| Hybrid  | 70 | $77,854.46 | $423,000 | $12,000 |

3. Engineers

| Remoteness  | # of employees | Average Salary | Maximum Salary | Minimum Salary |
| ------------- | ------------- | ------------- | ------------- | ------------- |
| On-site  | 883 | $150,760.26 | $342,810 | $5,882 |
| Remote  | 700 | $141,679.48 | $375,000 | $5,132 |
| Hybrid  | 57 | $80,113.7 | $250,000 | $7,500 |

