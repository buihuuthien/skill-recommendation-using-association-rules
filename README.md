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

## Result
### Prolem 1:
#### Kết quả lần chạy gần nhất

Dữ liệu gồm 1.830 tin thuộc 10 nhóm role; 1.464 tin dùng để khai phá và 366 tin giữ lại để đánh giá. Có 304 tin test có danh sách kỹ năng hợp lệ được dùng tính chất lượng gợi ý.

| Mô hình | K | Precision@K | Recall@K | Hit@K | NDCG@K |
|---|---:|---:|---:|---:|---:|
| Apriori | 5 | 0,256 | 0,259 | 0,599 | 0,353 |
| FP-Growth | 5 | 0,256 | 0,259 | 0,599 | 0,353 |
| Baseline phổ biến toàn cục | 5 | 0,164 | 0,194 | 0,516 | 0,228 |
| Apriori | 10 | 0,152 | 0,287 | 0,625 | 0,333 |
| FP-Growth | 10 | 0,152 | 0,287 | 0,625 | 0,333 |
| Baseline phổ biến toàn cục | 10 | 0,133 | 0,264 | 0,684 | 0,257 |

Apriori và FP-Growth cho cùng 94 luật và cùng điểm chất lượng trong lần chạy này. Cả hai vượt baseline về Precision, Recall và NDCG ở K=5,10; Hit@5 cũng cao hơn. Ở K=10, Hit@10 thấp hơn baseline (0,625 so với 0,684), trong khi baseline luôn lấp đủ 10 vị trí gợi ý còn mô hình luật có coverage 0,412. Apriori chạy 14,44 giây, FP-Growth 14,87 giây; FP-Growth dùng ít bộ nhớ Python hơn (143,53 MB so với 208,16 MB). Các số đo phụ thuộc môi trường chạy.

#### Biểu đồ

##### Recommendation Quality

![So sánh chất lượng gợi ý Problem 1](problem_1/figures/recommendation_quality.png)

##### Benchmark tài nguyên

![So sánh runtime, số luật và bộ nhớ Problem 1](problem_1/figures/resource_benchmark.png)

### Prolem 2:

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