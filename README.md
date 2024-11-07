# Cohort-Analysis-Project-on-Super-Store-Retail-Data


This repository contains a cohort analysis project based on a Kaggle superstore retail dataset.

# Dataset Attribute Information

- **InvoiceNo :** Invoice number. Nominal, a 6-digit integral number uniquely assigned to each transaction. If this code starts with the letter 'c', it indicates a cancellation.
- **Quantity :** The quantities of each product (item) per transaction. Numeric.
- **InvoiceDate :** Invoice Date and time. Numeric, the day and time when each transaction was generated.
- **UnitPrice :** Unit price. Numeric, Product price per unit in sterling.
- **CustomerID :** Customer number. Nominal, a 5-digit integral number uniquely assigned to each customer.
- **Country :** Country name. Nominal, the name of the country where each customer resides.

# Data Preprocessing Steps

After obtaining the dataset, the following preprocessing steps were performed:

1. Fixed data types:
   
   - `InvoiceNo` to text
   - `InvoiceDate` to date (mm/dd/yyyy)
   - `CustomerID` to whole number

2. Removed empty values from `CustomerID`.

3. Removed canceled orders (invoices starting with 'C') from `InvoiceNo`.

4. Created a "sales" column by multiplying the `Quantity` and `Price` columns.

5. Removed sales entries with 0 values.

# Additional Tables Created

Two additional tables were created for the project:

### 1. DimCustomers:

  - Contains `CustomerID` and First `Transaction Month`.

### 2. DimDate:

   - Contains all unique dates from 1/1/2009 to 12/31/2011.
   - Includes `Start of Month` attributes.

# Data Modeling in Power BI
The following data modeling steps were performed in Power BI: 

![ss7](https://github.com/user-attachments/assets/9e77bafb-b1d9-42ee-89fe-c96b3d2c08b2)

 
 - Connected `CustomerID` from **DimCustomers** to `CustomerID` of the main table **FactSales**.
 - Connected `Date` from **DimDate** to `InvoiceDate` of **FactSales**.
   
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

### Executive Dashboard:
---

![SS1](https://github.com/user-attachments/assets/a7c74728-dfa0-4090-a3a0-080b346e7853)

### Visual Analysis:
---

![SS2](https://github.com/user-attachments/assets/11e4010f-13d1-47cc-a49c-e40e6757177a)

### Cohort Analysis:
---

![ss3](https://github.com/user-attachments/assets/9c8c259a-39dc-40fb-a0f7-73377bff0c47)


### Trend Analysis:
---

![ss4](https://github.com/user-attachments/assets/11e383ae-cbff-449e-9764-4ced66a9ac09)






