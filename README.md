1️⃣ Are shipments often delayed?
Methodology

Convert scheduled and actual delivery dates to datetime

Compute delivery delay in days
delay = delivered_date − scheduled_date

Analyze the distribution of delays

Why this works

Quantifies overall delivery performance

Identifies on-time deliveries vs delays and extreme cases

2️⃣ Which shipment modes cause the longest delays?
Methodology

Group shipments by shipment_mode

Compute delivery delay for each shipment

Compare delay distributions across shipment modes

Why this works

Boxplots reveal median delay, variability, and outliers

Highlights risky or inefficient transport modes

3️⃣ Do delivery times vary by country?
Methodology

Group data by country

Calculate average (or median) delivery delay per country

Rank countries by delay

Why this works

Aggregation removes noise from individual shipments

Allows fair geographic comparison

4️⃣ Do delayed shipments cost more?
Methodology

Create a binary variable: delayed (delay > 0)

Compare transport costs between delayed and on-time shipments

Use medians to reduce outlier impact

Why this works

Separates operational performance from cost impact

Reveals financial consequences of delays

5️⃣ Is there a trade-off between delivery time and cost?
Methodology

Plot delivery delay against transport cost

Inspect correlation patterns and anomalies

Optionally compute correlation coefficient

Why this works

Scatter plots show relationships without assumptions

Identifies inefficiencies (high cost + long delay)

6️⃣ Which product groups are most at risk of delays?
Methodology

Group shipments by product_group

Calculate delay rate (percentage of delayed shipments)

Rank product groups by delay rate

Why this works

Delay rate normalizes across different shipment volumes

Highlights vulnerable product categories

7️⃣ Which vendors are the most reliable?
Methodology

Group shipments by vendor

Compute delay rate per vendor

Focus on vendors with sufficient shipment volume

Why this works

Reliability is better measured by consistency than averages

Prevents misleading conclusions from small samples


Are shipments often delayed?
Visualization: Histogram of delivery delay (in days)

Which shipment modes cause the longest delays?
Visualization: Boxplot of delivery delay by shipment mode

Do delivery times vary by country?
Visualization: Bar chart of average delivery delay by country

Do delayed shipments cost more?
Visualization: Boxplot comparing transport cost for delayed vs on-time shipments

Is there a trade-off between delivery time and cost?
Visualization: Scatter plot of delivery delay vs transport cost

Which product groups are most at risk of delays?
Visualization: Bar chart of delay rate by product group

Which vendors are the most reliable?
Visualization: Bar chart of delay rate by vendor
