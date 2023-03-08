# Total-sales-Data-Modeling-

Data Modeling is one of the features used to connect multiple data sources in BI tool using a relationship. A relationship defines how data sources are connected with each other and you can create interesting data visualisations on multiple data sources. In this project we have an excel file with many columns and most of columns have same and iterative data. With data modeling we can decrease redundancy and improve speed of reporting. Our dataset is flat with 45 columns and 120000 rows. 
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
