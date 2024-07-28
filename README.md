### Profiling Customer groups for Segmentation

The data provided contains information on customers purchasing and useage behavior with the telecom products. 


```{python, echo=FALSE}
import pandas as pd
from sklearn.preprocessing import OneHotEncoder, StandardScaler
from sklearn.decomposition import PCA
from sklearn.cluster import KMeans
import numpy as np
from matplotlib import pyplot as plt
```


```python
# Read the CSV file into a DataFrame
df = pd.read_csv('data/telco_churn_data.csv')

# Display the first 5 rows
#print(df.head().to_markdown(index=False, numalign="left", stralign="center"))

# Print the column names and their data types
print(df.info())
```

## Columns to drop

  We will exclude these features from the clustering analysis.

  - '**Customer ID**' (irrelevant to consideration here)
  - '**Referred a Friend**' : already captured in 'Number of referrals'
  - '**Under 30**' and '**Senior Citizen**' : already captued by 'Age'
  - '**City**', '**Lat.**' , '**Long.**', '**Zip Code**', '**Population**' - proximity is ignored for this iteration
  - '**Dependents**' : 'Number of Dependents' == 0;  captures this data
  - '**Offer**, '**Internet Type**', '**Churn Reason**', '**Churn Category**', Customer Satisfaction : too many NaN values
  - Given that the columns '**Churn Value**' and '**CLTV**' appear to be related to the customer's value and likelihood to churn.


```python
# Drop the selected columns 
cols_to_drop = ['Customer ID', 'Referred a Friend', 'Under 30', \
                'Senior Citizen', 'City', 'Latitude' , 'Longitude', \
                'Zip Code', 'Population', \
                'Dependents', 'Offer', 'Internet Type', 'Churn Reason', \
                'Churn Category', 'Customer Satisfaction', \]
                'Churn Value','CLTV', ]
df_pca_cluster = df.drop(cols_to_drop, axis = 1, inplace = True)
```
We will convert the remaining object columns to numeric using one-hot encoding. Then, we will standardize the data and apply PCA to reduce dimensionality. Finally, we will use K-Means clustering to segment the customers.

Given that the data appears to be on the customer-level, and we are looking to segment customers into groups, we will not be using any sampling methods.

