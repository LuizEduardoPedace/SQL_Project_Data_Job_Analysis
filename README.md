# Introduction

📊 Dive into the data job market! Focusing on data scientists roles, this project explores 💰 top-paying jobs, 🔥 in-demand skills, and 📈 where high demand meets high salary in data analytics.

🔍 SQL queries? Check them out here: [project_sql folder](/project_sql/)

# Background

Driven by a quest to navigate the data scientist job market more effectively, this project was born from a desire to pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.

### The questions I wanted to answer through my SQL queries were:

1. What are the top-paying data scientist jobs?
2. What skills are required for these top-paying jobs?
3. What skills are most in demand for data scientists?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn?

# Tools I Used

- **SQL:** The backbone of my analysis, allowing me to query the database and unearth critical insights.
- **PostgreSQL:** The chosen database management system, ideal for handling the job posting data.
- **Visual Studio Code:** My go-to for database management and executing SQL queries.
- **Git & GitHub:** Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.

# The Analysis

Each query for this project aimed at investigating specific aspects of the data scientist job market. Here’s how I approached each question:

### 1. Top Paying Data Scientist Jobs

To identify the highest-paying roles, I filtered data scientist positions by average yearly salary and location, focusing on remote jobs. This query highlights the high paying opportunities in the field.

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_year_avg,
    job_posted_date,
    name AS company_name
FROM job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE 
    job_title_short LIKE '%Data Scientist%' AND 
    job_location LIKE 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10
```
Here's the breakdown of the top data scientist jobs in 2023:
- **High Salaries:** Data science roles, especially at senior and leadership levels, command impressive salaries, reflecting the high demand for specialized skills.
- **Diverse Roles:** The variety of positions, from quant research to product management, illustrates the wide applicability of data science across industries.
- **Strategic Importance:** As organizations increasingly rely on data, the strategic importance of data science roles is only expected to grow, making them pivotal in shaping business strategies.

![Top Paying roles](assets/Top%20Paying%20Data%20Science%20jovs%20in%202023.png)
*Bar graph visualizing the salary for the top 10 salaries for data analysts*

### 2. Skills for Top Paying Jobs

To understand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.

```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE 
        job_title_short LIKE '%Data Scientist%' AND 
        job_location LIKE 'Anywhere' AND
        salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)

SELECT
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC
```

Here's the breakdown of the most demanded skills for the top 10 highest paying data science jobs in 2023:

- **SQL** – **4** occurrences
- **Python** – **3** occurrences
- **Java**, **Cassandra**, **Spark**, **Hadoop**, and **Tableau** – **2** occurrences each
- Other important skills mentioned once include **C++**, **Azure**, **AWS**, **TensorFlow**, **Keras**, **PyTorch**, **scikit-learn**, and **DataRobot**.

![Top paying skills](/assets/Top%20skills%20in%20High-Paying%20Data%20Scientist%20Roles%20(2023).png)
*Bar graph visualizing the count of skills for the top 10 paying jobs for data science; ChatGPT generated this graph from my SQL query results*


### 3. In-Demand Skills for Data Analysts

This query helped identify the skills most frequently requested in job postings, directing focus to areas with high demand.

```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short LIKE '%Data Scientist%' 
GROUP BY
    skills
ORDER BY demand_count DESC
LIMIT 5
```

Here's the breakdown of the most demanded skills for data scientists in 2023:

- **Python** leads with **140,012** demand counts, highlighting its essential role in various data tasks, from analysis to machine learning.
- **SQL** follows with **97,835**, underscoring its foundational importance for managing and querying large datasets.
- **R** is significant with **72,526**, particularly in statistical analysis and research contexts.
- **SAS** and **Tableau** each see demand counts of approximately **35,000**, indicating a focus on analytics in specific industries and the growing importance of effective data visualization.

| Skill      | Demand Count |
|------------|--------------|
| **Python** | 140,012      |
| **SQL**    | 97,835       |
| **R**      | 72,526       |
| **SAS**    | 35,934       |
| **Tableau**| 35,472       |

*Table of the demand for the top 5 skills in data science job postings*


### 4. Skills Based on Salary

Exploring the average salaries associated with different skills revealed which skills are the highest paying.

```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE job_title_short LIKE '%Data Scientist%' AND salary_year_avg IS NOT NULL
GROUP BY skills
ORDER BY avg_salary DESC
LIMIT 25
```

Here's a breakdown of the results for top paying skills for Data Science:

- **Top-Paying Skill**: **Asana** leads with the highest average salary at **$200,284**, highlighting the importance of project management in data science roles.
- **Emerging Tech**: Skills like **Neo4j** (graph databases) and **Solidity** (blockchain) offer high salaries, reflecting demand for expertise in cutting-edge technologies.
- **Niche Programming**: Languages like **Elixir**, **Lua**, and **Haskell** command premium pay, showing the value of specialized knowledge.
- **AI & ML Tools**: Frameworks such as **Hugging Face** and **Theano** emphasize the growing importance of AI/ML expertise in high-paying data science roles.
- **Collaboration Tools**: High salaries for skills like **Slack**, **Zoom**, and **Notion** underscore the need for managing workflows effectively in hybrid work environments.

| **Skill**         | **Average Salary (USD)** |
|-------------------|--------------------------|
| Asana             | $200,284                 |
| Airtable          | $189,600                 |
| RedHat            | $189,500                 |
| Watson            | $183,460                 |
| RingCentral       | $182,500                 |
| Neo4j             | $170,861                 |
| Elixir            | $170,824                 |
| Lua               | $170,500                 |
| Solidity          | $166,980                 |
| Ruby on Rails     | $166,500                 |

*Table of the average salary for the top 10 paying skills for data scientists*


### 5. Most Optimal Skills to Learn

Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and have high salaries, offering a strategic focus for skill development.

```sql
WITH skills_demand AS (
    SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        COUNT(skills_job_dim.job_id) AS demand_count
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short LIKE '%Data Scientist%' AND salary_year_avg IS NOT NULL
    GROUP BY
        skills_dim.skill_id
), average_salary AS (
        SELECT
        skills_dim.skill_id,
        skills_dim.skills,
        ROUND(AVG(salary_year_avg), 0) AS avg_salary
    FROM job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
    WHERE job_title_short LIKE '%Data Scientist%' AND salary_year_avg IS NOT NULL
    GROUP BY skills_dim.skill_id
)

SELECT
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    average_salary.avg_salary
FROM skills_demand
INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
WHERE demand_count > 10
ORDER BY
    avg_salary DESC,
    demand_count DESC
LIMIT 25
```

| **Skill**      | **Demand Count** | **Average Salary ($)** |
|----------------|------------|------------------------|
| Watson         | 19         | $183,460               |
| Neo4j          | 44         | $170,861               |
| Dplyr          | 16         | $163,111               |
| Slack          | 22         | $162,306               |
| DynamoDB       | 23         | $160,361               |
| Notion         | 12         | $159,177               |
| Airflow        | 241        | $158,922               |
| Hugging Face   | 28         | $156,812               |
| Zoom           | 22         | $155,873               |
| RShiny         | 24         | $154,712               |

*Table of the most optimal skills for data scientist sorted by salary*

Here's a breakdown of the most optimal skills for Data Scientists in 2023:

- **High-Paying Skills with Low Demand**: Tools like **Watson**, **Neo4j**, and **Dplyr** offer high salaries (over $160K), despite having relatively low demand (below 50). These are specialized skills with high value for specific roles.

- **Collaboration Tools**: Skills like **Slack** and **Zoom** are both in moderate demand and provide solid salaries, indicating the growing importance of effective communication in data science projects.

- **Emerging Data Technologies**: **DynamoDB** and **Airflow**, which are used for data management and workflow orchestration, are in high demand, especially **Airflow** with 241 demand instances. Their salaries are also competitive, reflecting their increasing significance in large-scale data operations.

- **Niche and Cutting-Edge Skills**: Tools like **Hugging Face** (AI/ML) and **RShiny** (data visualization) have moderate demand, showing that AI-related skills are important, but still not as widely adopted as mainstream data tools.

# Conclusion

The insights reveal a dynamic landscape in the data science field, characterized by high salaries and a diverse array of roles driven by increasing organizational reliance on data. Key skills such as SQL and Python are fundamental, while emerging technologies like Neo4j and blockchain tools offer significant earning potential, despite lower demand. Notably, collaboration skills are becoming essential in hybrid work environments, highlighting the need for effective communication in data projects.

Moreover, while certain high-paying skills, like Asana and specialized programming languages, stand out, there is also a trend toward the importance of niche skills in AI and data management technologies like Airflow. Overall, the demand for specialized knowledge in both traditional and cutting-edge areas continues to shape career opportunities, underscoring the strategic value of data science roles in contemporary business environments.