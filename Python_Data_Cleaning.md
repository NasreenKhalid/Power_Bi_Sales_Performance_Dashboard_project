
### Python Data Analysis and Cleaning:

- One exact duplicate transaction was identified in Orders and removed.
- Order_Date and Unit_Price were stored as strings and converted to appropriate analytical data types.
- Negative quantities associated with returned orders were retained because they represent the business transaction rather than erroneous data.

Analytical Problem 2 — Sales Performance Analysis

Once the data is trustworthy enough to analyze, investigate Nexa Retail's sales performance.

Answer:

What are the company's overall Gross Sales, Returns and Net Sales?
gross_sales = (orders['Quantity']* orders['Unit_Price']).sum()
print(f"\nGross Sales: ${gross_sales:,.2f}"). 
**Gross Sales: $65,475.00**

returns = abs((orders[orders['Status'] == 'Returned']['Quantity'] * 
               orders[orders['Status'] == 'Returned']['Unit_Price']).sum())
print(f"Total Returns: ${returns:,.2f}")
**Total Returns: $1,200.00**

Net Sales = Gross Sales - (Returns + Discounts + Allowances)
net_sales = gross_sales - returns
print(f"Net Sales: ${net_sales:,.2f}")
**Net Sales: $64,275.00**


How does sales performance change over time?

Which month had the strongest Net Sales?
Which month had the weakest?
How consistent is monthly performance?
How does actual sales performance compare with the company's targets?
What is the overall target achievement?
How large is the remaining target shortfall?
