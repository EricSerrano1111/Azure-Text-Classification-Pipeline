# Azure-Text-Classification-Pipeline

## Description
The objective of this project is to use a BBC news dataset to create a text classification model that is able to assign the news article to one of five news categories - business, entertainment, politics, sport, and tech. With the use of Azure Machine Learning, a cloud-based pipeline was both designed and executed solely through the use of a user interface (UI). Specifically, Natural Language Processing, TF-IDF (Term Frequency Inverse Document Frequency for feature engineering, and Multiclass Logistic Regression was leveraged to achieve an overall accuracy of 96.7% on unseen data. This demonstrates an end-to-end data science lifecycle from raw data to comprehensive model evaluation. 

## Getting Started

### Dependencies:
* Active Azure Subscription
  * Required to provision ML resources
* Azure Machine Learning Workspace
  * Centralized cloud environment to orchestrate pipeline
*	Current Web Browser
  * Required to access Azure ML Studio UI

### Executing Program
Architecture does not require local software installation such as Anaconda or Python as it utilized Azure’s managed services. In order to replicate this pipeline, the cloud environment must be configured as follows:
1. Provision Workspace: Create a new Machine Learning Workspace with the Azure portal.
2. Register the Data: Upload the BBC News dataset to the workspace and register it as a tabular data asset in order to be dragged onto the Designer canvas. 
3. Establish Computer (Cost Optimized): Navigate to the Compute section and create a new Computer Cluster
  * Select a general-purpose CPU virtual machine (ex. Standard_D2ads_v5)
  * Important: Minimum number of nodes should be set as 0 and maximum as 1 with 120 second idle scale down. This ensures that billing is only active while the model is running. 

### Program Setup Reference
This architecture follows a linear progression from raw text ingestion to mathematical vectorization, training, and ultimately final evaluation. The below is the configuration for each module:
1. Data Ingestion
  * Module: Dataset 
  * Asset: BBC-News-Dataset (Imported via workspace blob store)
  * Text Cleaning
  * Module: Preprocess Text / Text column to clean: New
  * Parameters: Standard natural language scrubbing applied (Remove stop words, Normalize case to lowercase, Remove numbers, and Remove special characters all set to - True 
2. Feature Engineering
  * Module: Extract N-Gram Features from Text
  * Text column: News
  * Vocabulary mode: Create
  * Weight function: TF-IDF (Term Frequency-Inverse Document Frequency)
  * N-grams size: 1
  * Minimum/Maximum word length: 3 /25
3. Data Sterilization (Feature Selection)
  * Module Select Columns in Dataset
  * Purpose: Drop raw string columns to prevent failure during algorithm training
  * Rules - Include: All columns / Exclude Column Names: News, No
4. Training & Testing
  * Module: Split Data
  * Splitting mode: Split Rows
  * Fraction of rows (Training set): 0.7 (70/30 data split)
  * Randomized split: True (Random Seed: 42)
  * Stratified split: True (Ensures equal class distribution)
  * Stratification key column: Category
5. Algorithm Initialization
  * Module: Multiclass Logistic Regression
  * Create trainer mode: SingleParameter
  * Optimization tolerance: 1e-07
  * L2 regularization weight: 1.0
  * Random number seed: 42
6. Model Training
  * Module: Train Model
  * Label column: Category (Target)
  * Prediction & Evaluation
7. Modules: Score Model & Evaluate Model
  * Configuration: Default parameters to run 0.3 unseen test split againsts trained algorithm and generate accuracy metrics. 

![Architecture](/images/png1.png)

### Expected Results
The model performed exceptionally as overall accuracy shows 96.7% meaning nearly 97 out of every 100 articles were correctly categorized. The Macro Precision 0.968 and Micro Precision 0.967 indicate a balanced model. 

![Metrics](/images/png2.png)

The confusion matrix reveals that the model performed best in the Sports category at 99.3% likely due to domain specific vocabulary. Performance was lowest within the Politics category at 90.4% which could be due to similar terminologies when compared against Business and Tech. 

![Matrix](/images/png3.png)

## Help / Issue Log
* Issue: Training Module Failure (ColumnUniqueValuesExceededError)
  * Symptom: Pipe successfully extracted N-Gram features but crashed at the Train Model module throwing an error as ‘Number of unique values in column: “News” is greater than allowed. 
  * Root Cause: Train Model received raw string type text column alongside the engineered numerical features. 
  * Resolution: Introduced the Select Columns in Dataset module prior to the Split Data step to filter out the raw News and No columns. 

## TODO
The baseline model achieved a strong 96.7% accuracy. However, a production grade deployment would benefit from the following iterations:
  * Algorithm A/B Testing: A parallel track on Designer canvas to train an alternative model such as a Multiclass Neural Network or Multiclass Decision Forest would allow for a comparison against the baseline. 
  * Hyperparameter Tuning: Experiment by implementing the module Tune Model Hyperparameters to help iterate through different weights and ranges. 
  * Real-Time API Deployment: Transition completed training pipeline into a Real-Time Inference Pipeline. This would deploy the model as an Azure REST endpoint allowing for external web applications to send raw news text       via HTTP requests and receive live category predications.

## Authors
* Lead Developer – **Eric Serrano**

## Version History
* 0.1 – Initial Release


## License
The MIT License (MIT) 
Copyright (c) 2026 Eric Serrano 
Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

