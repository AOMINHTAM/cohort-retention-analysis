# 🔄 Cohort Analysis & Customer Retention Dynamics

> 🌐 **Portfolio Case Study:** [View full Case Study & Bias Fix Notes →](https://aominhtam-portfolio.vercel.app/projects/cohort-retention.html)

A behavioral analytics case study using time-based cohort modeling to evaluate customer retention rates across B2B/B2C segments, income tiers, and first-purchase categories while fixing left-truncation bias.

---

## 📦 Dataset & Scope

* **Project Type:** Personal Project / Simulated Business Case
* **Dataset Source:** Simulated E-Commerce CSVs (`EcomSales`, `Customer`, `Product`, `Region`)
* **Scale & Range:** `51,290` line-items (`25,728` unique `OrderID`s, `17,415` customers; Jan 2020 – Dec 2023 global anchor, USD)
* **Cohort Window:** 2022 monthly acquisition cohorts anchored globally against `2020–2023` purchase history.

---

## 🎯 1. Business Context & Strategic Objectives

* **Business Scenario:** Despite stable acquisition volumes, the business was experiencing unpredictable revenue fluctuations. The analytics team suspected a "leaky bucket" problem where new customers were churning prematurely before generating sustainable ROI.
* **Analytical Objectives:**
  * Construct a strict Time-Based Cohort model (Acquisition Month vs. Month After).
  * Segment retention performance to identify whether the churn issue was concentrated in specific business units (B2B vs B2C).
  * Evaluate the Scale vs. Efficiency tradeoff by analyzing returning customer profit margins across income brackets.
  * Implement global dataset anchoring to eliminate left-truncation bias in cohort tracking.

---

## 🛠️ 2. Tech Stack & Methodology

* **Language:** Python 3.x
* **Core Libraries:** `pandas`, `numpy`, `matplotlib`, `seaborn`.
* **Methodology:** 
  * Re-architected data ingestion to establish a unified `df_master` table, improving compute runtime by preventing isolated DataFrame merges.
  * Corrected cohort acquisition mapping by extracting `FirstPurchaseDate` globally before filtering temporal analysis windows, ensuring returning veterans were not misclassified as new acquisitions.
  * Developed a dual-axis visualization system pairing Acquisition Volume (Bar Charts) alongside Retention Matrices (Heatmaps) for statistically significant context.

---

## 📈 3. Key Findings & Insights

### 1. The Month-1 Churn Cliff
* **Acquisition vs. Repeat Purchase:** Q1 generated strong acquisition volume, yet Month-1 repeat purchase rates drop sharply to a **1% – 3%** baseline across 2022 cohorts—highlighting an early post-purchase retention gap worth testing via lifecycle onboarding (clickstream engagement itself is not directly measured in order logs).

### 2. Segment Dynamics (Corporate vs. Consumer)
* **Corporate (B2B — Sample Caveat):** Exhibits observable retention bumps (`5% – 6%`) around Month 5 and Month 8, suggesting periodic inventory restock cycles; because the B2B cohort base is smaller than B2C, stability should be monitored across future cohorts.
* **Consumer (B2C):** Low baseline repeat frequency compared to corporate accounts.

### 3. The Income Profitability Paradox
* **Low-Income Bracket:** Despite yielding lower AOV per order, high order volume and consistency generate the largest cumulative profit stream (`Profit` sum).
* **High-Income Bracket:** Highest mean `Profit` per transaction, yet lower repurchase retention rates.

---

## 🚀 4. Proposed Retention Experiments & Measurement

1. **Automated Post-Purchase Nurture:** Pilot an automated "Subscribe & Save" or re-engagement sequence at Day 20–25 to test lift over the observed Month-1 drop-off baseline.
2. **B2B Restock CRM Alerts:** Schedule outbound prompts for Corporate account managers at Months 4 and 7 ahead of the observed Month 5 / Month 8 B2B cycle.
3. **First-Session Cart Cross-Sells:** Test first-order bundle incentives for high-income buyers where single-order margin is high, measuring **Month-1 & Month-3 Retention Rate**, **B2B Reorder Conversion**, and **Contribution Margin After Discount** against holdout groups.

---

## 🛠️ 5. Methodology & Analysis Fixes

During the analytical sprint and peer code review, several data logic issues and architectural bottlenecks were systematically audited and resolved:

### 1. Left-Truncation Bias in First-Purchase Timestamp
* **The Mistake:** Initially, `FirstPurchaseDate` was calculated *after* filtering transactions for 2022. Consequently, existing customers from 2020–2021 who purchased in Jan 2022 were incorrectly labeled as "New Acquisitions" (`Month 0`).
* **The Business Impact:** Falsely inflated retention rates and distorted the onboarding churn curve.
* **How It Was Fixed:** Calculated `FirstPurchaseDate` globally across the entire unpartitioned dataset (`df_sales`) before subsetting 2022 cohort records.

---

### 2. Lack of Cohort Size Weighting
* **The Mistake:** Displaying retention rates purely as percentages without displaying the underlying customer counts.
* **The Business Impact:** A 5% retention on a cohort of 1,000 customers has completely different statistical significance compared to a 5% rate from a cohort of 10 customers.
* **How It Was Fixed:** Engineered a dual-panel layout pairing a horizontal bar chart of absolute Acquisition Volumes (`sizes_2022`) directly alongside the cohort heatmap (`gridspec_kw={'width_ratios': [1, 4]}`).

---

### 3. Matplotlib Execution Scope Disconnection
* **The Mistake:** Initialized `fig, axes = plt.subplots(1, 2)` but split Corporate and Consumer heatmap plotting commands across separate code cells.
* **The Impact:** Non-sequential cell execution resulted in broken rendering and blank subplots.
* **How It Was Fixed:** Refactored visualization into a unified Object-Oriented block, binding both subplots within a single execution scope.

---

### 4. Performance Overhead from Repeated Joins
* **The Mistake:** Relational tables (`Customer`, `Product`, `Region`) were merged multiple times across different cells.
* **How It Was Fixed:** Consolidated upstream entities into a single, clean `df_master` table right after ingestion, cutting redundant runtime memory usage.

---

## 💻 6. Local Setup & Reproduction Guide

### Environment Setup
```bash
# Clone the repository
git clone https://github.com/AOMINHTAM/cohort-retention-analysis.git
cd cohort-retention-analysis

# Create and activate virtual environment
python -m venv venv
.\venv\Scripts\Activate.ps1  # Windows PowerShell

# Install dependencies
pip install -r requirements.txt
```