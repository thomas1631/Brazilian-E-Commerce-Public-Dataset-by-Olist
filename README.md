# Brazilian-E-Commerce-Public-Dataset-by-Olist
Brazilian E-Commerce Public Dataset by Olist

1.
import pandas as pd
import numpy as np
import seaborn as sns                       #visualisation
import matplotlib.pyplot as plt             #visualisation
%matplotlib inline     
sns.set(color_codes=True)
2.
df = pd.read_csv
# Show the first 5 rows
df.head()
3.
# Check for missing values
df.isnull().sum()
# Create a copy of the original dataset for diagnostics
df_cpy = df.copy()

df_cpy.describe()

#the min unit price is negative so that's illogical so it's typo mistake we need to further investigation for it , also the quantity column the mind is -ve so we need further investigation
4.
#identify the columns datatypes and find null content
df.info()
5.
# Transform negative Values to postive for Quantity,UnitPrice and remove duplicates
# Convert negative Quantity and UnitPrice values to positive (for inspection only)
df_cpy[['Quantity', 'UnitPrice']] = df_cpy[['Quantity', 'UnitPrice']].abs()
6.
# Remove duplicate rows
df_cpy = df_cpy.drop_duplicates()
7.
# Display statistical summary to check for outliers and anomalies
summary_stats = df_cpy[['Quantity', 'UnitPrice']].describe()
print("Summary statistics before filtering:")
print(summary_stats)
#then after investigation we've found it's typo for -ve sign so we make absolute value to get postive value from it after that we've found min unit price is zero so we need further investigation for it

8.
#Find total orders for products that have Unitprice=0 and CustomerID is Null
zero_price=df_cpy.query('UnitPrice==0') #to filter only unitPirce =0
zero_price_null_cust=zero_price[zero_price['CustomerID'].isna()==True] #to filter only customerID is Null
null_customer_null_price=zero_price_null_cust.groupby('Description',as_index=False)['InvoiceNo'].count().sort_values(by='InvoiceNo',ascending=False)
null_customer_null_price.rename(columns={'InvoiceNo':'No_of_orders','Description':'Product_name'},inplace=True) # to rename the column inoviceNo to no_of_orders
null_customer_null_price

#after further investigation after group by product_name there's unit price=0 and customer Id=null this likely due to some errors and damages in stores warehouse,and after that we should provide these details for stakeholder.

9.
# Filter only where CustomerID Is Not Null
df_cpy=df_cpy[df_cpy['CustomerID'].isna()==False] # we filter for customer ID not null to used for further analysis
df_cpy
10.
#Create Multiple Columns dervied From InvoiceDate like Month, Year, Quarter....etc
df_cpy['month']=df_cpy.loc[:,'InvoiceDate'].dt.month
df_cpy['year']=df_cpy.loc[:,'InvoiceDate'].dt.year
df_cpy['day']=df_cpy.loc[:,'InvoiceDate'].dt.day
df_cpy['weekday']=df_cpy.loc[:,'InvoiceDate'].dt.weekday # it returns weekdays as 0  which =monday  etc
week_days={0:'Monday',1:'Tuesday',2:'Wednesday',3:'Thursday',4:'Friday',5:'Tuesday',6:'Sunday'} # to use in mapping to create weekday column from numerical to actual week names
df_cpy['weekday']=df_cpy['weekday'].map(week_days)
df_cpy['hour']=df_cpy.loc[:,'InvoiceDate'].dt.hour
df_cpy['year_month']=df_cpy.loc[:,'InvoiceDate'].dt.to_period('M') # create column month with the year countinous data
df_cpy['quarter']=df_cpy.loc[:,'InvoiceDate'].dt.to_period('Q') # to create quarter

df_cpy['year_month']=df_cpy.loc[:,'year_month'].dt.to_timestamp() #to change year month column to timestamp data type

df_cpy

#Create Total_sales(Revenue) Column
#Total orders per hour
#Top customers make orders
#Top customers make revenue
#Number Of Orders per Weekday
#Top products make revenue
#Top selling Products by Total Quantity
#Trends of total sales during the year
# Calculate and display the mean and median of Quantity in cleaned data +# Group and plot
#Total Sales Per Quarter
#Total sales Percent per Country
#Trends of Cancellation during the year per weekday
#cancellation orders per customer
