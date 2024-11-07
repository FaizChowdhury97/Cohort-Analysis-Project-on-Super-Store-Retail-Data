# Cohort-Analysis-Project-on-Super-Store-Retail-Data

This repository contains a cohort analysis project based on a Kaggle superstore retail dataset.

# Dataset Attribute Information

** - InvoiceNo : ** Invoice number. Nominal, a 6-digit integral number uniquely assigned to each transaction. If this code starts with the letter 'c', it indicates a cancellation.
** - Quantity :** The quantities of each product (item) per transaction. Numeric.
** - InvoiceDate: ** Invoice Date and time. Numeric, the day and time when each transaction was generated.
** - UnitPrice: ** Unit price. Numeric, Product price per unit in sterling.
** - CustomerID: ** Customer number. Nominal, a 5-digit integral number uniquely assigned to each customer.
** - Country: ** Country name. Nominal, the name of the country where each customer resides.

# Data Preprocessing Steps

After obtaining the dataset, the following preprocessing steps were performed:

1. Fixed data types:
   
   - InvoiceNo to text
   - StockCode to text
   - InvoiceDate to date (mm/dd/yyyy)
   - CustomerID to whole number

2. Removed empty values from CustomerID.

3. Removed canceled orders (invoices starting with 'C') from Invoice No.

4. Created a "sales" column by multiplying the Quantity and UnitPrice columns.

5. Removed sales entries with 0 values.

# Additional Tables Created

Two additional tables were created for the project:

### 1. DimCustomers:

    - Contains CustomerID, First Transaction Month, and First Transaction Week.

### 2. DimDate:

   - Contains all unique dates from 12/1/2010 to 12/9/2011.
   - Includes Start of Month and Start of Week attributes.

# Data Modeling in Power BI
The following data modeling steps were performed in Power BI: image
 
 - Connected CustomerID from DimCustomers to CustomerID of the main table FactSales.
 - Connected Date from DimDate to InvoiceDate of FactSales.
   
# DAX Measures
Eighteen DAX measures were created for cohort analysis:

1. Active Customers = DISTINCTCOUNT(FactSales[CustomerID])

2. Churned Customers = SWITCH(TRUE(), ISBLANK([Retention Rate]), BLANK(), [New Customers] - [Cohort Performance])

3. Churned Rate = DIVIDE([Churned Customers],[New Customers])

4. Cohort Performance = VAR _minDate = MIN(DimDate[Start of Month]) VAR _maxDate = MAX(DimDate[Start of Month]) RETURN CALCULATE( [Active Customers], REMOVEFILTERS(DimDate[Start of Month]), RELATEDTABLE(DimCustomers), DimCustomers[First Transaction Month] >= _minDate && DimCustomers[First Transaction Month] <= _maxDate )

5. Difference = [Active Customers] - [Validation]

6. Lost Customers = VAR _customersThisMonth = VALUES(FactSales[CustomerID]) VAR _customersLastMonth = CALCULATETABLE( VALUES(FactSales[CustomerID]), PREVIOUSMONTH(DimDate[Start of Month]) ) VAR _lostCustomers = EXCEPT( _customersLastMonth, _customersThisMonth ) RETURN COUNTROWS(_lostCustomers)

7. New Customers = CALCULATE([Active Customers], FactSales[Months Since First Transaction] = 0)

8. Recovered Customers = VAR _customersThisMonth = VALUES(FactSales[CustomerID]) VAR _customersLastMonth = CALCULATETABLE( VALUES(FactSales[CustomerID]), PREVIOUSMONTH(DimDate[Start of Month]) ) VAR _newCustomers = CALCULATETABLE( VALUES(FactSales[CustomerID]), FactSales[Months Since First Transaction] = 0 ) VAR _recoveredCustomers = EXCEPT( EXCEPT(_customersThisMonth, _customersLastMonth), _newCustomers ) RETURN COUNTROWS(_recoveredCustomers)

9. Retained Customers = VAR _customersThisMonth = VALUES(FactSales[CustomerID]) VAR _customersLastMonth = CALCULATETABLE( VALUES(FactSales[CustomerID]), PREVIOUSMONTH(DimDate[Start of Month]) ) VAR _retainedCustomers = INTERSECT( _customersLastMonth, _customersThisMonth ) RETURN COUNTROWS(_retainedCustomers)

10. Retention Rate = DIVIDE([Cohort Performance],[New Customers])

11. Validation =  [New Customers] + [Retained Customers] + [Recovered Customers]

# Visualizations
Finally, the project is ready to create visualizations based on the calculated DAX measures to analyze and present the cohort analysis results.

Monthly Cohort Analysis:
