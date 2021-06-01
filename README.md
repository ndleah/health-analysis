[![View on GitHub](https://img.shields.io/badge/GitHub-View_on_GitHub-red?logo=GitHub)](https://github.com/nduongthucanh)
# **SERIOUS SQL: HEALTH ANALYTICS MINI CASE STUDY**

<p align="center">
  <img width="300" height="300" src="https://s3.amazonaws.com/thinkific-import/357412/n0nS0vA3RmOtzsH99jyf_Data_With_Danny_Round_Logo_png">
</p>

# Table of contents
<!--ts-->
   * [Overview](#**I.-OVERVIEW**)
   * [Solutions](#**II.-SOLUTIONS**)
   * [Key Highlights](#**III.-KEY-HIGHLIGHTS**)

---
  
## **I. OVERVIEW**
This case study is contained within the [Serious SQL](https://www.datawithdanny.com) by [Danny Ma](https://www.linkedin.com/in/datawithdanny/). With the **Health Analytics Mini Case Study**, I queried data to bring insights to the following questions:
1. How many `unique users` exist in the logs dataset?
2. How many total `measurements` do we have `per user on average`?
3. What about the `median` number of measurements per user?
4. How many users have `3 or more` measurements?
5. How many users have `1,000 or more` measurements?
6. Have logged `blood glucose` measurements?
7. Have `at least 2 types` of measurements?
8. Have all 3 measures - `blood glucose, weight and blood pressure`?
9. What is the `median systolic/diastolic` **blood pressure** values?
<p align="center">
<img src="https://github.com/nduongthucanh/Health-Analytics-Mini-Case-Study/blob/main/IMG/BigDataInHospitals.jpg" width=60% height=60%>
</p>

---
## **II. SOLUTIONS**
Before going into each question case by case, I needed to have a further analysis into the dataset in order to understand different variables and values needed to answer questions. Therefore, the following query was executed:
```sql
SELECT * FROM health.user_logs;
```
As transparent from the table, there are total **6 columns** which contains values of `id`, `log_date`, `measure`, `measure_value`, `systolic` and `diastolic`. 
<p align="center">
<img src="https://github.com/nduongthucanh/Health-Analytics-Mini-Case-Study/blob/main/IMG/z2513921830835_b2f645dc08b9ae4ecfd59aa2f37f74f5.jpg" width=100% height=100%>
</p>

I noticed that the **`measure`** column's values are divided into different variables. However, these variables were hidden due to large number of dataset. 
<p align="center">
<img src="https://github.com/nduongthucanh/Health-Analytics-Mini-Case-Study/blob/main/IMG/z2513953538301_86b8f067910a3700d4d4199e7bf5e690.jpg" width=60% height=60%>
</p>

Therefore, **```DISTINCT```** function was useful to have a more detailed data evaluation of this column: 

```sql
SELECT DISTINCT measure FROM health.user_logs;
```
<p align="center">
<img src="https://github.com/nduongthucanh/Health-Analytics-Mini-Case-Study/blob/main/IMG/z2513958512711_8698af869ba03d103dd7a1ef2fe33e79.jpg" width=100% height=100%>

After running the query, I knew that there are total 3 variables within the **`measure`** column which are `blood_glucose`, `blood_pressure`, `weight`. I then had enough necessary information that I needed for data analysis. Next, I started to run into each question and **SOLVE IT!**

---
### 1. **How many unique users exist in the logs dataset?**
```sql
SELECT COUNT (DISTINCT id)
FROM health.user_logs;
```
Result:

|count                                   |
|----------------------------------------|
|554                                     |

---

**`Note:` For question 2-8, I created a temporary table:**

**Step 1:** Firstly, I ran a code `DROP TABLE IF EXISTS` statement to clear out any previously created tables:
  ```sql
  DROP TABLE IF EXISTS user_measure_count;
  ```
**Step 2:** Next, I created a new **temporary table** using the results of the query below:
  ```sql
  CREATE TEMP TABLE user_measure_count AS
  SELECT 
    id,
    COUNT(*) AS measure_count,
    COUNT (DISTINCT measure) AS unique_measures
  FROM health.user_logs
  GROUP BY 1;
  ```
  ---
### 2. **How many total measurements do we have per user on average?**
```sql
SELECT
  ROUND (AVG(measure_count), 2) AS mean_value
FROM user_measure_count;
```

Result:
|mean_value                              |
|----------------------------------------|
|79.23                                   |
---
### 3. **What about the median number of measurements per user?**
```sql 
SELECT 
   PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY measure_count) AS median_value 
FROM user_measure_count;
```
Result:
|median_value                            |
|----------------------------------------|
|2                                       |
---
### 4. **How many users have 3 or more measurements?**
```sql
SELECT COUNT(*)
FROM user_measure_count
WHERE measure_count >= 3;
```
Result:
|count                                   |
|----------------------------------------|
|209                                     |
---
### 5. **How many users have 1,000 or more measurements?**
```sql 
SELECT COUNT(*)
FROM user_measure_count
WHERE measure_count >= 1000;
```
Result:
|count                                   |
|----------------------------------------|
|5                                       |
---

### 6. **Have logged blood glucose measurements?**
```sql
SELECT 
  COUNT(DISTINCT id)
FROM health.user_logs
WHERE measure = 'blood_glucose';
```
Result:
|count                                   |
|----------------------------------------|
|325                                     |
---
### 7. **Have at least 2 types of measurements?**
```sql
SELECT 
  COUNT(*)
FROM user_measure_count
WHERE unique_measures >= 2;
```

Result:
|count                                   |
|----------------------------------------|
|204                                     |
---

### 8. **Have all 3 measures - blood glucose, weight and blood pressure?**
```sql
SELECT
  COUNT(*)
FROM user_measure_count
WHERE unique_measures = 3;
```
Result:
|count                                   |
|----------------------------------------|
|50                                      |
---
### 9. **What is the median systolic/diastolic blood pressure values?**
```sql
SELECT
  PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY systolic) AS median_systolic,
  PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY diastolic) AS median_diastolic
FROM health.user_logs
WHERE measure = 'blood_pressure';
```
Result:
|median_systolic|median_diastolic|
|---------------|----------------|
|126            |79              |
---
## **III. KEY HIGHLIGHTS**
> **Initial thoughts:** 
Even though this is a short assignment which cover basic SQL syntax, I did run into problems several time during the solving process. However, it helped me to have a better understanding about data exploration using SQL from theories to real life application.

Some of the main areas covered in this case study, including:
* **Sorting Values**
* **Inspect Row Counts** 
* **Duplicates & Record Frequency Review**
* **Summary Statistics** `(MEAN, MEDIAN)`
