<div align="center">

# 👗 Fashion Marketing & Sales Analysis Dashboard

### Power BI Project | Marketing Performance, Sales Efficiency, Profitability & Customer Insights

</div>

---

## ✨ Project Summary
This Power BI project analyzes the relationship between **marketing performance** and **sales outcomes** for a fashion retail business.

The dashboard combines campaign-level marketing data with order-level sales data to help stakeholders monitor:
- 📢 Advertising performance
- 💰 Revenue and profitability
- 🛒 Orders and conversions
- 👥 New vs. repeat clients
- 📈 Week-over-week (WoW) KPI movement
- 🎯 Budget utilization and return on spend

The report is designed to answer both **marketing** and **commercial** questions by linking campaign spend, impressions, clicks, conversions, and orders to revenue, gross profit, ROAS, ROI, and customer behavior.

---

## 🎯 Objectives
- Track core business KPIs such as **Revenue**, **Orders**, **Profit**, **ROAS**, and **ROI**
- Evaluate campaign efficiency using **CTR**, **CPM**, **CPC**, and **Cost per Cmt+Ib**
- Compare current performance with **last week** using WoW measures
- Analyze marketing performance by **campaign**, **SKU**, **day**, and **category**
- Understand revenue contribution from **Ads Sales** vs **Direct Sales**
- Assess customer acquisition efficiency using **CAC** and **new/repeat client ratio**
- Support dynamic KPI analysis through parameter-driven measures

---

## ❓ Business Questions
This dashboard helps answer the following questions:

- How much revenue is generated from total orders?
- How efficiently is marketing spend converted into sales and profit?
- Which campaigns and SKUs deliver the strongest ROAS and ROI?
- How are impressions, clicks, CTR, CPM, and conversions trending over time?
- What is the relationship between ad spend and budget allocation?
- How much of total revenue comes from **Ads Sales** versus **Direct Sales**?
- How are new clients performing compared with repeat clients?
- How do revenue, orders, profit, and budget change week over week?

---

## 🗂️ Dataset Description
The report is built from a retail marketing and sales model with the following main tables:

- **fact_order**
- **fact_mkt_camp_by_sku_cost**
- **dim_mkt_camp_cost**
- **dim_date**
- **Parameter**

### 🔑 Key Fields Used
Some important fields visible from the DAX measures include:
- `fact_order[Giá]`
- `fact_order[Giá vốn]`
- `fact_order[Số lượng]`
- `fact_order[Chiết khấu]`
- `fact_order[Ads/Direct]`
- `fact_mkt_camp_by_sku_cost[Lượt hiển thịTheo AM]`
- `fact_mkt_camp_by_sku_cost[Click Theo AM]`
- `fact_mkt_camp_by_sku_cost[Inbox + CommentsTheo AM]`
- `fact_mkt_camp_by_sku_cost[Khách mớiTheo AM]`
- `fact_mkt_camp_by_sku_cost[Khách cũTHeo AM]`
- `fact_mkt_camp_by_sku_cost[Ngân sáchTheo sản phẩm]`
- `fact_mkt_camp_by_sku_cost[Tiền đã chạy Theo Sản phẩm]`
- `fact_mkt_camp_by_sku_cost[SL bán được phân bổ theo tỉ lệ]`
- `fact_mkt_camp_by_sku_cost[Giá bán]`
- `dim_mkt_camp_cost[Số tiền đã chi tiêu]`
- `dim_date[Date]`

---

## 🧩 Data Model
The data model appears to combine:
- **Order fact data** for revenue, orders, discounts, COGS, and profit
- **Marketing campaign fact data** for spend, impressions, clicks, conversions, and budget by SKU/campaign
- **Date dimension** for WoW comparisons
- **Parameter table** to support dynamic KPI switching in visuals

This structure supports analysis across:
- ⏳ Day / Week / WoW trend
- 📣 Campaign
- 🧾 SKU / Category
- 👤 Client type
- 🛒 Orders and revenue channel
- 💸 Marketing budget and spend efficiency

---

## 📐 Key DAX Measures

<details>
<summary><b>💰 Sales & Profitability Measures</b></summary>

```DAX
Revenue =
SUMX(
    fact_order,
    fact_order[Giá] * fact_order[Số lượng]
)

Total Orders = DISTINCTCOUNT('fact_order'[ID])

COGS =
SUMX(
    'fact_order',
    'fact_order'[Số lượng] * 'fact_order'[Giá vốn]
)

Net Revenue = [Revenue] - SUM('fact_order'[Chiết khấu])

Gross Profit = [Net Revenue] - [COGS]

Total Profit =
SUMX(
    'fact_order',
    ('fact_order'[Giá] - 'fact_order'[Giá vốn]) * 'fact_order'[Số lượng]
)

_Total Profit =
SUMX(
    'fact_order',
    ('fact_order'[Giá] - 'fact_order'[Giá vốn]) * 'fact_order'[Số lượng]
) - [Tooltip Marketing Spend]

Profit Margin (%) = [Gross Profit] / [Net Revenue]

ROI =
DIVIDE(
    [Revenue] - [_Total Spend],
    [_Total Spend]
)
```

</details>

<details>
<summary><b>📣 Marketing Performance Measures</b></summary>

```DAX
_Total Spend = SUM('fact_mkt_camp_by_sku_cost'[ Tiền đã chạy Theo Sản phẩm ])

Total Budget = SUM('fact_mkt_camp_by_sku_cost'[Ngân sáchTheo sản phẩm])

_Total Impressions = SUM('fact_mkt_camp_by_sku_cost'[Lượt hiển thịTheo AM])

_Total Clicks = SUM('fact_mkt_camp_by_sku_cost'[Click Theo AM])

_Total Cmt+ib = SUM('fact_mkt_camp_by_sku_cost'[ Inbox + CommentsTheo AM])

_Revenue of Ads by Camp & SKU =
SUMX(
    'fact_mkt_camp_by_sku_cost',
    'fact_mkt_camp_by_sku_cost'[SL bán được phân bổ theo tỉ lệ] *
    'fact_mkt_camp_by_sku_cost'[Giá bán]
)

_ROAS (Return on Ad Spend) =
DIVIDE(
    [_Revenue of Ads by Camp & SKU],
    [_Total Spend]
)

_CTR = DIVIDE([_Total Clicks], [_Total Impressions])

_CPM = DIVIDE([_Total Spend], [_Total Impressions]) * 1000

_CPC = DIVIDE([_Total Spend], [_Total Clicks])

_Cost per Cmt+Ib = DIVIDE([_Total Spend], [_Total Cmt+ib])

Remaining Budget = [Total Budget] - [_Total Spend]

% Budget Used = DIVIDE([_Total Spend], [_Total Budget])
```

</details>

<details>
<summary><b>👥 Client & Acquisition Measures</b></summary>

```DAX
_New/Rep Clients =
DIVIDE(
    SUM(fact_mkt_camp_by_sku_cost[Khách mớiTheo AM]),
    SUM(fact_mkt_camp_by_sku_cost[Khách cũTHeo AM]),
    0
)

_CAC =
DIVIDE(
    SUM(dim_mkt_camp_cost[Số tiền đã chi tiêu]),
    (
        SUM(fact_mkt_camp_by_sku_cost[Khách mớiTheo AM]) +
        SUM(fact_mkt_camp_by_sku_cost[Khách cũTHeo AM])
    ),
    0
)
```

</details>

<details>
<summary><b>📊 WoW Tracking Measures</b></summary>

```DAX
LW Budget Spent = CALCULATE([_Total Spend], DATEADD('dim_date'[Date], -7, DAY))
Budget Spent WoW (%) = IF([LW Budget Spent] > 0, DIVIDE([_Total Spend] - [LW Budget Spent], [LW Budget Spent]), [LW Budget Spent])

LW Revenue = CALCULATE([Revenue], DATEADD('dim_date'[Date], -7, DAY))
Revenue WoW (%) = IF([LW Revenue] > 0, DIVIDE([Revenue] - [LW Revenue], [LW Revenue]), [LW Revenue])

LW ROAS = CALCULATE([_ROAS (Return on Ad Spend)], DATEADD('dim_date'[Date], -7, DAY))
ROAS WoW (%) = IF([LW ROAS] > 0, DIVIDE([_ROAS (Return on Ad Spend)] - [LW ROAS], [LW ROAS]), [LW ROAS])

LW Orders = CALCULATE(DISTINCTCOUNT('fact_order'[ID]), DATEADD('dim_date'[Date], -7, DAY))
Orders WoW (%) = IF([LW Orders] > 0, DIVIDE([Total Orders] - [LW Orders], [LW Orders]), [LW Orders])

LW Profit = CALCULATE([Total Profit], DATEADD('dim_date'[Date], -7, DAY))
Profit WoW (%) = IF([LW Profit] > 0, DIVIDE([Total Profit] - [LW Profit], [LW Profit]), [LW Profit])

LW Profit Margin = CALCULATE([Profit Margin (%)], DATEADD('dim_date'[Date], -7, DAY))
Profit Margin WoW = IF([LW Profit Margin] > 0, DIVIDE([Profit Margin (%) - [LW Profit Margin]], [LW Profit Margin]), [LW Profit Margin])

LW Impress = CALCULATE(SUM('fact_mkt_camp_by_sku_cost'[Lượt hiển thịTheo AM]), DATEADD('dim_date'[Date], -7, DAY))
Impress WoW (%) = IF([LW Impress] > 0, DIVIDE([_Total Impressions] - [LW Impress], [LW Impress]), [LW Impress])

LW Click = CALCULATE(SUM('fact_mkt_camp_by_sku_cost'[Click Theo AM]), DATEADD('dim_date'[Date], -7, DAY))
Click WoW (%) = IF([LW Click] > 0, DIVIDE([_Total Clicks] - [LW Click], [LW Click]), [LW Click])

LW Ctr = CALCULATE([_CTR], DATEADD('dim_date'[Date], -7, DAY))
Ctr WoW (%) = IF([LW Ctr] > 0, DIVIDE([_CTR] - [LW Ctr], [LW Ctr]), [LW Ctr])

LW CPM = CALCULATE([_CPM], DATEADD('dim_date'[Date], -7, DAY))
CPM WoW (%) = IF([LW CPM] > 0, DIVIDE([_CPM] - [LW CPM], [LW CPM]), [LW CPM])
```

</details>

<details>
<summary><b>🧠 Dynamic KPI & Display Measures</b></summary>

```DAX
Selected KPI Value =
SWITCH(
    MIN(Parameter[Parameter]),
    "_Total Impressions", [_Total Impressions],
    "_Revenue of Ads by Camp & SKU", [_Revenue of Ads by Camp & SKU],
    "Total Budget", [Total Budget],
    "_Total Clicks", [_Total Clicks],
    "_ROAS (Return on Ad Spend)", [_ROAS (Return on Ad Spend)],
    "Cost per cmt+ib", [_Cost per Cmt+Ib],
    "Conversion", [_Total Cmt+ib],
    "ROI", [ROI]
)

Selected Metric Value =
SWITCH(
    SELECTEDVALUE(Parameter[Name]),
    "Total Impressions", [_Total Impressions],
    "Total Budget", [Total Budget],
    "Total Clicks", [_Total Clicks],
    "ROAS", [_ROAS (Return on Ad Spend)],
    "Cost per Cmt+Ib", [_Cost per Cmt+Ib],
    "Conversions", [_Total Cmt+Ib],
    "ROI", [ROI]
)

Display Label for Gauge =
VAR Spent = [_Total Spend]
VAR Budget = [Total Budget]
VAR Pct = DIVIDE(Spent, Budget, 0)
RETURN
FORMAT(Spent, "#,##0,,.0#M") & " (" & FORMAT(Pct, "0.1%") & ")"
```

</details>

---

## 🖼️ Report Preview

> **Note:** The screenshots below use the current file names you uploaded. If you later move them into an `images/` folder, simply update the image paths in this README.

### 1️⃣ Sales Overview Dashboard
This page summarizes order and revenue performance with key cards for **Total Orders**, **Total Revenue**, **Total Spend**, **Total Profit**, and **ROAS**. It also breaks down revenue by **Ads Sales vs Direct Sales**, city contribution, category mix, and daily revenue/profit trend.

<p align="center">
  <img src="Sales Overview Dashboard.png" alt="Sales Overview Dashboard" width="92%">
</p>

### 2️⃣ Marketing Overview Dashboard
This page focuses on campaign efficiency, combining **budget**, **clicks**, **impressions**, **CTR**, **CPM**, and **ROAS** into one view. It supports campaign comparison and budget monitoring.

<p align="center">
  <img src="Marketing Overview Dashboard.png" alt="Marketing Overview Dashboard" width="92%">
</p>

### 3️⃣ Trend Dashboard
This page explores trend relationships between marketing and client behavior, such as spend vs budget, clicks vs CTR, impressions vs CPM, and ROAS over time.

<p align="center">
  <img src="Trend Dashboard.png" alt="Trend Dashboard" width="92%">
</p>

### 4️⃣ Customer Insight Dashboard
This page highlights client acquisition patterns, category-level customer mix, CAC, new/repeat client distribution, and campaign-level conversion behavior.

<p align="center">
  <img src="Customer Insight Dashboard.png" alt="Customer Insight Dashboard" width="92%">
</p>

### 5️⃣ Marketing Detail View – Budget & KPI Selector
This detailed page demonstrates parameter-driven KPI switching, campaign table exploration, ad spend versus total budget, and weekly budget versus actual spend.

<p align="center">
  <img src="Marketing Detail View.png" alt="Marketing Detail View" width="92%">
</p>

### 6️⃣ Sales Detail View – Profitability Tooltip Interaction
This detailed view shows how the report supports hover tooltip analysis by displaying **COGS**, **Tooltip Marketing Spend**, and **_Total Profit** alongside Revenue.

<p align="center">
  <img src="Sales Detail Tooltip View.png" alt="Sales Detail Tooltip View" width="92%">
</p>

### 7️⃣ Marketing Tooltip / Dynamic KPI Example
This screenshot illustrates a focused trend visual for **Total Impressions**, **Total Budget**, **Total Clicks**, **ROAS**, **Cost per Cmt+Ib**, **Conversion**, and **ROI** by day.

<p align="center">
  <img src="Marketing Trend Tooltip Example.png" alt="Marketing Trend Tooltip Example" width="92%">
</p>

### 8️⃣ Profitability Tooltip Example
This screenshot highlights the relationship between **COGS**, **Tooltip Marketing Spend**, and **_Total Profit**, with a Revenue KPI card shown alongside.

<p align="center">
  <img src="Profitability Tooltip Example.png" alt="Profitability Tooltip Example" width="92%">
</p>

---

## 🔍 Key Insights
Based on the screenshots and measures provided, the dashboard reveals several high-level insights:

- 💵 Total Revenue is displayed at approximately **$4.77bn** on the main sales overview page
- 🧾 Total Orders are shown at around **3K**
- 📣 Total Spend is shown at approximately **394M**
- 📈 Return On Ad Spend (**ROAS**) is displayed at around **7.67**
- 💼 Total Profit is shown at approximately **590M**
- 👀 Total Impressions are displayed around **5M** in the marketing overview
- 🖱️ Total Clicks are shown around **41.65K**
- 📊 CTR is displayed at approximately **0.82%**
- 💸 CPM is shown around **77.14K**
- 🔁 The dashboard emphasizes **week-over-week changes** for revenue, spend, ROAS, orders, profit, impressions, clicks, CTR, and CPM
- 🧠 The model supports both **overview monitoring** and **interactive drill-down / tooltip analysis**

---

## ✅ Recommendations
1. **Prioritize profitable growth**
   - Review campaigns with strong revenue but weak profit margin
   - Compare gross profit against marketing spend to protect profitability

2. **Optimize media efficiency**
   - Use **CTR**, **CPM**, **CPC**, and **Cost per Cmt+Ib** to identify inefficient campaigns
   - Reallocate spend toward campaigns with strong ROAS and conversion quality

3. **Track budget usage more proactively**
   - Monitor **% Budget Used** and **Remaining Budget** daily
   - Adjust campaign pacing before overspending or underspending occurs

4. **Strengthen client acquisition analysis**
   - Use **CAC** and **new/repeat client ratio** to assess whether campaign spend is generating sustainable customer growth
   - Segment acquisition results by campaign and category

5. **Use WoW KPI alerts for rapid decision-making**
   - Focus on sudden drops in revenue, orders, CTR, or ROAS
   - Combine display/color measures with dashboard alerts for faster action

---

## 🛠️ Tools & Technologies
- **Power BI Desktop**
- **DAX**
- **Data Modeling**
- **Marketing & Sales Fact Tables**
- **Date Intelligence / WoW Analysis**
- **Dynamic Parameter Measures**

---

## 🧠 Skills Demonstrated
- Marketing analytics dashboard development
- Sales and profitability analysis
- Campaign performance measurement
- WoW time-based analysis using DAX
- KPI card design and conditional formatting
- Parameter-driven dynamic visuals
- Tooltip page design
- Business storytelling for marketing and commerce teams

---

## 🚀 How to Use
1. Open the `Fashion Marketing & Sales Analysis Dashboard.pbix` file in **Power BI Desktop**
2. Refresh the dataset if needed
3. Navigate through the main report pages:
   - Sales Overview
   - Marketing Overview
   - Trend
   - Customer Insight
4. Use slicers / selectors to analyze by:
   - Day / Week
   - Campaign
   - Category / SKU
   - Ads vs Direct
   - KPI parameter
5. Hover over supported visuals to view tooltip pages and additional metric context

---

## 👨‍💻 Author
**Vuong Minh Toan**

---

## 📝 Notes
- This project is intended for **portfolio** and **learning** purposes.
- The model blends **marketing** and **sales** data, making it useful for commercial performance analysis.
- Some screenshots include tooltip interactions and parameter-driven views, demonstrating advanced Power BI report design.
- If you rename or move image files, remember to update the image paths in `README.md`.
