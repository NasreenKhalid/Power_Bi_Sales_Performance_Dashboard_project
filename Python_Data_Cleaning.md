
### Python Data Analysis and Cleaning:

- One exact duplicate transaction was identified in Orders and removed.
- Order_Date and Unit_Price were stored as strings and converted to appropriate analytical data types.
- Negative quantities associated with returned orders were retained because they represent the business transaction rather than erroneous data.
- Inconsistent values in orders['Region'] column 

### Analytical Problem 2 — Sales Performance Analysis

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
**Net Sales: $64,275.00**. 



How does sales performance change over time?  


monthly_sales = (orders.groupby(orders['Order_Date'].dt.to_period("M"))['Sales'].sum().reset_index(name="total_sales")). 
monthly_sales.plot(x='Order_Date', y='total_sales', kind='line', marker='o'). 
<img width="589" height="477" alt="image" src="https://github.com/user-attachments/assets/0b316a91-5900-46c7-874f-18446bdcce8f" />


Which month had the strongest Net Sales?  
max_sales_idx = monthly_sales['total_sales'].idxmax(). 
top_month = monthly_sales.loc[max_sales_idx]. 
print(f"Month with most sales: {top_month['Order_Date']}"). 
print(f"Sales: ${top_month['total_sales']:,.2f}"). 
**Month with most sales: 2024-05**. 
**Sales: $7,700.00**.  

Which month had the weakest?       
min_sales_idx = monthly_sales['total_sales'].idxmin(). 

worst_month = monthly_sales.loc[min_sales_idx]. 
print(f"Month with least sales: {worst_month['Order_Date']}"). 
print(f"Sales: ${worst_month['total_sales']:,.2f}"). 
**Month with least sales: 2024-04**. 
**Sales: $2,150.00**. 


How consistent is monthly performance?
How does actual sales performance compare with the company's targets?
Create a monthly_targets table to check the total target for each month:
monthly_targets = (targets.groupby(targets['Month'].dt.to_period("M"))['Sales_Target'].sum().reset_index(name="total_target"))

df_combined = pd.merge(monthly_sales, monthly_targets, on='Month', how='inner')

<img width="322" height="234" alt="image" src="https://github.com/user-attachments/assets/5c7aa5ce-7924-4b32-8e62-c7b364c5bdd3" />

df_combined.plot(
    x='Month', 
    y=['total_sales', 'total_target'],  
    kind='bar',                           
    color=['#2ca02c', '#f72328'],         # Green for sales, red for target
    figsize=(12, 6),
    width=0.8                             
)

plt.title('Monthly Sales vs. Target Comparison', fontsize=14, fontweight='bold')
plt.ylabel('Amount ($)')
plt.xlabel('Month')
plt.xticks(rotation=45)                   
plt.legend(['Actual Sales', 'Target'])
plt.grid(axis='y', linestyle='--', alpha=0.5)

plt.tight_layout()                        # Prevents labels from getting cut off
plt.show()


<img width="1120" height="539" alt="image" src="https://github.com/user-attachments/assets/d2eebbf4-39ec-4e4a-9ebc-358902d89c1d" />

What is the overall target achievement?

How large is the remaining target shortfall?


df_combined['Target_Variance'] = df_combined['total_sales'] - df_combined['total_target']


### Analytical Problem 3 — Regional Performance

Management wants to know whether sales performance differs significantly across regions.

Investigate:

Which region generates the most Net Sales?  
**North**

Which region generates the least?  
**South**. 

sales_by_region = orders.groupby('Region')['Sales'].sum()
Region
East     17075.0
North    18450.0
South    12325.0
West     17625.0
Name: Sales, dtype: float64

sales_by_region.plot(
    kind='bar', 
    color='#1f77b4',       # Clean professional blue
    edgecolor='black',     # Adds a sharp border to the bars
    figsize=(8, 5)
)

# Customize labels and title
plt.title('Total Sales by Region', fontsize=14, fontweight='bold', pad=15)
plt.xlabel('Region', fontsize=12)
plt.ylabel('Sales ($)', fontsize=12)
plt.xticks(rotation=0)     # Keeps region names horizontal (East, North, etc.) so they are easy to read
plt.grid(axis='y', linestyle='--', alpha=0.7) # Adds a light horizontal background grid

# Display the plot
plt.tight_layout()
plt.show()

<img width="843" height="498" alt="image" src="https://github.com/user-attachments/assets/2a53a026-6c20-4e0b-ae2d-67bb1e993c21" />


What proportion of total sales comes from each region?

region_sales = pd.Series({
    'East': 17075.0,
    'North': 18450.0,
    'South': 12325.0,
    'West': 17625.0
})

# Total net sales
net_sales = 64275.00

# Calculate percentage share
region_share = (region_sales / net_sales) * 100
print(region_share.round(2))

East     26.57
North    28.70
South    19.18
West     27.42

1. Regional share ✅

Your calculation gives:

Region	Net Sales	Share
North	  $18,450	  28.70%
West	  $17,625	  27.42%
East	  $17,075	  26.57%
South	  $12,325	  19.18%

North, West, and East are actually quite close — all around 26–29%. South is the clear lower-performing region.

Does the strongest region also perform well when considering returns?  
returns_by_region = orders[orders['Status'] == 'Returned'].groupby('Region')['Sales'].sum()
print("Returns Value by Region:")
print(returns_by_region). 
Returns Value by Region:
Region
North   -1200.0. 
**all recorded returns in this dataset are coming from North**. 
Are there regions whose performance deserves further investigation?
Deliverable

Create a regional performance analysis and write a short explanation of what management should take away from it.
I have created a bar graph with a comparison between the sales region-wise, which shows that North is the most sales-making region, whereas the least sale comes from the South region. West and East are in the middle.
North is driving the volume, while South highlights an area that needs attention and strategy.



### Analytical Problem 4 — Customer Concentration & Returns. 


Nexa Retail wants to understand its customer base.  


Investigate:  

Which customers generate the most Gross Sales?
customers_sales = orders.groupby('Customer_ID')['Sales'].sum()
# Get the customer with the highest sales
top_customer = customers_sales.idxmax()
top_sales = customers_sales.max()

print(f"Top Customer: {top_customer}")
print(f"Total Sales: ${top_sales:,.2f}"). 
Top Customer: C001
Total Sales: $4,800.00.  
# Get top 5 customers
top_5_customers = customers_sales.nlargest(5)
print("Top 5 Customers by Sales:")
print(top_5_customers). 
Top 5 Customers by Sales:
Customer_ID
C001    4800.0
C031    4000.0
C003    3200.0
C002    2000.0
C012    2000.0
How concentrated is revenue among the largest customers?


What percentage of Gross Sales comes from the top 5 customers?

Highest-grossing customer: C001 → $4,800
Top 5: C001, C031, C003, C002, C012
Top 5 Gross Sales: $16,000
Top 5 contribution: 24.44%  

Are there customers with unusually high returns?
Does high Gross Sales necessarily correspond to high Net Sales?
Are there customers whose return behavior deserves attention?
returns_by_customers = orders[orders['Status'] == 'Returned'].groupby('Customer_ID')['Sales'].sum()
print("Returns Value by Customers:")
print(returns_by_customers)

Returns Value by Customers:
Customer_ID
C020   -1200.0

Only one customer has a returned order: C020
Return value: $1,200
None of the Top 5 customers (C001, C031, C003, C002, C012) had a recorded return.


Deliverable

Identify the most commercially important customers and any customer-level risks or patterns you discover.

- Customer concentration: Top 5 customers generated $16,000, or 24.44% of Gross Sales.
- Returns: Only C020 had a recorded return, totaling $1,200.
- Top-5 return exposure: 0% of returns came from the Top 5 customers, because none of them had a return.
- Business interpretation: Revenue is somewhat concentrated among the top customers, but the current return data does not indicate return risk among those highest-value customers.

- P03 and P08 are the joint highest-selling products, each contributing 18.67% of Net Sales, while P10 is the weakest at 1.98%.
- The product mix is therefore somewhat concentrated around P03 and P08, while P10 contributes very little to overall sales.


Final Python Analysis — Completed

Topics covered:

1. Data Quality

Missing values
Duplicates
Data types
Currency formatting
Negative quantities/returns
Cleaning decisions

2. Sales Performance

Gross Sales: $65,475
Returns: $1,200
Net Sales: $64,275
Monthly sales trend
Strongest/weakest months
Monthly sales vs targets

3. Regional Performance

North: 28.70%
West: 27.42%
East: 26.57%
South: 19.18%
Returns by region

4. Customer Analysis

Top customer: C001 — $4,800
Top 5 customers: $16,000
Top 5 contribution: 24.44%
Returns by customer
None of the Top 5 had recorded returns

5. Independent Insight

Product-level sales analysis
P03 and P08 are joint leaders at $12,000
P10 is the weakest at $1,275


