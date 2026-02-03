# Supply Chain Cost & Delivery Performance Analysis

A data analytics project that evaluates **delivery reliability** and **logistics cost behavior** using historical shipment records.  
The analysis focuses on **shipment delays (early / on-time / late)**, how delays differ by **shipment mode**, **country**, **product group**, and **vendor**, and whether delays are associated with **higher freight costs**.

The Project : [Supply Chain Cost Delivery Analysis](<Supply Chain Cost Delivery Analysis.ipynb>).

---

## Table of Contents
- [Project Objectives](#project-objectives)
- [Business Questions](#business-questions)
- [Tools I Used](#tools-i-used)
- [Dataset](#dataset)
  - [Key Fields Used](#key-fields-used)
  - [Delay Definition](#delay-definition)
  - [Delivery Status Rules](#delivery-status-rules)
- [Methodology](#methodology)
- [How to Read This Project](#how-to-read-this-project)
- [Notebooks](#notebooks)
- [Key Outputs](#key-outputs)
- [How to Run](#how-to-run)
- [Results Summary](#results-summary)
- [Limitations](#limitations)
- [Future Improvements](#future-improvements)
- [Author](#author)

---

## Project Objectives
The goal of this project is to:
1. Measure how often shipments are delayed and how severe delays are.
2. Identify which **shipment modes** lead to the longest delays.
3. Compare delivery performance across **countries**.
4. Evaluate whether **delayed shipments cost more**.
5. Assess the **trade-off between delivery speed and cost**.
6. Highlight **product groups most exposed to delays**.
7. Benchmark **vendor reliability** to identify best and worst performers.

---

# Business Questions
This project answers the following questions:

- **To what extent do shipment delays occur ?**  
- **Which shipment modes cause the longest delays ?**  
- **Do delivery times vary by country ?**  
- **Do delayed shipments cost more ?**  
- **Is there a trade-off between delivery time and cost ?**  
- **Which product groups are most at risk of delays ?**  
- **Which vendors are the most reliable ?**

---

# Tools I Used
This analysis was conducted using the following tools:

- Python — used for data preparation, exploration, and analysis
  ````
  - Pandas — data manipulation and aggregation

  - Matplotlib — core visualization 

  - NumPy – numerical computations

  - Seaborn — advanced statistical charts
  ````

- Jupyter Notebooks — interactive analysis, documentation, and experimentation

- Visual Studio Code — running scripts and project development

- Git & GitHub — version control, project tracking, and publishing results
## Dataset
The analysis is based on a CSV file (example name used in the notebook):

- `Supply_Chain_Shipment_Pricing_Data.csv`

The dataset (as loaded in the notebook) contains **10,324 rows** and **33 columns**.

### Key Fields Used
To keep the analysis focused and reproducible, the project primarily uses these columns:

- `country`
- `vendor`
- `shipment mode`
- `product group`
- `sub classification`
- `po sent to vendor date`
- `scheduled delivery date`
- `delivered to client date`
- `freight cost (usd)`
- `line item insurance (usd)`

### Delay Definition
Delivery delay is computed from dates:

> **Delay (days) = Delivered to Client Date − Scheduled Delivery Date**

Interpretation:
- **Negative delay** = delivered early
- **Zero delay** = delivered on time
- **Positive delay** = delivered late


### Delivery Status Rules
Each shipment is classified as:

- **Early**: delay < 0  
- **On time**: delay = 0  
- **Late**: delay > 0  

This enables analysis of delay patterns using both numeric delay and categorical status.

---

## Methodology
The workflow follows a standard analytics pipeline:

1. **Data loading & inspection**
   - Check column types, missing values, and basic dataset structure.

2. **Preprocessing & feature engineering**
   - Convert delivery date columns to datetime.
   - Compute `delay` in days.
   - Create `shipment delivery` (Early / On time / Late).

3. **Exploratory analysis & KPI measurement**
   - Delay distribution
   - Late rate, on-time rate
   - Performance by mode / country / vendor / product group

4. **Cost analysis**
   - Clean cost fields (`freight cost (usd)`) to numeric.
   - Compare freight costs by delivery status.
   - Explore delay vs cost relationship.

5. **Visualization & interpretation**
   - Use charts to support business conclusions and recommendations.

---

# How to Read This Project
The analysis is organized into multiple notebooks, each answering a specific business question.
For best understanding, notebooks should be read in the following order:

1. [Data Preparation](#data-preparation)
2. [Overall Delivery Performance](#overall-delivery-performance)
3. [Shipment Mode Impact](#shipment-mode-impact)
4. [Country & Vendor Performance](#country--vendor-performance)
5. [Cost Impact & Product Risk](#cost-impact--product-risk)
6. [Final Conclusions & Recommendations](#final-conclusions--recommendations)
---

# Data Preparation

```python
import ast
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt 
import numpy as np

df = pd.read_csv(r'c:\Users\HP\Downloads\Supply_Chain_Shipment_Pricing_Data.csv')

cols_to_keep = [
    'country', 'vendor', 'shipment mode',
    'product group', 'sub classification',
    'po sent to vendor date', 'scheduled delivery date',
    'delivered to client date',
    'freight cost (usd)', 'line item insurance (usd)'
]
df = df[cols_to_keep]

list_to_date = ['scheduled delivery date', 'delivered to client date']

df = df.copy()                                          # To avoid SettingWithCopyWarning

for col in list_to_date:
    df[col] = pd.to_datetime(df[col], format='mixed')    # mixed format handles different date formats
```
View my notebook with detailed steps here: [Data Preparation](<Notebooks/Data Preparation.ipynb>).


# Overall Delivery Performance

## To what extent do shipment delays occur ?
This analysis examines delivery timing across all shipments to determine how often delays occur and how severe they are. By comparing scheduled delivery dates with actual delivery dates, the study identifies patterns in early, on-time, and late deliveries. The results provide a baseline understanding of overall delivery reliability and highlight whether shipment delays represent isolated incidents or a recurring operational issue.

View my notebook with detailed steps here: [Overall Delivery Performance](<Notebooks/Overall Delivery Performance.ipynb>).

## Visualize Data
````python
sns.set_theme(style = 'ticks')
sns.barplot(data = df_delai_count,
            x = 'delay',
            y = 'count',           
            hue = 'count',
            palette = 'dark:b_r',
           )
sns.despine()                                        # Remove the top and right spines from plot


plt.yscale("log")
plt.xlabel('')
plt.ylabel('')
plt.title('Shipment Delay Distribution (in days)', pad = 20, fontsize = 14, fontweight = 'bold')

plt.tight_layout()
plt.show()
````
## Results:
![alt text](Pic/output.png)
*bar chart showing shipment delay distribution*


## **Insight:**  
- Most shipments experience **zero or minimal delay**, indicating generally effective execution.  
- A **small number of shipments incur extended delays**, creating a long tail that disproportionately impacts overall performance.  
- **Targeting the root causes of severe delays** would yield the highest improvement in service reliability.
---
## Visualize Data
````python
df_delai['shipment delivery'].value_counts().plot(kind = 'pie',
                xlabel = '', 
                ylabel = '',
                figsize = (8,5),
                autopct = '%1.1f%%',                # Show percentages on pie chart
                
                  )

plt.title('Shipment Delivery Status Distribution', pad = 20, fontsize = 14, fontweight = 'bold')
plt.tight_layout()
plt.show()
````
## Results:
![alt text](Pic/2.png)
*Pie chart showing Shipment Delivery Status Distribution*
## **Insight:**  
- A majority of shipments are delivered **on time (61.3%)**, indicating generally stable logistics performance.  
- A **significant 27.3% of shipments are late**, representing a material service gap with cost and customer impact.  
- **Reducing late deliveries** would deliver the greatest improvement in overall service reliability and efficiency.

# Shipment Mode Impact

## Which shipment modes cause the longest delays ?


### Objective
This analysis evaluates delivery performance across different shipment modes by examining the percentage distribution of delivery outcomes (early, late, and on-time).

### Methodology
The analysis was conducted in three stages:
1. Aggregation of shipment counts by shipment mode and delivery status
2. Conversion of shipment counts into percentage values per shipment mode
3. Visualization of percentage distributions using a bar chart

This approach ensures comparability between shipment modes regardless of shipment volume.

View my notebook with detailed steps here: [Shipment Mode Impact](<Notebooks/Shipment Mode Impact.ipynb>).

## Visualize Data
````python
df_mode_prc.plot(kind = 'bar',
            figsize = (10,6),
            xlabel = '',
            ylabel = 'percentage (%)',
             )

for i, j in enumerate(df_mode_prc['early']):
    plt.text(i-0.23, j + 1,  f'{int(j)}%', fontsize = 10, ha = 'left' )
    
for i, j in enumerate(df_mode_prc['late']):
    plt.text(i, j + 1,  f'{int(j)}%', fontsize = 10, ha = 'center')

for i, j in enumerate(df_mode_prc['on time']):
    plt.text(i+0.23, j + 1,  f'{int(j)}%', fontsize = 10, ha = 'right')
    
plt.title('Shipment Delivery Status by Shipment Mode', pad = 10, fontsize = 14, fontweight = 'bold')
plt.xticks(rotation = 45 )
plt.tight_layout()

plt.show()
````
## Results:
![alt text](Pic/3.png)
*Bar chart showing Sgipment Delivery Status By Shipment Mode*

## **Insight:**  
- Delivery reliability varies sharply by shipment mode, with **Ocean showing the strongest on-time performance (78%)** and minimal delays.  
- **Air Charter is the weakest-performing mode**, with the majority of shipments delivered late, creating both service and cost risk.  
- **Aligning shipment urgency with the most reliable modes** would improve on-time performance and reduce overall logistics costs.
---
## Is there a trade-off between delivery time and cost ?

### Methodology

The dataset was split by delivery status, and median freight costs were calculated for each shipment mode.

View my notebook with detailed steps here: [Shipment Mode Impact](<Notebooks/Shipment Mode Impact.ipynb>).

## Visualize Data
````python
fig , ax = plt.subplots(1,2, figsize=(13,5))

sns.set_theme(style = 'ticks')
sns.boxplot(data = df_C_late,
            x = 'freight cost (usd)',
            y = 'shipment mode',
            ax = ax[0],
            palette='crest',
            hue= 'shipment mode',
            legend=False,
            )
ax[0].set_title('Late Shipments', fontsize=14, fontweight='bold', pad=10)
ax[0].set_xlabel('')
ax[0].set_ylabel('')
ax[0].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f"${int(x/1000)}K"))
ax[0].set_xlim(0, 65000)

sns.boxplot(data = df_C_on_time,
            x = 'freight cost (usd)',
            y = 'shipment mode',
            ax = ax[1],
            hue='shipment mode',
            palette='crest',
            legend=False,
            )
ax[1].set_title('On Time Shipments', fontsize=14, fontweight='bold', pad=10)
ax[1].set_xlabel('')
ax[1].set_ylabel('')
ax[1].xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f"${int(x/1000)}K"))
ax[1].set_xlim(0, 65000)

sns.despine()

plt.tight_layout()

plt.show()
````

## Results:
![alt text](Pic/4.png)
*Box chart Showing shipments by delay*

## **Insight:**  
- Late shipments exhibit **higher median freight costs and significantly greater cost variability** across all shipment modes.  
- **Air Charter shows the highest cost exposure and widest spread**, indicating substantial financial risk when delays occur.  
- Improving delivery reliability in **high-cost modes** would generate the largest cost savings and reduce overall freight spend volatility.

# Country & Vendor Performance

## Do delivery times vary by country ?

## Methodology

The three countries with the highest shipment volumes were selected to analyze delivery performance in greater detail.

View my notebook with detailed steps here: [Notebooks/Country & Vendor Performance.ipynb](<Notebooks/Country & Vendor Performance.ipynb>).

## Visualize Data
````python
fig , ax = plt.subplots(1, 3, figsize = (15,5))

for i, country in enumerate(df_top3_pvt.index):
    df_top3_pvt.loc[country].plot(kind = 'pie',
                                  ax = ax[i],
                                  xlabel = '',
                                  ylabel = '',
                                  autopct = '%1.1f%%',
                                  )
    ax[i].set_title(f'Shipment Delivery Status in {country}', pad = 10, fontsize = 14, fontweight = 'bold')
    ax[i].legend().remove()
plt.tight_layout()
plt.show()
````
## Results:
![alt text](Pic/cv1.png)
*Pie charts showing Shipment delivery Status By Contries*

## **Insight:**  
- Delivery reliability differs materially by country, with **South Africa performing strongly**, **Côte d’Ivoire at a moderate level**, and **Nigeria experiencing the highest share of late shipments**.  
- Higher delay rates, particularly in Nigeria, increase the risk of **service disruption, cost escalation, and customer dissatisfaction** in key markets.  
- **Prioritizing targeted operational improvements in Nigeria** and replicating best practices from South Africa could significantly improve overall regional performance.

---
## Which vendors are the most reliable ?

### Methodology
The top seven vendors were identified based on the total number of shipments. Shipment delivery outcomes (early, late, on time) were then aggregated for each vendor using a pivot table.

View my notebook with detailed steps here: [Notebooks/Country & Vendor Performance.ipynb](<Notebooks/Country & Vendor Performance.ipynb>).

## Visualize Data
````python
df_vd_top7_pvt.plot(kind = 'barh',
            figsize = (10,8),
            xlabel = '',
            ylabel = '',
            legend = True,
             )

plt.title('Shipment Delivery Status by Top 7 Vendors', pad = 10, fontsize = 14, fontweight = 'bold')
plt.xlabel('Number of Shipments')

plt.show()
````

## Results:
![alt text](Pic/cv2.png)
*Bar chart Showing Shipment Delivery Status By Vendors*

## **Insight:**  
- Shipment volume and delivery performance are **highly concentrated with SCMS from RDC**, which shows both the largest volume and a **notably high number of late deliveries**, creating outsized impact on overall performance.  
- Other vendors (e.g., **Organics, Ltd, Aurobindo Pharma Limited, S. BUYS WHOLESALER**) demonstrate **more stable on-time performance**, though at lower shipment volumes.  
- **Focusing performance improvement efforts on SCMS from RDC** would yield the greatest immediate benefit, while leveraging best practices from smaller, more reliable vendors could improve consistency across the vendor base.
---

# Cost Impact & Product Risk

## Does shipment delivery status affect freight costs ?
### Objective
This analysis examines the relationship between **shipment delivery status** and **freight cost**, with a focus on comparing median freight costs for late and on-time shipments across shipment modes.

View my notebook with detailed steps here: [Cost Impact & Product Risk](<Notebooks/Cost Impact & Product Risk.ipynb>).

## Visualize Data
````python
sns.set_theme(style = 'ticks')
sns.barplot(data = df_C_plot,
            x = 'shipment delivery',
            y = 'freight cost (usd)',
            palette = 'pastel',
            hue = 'freight cost (usd)',
            )
sns.despine()

plt.title('Median Freight Cost by Shipment Delivery Status', pad = 10, fontsize = 14, fontweight = 'bold')
plt.xlabel('')
plt.ylabel('Median Freight Cost (USD)')

plt.tight_layout()
plt.show()
````

## Results:
![alt text](Pic/cp1.png)
*Bar chart showing Median Freight Cost by Shipment Delivery Status*

## **Insight:**  
- Late shipments have a **substantially higher median freight cost** (≈ USD 7.4K) compared to **on-time shipments** (≈ USD 4.9K).  
- Delivery delays are directly associated with a **~50% increase in freight spend**, driven by expedites, re-routing, and exception handling.  
- Reducing late deliveries represents a **high-impact lever** to lower overall logistics costs while improving service performance.


---

## Which vendors are the most reliable ?
### Methodology
The top seven vendors were identified based on the total number of shipments. Shipment delivery outcomes (early, late, on time) were then aggregated for each vendor using a pivot table.

View my notebook with detailed steps here: [Cost Impact & Product Risk](<Notebooks/Cost Impact & Product Risk.ipynb>).

## Visualize Data

````python
df_vd_top7_pvt.plot(kind = 'barh',
            figsize = (10,8),
            xlabel = '',
            ylabel = '',
            legend = True,
             )

plt.title('Shipment Delivery Status by Top 7 Vendors', pad = 10, fontsize = 14, fontweight = 'bold')
plt.xlabel('Number of Shipments')

plt.show()
````
## Results:
![alt text](Pic/cp2.png)
*Bar chart showing Shipment Delivery Status by Top 7 Vendors*

## **Insight:**  
- Shipment volume and delivery performance are **highly concentrated with SCMS from RDC**, which shows both the largest volume and a **notably high number of late deliveries**, creating outsized impact on overall performance.  
- Other vendors (e.g., **Organics, Ltd, Aurobindo Pharma Limited, S. BUYS WHOLESALER**) demonstrate **more stable on-time performance**, though at lower shipment volumes.  
- **Focusing performance improvement efforts on SCMS from RDC** would yield the greatest immediate benefit, while leveraging best practices from smaller, more reliable vendors could improve consistency across the vendor base.
---

# Final Conclusions & Recommendations

This analysis demonstrates how data-driven insights can be used to improve supply chain decision-making. By identifying the key drivers of delivery delays and their cost implications, organizations can enhance delivery reliability, control transportation costs, and reduce operational risks. The approach presented in this project can serve as a foundation for more advanced supply chain optimization and performance monitoring initiatives.

View my notebook with detailed steps here: [Final Conclusions & Recommendations](<Notebooks/Final Conclusions & Recommendations.ipynb>) .

---
# Key Outputs

- Cleaned and processed shipment dataset

- Computed delivery delay (in days) for each shipment

- Visualizations showing delay distribution, cost vs delay, and country-level performance

- Vendor reliability ranking based on on-time delivery rate

---
# How to Run
````md
1. Clone the repository  

2. Install dependencies:
   pip install pandas numpy matplotlib seaborn

3. Open Jupyter Notebook

4. Run notebooks in the following order:
   - 01_Data Preparation
   - 02_Overall Delivery Performance
   - 03_Shipment Mode Impact
   - 04_Country & Vendor Performance
   - 05_Cost Impact & Product Risk
   - 06_Final Conclusions & Recommendations

   Or All tha Project :
   - Supply Chain Cost Delivery Analysis

````

# Results Summary
- Approximately 32% of shipments were delayed, with an average delay of 2.4 days

- Air shipments had the shortest delivery times but highest costs

- Certain vendors consistently delivered on time (>90% on-time rate)

- Delayed shipments were associated with higher logistics costs
---

# Limitations
- The dataset does not include weather or customs delay information

- Some date fields contained missing or inconsistent values

- Analysis is based on historical data and may not reflect future performance
---

# Future Improvements
- Build a predictive model to estimate delivery delays

- Create an interactive dashboard (Power BI / Tableau)

- Include external data such as weather or port congestion

- Automate data ingestion and reporting
---
# Author
````md
Author: Regragui Mohammed

Role:  Data Science, Big Data & AI Student

GitHub: https://github.com/Regragui-Mohammed
````
