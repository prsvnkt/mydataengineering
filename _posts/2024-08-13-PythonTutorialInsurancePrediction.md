---
title: Car Insurance Prediction Modeling using Python
description: Descriptive Analysis, Statistics and Machine Learning Models for prediction using Python on an Insurance Company Dataset.
author: prasanna
date: 2024-08-13T00:30:00+0000
categories: [Tutorial]
tags: [Python, Data Analysis, Pandas, Data Science, Scikit-Learn, Seaborn]
mermaid: true
---

## Objective 

An insurance company has been providing health insurance to its customers and are now hoping to build a model to predict whether the policy holders from past will also be interested in Vehicle Insurance provided by the company.

Building this model would help the company in its communication strategy to reach out to those customers and optimize its business model and revenue.

Now, in order to predict, whether the customer would be interested in Vehicle Insurance, the following information are at hand in the dataset,

1. id
2. Gender
3. Age
4. Driving_License - 0 : Customer does not have DL, 1 : Customer already has DL
5. Region_Code
6. Previously_Insured - 1 : Customer already has Vehicle Insurance, 0 : Customer doesn't have Vehicle Insurance
7. Vehicle_Age
8. Vehicle_Damage - 1 : Customer got his/her vehicle damaged in the past. 0 : Customer didn't get his/her vehicle damaged in the past
9. Annual_Premium - The amount customer needs to pay as premium in the year
10. PolicySalesChannel - Anonymized Code for the channel of outreaching to the customer ie. Different Agents, Over Mail, Over Phone, In Person, etc.
11. Vintage - Number of Days, Customer has been associated with the company
12. Response - 1 : Customer is interested, 0 : Customer is not interested

In this report, descriptive analytics and statistics is produced along with data preprocessing for the dataset. You can find the Github repo for this project [here](https://github.com/prsvnkt/Insurance-Prediction-ML).

## 1. Load the data


```python
#the required libraries for the tasks are imported
import numpy as np           #for efficient numerical operations
import pandas as pd          #for manipulating and visualising data
import matplotlib.pyplot as plt   #for data visualization
import seaborn as sns             #for data visualization

#load the dataset
dataset = pd.read_csv('dataset.csv')
dataset.head(5)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>Gender</th>
      <th>Age</th>
      <th>Driving_License</th>
      <th>Region_Code</th>
      <th>Previously_Insured</th>
      <th>Vehicle_Age</th>
      <th>Vehicle_Damage</th>
      <th>Annual_Premium</th>
      <th>Policy_Sales_Channel</th>
      <th>Vintage</th>
      <th>Response</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Male</td>
      <td>44</td>
      <td>1</td>
      <td>28</td>
      <td>0</td>
      <td>&gt; 2 Years</td>
      <td>Yes</td>
      <td>40454</td>
      <td>26</td>
      <td>217</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Male</td>
      <td>76</td>
      <td>1</td>
      <td>3</td>
      <td>0</td>
      <td>1-2 Year</td>
      <td>No</td>
      <td>33536</td>
      <td>26</td>
      <td>183</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Male</td>
      <td>47</td>
      <td>1</td>
      <td>28</td>
      <td>0</td>
      <td>&gt; 2 Years</td>
      <td>Yes</td>
      <td>38294</td>
      <td>26</td>
      <td>27</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Male</td>
      <td>21</td>
      <td>1</td>
      <td>11</td>
      <td>1</td>
      <td>&lt; 1 Year</td>
      <td>No</td>
      <td>28619</td>
      <td>152</td>
      <td>203</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Female</td>
      <td>29</td>
      <td>1</td>
      <td>41</td>
      <td>1</td>
      <td>&lt; 1 Year</td>
      <td>No</td>
      <td>27496</td>
      <td>152</td>
      <td>39</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>


## 2. Split the data into Training and Testing Dataset

```python
#import scikit-learn library for machine learning and also for sampling the dataset
from sklearn.model_selection import train_test_split

#the dataset is split into train and test dataset using random sampling
train, test = train_test_split(dataset, test_size=0.4, random_state = 7)

print(f"{train.shape[0]} train and {test.shape[0]} test instances")
```

228665 train and 152444 test instances
    

The dataset is split into train and test dataset. Train dataset alone would be used in this report to avoid data snooping.


```python
#to provide information on the train dataset
train.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 228665 entries, 301388 to 61615
    Data columns (total 12 columns):
     #   Column                Non-Null Count   Dtype 
    ---  ------                --------------   ----- 
     0   id                    228665 non-null  int64 
     1   Gender                228665 non-null  object
     2   Age                   228665 non-null  int64 
     3   Driving_License       228665 non-null  int64 
     4   Region_Code           228665 non-null  int64 
     5   Previously_Insured    228665 non-null  int64 
     6   Vehicle_Age           228665 non-null  object
     7   Vehicle_Damage        228665 non-null  object
     8   Annual_Premium        228665 non-null  int64 
     9   Policy_Sales_Channel  228665 non-null  int64 
     10  Vintage               228665 non-null  int64 
     11  Response              228665 non-null  int64 
    dtypes: int64(9), object(3)
    memory usage: 22.7+ MB
    


```python
#to provide information on the test dataset
test.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 152444 entries, 87112 to 221223
    Data columns (total 12 columns):
     #   Column                Non-Null Count   Dtype 
    ---  ------                --------------   ----- 
     0   id                    152444 non-null  int64 
     1   Gender                152444 non-null  object
     2   Age                   152444 non-null  int64 
     3   Driving_License       152444 non-null  int64 
     4   Region_Code           152444 non-null  int64 
     5   Previously_Insured    152444 non-null  int64 
     6   Vehicle_Age           152444 non-null  object
     7   Vehicle_Damage        152444 non-null  object
     8   Annual_Premium        152444 non-null  int64 
     9   Policy_Sales_Channel  152444 non-null  int64 
     10  Vintage               152444 non-null  int64 
     11  Response              152444 non-null  int64 
    dtypes: int64(9), object(3)
    memory usage: 15.1+ MB
    

Information on the train and test dataset can be seen above.


```python
#to check for null values
train.isnull().sum()
```




    id                      0
    Gender                  0
    Age                     0
    Driving_License         0
    Region_Code             0
    Previously_Insured      0
    Vehicle_Age             0
    Vehicle_Damage          0
    Annual_Premium          0
    Policy_Sales_Channel    0
    Vintage                 0
    Response                0
    dtype: int64




```python
#to check for null values
test.isnull().sum()
```




    id                      0
    Gender                  0
    Age                     0
    Driving_License         0
    Region_Code             0
    Previously_Insured      0
    Vehicle_Age             0
    Vehicle_Damage          0
    Annual_Premium          0
    Policy_Sales_Channel    0
    Vintage                 0
    Response                0
    dtype: int64


There are no missing values in the train and test dataset.


## 3. Exploratory Data Analysis


```python
#descriptive statistics on the train dataset
train.describe()
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>Age</th>
      <th>Driving_License</th>
      <th>Region_Code</th>
      <th>Previously_Insured</th>
      <th>Annual_Premium</th>
      <th>Policy_Sales_Channel</th>
      <th>Vintage</th>
      <th>Response</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>228665.000000</td>
      <td>228665.000000</td>
      <td>228665.000000</td>
      <td>228665.000000</td>
      <td>228665.000000</td>
      <td>228665.000000</td>
      <td>228665.000000</td>
      <td>228665.000000</td>
      <td>228665.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>190652.209538</td>
      <td>38.828557</td>
      <td>0.997853</td>
      <td>26.397398</td>
      <td>0.458269</td>
      <td>30595.860070</td>
      <td>112.027350</td>
      <td>154.434920</td>
      <td>0.122940</td>
    </tr>
    <tr>
      <th>std</th>
      <td>110061.420927</td>
      <td>15.535094</td>
      <td>0.046289</td>
      <td>13.222463</td>
      <td>0.498257</td>
      <td>17292.567811</td>
      <td>54.194537</td>
      <td>83.736668</td>
      <td>0.328369</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>20.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>2630.000000</td>
      <td>1.000000</td>
      <td>10.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>95377.000000</td>
      <td>25.000000</td>
      <td>1.000000</td>
      <td>15.000000</td>
      <td>0.000000</td>
      <td>24432.000000</td>
      <td>29.000000</td>
      <td>82.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>190495.000000</td>
      <td>36.000000</td>
      <td>1.000000</td>
      <td>28.000000</td>
      <td>0.000000</td>
      <td>31692.000000</td>
      <td>131.000000</td>
      <td>154.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>286007.000000</td>
      <td>49.000000</td>
      <td>1.000000</td>
      <td>35.000000</td>
      <td>1.000000</td>
      <td>39432.000000</td>
      <td>152.000000</td>
      <td>227.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>381107.000000</td>
      <td>85.000000</td>
      <td>1.000000</td>
      <td>52.000000</td>
      <td>1.000000</td>
      <td>540165.000000</td>
      <td>163.000000</td>
      <td>299.000000</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



Descriptive statistics on the train dataset is seen above.


```python
#plot correlation matrix
plt.figure(figsize=(16,6))
ht=sns.heatmap(train.corr(), vmin=-1, vmax=1, annot=True)
ht.set_title('Correlation Heatmap', fontdict={'fontsize':12}, pad=12);
```


    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_14_0.png)
    


The attributes that are highly correlated with "Response" are "Previously_Insured", Policy_Sales_Channel" and "Age", in that order.


```python
#plot distribution of age as an histogram
sns.histplot(x=train["Age"], bins=20)
```

    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_16_1.png)
    

From the graph above, the histogram of age is right skewed upon inspection.


```python
#plot distribution of annual premium as an histogram
sns.boxplot(y=train["Age"])
```

    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_18_1.png)
    


From the above boxplot, it can be seen that there are no outliers present in the Age distribution. 


```python
#plot distribution of annual premium as an histogram
sns.histplot(x=train["Annual_Premium"], bins=20)
```

    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_20_1.png)
    


The histogram for Annual Premium also shows that it is right skewed similar to age.


```python
#plot distribution of annual premium as an histogram
sns.boxplot(y=train["Annual_Premium"])
```

    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_22_1.png)
    


From the above boxplot on Annual premium, it can be seen that there are a large number of outliers present in the annual premium distribution.


```python
#plotting the distribution of gender based on customer's response
sns.catplot(x="Gender",  col="Response", kind="count", data=train)
```

    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_24_1.png)
    


From the graph above, it can be seen that there are more number of male customers in the dataset and also male customers are more interested in vehicle insurance than female customers.


```python
#plotting the distribution of vehicle age based on customer's response
sns.catplot(x="Vehicle_Age",  col="Response", kind="count", data=train)
```

    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_26_1.png)
    


The above graph describes the distribution of Vehicle Age among the customers in the dataset. It can be seen that there are more number of customers with vehicles with 1-2 years old and less than 1 year old. The customers with vehicles greater than 2 years are significantly less. The same trend follows with the customers who responded saying that they would be interested in vehicle insurance. 


```python
#total number of responses under each category in vehicle age
train['Vehicle_Age'].value_counts()
```


    1-2 Year     120131
    < 1 Year      98879
    > 2 Years      9655
    Name: Vehicle_Age, dtype: int64



From the above values, it can be seen that the customers with vehicles more than 2 years old are significantly less.


```python
#plotting the distribution of vehicle damage based on customer's response
sns.catplot(x="Vehicle_Damage",  col="Response", kind="count", data=train)
```

    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_30_1.png)
    


The above graph describes the number of customers that have had their vehicle damaged.


```python
#plotting the distribution of previosuly owned vehicle insurance based on customer's response
sns.catplot(x="Previously_Insured",  col="Response", kind="count", data=train)
```

    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_32_1.png)
    


The above graph illustrates the number of customers in the dataset who already have a vehilce insurance. It can be seen that there are more customers in total who do not already have a vehicle insurance. Looking at the case of customers who are interested in a vehicle insurance, it is evident that customers who do not already own a vehicle insurance are more interested in getting a vehicle insurance.

## 4. Data Cleaning and Transformation

From the analysis in previous sections, there are no missing values in the dataset. Therefore, the only data preprocessing steps that needs to be taken care of is dropping the id column, data transformation of right skewed columns (Age and Annual_Premium), the removal of outliers and data scaling of the continuous variables in both train and test dataset.


```python
#display all numerical values in an histogram
dummy = train.hist(bins=50, figsize=(16,12))
```
    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_36_0.png)
    

```python
#drop the id-column in the train dataset
train = train.drop(['id'], axis = 1)
```


```python
#drop the id-column in the test dataset
test = test.drop(['id'], axis = 1)
```


```python
#changing categorical variables to numerical
train.loc[train['Gender'] == 'Male', 'Gender'] = 1
train.loc[train['Gender'] == 'Female', 'Gender'] = 0
test.loc[test['Gender'] == 'Male', 'Gender'] = 1
test.loc[test['Gender'] == 'Female', 'Gender'] = 0

train.loc[train['Vehicle_Age'] == '> 2 Years', 'Vehicle_Age'] = 2
train.loc[train['Vehicle_Age'] == '1-2 Year', 'Vehicle_Age'] = 1
train.loc[train['Vehicle_Age'] == '< 1 Year', 'Vehicle_Age'] = 0
test.loc[test['Vehicle_Age'] == '> 2 Years', 'Vehicle_Age'] = 2
test.loc[test['Vehicle_Age'] == '1-2 Year', 'Vehicle_Age'] = 1
test.loc[test['Vehicle_Age'] == '< 1 Year', 'Vehicle_Age'] = 0

train.loc[train['Vehicle_Damage'] == 'Yes', 'Vehicle_Damage'] = 1
train.loc[train['Vehicle_Damage'] == 'No', 'Vehicle_Damage'] = 0
test.loc[test['Vehicle_Damage'] == 'Yes', 'Vehicle_Damage'] = 1
test.loc[test['Vehicle_Damage'] == 'No', 'Vehicle_Damage'] = 0
```


```python
#changing all the dtypes to int in train dataset
for col in train.columns:
    train[col] = train[col].astype(np.int32)
```


```python
#changing all the dtypes to int in test dataset
for col in test.columns:
    test[col] = test[col].astype(np.int32)
```


```python
#log transformation of Age column
train['Age_log'] = np.log(train['Age'])
```


```python
#plot distribution of Age after log transformation in an histogram
sns.histplot(x=train["Age_log"], bins=20)
```

    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_43_1.png)
    



```python
#log transformation of Age column in the test dataset
test['Age_log'] = np.log(test['Age'])
```


```python
#log transformation of Annual_Premium column in train dataset
train['Annual_Premium_log'] = np.log(train['Annual_Premium'])
```


```python
#plot distribution of Annual_Premium after log transformation in an histogram
sns.histplot(x=train["Annual_Premium_log"], bins=20)
```
   
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_46_1.png)
    



```python
#removing outliers from Annual_Premium_log using z_score
from scipy import stats
train['z_score']=stats.zscore(train['Annual_Premium_log'])
```


```python
#checking the distribution of z_score so that values with high z_score can be removed
sns.histplot(x=train['z_score'], bins=20)
```
    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_48_1.png)
    



```python
#removing values with z_score greater than 2
train = train.loc[train['z_score']<=2]
```


```python
#removing values with z_score lesser than -1
train = train.loc[train['z_score']>=-1]
```


```python
#plot distribution of Annual_Premium after removal of outliers in an histogram
sns.histplot(x=train["Annual_Premium_log"], bins=20)
```

    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_51_1.png)
    


```python
#log transformation of Annual_Premium column in the test dataset
test['Annual_Premium_log'] = np.log(test['Annual_Premium'])
```


```python
#drop the age column in test and train dataset
train = train.drop(['Age'], axis = 1)
test = test.drop(['Age'], axis = 1)
```


```python
#drop the Annual_Premium column from the train and test dataset
train = train.drop(['Annual_Premium'], axis = 1)
test = test.drop(['Annual_Premium'], axis = 1)
```


```python
#drop the z_score column from train dataset
train = train.drop(['z_score'], axis = 1)
```


```python
#data scaling needs to be done for both train and test dataset using standard scaler
from sklearn.preprocessing import StandardScaler

scaler = StandardScaler()

train_target = train['Response'].values
train_predictors = train.drop(['Response'], axis=1)

#fit_transform returns a NumPy aray, so need to put it back 
#into a Pandas dataframe
scaled_vals = scaler.fit_transform(train_predictors)
train = pd.DataFrame(scaled_vals, columns=train_predictors.columns)

#put the non-scaled target back in
train['Response'] = train_target
```


```python
#repeat the same steps for the test dataset
test_target = test['Response'].values
test_predictors = test.drop(['Response'], axis=1)

scaled_vals = scaler.fit_transform(test_predictors)
test = pd.DataFrame(scaled_vals, columns=test_predictors.columns)

test['Response'] = test_target
```


```python
#plotting all the distribution of values in each column after data-preprocesing
dummy = train.hist(bins=50, figsize=(16,12))
```

    
![png](assets\img\PythonTutorialInsurancePrediction/datavisualisation-preprocessing_files/datavisualisation-preprocessing_58_0.png)
    


The train and test dataset is now clean and ready to be used for modeling the predictive models.


```python
train.to_csv('train.csv', index=False)
```

```python
test.to_csv('test.csv', index=False)
```

## 5. Modeling

The aim now is to implement and evaluate several alternative predictive models to predict whether an existing customer would be interested or not in Vehicle Insurance with the help of the predictors: Gender, Age, Driving License, Region Code, Previously Insured, Vehicle Age, Vehicle Damage, Annual Premium, Policy Sales Channel and Vintage.


```python

import time                  #for getting local time from the number of seconds elapsed

#load the required train and test datasets
train = pd.read_csv('train.csv')
test = pd.read_csv('test.csv')
```

```python
train.head()
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Gender</th>
      <th>Driving_License</th>
      <th>Region_Code</th>
      <th>Previously_Insured</th>
      <th>Vehicle_Age</th>
      <th>Vehicle_Damage</th>
      <th>Policy_Sales_Channel</th>
      <th>Vintage</th>
      <th>Age_log</th>
      <th>Annual_Premium_log</th>
      <th>Response</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.930654</td>
      <td>0.047053</td>
      <td>-1.197614</td>
      <td>-0.956976</td>
      <td>0.724263</td>
      <td>1.022372</td>
      <td>0.228267</td>
      <td>0.210398</td>
      <td>0.871617</td>
      <td>-0.345003</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-1.074513</td>
      <td>0.047053</td>
      <td>-0.654205</td>
      <td>1.044959</td>
      <td>-1.025121</td>
      <td>-0.978117</td>
      <td>0.747745</td>
      <td>-0.852524</td>
      <td>-0.785843</td>
      <td>-0.331879</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.930654</td>
      <td>0.047053</td>
      <td>-0.654205</td>
      <td>-0.956976</td>
      <td>0.724263</td>
      <td>1.022372</td>
      <td>-1.589904</td>
      <td>-1.461615</td>
      <td>0.489337</td>
      <td>-0.459352</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.930654</td>
      <td>0.047053</td>
      <td>-1.818652</td>
      <td>1.044959</td>
      <td>-1.025121</td>
      <td>-0.978117</td>
      <td>0.896167</td>
      <td>-0.780867</td>
      <td>-0.785843</td>
      <td>-1.036554</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-1.074513</td>
      <td>0.047053</td>
      <td>-1.896282</td>
      <td>1.044959</td>
      <td>-1.025121</td>
      <td>-0.978117</td>
      <td>0.747745</td>
      <td>-1.354128</td>
      <td>-0.598007</td>
      <td>1.223449</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
train.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 189674 entries, 0 to 189673
    Data columns (total 11 columns):
     #   Column                Non-Null Count   Dtype  
    ---  ------                --------------   -----  
     0   Gender                189674 non-null  float64
     1   Driving_License       189674 non-null  float64
     2   Region_Code           189674 non-null  float64
     3   Previously_Insured    189674 non-null  float64
     4   Vehicle_Age           189674 non-null  float64
     5   Vehicle_Damage        189674 non-null  float64
     6   Policy_Sales_Channel  189674 non-null  float64
     7   Vintage               189674 non-null  float64
     8   Age_log               189674 non-null  float64
     9   Annual_Premium_log    189674 non-null  float64
     10  Response              189674 non-null  int64  
    dtypes: float64(10), int64(1)
    memory usage: 15.9 MB
    


```python
train.shape[0]
```

    189674




```python
test.shape[0]
```

    152444

### Sampling

The train and test dataset are too large and would take massive amounts of time to train models on this dataset. Therefore, a sample is taken out of the train and test dataset.


```python
#sampling from train dataset
ftrain = train.sample(n=50000, random_state=7)
```


```python
ftrain.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Gender</th>
      <th>Driving_License</th>
      <th>Region_Code</th>
      <th>Previously_Insured</th>
      <th>Vehicle_Age</th>
      <th>Vehicle_Damage</th>
      <th>Policy_Sales_Channel</th>
      <th>Vintage</th>
      <th>Age_log</th>
      <th>Annual_Premium_log</th>
      <th>Response</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>50183</th>
      <td>0.930654</td>
      <td>0.047053</td>
      <td>-1.430503</td>
      <td>1.044959</td>
      <td>-1.025121</td>
      <td>-0.978117</td>
      <td>0.729192</td>
      <td>1.416637</td>
      <td>-1.096595</td>
      <td>0.815633</td>
      <td>0</td>
    </tr>
    <tr>
      <th>46028</th>
      <td>0.930654</td>
      <td>0.047053</td>
      <td>-1.430503</td>
      <td>1.044959</td>
      <td>-1.025121</td>
      <td>-0.978117</td>
      <td>0.747745</td>
      <td>-0.374806</td>
      <td>-0.988722</td>
      <td>2.021025</td>
      <td>0</td>
    </tr>
    <tr>
      <th>41101</th>
      <td>-1.074513</td>
      <td>0.047053</td>
      <td>0.587873</td>
      <td>-0.956976</td>
      <td>-1.025121</td>
      <td>-0.978117</td>
      <td>0.747745</td>
      <td>0.735888</td>
      <td>-0.509064</td>
      <td>-0.685612</td>
      <td>0</td>
    </tr>
    <tr>
      <th>39742</th>
      <td>-1.074513</td>
      <td>0.047053</td>
      <td>0.122094</td>
      <td>-0.956976</td>
      <td>0.724263</td>
      <td>1.022372</td>
      <td>-1.589904</td>
      <td>0.819489</td>
      <td>0.547607</td>
      <td>2.429836</td>
      <td>1</td>
    </tr>
    <tr>
      <th>187634</th>
      <td>0.930654</td>
      <td>0.047053</td>
      <td>-0.343685</td>
      <td>1.044959</td>
      <td>-1.025121</td>
      <td>-0.978117</td>
      <td>0.896167</td>
      <td>0.640345</td>
      <td>-1.327174</td>
      <td>0.145065</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>


```python
#sampling from test dataset
ftest = test.sample(n=35000, random_state=7)
```


```python
ftest.head()
```

<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Gender</th>
      <th>Driving_License</th>
      <th>Region_Code</th>
      <th>Previously_Insured</th>
      <th>Vehicle_Age</th>
      <th>Vehicle_Damage</th>
      <th>Policy_Sales_Channel</th>
      <th>Vintage</th>
      <th>Age_log</th>
      <th>Annual_Premium_log</th>
      <th>Response</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>140791</th>
      <td>0.922772</td>
      <td>0.045936</td>
      <td>0.122655</td>
      <td>-0.919475</td>
      <td>-1.074838</td>
      <td>0.991209</td>
      <td>0.220503</td>
      <td>-0.864108</td>
      <td>-1.257842</td>
      <td>-0.084741</td>
      <td>1</td>
    </tr>
    <tr>
      <th>21139</th>
      <td>-1.083691</td>
      <td>0.045936</td>
      <td>-1.161235</td>
      <td>-0.919475</td>
      <td>0.689124</td>
      <td>0.991209</td>
      <td>-1.587009</td>
      <td>1.170042</td>
      <td>0.518490</td>
      <td>1.032980</td>
      <td>1</td>
    </tr>
    <tr>
      <th>12854</th>
      <td>-1.083691</td>
      <td>0.045936</td>
      <td>-0.028391</td>
      <td>1.087577</td>
      <td>-1.074838</td>
      <td>-1.008869</td>
      <td>0.884487</td>
      <td>-0.253863</td>
      <td>-0.829732</td>
      <td>-2.127575</td>
      <td>0</td>
    </tr>
    <tr>
      <th>13704</th>
      <td>0.922772</td>
      <td>0.045936</td>
      <td>1.482068</td>
      <td>1.087577</td>
      <td>-1.074838</td>
      <td>-1.008869</td>
      <td>0.884487</td>
      <td>-0.026517</td>
      <td>-1.377059</td>
      <td>-0.049361</td>
      <td>0</td>
    </tr>
    <tr>
      <th>21554</th>
      <td>0.922772</td>
      <td>0.045936</td>
      <td>-0.406006</td>
      <td>-0.919475</td>
      <td>0.689124</td>
      <td>0.991209</td>
      <td>-1.587009</td>
      <td>-1.306835</td>
      <td>0.896837</td>
      <td>0.597470</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



### Train Models

The first step is to create seperate arrays for the predictors (`Xtrain`) and for the target (`ytrain`):


```python
from sklearn.model_selection import GridSearchCV

#seperating the predictors and target variable
Xtrain = ftrain.drop('Response', axis=1)

ytrain = ftrain['Response'].copy()
```

#### Baseline Model

A majority class classifier is used as baseline where most common class label in the training set would be found out and predicted as the output always.


```python
#count the number of instances
ftrain["Response"].value_counts()
```


    0    43867
    1     6133
    Name: Response, dtype: int64


0: Not interested, 1: Interested


```python
#train set size
ftrain.shape[0]
```

    50000


According to the baseline classifier, the output will be "Not interested" for all predictions. In this project, macro-averaging will be used (precision, recall and F-score are evaluated in each class seperately and then avergaed across classes).

Therefore, applying the baseline classifier to all of the train dataset.

For responses with "Not interested", the accuarcy measures will be:

 - Precision: 43867/50000 = 0.877
 - Recall: 50000/50000 = 1.0
 - F-score: 2/(1/precision+1/recall) = 0.935
 
For responses with "Interested", the accuarcy measures will be:

 - Precision: 0.0/0.0 = 0.0
 - Recall: 0.0/6133 = 0.0
 - F-score: 0.0
 
The averages of the two classes which is the eventual baseline scores, are:

 - Precision: 0.439
 - Recall: 0.5
 - F-score: 0.468

#### Random Forest


```python
from sklearn.ensemble import RandomForestClassifier

rf = RandomForestClassifier()

#put in the hyperparameters
param_grid = {
    'n_estimators': [10, 100, 200, 1000],
    'max_depth': [3, 5, 15],
    'min_samples_split': [5, 10],
    'random_state': [7]
}

#5-fold cross-validation is used
grid_search = GridSearchCV(rf, param_grid, cv=5,
                          scoring='f1_macro',
                          return_train_score=True)

start = time.time()
grid_search.fit(Xtrain, ytrain)
end = time.time() - start
print(f"Took {end} seconds")
```

    Took 899.0751824378967 seconds
    


```python
grid_search.best_estimator_
```

    RandomForestClassifier(max_depth=15, min_samples_split=5, n_estimators=10,
                           random_state=7)




```python
grid_search.best_score_
```

    0.5077910772485159



The best hyperparameters prove to be n_estimators = 200, max_depth = 15 and min_sample_split=5. Based on this, they achieve a F-score of 0.51 which is the best one so far.

The results of the best model are recorded in each split and the below command gives the index of the best performing model,


```python
grid_search.cv_results_['rank_test_score'].tolist().index(1)
```


    16



```python
rf_split_test_scores = []
for x in range(5):
    #extract f-score of the best model (index=18) from each of the 5 splits
    val = grid_search.cv_results_[f"split{x}_test_score"][18]
    rf_split_test_scores.append(val)
```

The scores achieved by all the models for different hyperparameter are reviewed:


```python
val_scores = grid_search.cv_results_['mean_test_score']
train_scores = grid_search.cv_results_['mean_train_score']
params = [str(x) for x in grid_search.cv_results_["params"]]

for val_score, train_score, param in sorted(zip(val_scores, train_scores, params), reverse=True):
    print(val_score, train_score, param)
```

    0.5077910772485159 0.6144937097675991 {'max_depth': 15, 'min_samples_split': 5, 'n_estimators': 10, 'random_state': 7}
    0.500160391905794 0.5800966185190405 {'max_depth': 15, 'min_samples_split': 10, 'n_estimators': 10, 'random_state': 7}
    0.4901043794684668 0.5983222542940497 {'max_depth': 15, 'min_samples_split': 5, 'n_estimators': 100, 'random_state': 7}
    0.489769462731983 0.5974611399846733 {'max_depth': 15, 'min_samples_split': 5, 'n_estimators': 200, 'random_state': 7}
    0.488091595400079 0.5962736744561885 {'max_depth': 15, 'min_samples_split': 5, 'n_estimators': 1000, 'random_state': 7}
    0.48802775984303126 0.565265257292125 {'max_depth': 15, 'min_samples_split': 10, 'n_estimators': 200, 'random_state': 7}
    0.4879105683806045 0.5624624248068428 {'max_depth': 15, 'min_samples_split': 10, 'n_estimators': 1000, 'random_state': 7}
    0.487125083987973 0.5651251866402451 {'max_depth': 15, 'min_samples_split': 10, 'n_estimators': 100, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 5, 'min_samples_split': 5, 'n_estimators': 200, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 5, 'min_samples_split': 5, 'n_estimators': 1000, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 5, 'min_samples_split': 5, 'n_estimators': 100, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 5, 'min_samples_split': 5, 'n_estimators': 10, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 5, 'min_samples_split': 10, 'n_estimators': 200, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 5, 'min_samples_split': 10, 'n_estimators': 1000, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 5, 'min_samples_split': 10, 'n_estimators': 100, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 5, 'min_samples_split': 10, 'n_estimators': 10, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 3, 'min_samples_split': 5, 'n_estimators': 200, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 3, 'min_samples_split': 5, 'n_estimators': 1000, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 3, 'min_samples_split': 5, 'n_estimators': 100, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 3, 'min_samples_split': 5, 'n_estimators': 10, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 3, 'min_samples_split': 10, 'n_estimators': 200, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 3, 'min_samples_split': 10, 'n_estimators': 1000, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 3, 'min_samples_split': 10, 'n_estimators': 100, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'max_depth': 3, 'min_samples_split': 10, 'n_estimators': 10, 'random_state': 7}
    

The performance of Random Forest varies between 0.47 and 0.51. It can also be noticed that better score is achieved for greater max_depth. However, this score is only slight better than the baseline model. Therefore, more models need to evaluted for better understanding.


```python
# put them into a separate variable for convenience
feature_importances = grid_search.best_estimator_.feature_importances_

# the order of the features in `feature_importances` is the same as in the Xtrain dataframe,
# so we can "zip" the two and print in the descending order:

for k, v in sorted(zip(feature_importances, Xtrain.columns), reverse=True):
    print(f"{v}: {k}")
```

    Vehicle_Damage: 0.23034578200483757
    Age_log: 0.15910325280867196
    Annual_Premium_log: 0.1399068056819435
    Previously_Insured: 0.13163255776225508
    Vintage: 0.11583783732321544
    Policy_Sales_Channel: 0.08475334501851194
    Region_Code: 0.06984894911839537
    Vehicle_Age: 0.05473726453721193
    Gender: 0.012907136629646949
    Driving_License: 0.0009270691153103203
    

Vehicle damage, age, annual premium, previously insured and vintage are quite predictive of whether a customer would be interested in vehicle insurance or not.

Every other variable has very little to do with the response of the customer.

Following on, the model is saved to the disk so that it can be used in the future directly for testing instead of re-training the model.


```python
import os
from joblib import dump

#creating a folder to save all the models
if not os.path.exists('ML models'):
    os.makedirs('ML models')

dump(grid_search.best_estimator_, 'ML models/rf-clf.joblib')
```


    ['ML models/rf-clf.joblib']



The model will be loaded later on using joblib's load function.

#### Support Vector Machines

**Linear SVMs**


```python
from sklearn.svm import LinearSVC

lsvm = LinearSVC()

# specify the hypermaters
param_grid = {
    'C': [0.1, 1, 3, 5],
    'max_iter': [5000],
    'random_state': [7]
}

#5-fold cross-validation is used
grid_search = GridSearchCV(lsvm, param_grid, cv=5,
                           scoring='f1_macro', 
                           return_train_score=True) 

start = time.time()
grid_search.fit(Xtrain, ytrain)
end = time.time() - start
print(f"Took {end} seconds")
```

    Took 498.01508927345276 seconds
    


```python
grid_search.best_estimator_
```


    LinearSVC(C=0.1, max_iter=5000, random_state=7)



```python
grid_search.best_score_
```


    0.4673314366705239



There is no significant difference between the f-score of the Linear SVM and the baseline model. Therefore, this model turns out to be very poor.


```python
val_scores = grid_search.cv_results_["mean_test_score"]
train_scores = grid_search.cv_results_["mean_train_score"]
params = [str(x) for x in grid_search.cv_results_["params"]]

for val_score, train_score, param in sorted(zip(val_scores, train_scores, params), reverse=True):
    print(val_score, train_score, param)
```

    0.4673314366705239 0.46733143701057855 {'C': 3, 'max_iter': 5000, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'C': 1, 'max_iter': 5000, 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'C': 0.1, 'max_iter': 5000, 'random_state': 7}
    0.46732576201501475 0.4673662960221964 {'C': 5, 'max_iter': 5000, 'random_state': 7}
    

From the above results, it can be seen that there is no difference in the F-score as the C-value changes. 

However, this model is now saved for future refernece.


```python
import os
from joblib import dump

# create a folder where all trained models will be kept
if not os.path.exists("ML models"):
    os.makedirs("ML models")
    
dump(grid_search.best_estimator_, 'ML models/svm-lnr-clf.joblib')
```

    ['ML models/svm-lnr-clf.joblib']


**Radial Basis Function**


```python
from sklearn.svm import SVC

svm = SVC()

#put in the parameters
param_grid = {
    'C': [1, 10, 100],
    'gamma': ["scale", "auto"],
    'kernel': ["rbf"],
    'random_state': [7]
}

#5-fold cross-validation is used
grid_search = GridSearchCV(svm, param_grid, cv=5,
                           scoring='f1_macro', 
                           return_train_score=True) 

start = time.time()
grid_search.fit(Xtrain, ytrain)
end = time.time() - start
print(f"Took {end} seconds")
```

    Took 5844.312863826752 seconds
    

This model took significant amount of time to train and are impartical for large datasets.


```python
grid_search.best_estimator_
```

    SVC(C=100, gamma='auto', random_state=7)


```python
grid_search.best_score_
```


    0.4752675855876845



The F-score of this model is approximately 0.475 which is 0.01 more than that of the baseline model. Therefore, this model turns out to be no better than the baseline model as well.


```python
# obtain the f-scores of the best models in each split

svmrbf_split_test_scores = []
for x in range(5):
    # extract f-score of the best model (at index=0) from each of the 5 splits
    val = grid_search.cv_results_[f"split{x}_test_score"][0]
    svmrbf_split_test_scores.append(val)
```


```python
val_scores = grid_search.cv_results_["mean_test_score"]
train_scores = grid_search.cv_results_["mean_train_score"]
params = [str(x) for x in grid_search.cv_results_["params"]]

for val_score, train_score, param in sorted(zip(val_scores, train_scores, params), reverse=True):
    print(val_score, train_score, param)
```

    0.4752675855876845 0.49304582093749144 {'C': 100, 'gamma': 'auto', 'kernel': 'rbf', 'random_state': 7}
    0.4749530799050228 0.49179605580100966 {'C': 100, 'gamma': 'scale', 'kernel': 'rbf', 'random_state': 7}
    0.4682958744065912 0.47032797495572937 {'C': 10, 'gamma': 'auto', 'kernel': 'rbf', 'random_state': 7}
    0.4682958744065912 0.470081072861702 {'C': 10, 'gamma': 'scale', 'kernel': 'rbf', 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'C': 1, 'gamma': 'scale', 'kernel': 'rbf', 'random_state': 7}
    0.4673314366705239 0.46733143701057855 {'C': 1, 'gamma': 'auto', 'kernel': 'rbf', 'random_state': 7}
    

From the above results, it can be seen that the F-scores of the model increase with increase in the C - value. This is similar to that of random forests where high values of dept produced better results.

Polynomial SVM was ignored as it took a significant amount of time to train. 

The SVM rbf model is saved.


```python
import os
from joblib import dump

# create a folder where all trained models will be kept
if not os.path.exists("ML models"):
    os.makedirs("ML models")
    
dump(grid_search.best_estimator_, 'ML models/svm-rbf-clf.joblib')
```


    ['ML models/svm-rbf-clf.joblib']



From the two SVM models above, the F-scores are less than what was observed for Random forest and are not significantly different from the baseline models. Compared to the SVM models, the random forest was slightly better with an F-score of 0.51. However, this is an extremly poor score as well in reality. A model with such poor score has significantly low prediction power.


### Test the Models

Even though, models that were trained have a poor f-score, the random forest with the relatively high f-score will be evaluated on the test dataset.

The model is loaded from the local disk:


```python
from joblib import load

best_rf = load("ML models/rf-clf.joblib")
```


```python
# drop labels for training set, but keep all others
Xtest = ftest.drop("Response", axis=1)
ytest = ftest["Response"].copy()
```


```python
from sklearn.metrics import precision_recall_fscore_support

# rf
yhat = best_rf.predict(Xtest)

# micro-averaged precision, recall and f-score
p, r, f, s = precision_recall_fscore_support(ytest, yhat, average="macro")
print("Random Forest:")
print(f"Precision: {p}")
print(f"Recall: {r}")
print(f"F score: {f}")
```

    Random Forest:
    Precision: 0.6461335174851621
    Recall: 0.5258023688231142
    F score: 0.5218418455618304
    

Thus, similar classification accuracy can be found with Random forrest classifier, as observed during cross-validation.

## 6. Future Improvements and Business Scenario

There is big room for future improvments for the model as the models accuracy is very poor. Different steps need to be taken to overcome this problem. One of the reason for this poor score could be that fact that there was significantly low number of customers interested in vehicle insurance compared to customers who weren't. This could have potentially created a bias in the models learning. Another problem is that the predictors may not be a good representative of the target variable. To support this, the correlation matrix in the group report showed very poor correlation between the target variable and the predictors. Addressing these problesm could be potential future improvements. 

Currently, this model cannot be used in real world scenarios due to its low accuarcy but similar models with high accuracy can be used in various business scenarios. For example, banks can use this type of model to predict who would be interested in a certain type of credit or debit cards.