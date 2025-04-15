# Capstone_Project_Engagement_Prediction

## 1. Problem Statement
*Socialinsider* is a social media analytics company that provides data and services for users, like influencers and marketers, to compare performance across channels, get competitor analysis, benchmarks and listening insights. *Socialinsider* is trying to improve their product to gain and retain more customers. The goal of this project is to predict the future number of followers on a daily basis for a single profile. 

## 2. Methods and Data
### 2.1 Benchmark Studies
There's no benchmark studies with similar goal and the data we are using. After discussed with our mentor, we decided to use linear regression as the baseline model.
### 2.2 Data
The data we are using to train and test the model is the daily count data of Instagram profiles, spanning from 2023 Jan 1st to 2024 Dec 31st. It contains more than 33 million rows in total. 
### 2.3 EDA

Upon observing the dataset, we have encountered the greatest challenge--discontinuity with lots of missing values—due to the nature of the data collection process. In order to understand these missing values more systematically, we explored the dataset more with stratified layers of profile record counts. The distribution result is demonstrated by the histogram below: 

<img width="718" alt="Screen Shot 2025-04-14 at 5 07 01 PM" src="https://github.com/user-attachments/assets/de00e2a2-d48c-4341-b612-6a01b0bac2ac" />

It can be noticed that most of the profiles have total records of less than 50 days among 366 days (one year data). Only a few profiles have total number of records positioned in the two upper layers, 300-365 days and 366 days. This tilted distribution of the dataset has create obstacles in predicting outcomes with time series models, which prefers continued data. Hence, data preprocessing and resampling has been taken to decrease the sparsity in the data. 

### 2.4 Data Preprocessing
`Resampling: `

An example source data is displayed in this table which has records on a daily basis:

| profile id | date       | followers | post | engagement | reach | impression |
|------------|------------|-----------|------|------------|-------|------------|
| profile 1  | 2024-01-07 | 300       | 0    | 0.0        | 0     | 0          |
| profile 2  | 2024-01-08 | undefined | 1    | 3          | 1     | 2          |

In order to make the dataset less sparse and easier to model, we first replace all "undefinded" as null value, and filled all nul value with the nearest vaid data in front of the null value. Then, since the dataset is too large, we aggregate the daily data into monthly and weekly data (simply use the last datapoint in that month or week) to testify which model we should use. The result shows that AutoTS is the best model. After that, we performed a daily pipeline to make prediction on sigle profile.
 

### 2.5 Modeling

`Removing unusual rows`: Replace all "undefinded" as null value, and filled all nul value with the nearest vaid data in front of the null value. 

`Train Test Split`: AutoTS Requires a 70-80% train-test split, which is reasonable , and we set it as 75%.

`Linear Regression Model`: Baseline model. 

`AutoTS Model`: Final model. 

## 3. Results

Since an industry benchmark was not found, the model results are compared to a baseline model we made. The baseline model is a linear regression and our evaluation matrixs include MAE and MSE. We use the best layer of data we have, which is the dataset with profiles that have more than 365 days record to test on linear regression and AutoTS model, and the results show that AutoTS is about two to three times better than basekine model. After we create a daily pipeline, since it will be using more datapoints and be feasible for prediction on single profile, we got the best result:

|               | Monthly LR       | Monthly AutoTS     | Weekly AutoTS     | Daily AutoTS     |
|---------------|------------------|--------------------|-------------------|------------------|
| Mean of MAE   | 1                | 41.11%             |45.73%             |1.29%             |
| Mean of MSE   | 1                | 29.62%             |35.16%             |0.0008%           |

`Improvement in RMSE:` From the figure below, we can see that RMSE dropped for approximately 50% for all 4 trained models. This is a significant improvement in the model performance as a lower RMSE indicates better performance. 

<img width="595" alt="Screen Shot 2025-04-14 at 5 39 25 PM" src="https://github.com/user-attachments/assets/d27c0c3a-21c5-4205-8226-5fa044f2b214" />

`Improvement in R²: ` From the figure below, we can see that R² improved for more than 100% for all 4 trained models. They all reached a value above 0.83 which is considered as a very good fit of the data, especially when compared to the original baseline R² which is 0.41. 

<img width="595" alt="Screen Shot 2025-04-14 at 5 40 28 PM" src="https://github.com/user-attachments/assets/e2109bd3-0384-436c-8163-b8ce1c87a488" />


## 4. Technical Documents
### 4.1 Files Included and How to Replicate
The repo doesn't contains the data we use for whole procedure since the whole dataset is too large and can't be pushed here. The file `between_365_300_data.csv` is the example data we prepared to use the deliverable, which created through our `Data Preprocessing and Modeling/data preprocessing.ipynb` and can be found through the google drive link below. We also uploaded monthly and weekly pipeline under folder `Data Preprocessing and Modeling/`. The file `Deliverable/daily_pipeline final.ipynb` is the final deliverable we will deliver to our client.

To use notebook under `Data Preprocessing and Modeling` folder, you will need whole dataset and please let us know in order to provide whole dataset. To run the Deliverable notebook, go to [here](https://drive.google.com/file/d/10GnhiTeR5adIZSYNqj65DtiQ8lg5L2lh/view?usp=sharing) to download the dataset in to this folder and simply install the required packages then click run.
## 5. Deliverables
The prediction pipeline will be integrated to *Socialinsider*'s website as an API. We will deliver the prediction pipeline (`daily_pipeline final.ipynb`) to our client. 
