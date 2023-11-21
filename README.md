# Data Science Job Family Salary Analysis

## Introduction

### Background/Problem Statement

In 2021, Harvard Business Review published an article titled _Data Scientist: The Sexiest Job of the 21st Century_. Within this article, the main topics discussed were how Big data jobs and jobs within that job family will have a boom and increase in demand due to how corporations see them as valuable assets when it comes to making business decisions. Through reading this article, I wanted to conduct a personal study/project about the job family by using data of salaries given to the job family from 2020 to 2023 and seeing if the statement made by the Harvard article was true. Another objective of this study was deducing what kind of factors affected their salary(Examples: Company Size and Level of Remoteness). 

### Fields/Preview

Within the Kaggle dataset regarding Data Job Salaries from 2020-2023, my objective is to look into 3 specific roles within the Data Science Job Family, which are
1. Data Analyst or any non-lead/managerial analyst role(Quality Analyst, Financial Data Analyst, etc.)
2. Data Scientist or any non-lead/managerial scientist role (Machine Learning, Applied Scientist, etc.)
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
(Small:Around 100 employees
Medium: Around 500 employees
Large: 1000+ employees)

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

## Questions/Exploratory Data Analysis
1. Across our 3 job families, what is the distribution of individuals that work in the different sizes of companies and the percent total of each company size?
2. When looking at the different levels of experience level within our job families, how many individuals fall under each experience level(Entry, Intermediate, Etc.)?
3. Throughout the years our dataset covers, What is the general trend in terms of demand and their respective pay across our 3 job families?
4. Within our dataset, what are the number of individuals under each of the different levels of remoteness (Remote, Hybrid, On-site)?
5. Across the different experience levels and job families, what conditions (Level of remoteness and Company Size) tend to have the highest salaries?
   - Example: For Entry Level Analyst, hybrid analyst roles at a smaller companies tends to issue the higher salaries on average than the other types of conditions?
      - This is done for the different levels of experience/job families (Intermediate Level Scientists, Senior Level Engineers, etc.)
