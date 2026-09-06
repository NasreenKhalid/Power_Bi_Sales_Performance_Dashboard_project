
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






<img width="457" height="258" alt="image" src="https://github.com/user-attachments/assets/0a81e093-746d-401b-ba07-11c94920d2fa" />
