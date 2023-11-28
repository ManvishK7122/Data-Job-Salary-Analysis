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

### Questions/Exploratory Data Analysis(EDA)
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

## Findings/Insights

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

- Something to note about the data scientist roles is that there is a slight decrease in the average salary in 2021, however, we still proceed to see the increase in average salary till 2023. 

3. Engineers

| Work Year  | Average Salary | Maximum Salary | Minimum Salary |
| ------------- | ------------- | ------------- | ------------- |
| 2020  | $85,916.26 | $250,000 | $21,669 |
| 2021  | $90,031.4 | $276,000 | $5,679 |
| 2022  | $139,642.03 | $375,000 | $8,000 |
| 2023  | $156,474.19 | $342,810 | $9,727 |

- When looking at the amount of employees that work at different levels of remoteness, the majority of the distribution is either on-site or fully remote roles across the different job families.
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

- Across the different experience levels and job families, the companies that have the highest average pay are medium sized companies and occationally larger/corporations at certain experience levels. When looking into what level of remoteness has the highest average salary usually on-site and fully remote jobs pay the highest which confirms the previous insight.
  - Here are the top 3 average salaries for each of the conditions.

***Analysts***

1. Entry Level Analysts 

| Remoteness  | Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- |
| Hybrid  | Small | 2 | $90,202.00 | 
| Remote  | Medium | 21 | $69,340.90 |
| On-site  | Medium | 13 | $64,958.54 |

- Something to note and I found interesting is that there are only 2 employees who were Hybrid Analysts at small sized companies, so it could be seen as not enough data and evidence to concretely conclude the fact that they have the highest average salary.  

2. Mid/Intermediate Level Analysts

| Remoteness  | Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- |
| On-site  | Medium | 115 | $111,422.22 | 
| Hybrid  | Medium | 2 | $85,161.50 |
| Remote  | Medium | 45 | $84,061.91 |

- Similar to the entry level positions, the hybrid analysts and medium sized companies have one of the highest average salaries in the group however, there are a lack of number of employees in the category to confirm this insight. The next highest average salary would be fully remote analysts at large companies with an average salary of $80,635.86. 

3. Senior Level Analysts

| Remoteness  | Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- |
| Remote  | Large | 5 | $145,930.80 | 
| On-site  | Large | 3 | $140,700.00 |
| On-site  | Medium | 204 | $121,357.82 |

4. Executive Level Analysts

| Remoteness  | Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- |
| Remote  | Large | 1 | $150,000.00 | 
| Remote  | Medium | 2 | $120,000.00 |
| On-site  | Medium | 1 | $15,000.0 |

- When looking at the executive level analysts, I believe that there is a lack employees within each field to concretely conclude that certain company sizes and levels of remoteness pay the highest salaries on average.
  - Along with the lack of entries, there is also a massive gap between the remote analysts and on-site analysts at medium sized companies.
    - Since the reasons mentioned above this data could be seen as an inaccurate representation of the executive level analysts salaries.
   
***Scientists***

1. Entry Level Scientists

| Remoteness  | Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- |
| On-site  | Large | 10 | $130,590.40 | 
| On-site  | Medium | 16 | $106,779.00|
| Hybrid  | Large | 11 | $79,632.09 |

- Something to note for entry level scientists is the gap between the on-site roles at a medium sized company and the hybrid role at a large company. The difference is substantial with almost a $30k difference in salary compared to on-site analysts at medium sized companies.

2. Mid/Intermediate Level Scientists

| Remoteness  | Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- |
| On-site  | Medium | 70 | $118,588.80 | 
| Remote  | Medium | 58 | $114,117.64|
| Hybrid  | Large | 20 | $100,541.75 |

3. Senior Level Scientists

| Remoteness  | Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- |
| On-site  | Large | 51 | $180,745.82 | 
| On-site  | Small | 2 | $175,618.50 |
| Remote  | Medium | 300 | $167,965.91 |

- Similar to the entry-level analysts, there are only 2 entries for on-site scientists at small companies, so this can be seen as not enough concrete evidence to conclude their average salary. If not including these entries, then the next highest average salary would be on-site scientists at medium sized companies with an average salary of $159,940.17. 

4. Executive Level Scientists

| Remoteness  | Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- |
| Remote  | Small | 4 | $249,000.00 | 
| On-site  | Medium | 10 | $187,875.00 |
| Remote  | Medium | 2 | $144,500.00 |

- Compared to the executive level analysts, there are more executive level employees among the scientist roles. Although compared to the other levels of experience, it is still quite difficult to deduce if this is entirely accurate, however, this does give a better esitmation in companrison to the analysts. 

***Engineers***

1. Entry Level Engineers

| Remoteness  | Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- |
| On-site  | Medium | 53 | $126,661.91 | 
| On-site  | Small | 1 | $108,000.0|
| Hybrid  | Large | 7 | $98,946.43 |

- Among entry level engineers there is only one entry so the next highest average salary would be remote engineers at small sized companies with an average salary of $74,633.42.

2. Mid/Intermediate Level Engineers

| Remoteness  | Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- |
| On-site  | Medium | 147 | $129,016.65 | 
| Remote  | Large | 25 | $111,521.56 |
| Remote  | Medium | 124 | $97,392.41 |

3. Senior Level Engineers

| Remoteness  | Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- |
| On-site  | Large | 27 | $173,172.26 | 
| Remote  | Medium | 402 | $162,259.08 |
| On-site  | Medium | 623 | $158,096.48 |

4. Executive Level Engineers

| Remoteness  | Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- |
| Remote  | Medium | 36 | $227,945.44 | 
| On-site  | Medium | 22 | $173,686.36 |
| Remote  | Small | 1 | $115,222.00 |

- Among our executive level engineers, the majority of our entries are distributed between remote and on-site engineers at medium sized companies. However, the amount of entries is abundant enough to make concrete conclusions on the average salaries across levels of remoteness and different company sizes.
  - This cannot be stated however for remote engineers at small sized companies.
 
- From our previous insight, medium and larged sized companies tend to pay the on-site and fully remote roles the highest average salary compared to the other circumstances. To confirm and make sure that this insight is concrete, is the same pattern shown when looking at the circumstances that pay higher than average salaries across the 3 job classes?
  - This table is shown across different levels of experience and aims to confirm the previous insight for each level of job role within the family. Along with this to ensure we have concrete evidence, I made sure to only include categories with more than 3 individuals in the category.
 
***Analysts***

| Remoteness  | Experience Level |Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- | ------------- |
| Remote | Senior | Large | 5 |	$145,930.80 |
| On-site | Senior | Large | 3 | $140,700.00 |
| On-site | Senior | Medium | 204 | $121,357.82 |
| Remote | Senior | Medium | 180 | $117,444.87 |
| On-site | Intermediate | Medium | 115| $111,422.22 |
| Remote | Senior | Small | 8 |	$106,350.75 |

***Scientists***

| Remoteness  | Experience Level |Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- | ------------- |
| Remote | Executive | Small | 4 | $249,000.00 |
| On-site | Executive | Medium | 10 | $187,875.00 |
| On-site | Senior | Large | 51 | $180,745.82 |
| Remote | Senior | Medium | 300 | $167,965.91 |
| On-site | Senior | Medium | 344| $159,940.17 |
| Remote | Senior | Large | 31 | $155,135.87 |

***Engineers***

| Remoteness  | Experience Level |Company Size | Number of employees | Average Salary | 
| ------------- | ------------- | ------------- | ------------- | ------------- |
| Remote | Executive | Medium | 36 | $227,945.44 |
| On-site | Executive | Medium | 22 | $173,686.36 |
| On-site | Senior | Large | 27 | $173,172.26 |
| Remote | Senior | Medium | 402 | $162,259.08 |
| On-site | Senior | Medium | 623 | $158,096.48 |
| Remote | Senior | Large | 37 | $150,560.14 |

- From the tables above, the statement is still true that on-site and fully remote roles at medium and larged sized companies tend to issue the highest average salary across the job types. 

## Conclusions

### Summary/EDA Conclusion

- Across our 3 job families, the distribution of employees within each size of company is heavily skewed towards **Medium** sized companies.
- When looking at the distribution of employees across the different levels of experience, the majority of employees are **primarily Senior** level positions and **Mid/Intermediate** level after.
- In retrospect to Harvard's statement towards the demand of big data jobs increasing, the **increase in average salary** across the years observed (2020-2023) proves that the demand for these big data jobs are continuing to rise.
  - This statement is true across the 3 different job families explored in this study.
- Observing the distribution of employees across the different levels of remoteness, the dataset's level of employeement is heavily skewed towards **On-site** and **Remote** roles across the job families.

### Recommendations 


