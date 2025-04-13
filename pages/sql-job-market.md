---
layout: default
title: SQL Job Market Analysis
permalink: /sql-job-market/
---

# Data Analyst Job Market Analysis (UK)

## 1. Introduction
This project contains an in-depth **SQL-based analysis** of the **Data Analyst job market in the UK**, with a focus on the most in-demand and highest-paying skills. The goal was to identify key trends, optimal skills to learn, and salary insights for professionals looking to break into or advance within the field.

## 2. Background
The demand for Data Analysts continues to grow, driven by the need for data-driven decision-making across industries. However, not all skills are valued equally in the job market. This project aimed to answer:
- Which skills are most frequently required in Data Analyst job postings?
- Which skills lead to the highest salaries?
- What are the most optimal skills to learn for career advancement?

## 3. Tools Used
- **SQL** for data querying and analysis
- **CSV files** to store the data
- **Git & GitHub** for version control

## 4. The Analysis

### Key Queries and Files

The project is structured as follows:

project_sql

1_top_paying_jobs.sql → Identifies the highest-paying jobs
2_job_skills_required.sql → Extracts required skills from job postings
3_top_demanded_skills.sql → Analyzes skill frequency in job postings
4_top_paying_skills.sql → Determines the highest-paying skills
5_optimal_skills.sql → Identifies the best skills to learn based on demand and salary
top_paying_skills.csv → Processed dataset for further analysis

sql_edit

2_dates.sql → Handles date-based job data processing
3_cases.sql → Manages case-based conditional logic in SQL

sql_load

1_create_database.sql → Creates the database schema
2_create_tables.sql → Defines tables for job postings and skills
3_modify_tables.sql → Adjusts table structures for optimized querying

## 5. Outcomes

### Most In-Demand Skills for Data Analysts in the UK

| Skill   | Job Postings Requiring It |
|---------|---------------------------|
| **SQL**    | 29 |
| **Excel**  | 20 |
| **Python** | 19 |
| **Tableau** | 10 |
| **R**      | 8 |

SQL is the most critical skill, followed by Excel and Python, reinforcing the importance of **data querying, manipulation, and visualization**.

### Highest-Paying Skills

| Skill | Average Salary (£) |
|--------|------------------|
| **C++** | 177,283 |
| **Pandas** | 177,283 |
| **PyTorch** | 177,283 |
| **NumPy** | 177,283 |
| **TensorFlow** | 177,283 |
| **MongoDB** | 165,000 |
| **AWS** | 131,438 |
| **SQL Server** | 120,379 |
| **Airflow** | 118,140 |
| **JavaScript** | 111,175 |

### Key Takeaways
- **Machine Learning & AI skills (TensorFlow, PyTorch, NumPy) command top salaries.**
- **Big Data & Cloud technologies (MongoDB, AWS, SQL Server) offer strong salaries.**
- **SQL-based database skills remain valuable across all salary ranges.**
- **DevOps & automation tools (Airflow, Kubernetes, GitHub) show increasing relevance.**
- **Traditional programming languages like JavaScript have lower pay in data-focused roles.**

## 6. Queries used

1_top_paying_jobs.sql 

```
/*
- Identify the top 10 highest-paying Data Analyst roles in the UK.
- Remove nulls
*/

SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    company_dim.name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
    job_title_short LIKE '%Data Analyst%' AND
    job_location LIKE '%UK%' AND
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT
    10;
```

2_job_skills_required.sql 

```
/* What are the skills required for these roles? */


WITH job_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        job_location,
        job_schedule_type,
        salary_year_avg,
        job_posted_date,
        company_dim.name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE
        job_title_short LIKE '%Data Analyst%' AND
        job_location LIKE '%UK%' AND
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
    LIMIT
        10
)

SELECT
    job_paying_jobs.*,
    skills_dim.skills AS skill_required
FROM job_paying_jobs
INNER JOIN skills_job_dim ON job_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC;
```

3_top_demanded_skills.sql 

```
/*
- What are the most in-demand skills?
*/

SELECT 
    skills_dim.skills AS skill_name,
    COUNT(job_postings_fact.job_id) AS job_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short LIKE '%Data Analyst%' AND
    job_location LIKE '%UK%' AND
    salary_year_avg IS NOT NULL
GROUP BY
    skills_dim.skill_id, skill_name
ORDER BY
    job_count DESC
LIMIT 5;
```

4_top_paying_skills.sql 

```
/*
What are the top skills based on salary?
*/

SELECT 
    skills_dim.skills AS skill_name,
    ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS average_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short LIKE '%Data Analyst%'
    AND job_location LIKE '%UK%' 
    AND salary_year_avg IS NOT NULL
GROUP BY
    skills_dim.skill_id, skill_name
ORDER BY
    average_salary DESC
LIMIT 25;
```

5_optimal_skills.sql 

```
/*
What are the most optimal skills to learn?
*/

-- combine both CTEs inside a single WITH statement, separating them with a comma

WITH skills_demand AS (
    SELECT
        skills_job_dim.skill_id,
        skills_dim.skills AS skill_name,
        COUNT(job_postings_fact.job_id) AS job_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short LIKE '%Data Analyst%'
        AND job_location LIKE '%UK%' 
        AND salary_year_avg IS NOT NULL
    GROUP BY
        skills_job_dim.skill_id, skill_name
),
average_salaries AS (
    SELECT
        skills_job_dim.skill_id,
        skills_dim.skills AS skill_name,
        ROUND(AVG(job_postings_fact.salary_year_avg), 0) AS average_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE
        job_title_short LIKE '%Data Analyst%'
        AND job_location LIKE '%UK%' 
        AND salary_year_avg IS NOT NULL
    GROUP BY
        skills_job_dim.skill_id, skill_name
)

SELECT
    skills_demand.skill_id,
    skills_demand.skill_name,
    job_count,
    average_salary
FROM
    skills_demand
INNER JOIN
    average_salaries
ON
    skills_demand.skill_id = average_salaries.skill_id
WHERE
    job_count > 5
ORDER BY
    average_salary DESC,
    job_count DESC;
```

3_cases.sql 

```
SELECT *
FROM skills_job_dim
LIMIT 10;

SELECT *
FROM job_postings_fact
LIMIT 10;

SELECT *
FROM skills_dim
LIMIT 10;


SELECT 
    COUNT(skills_job_dim.job_id) AS count_of_posts,
    skills_job_dim.skill_id,
    skills_dim.skills  AS skill_name
FROM skills_job_dim
INNER JOIN job_postings_fact ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_dim.skill_id = skills_job_dim.skill_id
WHERE job_postings_fact.job_location = 'Anywhere'
GROUP BY skills_job_dim.skill_id, skills_dim.skills
ORDER BY count_of_posts DESC
LIMIT 5;

SELECT job_title_short, salary_year_avg
FROM job_postings_fact
WHERE EXTRACT(MONTH FROM job_posted_date) IN (1, 2, 3)
AND salary_year_avg > 70000
AND job_title_short = 'Data Analyst'
ORDER BY salary_year_avg DESC
LIMIT 10;
```


