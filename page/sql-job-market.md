---
layout: default
title: SQL Job Market Analysis
permalink: /sql-job-market/
---

# SQL Job Market Analysis

Here are the SQL queries used in this project:

```sql
SELECT job_title, avg_salary
FROM jobs
ORDER BY avg_salary DESC
LIMIT 10;
```
