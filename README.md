# Association Rule-Based Skill Recommendation

A data mining project that compares Apriori and FP-Growth for two tasks:

- Discovering relationships between job titles and technical skills.
- Discovering co-occurrence relationships among technical skills.
---
## Problem Context

The Information Technology (IT) industry is evolving rapidly, resulting in constantly changing skill requirements in the labor market. Meanwhile, university curricula primarily focus on foundational and theoretical knowledge, which often creates a gap between graduates' competencies and industry expectations.

In addition, students frequently face challenges in identifying which skills should be prioritized for specific career paths due to the diversity and dynamic nature of job market requirements. Therefore, mining recruitment data to uncover relationships between job positions and technical skills is a meaningful and practical task.
---
## Research Problems

### Problem 1: Job Title → Skill Association Discovery

This problem focuses on discovering association rules between job titles and technical skills from the VietJobs_IT dataset.

The objective is to identify the skills commonly required for specific job positions and provide insights that can help students align their learning paths with real-world industry demands.

**Example:**

```text
Data Analyst → SQL, Power BI
Backend Developer → Java, Spring Boot
```
### Problem 2: Skill → Skill Association Discovery
Technical skills often appear together rather than independently in job postings. This problem aims to discover association rules among technical skills to identify frequently co-occurring skill sets.

The extracted rules reveal hidden relationships between skills and can be used to support skill recommendation and learning path planning.
**Example:**

```text
Python → SQL
SQL, Python → Power BI
Docker → Kubernetes
```
---
## Project Structure
```text
skill-recommendation-using-association-rules/
├── data/
│   ├── raw/
│   │   └── VietJobs_IT.csv
│   └── processed/
│       ├── processed_jobs.csv
│       ├── transaction_matrix.csv
│       └── unique_skills.txt
│ 
├── docs/
│   ├── Apriori.md
│   ├── Association Rules.md
│   ├── Confidence.md
│   ├── FP-Growth.md
│   ├── Lift.md
│   └── Support.md
│   
├── notebooks/
│   ├── EDA_and_Preprocessing.ipynb
│   ├── Problem_1.ipynb
│   └── Problem_2.ipynb
│ 
├── .gitignore
├── README.md
└── requirements.txt
```