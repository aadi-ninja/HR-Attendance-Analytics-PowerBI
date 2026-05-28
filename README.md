# Presence Insights: HR Attendance & Remote Work Analytics

An interactive, end-to-end Power BI dashboard designed to monitor employee attendance patterns, track remote work (WFH) preferences, and analyze sick leave percentages across multiple months.

## 📌 Project Overview & Requirements
The goal of this project was to transform raw HR records into actionable workforce insights. The dashboard satisfies three primary business requirements:
1. Work Preference Tracking: Visualizing the distribution and trends of Work From Home (WFH) vs. Office presence.
2. Attendance Monitoring: Providing high-level and granular visibility into daily presence metrics.
3. Health Metrics: Tracking Sick Leave (SL) percentages to spot operational risks or seasonal health spikes.

---

## 🛠️ Data Transformation & Power Query Steps
The raw dataset was structured horizontally with dates as individual columns, making it difficult to analyze. I performed the following ETL steps in Power Query to build a clean relational data structure:
* Promoted Headers: Set the initial row as column headers to establish clear fields.
* Date Normalization: Isolated the date data into a single entity.
* Unpivoted Columns: Transformed the dataset from a wide format to a long format by unpivoting the date columns. This created a dedicated `Date` column and a consolidated `Value` field containing daily attendance codes (e.g., P, WFH, WO, HO), allowing for robust time-series analysis.

---

## 📐 Data Modeling & DAX Measures
To avoid cluttering the core data tables, I created a dedicated Measure Table to house all key performance calculations. Below are the key DAX measures written for this model:

### 1. Total Working Days
Filters out non-working days like Weekends (WO) and Public Holidays (HO) to find the true denominator for workforce availability.

Total Working Days = 
VAR totaldays = COUNT('Final Data'[Value])
VAR noworkdays = CALCULATE(COUNT('Final Data'[Value]), 'Final Data'[Value] IN {"WO", "HO"})
RETURN
totaldays - noworkdays


### 2. Present Days
Combines standard office presence (P) and remote work (WFH Count) to calculate total active working days.

Present Days = 
VAR Presentdays = CALCULATE(COUNT('Final Data'[Value]), 'Final Data'[Value] = "P")
RETURN
Presentdays + [WFH Count]


### 3. Presence %
Presence % = DIVIDE([Present Days], 'Measure Table'[Total Working Days], 0)


### 4. Remote Work Metrics (WFH)
WFH Count = SUM('Final Data'[WFH Count])

WFH % = DIVIDE([WFH Count], [Present Days], 0)


### 5. Sick Leave (SL) Metrics
SL Count = SUM('Final Data'[SL Count])

SL % = DIVIDE([SL Count], [Present Days], 0)

---

## 📊 Dashboard Architecture & Visual Features:

  ![Presence Insights Dashboard]

  <img width="1280" height="721" alt="All month" src="https://github.com/user-attachments/assets/970fb048-f779-4bc4-b9ca-099a0eb481fc" />

The dashboard is structured into logical zones to optimize readability and user flow:
* Executive Summary (Top-Left): Clean KPI blocks displaying the aggregate Presence % (91.83%), WFH % (10.00%), and SL % (1.20%) to give managers an instant operational snapshot.
* Interactive Slicers (Top-Right): Clean, custom tile-style month buttons allowing users to filter the entire report view seamlessly.
* Granular Data Grids (Left Side): 
  * A summary matrix tracking individual employees alongside their specific Presence, WFH, and Sick Leave allocations.
  * A daily tracking grid displaying exact daily status codes (e.g., P, WFH, BRL, HLWP) for microscopic inspection of attendance records.
* Timeline Trend Analysis (Center): Line and Area charts configured with statistical trendlines to map exactly how employee presence, remote habits, and sickness rates evolve from April through June.
* Day-of-Week Breakdown (Right Side): Distinct ranking tables identifying which days of the week experience the highest or lowest rates of office presence, remote work, and sick leave.

---

## 💡 Key Business Insights Discovered
* Presence Stability: The workforce maintained a robust average presence rate of 91.83%, with Monday and Tuesday consistently showing the highest overall engagement.
* Sickness Trends: While the overall sick leave rate is low (1.20%), the integrated trendline reveals a subtle upward trajectory moving into June, signaling a potential period for management to monitor capacity risks.
* Remote Work Behavior: Work-from-home utilization remains stable at 10.00%, peaking visibly toward the end of the work week (Friday hitting an average of 13.01%), indicating a team preference for hybrid weekend transitions.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1277" height="719" alt="May" src="https://github.com/user-attachments/assets/f320e82d-95d9-4195-8305-55256457a936" />
<img width="1282" height="723" alt="April" src="https://github.com/user-attachments/assets/78bc89e8-1e64-4fa0-b8c1-7ac4a350edca" />
<img width="1283" height="714" alt="June" src="https://github.com/user-attachments/assets/86413120-143b-4356-874f-6a91c9f6a122" />
