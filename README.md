# Total-sales-Data-Modeling-

Data Modeling is one of the features used to connect multiple data sources in BI tool using a relationship. A relationship defines how data sources are connected with each other and you can create interesting data visualisations on multiple data sources. In this project we have an excel file with many columns and most of columns have same and iterative data. With data modeling we can decrease redundancy and improve speed of reporting. Our dataset is flat with 45 columns and 120000 rows (based on Adventure Works Sales). 

![Screenshot 2023-03-08 at 13 39 46](https://user-images.githubusercontent.com/65550422/223715660-e366ab9b-f7fc-4210-bca9-27b38e344e96.png)

The goals of this project are:
- Data Analysis Expressions (DAX)
- Creating Relationships Between Tables
- Creating Calculated Columns
- Creating Calculated Tables
- Creating Measures
- Creating Security Roles

So, for start, We should split sales flat table to some resonable Tables and create relationship between them. In this data set we have a problem based on Date, because we have Shamsi date and we should convert it to Georgian dates. So, we add Georgian date table to power query and relate these two table to each other and after that we calculate all of our calculation based on Georgian date and show result based on Shamsi date. For split table we use some main transformations like:
 - Promote header
 - Change type
 - Merge query (for ralating to Georgian date)
 - Remove other columns
 - Remove duplicate

In this project we should model Data Warehouse and split flat table based on related schema. We have to schema in Data Warehouse concept, Star and SnowFlake schema. Star and snowflake schema designs are mechanisms to separate facts and dimensions into separate tables. Snowflake schemas further separate the different levels of a hierarchy into separate tables. In either schema design, each table is related to another table with a primary key/foreign key relationship. Primary key/foreign key relationships are used in relational databases to define many-to-one relationships between tables [IBM]. Below is the initial sample of the Data Warehouse model:

 ![Screenshot 2023-03-09 at 15 27 33](https://user-images.githubusercontent.com/65550422/224055726-90be2a5e-080a-49a4-b46d-91d7a8268fee.png)


Now, We should do three things with DAX and M:
- create calculation columns
- create calculation tables
- create measures

## create calculation columns with DAX and M

In this step we create calculation columns like Delivery Duration, Not Delivered Duration with DAX in Power BI Environment and with M in Power query for showing how we can create these items.

### DAX query:
- Delivery Duration (PBI) = DATEDIFF(RELATED(Orders[Order Date]),'Order Details'[Delivery Date],DAY) 
- Not Delivered Duration (PBI) = IF(ISBLANK('Order Details'[Delivery Date]),DATEDIFF(RELATED(Orders[Order Date]),TODAY(),DAY),BLANK())

### M query:
- Table.AddColumn(#"Removed Columns", "Not Delivered Duration", each if [Delivery Date] = null then [Not Delivered Duration 1] else null)
- Table.AddColumn(#"Expanded Orders", "Delivery Duration", each [Delivery Date] - [Orders.Order Date])

You should know more important than memorizing DAX or M functions is that you should learn how you can combinate these functions and where you should create columns, Power BI or Power query Environment?

![Screenshot 2023-03-10 at 11 37 01](https://user-images.githubusercontent.com/65550422/224296791-4c693edf-6146-4bf6-8772-d6b961b05839.png)

## create calculation Tables with DAX and M

In this step we want to create calculation tables. One of the most important calculation table is that Date Dimension. If we want to create relation between date of each table, we should create date dimension and connect it to each table has date and we want to get report based on date. DAX query is very useful for creating this table.
 - at first we should select new table
 - DateDT = CALENDAR(DATE(YEAR(MIN(Orders[Order Date])),MONTH(MIN(Orders[Order Date])),1),EOMONTH(MAX('Order Details'[Delivery Date]),0))

Now, We should add this table to our model.

![Screenshot 2023-03-10 at 14 28 57](https://user-images.githubusercontent.com/65550422/224328529-bd839d0d-0975-454a-a854-5106ff55a571.png)

## create measures
In this step we create some measure with DAX function. Power BI measures are the way of defining calculations in a DAX model, which helps us to calculate values based on each row. But rather, it gives us aggregate values from multiple rows from a table.




Cumulative Values:
![Screenshot 2023-03-13 at 23 24 21](https://user-images.githubusercontent.com/65550422/224846436-31d91165-aabc-412a-8973-067a659f2fa9.png)

In this section, I will show you some measures and formula

![Screenshot 2023-03-18 at 10 50 46](https://user-images.githubusercontent.com/65550422/226098644-e7f8f885-19bf-4efd-8ba1-4745820d9982.png)

- Cost = CALCULATE(SUMX('Order Details','Order Details'[Quantity]*RELATED(Products[Unit Cost])),USERELATIONSHIP(Dates[Date],Orders[Order Date]))
- Cost RT = CALCULATE('Cost Analysis'[Cost],FILTER(ALL(Dates),Dates[Date]<=MAX(Dates[Date])))
- Cost YTD = TOTALYTD('Cost Analysis'[Cost],Dates[Date])
- Profit = [Ordered Amount]-'Cost Analysis'[Cost]
- Profit % = DIVIDE([Profit],[Ordered Amount])
- Profit YTD = [Ordered Amount YTD]-'Cost Analysis'[Cost YTD]
- AvgOrdersPerCustomer = AVERAGEX(Customers,[Net Sales Amount])
- AvgOrdersPerItems = AVERAGEX('Order Details','Order Details'[Quantity]*RELATED(Products[Unit Price])*(1-RELATED(Promotions[Discount])))
- CustomerBuyingMoreThanAverage Wrong = COUNTROWS(FILTER(Customers,[Net Sales Amount]>[AvgOrdersPerCustomer]))
- Delivered Amount = CALCULATE('Sales Analysis'[Net Sales Amount],USERELATIONSHIP(Dates[Date],'Order Details'[Delivery Date]),'Order Details'[Delivery Date]<>BLANK())
- Delivered Amount RT = CALCULATE([Delivered Amount],FILTER(ALL(Dates),Dates[Date]<=MAX(Orders[Order Date])))
- Delivered Amount Total = CALCULATE([Delivered Amount],ALL('Order Details'))
- Delivered Amount YTD = TOTALYTD([Delivered Amount],Dates[Date])
- Delivery Duration = CALCULATE(AVERAGE('Order Details'[Delivery Duration]),USERELATIONSHIP(Dates[Date],'Order Details'[Delivery Date]))
- Delivery Duration Max = CALCULATE(MAX('Order Details'[Delivery Duration]),ALL('Order Details'))
- Delivery Duration Min = CALCULATE(MIN('Order Details'[Delivery Duration]),ALL('Order Details'))
- Net Sales Amount = SUMX('Order Details','Order Details'[Quantity]*RELATED(Products[Unit Price])*(1-RELATED(Promotions[Discount])))
- Not Delivered Amount = CALCULATE('Sales Analysis'[Net Sales Amount],USERELATIONSHIP(Dates[Date],Orders[Order Date]),'Order Details'[Delivery Date]=BLANK())
- Ordered Amount = CALCULATE('Sales Analysis'[Net Sales Amount],USERELATIONSHIP(Dates[Date],Orders[Order Date]))
- Ordered Amount % = DIVIDE([Ordered Amount],[Ordered Amount Total])
- Ordered Amount Growth = DIVIDE([Ordered Amount]-'Sales Analysis'[Ordered Amount SPLY],'Sales Analysis'[Ordered Amount SPLY])
- Ordered Amount MTD = TOTALMTD([Ordered Amount],Dates[Date])

## Data visualization

Data visualization brings data to life, making you the master storyteller of the insights hidden within your numbers. Through live data dashboards, interactive reports, charts, graphs, and other visual representations, data visualization helps users develop powerful business insight quickly and effectively. In this part, we'll demonstrate some visualization like chart, card, KPI,Donut chart, map and etc. As a power bi developer, you should know, which diagram and visualization share more sense about analysis business.


![Screenshot 2023-03-18 at 11 10 59](https://user-images.githubusercontent.com/65550422/226099281-e5bb274d-479c-4ad8-8337-582347346a09.png)

![Screenshot 2023-03-18 at 11 10 41](https://user-images.githubusercontent.com/65550422/226099284-1af087e2-373c-4bee-ae65-a975266b1a5e.png)

![Screenshot 2023-03-18 at 11 10 33](https://user-images.githubusercontent.com/65550422/226099286-e851191f-986c-4371-a10b-f24ea6c3ecfa.png)

![Screenshot 2023-03-18 at 11 10 24](https://user-images.githubusercontent.com/65550422/226099287-2bb7728b-d992-4815-addb-ec5a1ad00a83.png)

![Screenshot 2023-03-18 at 11 10 16](https://user-images.githubusercontent.com/65550422/226099289-5059153a-8722-4e92-bab3-ec4515e5bfc1.png)

![Screenshot 2023-03-18 at 11 09 52](https://user-images.githubusercontent.com/65550422/226099293-3557f5ca-3543-4ad8-9483-893db95beb82.png)


After visulization, We can share our reports to power bi service, power bi server and mobile format. In the another topic, I'll show, how we can share our report.
