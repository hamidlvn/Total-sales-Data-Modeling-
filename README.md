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

You should know more important than memorizing DAX or M is that you should learn how you can combinate these functions and where you should create columns, Power BI or Power query Environment?

![Screenshot 2023-03-10 at 11 37 01](https://user-images.githubusercontent.com/65550422/224296791-4c693edf-6146-4bf6-8772-d6b961b05839.png)

