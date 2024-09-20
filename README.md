# Power BI Portfolio Projects
![Power Bi](https://img.shields.io/badge/power_bi-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)
![](https://img.shields.io/badge/MySQL-00000F?style=for-the-badge&logo=mysql&logoColor=white)
![](https://img.shields.io/badge/SQLite-07405E?style=for-the-badge&logo=sqlite&logoColor=white)
![](https://img.shields.io/badge/Tableau-E97627?style=for-the-badge&logo=Tableau&logoColor=white)
![Canva](https://img.shields.io/badge/Canva-%2300C4CC.svg?style=for-the-badge&logo=Canva&logoColor=white)
![PPT](https://img.shields.io/badge/Microsoft_PowerPoint-B7472A?style=for-the-badge&logo=microsoft-powerpoint&logoColor=white)
![MS OFFICE](https://img.shields.io/badge/Microsoft_Office-D83B01?style=for-the-badge&logo=microsoft-office&logoColor=white)
![EXCEL](https://img.shields.io/badge/Microsoft_Excel-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white)
![GOOGLE SHEETS](https://img.shields.io/badge/Google%20Sheets-34A853?style=for-the-badge&logo=google-sheets&logoColor=white)
![VSCODE](https://img.shields.io/badge/VSCode-0078D4?style=for-the-badge&logo=visual%20studio%20code&logoColor=white)
![Figma](https://img.shields.io/badge/Figma-F24E1E?style=for-the-badge&logo=figma&logoColor=white)
![PREZI](https://img.shields.io/badge/Prezi-3181FF?style=for-the-badge&logo=prezi&logoColor=white)
![SQL Server](https://img.shields.io/badge/Microsoft_SQL_Server-CC2927?style=for-the-badge&logo=microsoft-sql-server&logoColor=white)
![GITHUB ACTIONS](https://img.shields.io/badge/Github%20Actions-282a2e?style=for-the-badge&logo=githubactions&logoColor=367cfe)
![WINDOWS TERMINAL](https://img.shields.io/badge/windows%20terminal-4D4D4D?style=for-the-badge&logo=windows%20terminal&logoColor=white)




Welcome to my personal Power BI portfolio repository! Here you will find a collection of Power BI projects and dashboards that demonstrate my skills and expertise in data visualization, business intelligence, and analytics using Power BI.

## About Me
I am a passionate data enthusiast with a strong background in Power BI. I have extensive experience in transforming raw data into meaningful insights, creating interactive dashboards, and helping businesses make data-driven decisions. This portfolio showcases my proficiency in Power BI and highlights my ability to effectively communicate complex data visually.

---
## [Project 1: Plant Performance]

Consumable Performance Dashboard, a powerful and intuitive data visualization tool built using Power BI. This dashboard offers a comprehensive view of "sales, gross profit and quantity" data for consumable plant products, allowing you to quickly and easily analyze performance and identify trends.
### Project Structure:
A condensed, dynamic performance report that utilises **SWITCH** measures and conditional formatting.
* Data: power query and virtual tables
* Model: Measures calculated column
* Visuals: layout design
* Review
### Steps to Follow
  **Vitual tables**
  - The measure **Inpast** is designed to filter the dates from the **Dim_Date** table that are less than or equal to one year before the last recorded sales date in **Fact_Sales**. This can be useful for comparisons like **"Same period last year"** (PY), helping analyze data from the prior year relative to the latest sales date.
    ```bash
    Dim_Date = 
    CALENDAR(
        DATE(2022, 1, 1),
        DATE(2024, 12, 31)
    )
    Inpast = 
    VAR lastsalesdate = MAX(Fact_Sales[Date_Time])
    VAR lastsalesdatePY = EDATE(lastsalesdate,-12)
    RETURN
    Dim_Date[Date] <= lastsalesdatePY
    ```
  **The Model: Building Measures and Core Visuals**
  - **COGs** calculates the total cost of goods sold. **Quantity** calculates the total number of items sold. **Sales** calculates the total sales amount in USD. **Gross Profit** is the difference between the total sales and the cost of goods sold, giving the gross profit margin (before other expenses).
     ```bash
     COGs = SUM(Fact_Sales[COGS_USD])
     Quantity = SUM(Fact_Sales[quantity])
     Sales = SUM(Fact_Sales[Sales_USD])
     Gross Profit = [Sales]-[COGs]
    ```
  - The **PYTD_Sales** measure calculates sales from the same period last year using the **SAMEPERIODLASTYEAR** function. It adjusts the context to filter only the dates from last year and applies the Inpast = TRUE condition to ensure only relevant past dates are included. This measure is useful for year-over-year sales comparisons.
 For example: if today is September 15, 2024, **PYTD_Sales** will calculate the sales from January 1, 2023, to September 15, 2023, but only for those dates marked as TRUE in the Inpast column.
     ```bash
     PYTD_Sales = 
     CALCULATE(
 	           [Sales],
 	           SAMEPERIODLASTYEAR(Dim_Date[Date]),
            	Dim_Date[Inpast] = TRUE
    )
    ```
  - create DAX measure as **PYTD_Quantity**,  **PYTD_GrossProfit** which is similar to **PYTD_Sales**
     ```bash
     PYTD_Quantity = 
                 CALCULATE(
 	               [Quantity],
 	               SAMEPERIODLASTYEAR(Dim_Date[Date]),
                	Dim_Date[Inpast] = TRUE
     )

     PYTD_GrossProfit = 
     CALCULATE(
             [Gross Profit],
             SAMEPERIODLASTYEAR(Dim_Date[Date]),
     	       Dim_Date[Inpast] = TRUE
     )
    ```
  - **YTD_Sales, YTD_Quantity, and YTD_GrossProfit** calculate the **year-to-date** totals for sales, quantity, and gross profit respectively, up to the current date in the year, using the **TOTALYTD** function on the **Fact_Sales[Date_Time]** column.
     ```bash
     YTD_Sales = TOTALYTD([Sales], Fact_Sales[Date_Time])

     YTD_Quantity = TOTALYTD([Quantity], Fact_Sales[Date_Time])
     
     YTD_GrossProfit = TOTALYTD([GrossProfit], Fact_Sales[Date_Time])
    ```
  - **S_PYTD** dynamically returns **PYTD Sales, Quantity, or Gross Profit** depending on what the user selects in the slicer **(Slc_Values[Values])** (Note: create a new table **Slc_Value** including **Sales, Quantity, and Gross Profit**).
This allows for flexibility in viewing different metrics' prior year-to-date performance based on user selection.
     ```bash
     S_PYTD =
     VAR selected_value = SELECTEDVALUE(Slc_Values[Values])
     VAR result = SWITCH(selected_value,
            	"Sales", [PYTD_Sales],
            	"Quantity", [PYTD_Quantity],
            	"Gross Profit", [PYTD_GrossProfit],
            	Blank()
     )
     RETURN
     result
    ```
  - Create a DAX measure that functions similarly to **S_YTD**
     ```bash
     S_YTD =
     VAR selected_value = SELECTEDVALUE(Slc_Values[Values])
     VAR result = SWITCH(selected_value,
            	"Sales", [YTD_Sales],
            	"Quantity", [YTD_Quantity],
            	"Gross Profit", [YTD_GrossProfit],
            	Blank()
     )
     RETURN
     result
    ```
  - **YTD vs PYTD** calculates the change in performance between the current year-to-date and the previous year-to-date. **GP%** calculates the percentage of gross profit relative to sales, providing insight into how much profit is generated from sales.
     ```bash
     YTD vs PYTD = [S_YTD] - [S_PYTD]
     GP% = DIVIDE([Gross Profit], [Sales])
    ```
  **Star Model Linking**
 ![3dash](https://github.com/taytran1510/Power-BI-Portfolio-Project/blob/main/Images/StarModelView.png)
  **Charts Visualization**
 ![3dash](https://github.com/taytran1510/Power-BI-Portfolio-Project/blob/main/Images/Review2024.png)
 ![3dash](https://github.com/taytran1510/Power-BI-Portfolio-Project/blob/main/Images/ReviewSales.png)
 ![3dash](https://github.com/taytran1510/Power-BI-Portfolio-Project/blob/main/Images/ReviewGrossProfit.png)
 ![3dash](https://github.com/taytran1510/Power-BI-Portfolio-Project/blob/main/Images/ReviewQuantity.png)
  **[Addition] Tydying up: Titles and Formating**
  - The three DAX expressions, which are dynamically creating chart titles based on user selections from a slicer **Slc_Values[Values]**. Furthermore, the charts are formated and restyled based on personal perspective.
     ```bash
     _Waterfall_title = SELECTEDVALUE(Slc_Values[Values]) & " YTD vs PYTD | Month - Country - Product"
     _Column Chart title = SELECTEDVALUE(Slc_Values[Values]) & " YTD & PYTD | Month"
     _Scatter title = "Account Profitability Segmentation | GP% and " & SELECTEDVALUE(Slc_Values[Values])
    ```

## [Project 2: Superstore Sales Dashboard with Streamlit](https://github.com/tushar2704/Superstore-Sales-Dashboard-with-Streamlit)

Superstore Sales with Streamlit is a data visualization and analysis project that uses the Streamlit framework to create an interactive web application for exploring and analyzing sales data from a superstore. This project aims to provide an easy-to-use interface for users to gain insights into sales trends, Sales performance, product performance, Shippin analysis and Location analysis. 
###### Deployment [![Streamlit App](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://tushar2704-superstore-dashboard.streamlit.app/)



