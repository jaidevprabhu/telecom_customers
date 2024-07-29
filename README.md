# Profiling Customer groups for Segmentation

The data provided contains information on customers' purchasing and usage behavior with telecom products. The project will use this to create customer group profiles to better understand customer behavior, which could be used in a marketing campaign.



```python
# Read the CSV file into a DataFrame
df = pd.read_csv('data/telco_churn_data.csv')

# Display the first 5 rows
print(df.head().to_markdown(index=False, numalign="left", stralign="center"))

|  Customer ID  |  Referred a Friend  | Number of Referrals   | Tenure in Months   |  Offer  |  Phone Service  | Avg Monthly Long Distance Charges   |  Multiple Lines  |  Internet Service  |  Internet Type  | Avg Monthly GB Download   |  Online Security  |  Online Backup  |  Device Protection Plan  |  Premium Tech Support  |  Streaming TV  |  Streaming Movies  |  Streaming Music  |  Unlimited Data  |    Contract    |  Paperless Billing  |  Payment Method  | Monthly Charge   | Total Regular Charges   | Total Refunds   | Total Extra Data Charges   | Total Long Distance Charges   |  Gender  | Age   |  Under 30  |  Senior Citizen  |  Married  |  Dependents  | Number of Dependents   |    City     | Zip Code   | Latitude   | Longitude   | Population   | Churn Value   | CLTV   |  Churn Category  |         Churn Reason         | Total Customer Svc Requests   | Product/Service Issues Reported   | Customer Satisfaction   |
|:-------------:|:-------------------:|:----------------------|:-------------------|:-------:|:---------------:|:------------------------------------|:----------------:|:------------------:|:---------------:|:--------------------------|:-----------------:|:---------------:|:------------------------:|:----------------------:|:--------------:|:------------------:|:-----------------:|:----------------:|:--------------:|:-------------------:|:----------------:|:-----------------|:------------------------|:----------------|:---------------------------|:------------------------------|:--------:|:------|:----------:|:----------------:|:---------:|:------------:|:-----------------------|:-----------:|:-----------|:-----------|:------------|:-------------|:--------------|:-------|:----------------:|:----------------------------:|:------------------------------|:----------------------------------|:------------------------|
|  8779-QRDMV   |         No          | 0                     | 1                  |   nan   |       No        | 0                                   |        No        |        Yes         |   Fiber Optic   | 9                         |        No         |       No        |           Yes            |           No           |       No       |        Yes         |        No         |        No        | Month-to-Month |         Yes         | Bank Withdrawal  | 41.236           | 39.65                   | 0               | 0                          | 0                             |   Male   | 78    |     No     |       Yes        |    No     |      No      | 0                      | Los Angeles | 90022      | 34.0238    | -118.157    | 68701        | 1             | 5433   |    Competitor    | Competitor offered more data | 5                             | 0                                 | nan                     |
|  7495-OOKFY   |         Yes         | 1                     | 8                  | Offer E |       Yes       | 48.85                               |       Yes        |        Yes         |      Cable      | 19                        |        No         |       Yes       |            No            |           No           |       No       |         No         |        No         |        No        | Month-to-Month |         Yes         |   Credit Card    | 83.876           | 633.3                   | 0               | 120                        | 390.8                         |  Female  | 74    |     No     |       Yes        |    Yes    |     Yes      | 1                      | Los Angeles | 90063      | 34.0443    | -118.185    | 55668        | 1             | 5302   |    Competitor    | Competitor made better offer | 5                             | 0                                 | nan                     |
|  1658-BYGOY   |         No          | 0                     | 18                 | Offer D |       Yes       | 11.33                               |       Yes        |        Yes         |   Fiber Optic   | 57                        |        No         |       No        |            No            |           No           |      Yes       |        Yes         |        Yes        |       Yes        | Month-to-Month |         Yes         | Bank Withdrawal  | 99.268           | 1752.55                 | 45.61           | 0                          | 203.94                        |   Male   | 71    |     No     |       Yes        |    No     |     Yes      | 3                      | Los Angeles | 90065      | 34.1088    | -118.23     | 47534        | 1             | 3179   |    Competitor    | Competitor made better offer | 1                             | 0                                 | nan                     |
|  4598-XLKNJ   |         Yes         | 1                     | 25                 | Offer C |       Yes       | 19.76                               |        No        |        Yes         |   Fiber Optic   | 13                        |        No         |       Yes       |           Yes            |           No           |      Yes       |        Yes         |        No         |        No        | Month-to-Month |         Yes         | Bank Withdrawal  | 102.44           | 2514.5                  | 13.43           | 327                        | 494                           |  Female  | 78    |     No     |       Yes        |    Yes    |     Yes      | 1                      |  Inglewood  | 90303      | 33.9363    | -118.333    | 27778        | 1             | 5337   | Dissatisfaction  |  Limited range of services   | 1                             | 1                                 | 2                       |
|  4846-WHAFZ   |         Yes         | 1                     | 37                 | Offer C |       Yes       | 6.33                                |       Yes        |        Yes         |      Cable      | 15                        |        No         |       No        |            No            |           No           |       No       |         No         |        No         |        No        | Month-to-Month |         Yes         | Bank Withdrawal  | 79.56            | 2868.15                 | 0               | 430                        | 234.21                        |  Female  | 80    |     No     |       Yes        |    Yes    |     Yes      | 1                      |  Whittier   | 90602      | 33.9721    | -118.02     | 26265        | 1             | 2793   |      Price       |      Extra data charges      | 1                             | 0                                 | 2                       |

```

### Data Investigation

The data contain information about customer demographics, service usage, charges, and a churn indicator. 


### Data Cleaning

 There are missing values in some columns, and many columns are already in a numeric format, which is suitable for PCA.  
  
### Columns to drop

  But there are some columns that capture same information in a different manner and some columns with lots of NaN's. 
  We will exclude these features from the clustering analysis.

  - '**Customer ID**' (irrelevant to consideration here)
  - '**Referred a Friend**' : already captured in 'Number of referrals'
  - '**Under 30**' and '**Senior Citizen**' : already captued by 'Age'
  - '**City**', '**Lat.**' , '**Long.**', '**Zip Code**', '**Population**' - proximity is ignored for this iteration
  - '**Dependents**' : 'Number of Dependents' == 0;  captures this data
  - '**Offer**, '**Internet Type**', '**Churn Reason**', '**Churn Category**', Customer Satisfaction : too many NaN values
  - '**Churn Value**' and '**CLTV**' appear to be related to the customer's value and likelihood to churn, not directly helpful in profiling.


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

### Converting categorical data

We will convert the remaining object columns to numeric using one-hot encoding, from the sklearn package. We drop the first category that enables us to view the variance from columns with > 1 category.

```python
# Get all object columns
obj_cols = df_pca_cluster.select_dtypes(include='object').columns

# One-hot encode the object columns
encoder = OneHotEncoder(drop='first', sparse_output=False)
encoded_data = encoder.fit_transform(df_pca_cluster[obj_cols])
encoded_df = pd.DataFrame(encoded_data, columns=encoder.get_feature_names_out(obj_cols))
```

