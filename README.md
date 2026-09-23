# 🏃 Strava & Bellabeat: Fitness Tracking Data Analytics Case Study

[![Power BI](https://img.shields.io/badge/Power_BI-Dashboard-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)](https://github.com/vishalyadav351/strava-fitness-data-analytics)
[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://github.com/vishalyadav351/strava-fitness-data-analytics)
[![SQL](https://img.shields.io/badge/SQL-Data_Cleaning-003B57?style=for-the-badge&logo=sqlite&logoColor=white)](https://github.com/vishalyadav351/strava-fitness-data-analytics)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

---

## 📌 Executive Summary

Wearable fitness trackers generate continuous telemetry on human activity, calories, sleep architecture, and biometric patterns. This case study examines smart device telemetry from Fitbit/Strava tracking users to understand real-world consumer behavior and identify data-driven product and marketing strategies for **Bellabeat / Strava Fitness**.

Through data cleaning in **Python**, validation in **SQL**, and dynamic dashboard development in **Power BI**, this project reveals critical behavioral traits, including activity deficits, peak caloric expenditure windows, and severe sedentary dominance.

---

## 🎯 Business Problem & Objectives

- **Business Task:** Analyze consumer smart device usage trends to guide marketing strategy and feature prioritization.
- **Primary Stakeholders:** Urška Sršen (Chief Creative Officer) & Sando Mur (Co-founder & Mathematician).
- **Target Deliverables:**
  1. Data cleaning & transformation scripts (Python & SQL).
  2. Exploratory Data Analysis identifying movement rhythms & sleep gaps.
  3. Interactive, multi-page Power BI executive dashboard with custom theme & DAX measures.
  4. Actionable, high-level business and product recommendations.

---

## 📂 Data Sources & Architecture

The raw telemetry originates from the public **FitBit Fitness Tracker Dataset** (CC0 Public Domain via Mobius/Zenodo).

| Dataset File | Granularity | Records | Key Fields |
| :--- | :--- | :--- | :--- |
| `dailyActivity_merged.csv` | Daily | 940 | `Id`, `ActivityDate`, `TotalSteps`, `Calories`, `SedentaryMinutes`, `VeryActiveMinutes`[cite: 1] |
| `hourlySteps_merged.csv` | Hourly | 22,099 | `Id`, `ActivityHour`, `StepTotal`[cite: 1] |
| `hourlyCalories_merged.csv` | Hourly | 22,099 | `Id`, `ActivityHour`, `Calories`[cite: 1] |
| `sleepDay_merged.csv` | Daily | 413 | `Id`, `SleepDay`, `TotalMinutesAsleep`, `TotalTimeInBed`[cite: 1] |
| `weightLogInfo_merged.csv` | Event-based | 67 | `Id`, `Date`, `WeightKg`, `BMI`, `IsManualReport`[cite: 1] |

---

## 🛠️ Data Cleaning & Transformation Pipeline

The data engineering pipeline was executed via Python (`step1_clean.py`) and verified using SQL queries[cite: 1]:

1. **Date-Time Parsing:** Converted string timestamps into standardized ISO formats (`YYYY-MM-DD` and `HH:MM:SS`)[cite: 1].
2. **Deduplication:** Identified and removed duplicate sleep records from `sleepDay_merged.csv`.
3. **Sparse Data Handling:** Dropped the `Fat` column in `weightLogInfo_merged.csv` due to 97% missing entries.
4. **Non-Wear Anomaly Detection:** Identified **79 records** where `SedentaryMinutes == 1440` (24 full hours of zero movement)[cite: 1]. These signify devices left on nightstands rather than true biological inactivity[cite: 1].
5. **Feature Engineering:** Added `DayOfWeek`, `TrackerWorn` boolean flags, and mapped activity intensity categories according to CDC benchmarks[cite: 1].

```sql
-- Checking for False Inactivity (1440 Sedentary Minutes)
SELECT 
    Id, 
    COUNT(*) AS unworn_days
FROM dailyActivity_merged
WHERE SedentaryMinutes >= 1440
GROUP BY Id
ORDER BY unworn_days DESC;
