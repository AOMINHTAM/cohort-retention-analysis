# 🔄 Cohort Analysis & Customer Retention Dynamics

An advanced behavioral analytics project utilizing cohort modeling to track customer retention rates and lifetime value metrics across B2B/B2C segments, demographic profiles, and first-purchase categories.

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

### 1. The Month-1 Churn Crisis
* **Acquisition:** Q1 generated strong top-of-funnel acquisition, but onboarding failed to convert them into habitual buyers.
* **Retention Drop:** Month-1 retention rate plummets dramatically to a **1% - 3%** baseline across the board. The platform suffers from a critical post-purchase engagement void.

### 2. Segment Dynamics (Corporate vs. Consumer)
* **Corporate (B2B):** Exhibits structural cyclicality. We observe retention "bumps" (reaching 5%-6%) at Month 5 and Month 8, indicating predictable inventory restock cycles.
* **Consumer (B2C):** Highly erratic purchasing behavior with extremely low baseline loyalty.

### 3. The Income Profitability Paradox
* **Low-Income Bracket (The Cash Cow):** Despite yielding the lowest Average Order Value (AOV) per transaction, their high volume and consistency generate the largest and most stable cumulative profit stream (Sum Profit).
* **High-Income Bracket (The Cash Grab):** These customers boast exceptional transaction efficiency (highest Mean Profit per order) but exhibit the worst return rates.

---

## 🚀 4. Strategic Recommendations

1. **Implement an Automated Post-Purchase Sequence:** Deploy an automated "Subscribe & Save" or proactive email nurture sequence triggering at Day 20-25 to combat the catastrophic Month-1 churn rate.
2. **B2B Lifecycle Management:** Integrate CRM alerts for the Corporate sales team to proactively dial B2B accounts at the 4-month and 7-month marks, capitalizing on their proven restock rhythm.
3. **Upsell Optimization:** Given the High-Income segment's tendency for single-purchase "hit-and-runs", immediately optimize the first-visit cart experience with aggressive premium cross-sells to maximize initial capture value.

---

## 🛠️ 5. Challenges, Technical Debt & Bug Fixes

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
git clone [https://github.com/AOMINHTAM/cohort-retention-analysis.git](https://github.com/AOMINHTAM/cohort-retention-analysis.git)
cd cohort-retention-analysis

# Create and activate virtual environment
python -m venv venv
.\venv\Scripts\Activate.ps1  # Windows PowerShell

# Install dependencies
pip install -r requirements.txt