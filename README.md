# Superstore-Profitability-Analysis

---

![](https://github.com/Fav0urDike/Superstore-Profitability-Analysis/blob/main/ChatGPT%20Image%20May%203%2C%202026%2C%2003_57_19%20AM.png)

---

## PROJECT OVERVIEW

Profitability is a key measure of business success, showing how effectively a company generates profit after covering its costs. 

For a superstore, profitability is especially important because it operates with large inventories, diverse product categories, and frequent discount strategies. By analyzing profitability, a superstore can identify high-performing products, manage discounts effectively, control operational costs, and make better strategic decisions. This project analyzes four (4) years of transactional sales data to assess whether the superstore is profitable.

---

## PROBLEM STATEMENT

A store manager met me and made this complaint. “_I don't understand what's going on with our store performance lately. We are making plenty of sales, sometimes even more than before, but it doesn't feel like the business is improving. We are constantly pushing discounts to meet targets, and customers seem to expect them now. Even when we hit our sales numbers, something still feels off at the end of the month. Some products move very quickly, but others just sit there for a long time. It's hard to tell what we should really be focusing on. Also, certain customers place large orders, which looks great, but I'm not sure if those orders are actually helping us overall. On top of that, shipping and delivery has been inconsistent. Sometimes orders take longer than expected, and other times, we use faster shipping, but I'm not sure it's worth it.
Overall, we are hitting sales target, but the store doesn't feel as successful as it should be. I can't quite pinpoint why.”_

This analysis was conducted to help the store manager investigate and better understand the store’s overall performance, profitability, and key drivers of profit. By identifying whether the store is truly profitable and understanding the factors influencing profitability, management can make informed decisions that will help maximize areas of profitability and improve overall business performance.

---

## Key Business Questions
This analysis was guided by the following business questions:
-	Is the store improving as sales increase over time?
-	Is discount affecting profitability at the end of the month?
- Which products are driving profit and which are underperforming?
-	Do large others contribute positively to overall business performance?
-	Is faster shipping worth the cost?
  
By answering these concerns, the project aims to help the store manager understand the key performance indicators that provide clear insight into the store’s overall performance, productivity, and profitability across sales, orders, and products. This includes identifying how different categories and products perform relative to one another, such as determining which product categories generate more revenue compared to others, so that management can make informed strategic decisions based on actual business performance.

---

## DATASET OVERVIEW

The dataset used for this analysis was sourced from Kaggle and captures the transactional sales data in a superstore over a four-year period (2014–2017). Each record represents a completed sales transaction. 
Key characteristics of the dataset:
- Time period: January 2014 — December 2017
- Format: Comma Separated Values (CSV)
  
It provides detailed information on customer demographics and transaction patterns. The dataset contains 9,995 rows and 21 columns, where each row represents an individual transaction and each column corresponds to specific variables such as , Order ID, Order Date, Ship Date, Ship Mode, Customer ID, Customer Name, Segment, Country, City, State, Postal Code, Region, Product ID, Category, Sub-Category, Product Name, Sales, Quantity, Discount.

---

## DATA CLEANING AND TRANSFORMATION

This analysis was conducted using Microsoft excel and Power bi, leveraging, DAX, Data modelling and visual charts.

During the data cleaning process, the dataset was found to have no missing or duplicate values. 

#### TOOLS & TECHNIQUES USED
1.	Table creation on Microsoft Excel.
2.	Data Tranformation (Data Types).
3.	DAX Functions for Measures and column creation.


#### MEASURES AND COLUMNS CREATED.
- Total sales = TotalSales = SUM('Sample - Superstore'[Sales])

- AOV = DIVIDE(SUM('Sample - Superstore'[Sales]),COUNT(Orders_Summary[Order ID]))

- Profit Margin = DIVIDE(SUM('Sample - Superstore'[Profit]),SUM('Sample - Superstore'[Sales]))

- Discount Amt = (DIVIDE('Sample - Superstore'[Sales],(1-'Sample - Superstore'[Discount])))-'Sample - Superstore'[Sales]

- Total Discount Amount = SUM('Sample - Superstore'[Discount Amt])

- Discount Rate % = DIVIDE([Total Discount Amount],[TotalSales])
  
_The key logic here was to calculate ratio metrics only after aggregation. That avoids the error of averaging daily or row-level margins when the report is grouped by month or any other time period._

- Month = FORMAT('Sample - Superstore'[Order Date],"MMM")
  
- Sort column = MONTH('Sample - Superstore'[Order Date])

_The month name column is then sorted by the sort column in the model so the chart shows Jan, Feb, Mar, and so on instead of alphabetical order._

- Orders_Summary = 
SUMMARIZE(
    'Sample - Superstore',
    'Sample - Superstore'[Order ID],
    "Total Sales", SUM('Sample - Superstore'[Sales]),
    "Total Profit", SUM('Sample - Superstore'[Profit])
Order size classification logic using the business-focused percentile split (bottom 50%, next 30%, top 20%):
Order Size = 
VAR RankValue =
    RANKX(
        ALL(Orders_Summary),
        Orders_Summary[Total Sales],
        ,
        ASC
    )
VAR TotalOrders =
    COUNTROWS(ALL(Orders_Summary))
VAR Percentile =
    DIVIDE(RankValue, TotalOrders)
RETURN
SWITCH(
    TRUE(),
    Percentile <= 0.5, "Small",
    Percentile <= 0.8, "Medium",
    "Large"
)

_During the analysis I noticed that one order can span multiple product rows. Therefore, I decided to create another table that summarizes total orders made. The important grouping key is Order ID, and the grouped table uses total sales per order as the size basis._

- S/N = 
RANKX(
    ALLSELECTED('Sample - Superstore'[Sub-Category], 'Sample - Superstore'[Category]),
    [TotalSales], ,DESC,
    Dense
)


|Image 1| Image 2|
|---------|----------|
|![](https://github.com/Fav0urDike/Superstore-Profitability-Analysis/blob/main/order%20summ%20tbl.png)|  ![](https://github.com/Fav0urDike/Superstore-Profitability-Analysis/blob/main/superstore%20data.png)|


---

## DATA MODELLING

The Sample Superstore table was connected with the Order Summary table in a many to one relationship using the Order ID. One Order ID in the Order summary table equal to multiple orders in the sample Superstore table.

![](https://github.com/Fav0urDike/Superstore-Profitability-Analysis/blob/main/model.png)

---

## ANALYTICAL APPROACH & METRIC SELECTION

|Question| Visual used|	What it shows|
|---------|------------|--------------|
|Is the store improving as sales increase over time?|	Sales vs Profit by Month|	Whether the business is actually improving over time|
|Is discount hurting profitability?|	Profit Margin vs Discount rate% by Month|	Whether higher discount levels reduce margin|
|Which products should receive attention?|	Product by Sales and Profit, Top 5, Bottom 5 products|	What is moving, what is profitable, and what is lagging|
|Do large orders help the business?|	Order Size Group by Profit|	Whether larger purchase values contribute to profit|
|Is faster shipping worth it?|	Ship Mode by Profit|	Whether premium shipping modes still contribute positively|

---

## KEY INSIGHTS


1. ### Profit also generally increased alongside sales
![](https://github.com/Fav0urDike/Superstore-Profitability-Analysis/blob/main/CHART%201.png)

  The superstore shows an overall upward trend in sales across the year, especially from September to December, where sales rose sharply and reached their highest levels in November      and remained strong in December. Profit also generally increased alongside sales, which suggests that higher sales volumes were mostly reflected in better earnings. However, profit     remained a small share of sales throughout the period, showing that sales growth did not translate into equally strong profitability. November stands out as the main exception,       where sales were very high but profit was lower than in December, indicating weaker efficiency in converting sales into profit during that month.


2. ### Discounting is reducing profitability for the superstore.
![](https://github.com/Fav0urDike/Superstore-Profitability-Analysis/blob/main/CHART%202.png)

Although sales are being supported by discounts, the profit margin remains much lower than the discount rate in most months, showing that growth is being achieved at the expense of earnings. The sharpest margin pressure appears in months with the highest discount levels, especially April, July, and November. Overall, the chart indicates ongoing margin erosion rather than strong profit conversion from sales.


3. ### Technology is the strongest profit driver
|Image 1| Image 2|
|---------|----------|
|![](https://github.com/Fav0urDike/Superstore-Profitability-Analysis/blob/main/CHART%203.png)|  ![](https://github.com/Fav0urDike/Superstore-Profitability-Analysis/blob/main/CHART%204.png)|

The chart shows that Technology is the strongest profit driver. It has the highest sales ($836K) and also the highest profit ($145K), making it the most efficient category in converting sales into profits.

Furniture is the main underperforming category. Even though it records high sales ($742K), its profit is only $18K, which is extremely low compared with the sales generated. This shows a major gap between sales and profit in that category.

Office Supplies is not the least-performing category in this chart. Its sales are slightly lower at $719K, but its profit is $122K, which is much stronger than Furniture and close to Technology. That means Office Supplies is performing better on profitability than its sales level alone suggests.
Overall, the key insight is that high sales do not automatically mean high profit. The biggest issue is Furniture, where strong sales are not translating into earnings.


4. ### Large orders are the main profit driver.
![](https://github.com/Fav0urDike/Superstore-Profitability-Analysis/blob/main/CHART%205.png)

They generate the highest total profit by a wide margin, contributing over $200K, compared with about $49K from medium orders and $19K from small orders. This shows that profit is heavily concentrated in large orders, and they account for more than half of total profit. Overall, the chart indicates that larger order sizes contribute far more positively to business performance than medium and small orders.

5. ### Faster shipping does not appear to deliver higher profitability.
![](https://github.com/Fav0urDike/Superstore-Profitability-Analysis/blob/main/CHART%206.png)

The Standard Class shipping mode generates the highest profit at over $150K, while Second Class and First Class contribute much less. Same Day shipping produces the lowest profit.
This suggests that profitability declines as shipping speed increases. The faster shipping options appear to carry higher fulfillment costs that reduce the profit retained from each order. Overall, the chart indicates that slower shipping modes are more profitable for the business than faster delivery options.

---

## RECOMMENDATIONS 

1.	The first priority should be to review and reduce discount intensity, especially where discounts are eating into profit. The discount rate is too high in relation to the profit margin, and this is weakening overall store performance. The manager should treat discounting as a profit-control issue, not just a sales tool, because continuing at the current level could push the store toward low-return or loss-making operations.

2.	The Furniture category needs close attention. Even though it generates strong sales, its profit is extremely low, which suggests poor margin retention. The manager should examine the pricing and discount structure for Furniture products and identify why sales are not converting into profit. Since this category appears to be absorbing much of the margin pressure, it should be a major focus area.

3.	The store should also prioritize large orders because they generate the highest share of profit. Since large orders contribute more than half of total profit, they are a key driver of business performance. The manager should make sure operational attention is given to maintaining this segment and keeping it profitable.

4.	For shipping, the store should consider profitability alongside delivery speed when choosing shipping modes. Standard Class produces the highest profit, while faster shipping options generate much less. That means faster delivery is costing more than it returns in profit terms. Shipping decisions should therefore be based not only on customer service, but also on their effect on margin.

---

## CONCLUSION

![](https://github.com/Fav0urDike/Superstore-Profitability-Analysis/blob/main/Superstore%20Dashboard%20finished.png)

Overall, the store’s performance is being driven more by sales volume than by profit efficiency. The result shows that high sales do not always translate into high profit, because discounting and costly fulfillment choices (operational decisions involved in delivering products to customer) are reducing the earnings retained from each sale. 
The store manager should focus on tightening discount control, fixing Furniture profitability, protecting large-order revenue, and using the most profitable shipping mode where possible. These actions will improve margin, reduce profit erosion, and make the store’s growth more sustainable.

---

![](https://github.com/Fav0urDike/Superstore-Profitability-Analysis/blob/main/thank-you-words-on-notepad-and-office-supplies-free-photo.jpg)
