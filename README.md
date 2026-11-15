# Association Rule-Based Skill Recommendation

A data-mining project using the VietJobs_IT recruitment dataset to discover job-role skill requirements and evaluate context-aware next-skill recommendations. Apriori and FP-Growth are compared with task-matched baselines rather than only a global popularity benchmark.

---

## Research Problems

### Problem 1 — Job Role to Technical Skills

Discover `ROLE → skill` associations and test whether filtered association rules improve ranking over counting skill frequency within the same role. Evaluation uses stratified 5-fold cross-validation. This role-conditioned popularity baseline is intentionally strong because it captures the same role-to-skill frequency information without support, confidence, or lift filtering.

Mine luật ROLE → skill và kiểm tra xem luật sau khi lọc support/confidence/lift có xếp hạng tốt hơn việc đếm tần suất skill trong cùng role hay không. Đánh giá bằng stratified 5-fold cross-validation.

Baseline là Role Popularity: với mỗi role, đếm tần suất từng skill trong train rồi xếp hạng. Baseline này không lọc support/confidence/lift, nên giữ được cả skill ít gặp mà bước lọc luật có thể đã loại bỏ

### Problem 2 — Next-Skill Recommendation from Role and Known Skills

Given a role and skills already known, recommend a relevant skill using contextual rules equivalent to `{ROLE, known skill(s)} → next skill`. Evaluation hides skills from held-out postings and tests whether the models recover them. This is a masked-skill recovery experiment, not a real chronological learning sequence.

Cho một role và một số skill đã biết, gợi ý skill tiếp theo bằng luật ngữ cảnh dạng {ROLE, known skill(s)} → next skill. Cách đánh giá: giấu đi một phần skill của tin tuyển dụng trong test, dùng các skill còn lại làm ngữ cảnh, rồi kiểm tra mô hình có khôi phục được các skill bị giấu hay không. Đây là thí nghiệm masked-skill recovery, không phải mô phỏng thứ tự học thực tế.

---

## Results

Dữ liệu gồm các tin tuyển dụng IT đã qua tiền xử lý, tách thành 10 nhóm role và trích xuất danh sách skill kỹ thuật. Chi tiết tiền xử lý nằm trong notebooks/EDA_and_Preprocessing.ipynb.

Metric dùng trong cả hai bài toán:

- Precision@K — số skill liên quan trong K gợi ý đầu chia cho K.

- Recall@K — số skill liên quan trong K gợi ý đầu chia cho tổng số skill đích của query.

- Hit@K — tỷ lệ query có ít nhất một skill liên quan trong K gợi ý đầu.

- NDCG@K — chất lượng xếp hạng, ưu tiên skill liên quan xuất hiện sớm.

- Coverage — tỷ lệ slot gợi ý (trong K slot) mà mô hình thực sự điền được. Precision vẫn lấy K làm mẫu số khi mô hình trả ít hơn K gợi ý, nên coverage giúp thấy rõ giới hạn này.

K là số gợi ý đầu được chấm (K=5, K=10), không phải số fold. Điểm trong bảng là trung bình qua 5 fold; độ lệch chuẩn nằm trong các file CSV kết quả.

### Problem 1: Job Role → Technical Skills

Mine luật `ROLE → skill` và kiểm tra xem luật sau khi lọc support/confidence/lift có xếp hạng tốt hơn việc đếm tần suất skill trong cùng role hay không. Đánh giá bằng stratified 5-fold cross-validation.

Baseline là **Role Popularity**: với mỗi role, đếm tần suất từng skill trong train rồi xếp hạng. Baseline này không lọc support/confidence/lift, nên giữ được cả skill ít gặp mà bước lọc luật có thể đã loại bỏ.

| Model | Precision@5 | Recall@5 | Hit@5 | NDCG@5 | Precision@10 | Recall@10 | Hit@10 | NDCG@10 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Apriori | 0.282 | 0.331 | 0.675 | 0.411 | 0.172 | 0.367 | 0.708 | 0.397 |
| FP-Growth | 0.282 | 0.331 | 0.675 | 0.411 | 0.172 | 0.367 | 0.708 | 0.396 |
| **Role Popularity baseline** | **0.315** | **0.384** | **0.744** | **0.448** | **0.232** | **0.527** | **0.841** | **0.484** |

Baseline thắng ở cả 4 metric với K=5 và K=10. Coverage của baseline là 1.00, trong khi luật phủ trung bình 0.777 (K=5) và 0.521 (K=10). Nghĩa là khi antecedent chỉ có role, luật gần như chỉ phản ánh lại tần suất skill trong role; bước lọc support/confidence/lift còn làm mất một phần candidate hữu ích. Với dữ liệu và ngưỡng hiện tại, luật ROLE → skill chưa cải thiện xếp hạng so với đếm tần suất theo role.

| Model | Median runtime (s) | Mean rules per fold | Median Python memory (MB) |
|---|---:|---:|---:|
| Apriori | 0.242 | 58.4 | 51.965 |
| FP-Growth | 0.536 | 58.4 | 3.180 |
| Role Popularity baseline | 0.016 | N/A | 0.383 |

Bộ nhớ đo bằng `tracemalloc`, không bao gồm toàn bộ cấp phát native của NumPy/pandas. File benchmark có kèm độ lệch chuẩn theo fold.

**Figures:** [Recommendation quality](result/problem_1/figures/recommendation_quality.png) · [Runtime, rule count, and memory](result/problem_1/figures/benchmark.png)

**CSV files:** [Quality metrics (mean and standard deviation)](result/problem_1/quality_metrics.csv) · [Fold-level scores](result/problem_1/quality_metrics_by_fold.csv) · [Benchmark](result/problem_1/benchmark.csv) · [Apriori rules](result/problem_1/rules_apriori.csv) · [FP-Growth rules](result/problem_1/rules_fpgrowth.csv) · [Per-role rule coverage](result/problem_1/role_coverage.csv)

### Problem 2: Next-Skill Recommendation from Role and Known Skills

Đánh giá trên 1.289 query test hợp lệ. Mỗi query giấu 20% skill của tin trong test; phần còn lại làm ngữ cảnh; skill bị giấu là đích. Baseline là Role Popularity: xếp hạng skill theo tần suất trong role, loại các skill đã biết. Baseline thấy role nhưng không dùng tổ hợp skill đã biết, nên phép so sánh kiểm tra được liệu luật ngữ cảnh có thêm thông tin ngoài tần suất role hay không.


| Model | Precision@5 | Recall@5 | Hit@5 | NDCG@5 | Precision@10 | Recall@10 | Hit@10 | NDCG@10 |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| Apriori contextual rules | **0.141** | 0.427 | 0.541 | **0.386** | 0.081 | 0.475 | 0.584 | 0.405 |
| FP-Growth contextual rules | **0.141** | 0.427 | 0.542 | **0.386** | 0.081 | 0.475 | 0.584 | 0.405 |
| **Role Popularity baseline** | 0.124 | **0.439** | 0.540 | 0.373 | **0.085** | **0.570** | **0.683** | **0.422** |

Ở K=5, luật nhỉnh hơn baseline về Precision (0.141 so với 0.124) và NDCG (0.386 so với 0.373), nhưng Recall thấp hơn và Hit gần như hòa. Ở K=10, baseline thắng cả 4 metric. Coverage của luật trung bình 0.691 (K=5) và 0.532 (K=10), so với 1.00 của baseline.

Kết luận: ngữ cảnh skill đã biết giúp ích một chút ở top ngắn (K=5), nhưng chưa đủ để vượt baseline trên toàn bộ danh sách. Apriori và FP-Growth cho kết quả gần như trùng nhau, khác biệt chủ yếu ở tài nguyên.

| Model | Median runtime (s) | Mean rules per fold | Median Python memory (MB) |
|---|---:|---:|---:|
| Apriori | 1.908 | 10,015.4 | 8.682 |
| FP-Growth | 2.333 | 10,015.4 | 8.868 |
| Role Popularity baseline | 0.019 | N/A | 0.384 |

Memory is measured with Python `tracemalloc` and does not include all native NumPy/pandas allocations. The benchmark CSV also reports fold-level standard deviations.

**Figures:** [Recommendation quality](result/problem_2/figures/recommendation_quality.png) · [Runtime, rule count, and memory](result/problem_2/figures/benchmark.png)

**CSV files:** [Quality metrics (mean and standard deviation)](result/problem_2/quality_metrics.csv) · [Fold-level scores](result/problem_2/quality_metrics_by_fold.csv) · [Quality by role](result/problem_2/quality_by_role.csv) · [Benchmark](result/problem_2/benchmark.csv) · [Apriori rules](result/problem_2/rules_apriori.csv) · [FP-Growth rules](result/problem_2/rules_fpgrowth.csv) · [Per-role rule coverage](result/problem_2/role_coverage.csv) · [Recommendation examples](result/problem_2/recommendation_examples.csv)

---


## Project Structure

```text
skill-recommendation-using-association-rules/
├── data/
│   ├── raw/
│   │   └── VietJobs_IT.csv
│   └── processed/
│       ├── processed_jobs.csv
│       ├── processed_skills.csv
│       ├── transaction_matrix.csv
│       └── unique_skills.txt
├── docs/
│   ├── Apriori.md
│   ├── Association Rules.md
│   ├── Confidence.md
│   ├── FP-Growth.md
│   ├── Lift.md
│   └── Support.md
├── notebooks/
│   ├── EDA_and_Preprocessing.ipynb
│   ├── Problem_1.ipynb
│   └── Problem_2.ipynb
├── result/
│   ├── problem_1/
│   │   ├── figures/
│   │   ├── benchmark.csv
│   │   ├── quality_metrics.csv
│   │   ├── quality_metrics_by_fold.csv
│   │   ├── role_coverage.csv
│   │   ├── rules_apriori.csv
│   │   └── rules_fpgrowth.csv
│   └── problem_2/
│       ├── figures/
│       ├── benchmark.csv
│       ├── quality_by_role.csv
│       ├── quality_metrics.csv
│       ├── quality_metrics_by_fold.csv
│       ├── recommendation_examples.csv
│       ├── role_coverage.csv
│       ├── rules_apriori.csv
│       └── rules_fpgrowth.csv
├── .gitignore
├── README.md
└── requirements.txt
```

---
## How to run

```text
pip install -r requirements.txt
```

Then, run:

- notebooks/EDA_and_Preprocessing.ipynb

- notebooks/Problem_1.ipynb

- notebooks/Problem_2.ipynb