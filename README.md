# Capstone_Project_Engagement_Prediction

## 1. Problem Statement
*Socialinsider* is a social media analytics company that provides data and services for users, like influencers and marketers, to compare performance across channels, get competitor analysis, benchmarks and listening insights. *Socialinsider* is trying to improve their product to gain and retain more customers. The goal of this project is to predict the number of engagement for each profile in order to give users more social media insights. 

## 2. Methods and Data
### 2.1 Benchmark Studies
There's no benchmark studies with similar goal and the data we are using. 
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
| profile 2  | 2024-01-08 | undefined       | 1    | 3          | 1     | 2          |

In order to make the dataset less sparse and easier to model, we decided to aggregate the daily data into weekly data where each matrix represent the sum of that value for the entire week. This has significantly decrease the number of null values in the dataset as 7 null values in a week will be aggregated into 1 null value. The resulted aggregated processed table is shown: 

| profile id | date       | followers | post | engagement per post | reach | impression | monthly average engagement per post |
|------------|------------|-----------|------|----------------------|-------|------------|-------------------------------------|
| profile 1  | 2024-01-07 | 300       | 3    | 300                  | 20    | 10         | 500                                 |
| profile 2  | 2024-01-14 | 300       | 2    | 150                  | 50    | 12         | 500                                 |

`Feature Engineering: `

New features such as monthly average engagement per post are added to capture more general information on past values. In addition, lag features, which are features that store past values of existing variables in order to predict future values, are added to emphasize more recent past values (up to past 4 weeks). These lag features are created for followers, reach and impression. Lag 1 features of a variable is just the value of the variable from last week. From the example below, we can see that lag 1 feature of followers in week 2 is the value of followers in week 1. This applies to reach and impression as well. These lag feature value will be null if past week's value is missing. In this case, if all variables of a profile have missing values for the same week (in one row of data), the entire row will be dropped to avoid unuseful information. 

| profile id | date       | followers | followers_lag1 | reach | reach_lag1 | impression | impression_lag1 |
|------------|------------|-----------|------|----------------------|-------|------------|-------------------------------------|
| profile 1  | 2024-01-07 | 300       | -    | 300                  | -    | 10         | -                                 |
| profile 1  | 2024-01-14 | 500       | 300    | 150                  | 300    | 12         | 10                                 |

Target variables are also adapted from `engagement per post` and named as `engagement future 7 days`, `engagement future 14 days`, `engagement future 21 days`, `engagement future 28 days`. These 4 target variables record the future value of `engageent per post` in the according future windows and will be used separately for each training models. 

### 2.5 Modeling

`Removing unusual rows`: If all variables values are missing in the same row, the row will be dropped as it is not providing useful information for training. 

`Chronological Train Test Split`: Dataset is splitted into training and testing set in a chronological order. That is, dates before the cutoff date (2024-07-30) will be in the training set and dates after that will be in the testing set. 

`Linear Regression Model`: Four different linear regression models are trained for each targeted future windows of predicting engagement per post. 

## 3. Results

Since an industry benchmark was not found, the model results are compared to a baseline model we made. The baseline model is a linear regression model which predicts for engagement per post in the next 7 days without any feature engineering we did. The evaluation matric results are demonstrated in the table below: 

| Model         | RMSE       | R²     |
|---------------|------------|--------|
| baseline 7d   | 148560.38  | 0.41   |
| future 7d     | -55%       | 115%   |
| future 14d    | -51%       | 110%   |
| future 21d    | -50%       | 109%   |
| future 28d    | -45%       | 104%   |

`Improvement in RMSE:` From the figure below, we can see that RMSE dropped for approximately 50% for all 4 trained models. This is a significant improvement in the model performance as a lower RMSE indicates better performance. 

<img width="595" alt="Screen Shot 2025-04-14 at 5 39 25 PM" src="https://github.com/user-attachments/assets/d27c0c3a-21c5-4205-8226-5fa044f2b214" />

`Improvement in R²: ` From the figure below, we can see that R² improved for more than 100% for all 4 trained models. They all reached a value above 0.83 which is considered as a very good fit of the data, especially when compared to the original baseline R² which is 0.41. 

<img width="595" alt="Screen Shot 2025-04-14 at 5 40 28 PM" src="https://github.com/user-attachments/assets/e2109bd3-0384-436c-8163-b8ce1c87a488" />


## 4. Technical Documents
### 4.1 Files Included and How to Replicate
The repo contains the `model_training_pipeline` and `model_prediction_pipeline`. 
- **model training pipeline**

It includes the Jupyter Notebook `engagement_model(1).ipynb` that contains the process to train the model. The output of this file will be 4 exported models and an exported scaler.

To get the training model, get `ig_data-2024` and `ig_data-2023` (not included in this repo due to data size). Put it in the same folder as `engagement_model(1).ipynb`. Run the Jupyter Notebook `engagement_model(1).ipynb`. 

- **model prediction pipeline** It contains: 
    - Example data for 4 months in `ig_data-2024`
    - Models and scalers that will be used in the prediction pipeline in `models` folder
    - The Jupyter Notebook that contains the prediction pipeline in `engagement_model_prediction_pipeline.ipynb`

To get the prediction of engagement for certain account, run the python notebook `engagement_model_prediction_pipeline.ipnyb`. This notebook reads the example data from the folder `ig_data-2024` and load the pretrained models from `models` folder.

For more information about prediction process, go to [here](https://github.com/XueqingWu/Capstone_Project_Engagement_Prediction/tree/main/model_prediction_pipeline)

## 5. Deliverables
The prediction pipeline will be integrated to *Socialinsider*'s website as an API. We will deliver the prediction pipeline (`engagement_model_prediction_pipeline.ipynb`) to our client. 
