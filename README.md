## LendingClub Loan Default Classifier

This is a model library for running and deploying models based on the LendingClub Loan Default data, which can be found here: https://www.lendingclub.com/info/download-data.action. Models here were trained on a subset of this data.

### Interacting with the Model

The model can be interacted with in two ways. First, it has been deployed as a service to AWS. You can hit the endpoint by running a `curl` command with test data, or feel free to use your own data.

Sample `curl` command:
```
curl -XPOST -H "Content-Type: application/json" http://datarobot.bigstuffedanimal.com/predict -d '{"Id":1,"emp_title":"Time Warner Cable","emp_length":"10","home_ownership":"MORTGAGE","annual_inc":50000.0,"verification_status":"not verified","pymnt_plan":"n","Notes":null,"purpose_cat":"medical","purpose":"Medical","zip_code":"766xx","addr_state":"TX","debt_to_income":10.87,"delinq_2yrs":0.0,"earliest_cr_line":"12-01-1992","inq_last_6mths":0.0,"mths_since_last_delinq":null,"mths_since_last_record":null,"open_acc":15.0,"pub_rec":0.0,"revol_bal":12087,"revol_util":12.1,"total_acc":44.0,"initial_list_status":"f","collections_12_mths_ex_med":0.0,"mths_since_last_major_derog":1,"policy_code":"PC4"}'
```
Secondly, the model can be built locally. Setup a dev environment of your choosing. This was built using `Python 3.7.1`. Run `pip install -r requirements.txt`, which will install all necessary libraries. Next, run `python setup.py install` to install the package to your virtual environment.

### First Order Tasks
1. Partition your data into a [holdout set and 5 stratified CV folds](https://github.com/jakeweholt/DR_TH/blob/a2e338bff058b2f1ece09e153b83def85d4bb6f1/model/model.py#L23-L70).
2. Pick any two machine learning algorithms from the list below, and build a binary classification model with each of them:
    - [Regularized Logistic Regression](https://github.com/jakeweholt/DR_TH/blob/c27aba7d213097f43029e91f69c9bc5d7bc0aa81/train_model.py#L42-L50)
    - [Gradient Boosting Machine](https://github.com/jakeweholt/DR_TH/blob/c27aba7d213097f43029e91f69c9bc5d7bc0aa81/train_model.py#L28-L40)
3. Both of your models must make use of numeric, categorical, text, and date features.
    - [Commentary on which columns are used](https://github.com/jakeweholt/DR_TH/blob/master/analysis/01_data_cleaning.ipynb).
4. Compute out-of-sample LogLoss and F1 scores on cross-validation and holdout.
    - [Current production model notebook](https://github.com/jakeweholt/DR_TH/blob/master/analysis/logistic_regression_model_validation_1.ipynb). 
5. Which one of your two models would you recommend to deploy? Explain your decision.
    - [Problem intro, business motivation and model selection doc](https://docs.google.com/document/d/1V5CiQwuySPbKlDvfX8TpLxi0pXHN26-ehbBut4Noblc/edit?usp=sharing) (may require granted access).
6. (Advanced, optional) Which 3 features are the most impactful for your model? Explain
your methodology.

### Companion Docs/Notebooks
- [Problem Intro, business motivation and model selection doc](https://docs.google.com/document/d/1V5CiQwuySPbKlDvfX8TpLxi0pXHN26-ehbBut4Noblc/edit?usp=sharing) (may require granted access).
- [Codebase commentary](https://docs.google.com/document/d/1LpQ2jej05sPmCyDdtWpO6lI0z7dLOUxuDXVyR5YXJKc/edit?usp=sharing)
- [Raw data EDA notebook](https://github.com/jakeweholt/DR_TH/blob/master/analysis/00_raw_data_EDA.ipynb)
- [Data cleaning notebook](https://github.com/jakeweholt/DR_TH/blob/master/analysis/01_data_cleaning.ipynb)

### Current Version in Production
- Name: logistic_regression_model_1, 
- Version: 0.1.1556570371
  - `SGDClassifier(loss='log', penalty="l2", n_iter=1000)`
- [Validation notebook](https://github.com/jakeweholt/DR_TH/blob/master/logistic_regression_model_validation_1.ipynb)

### Other Model Versions

**Logistic Regression**

- Name: logistic_regression_model_1, 
- Version: 0.1.1556570371
  - `SGDClassifier(loss='log', penalty="l2", n_iter=1000)`
- [Validation notebook](https://github.com/jakeweholt/DR_TH/blob/master/analysis/logistic_regression_model_validation_1.ipynb)
- Name: logistic_regression_model_2
- Version: 0.1.1556570505
  - `SGDClassifier(loss='log', n_iter=1000)`
- [Validation notebook](https://github.com/jakeweholt/DR_TH/blob/master/analysis/logistic_regression_model_validation_2.ipynb)

**XGBoost**

- Name: xgboost_model_1
- Version: 0.1.1556570382
  - `XGBClassifier(n_estimators=100, scale_pos_weight=6.77)`
- [Validation notebook](  https://github.com/jakeweholt/DR_TH/blob/master/analysis/xgboost_model_validation_1.ipynb)<br/>
- Name: xgboost_model_2
- Version: 0.1.1556570523
  - `XGBClassifier(n_estimators=100)`
- [Validation notebook](  https://github.com/jakeweholt/DR_TH/blob/master/analysis/xgboost_model_validation_2.ipynb)

#### Service Level Architecture

The model service runs on an AWS ECS cluster as a Docker image. Inside the docker image is a webapp built using Flask. The ECS image runs behind an application load balancer. Pointed at the ALB is a Route 53 subdomain `datarobot` of my domain www.bigstuffedanimal.com. The actual model endpoint is located at www.datarobot.bigstuffedanimal.com. 
![Image of AWS Architecture](https://github.com/jakeweholt/DR_TH/blob/master/readme_helper_files/aws_architecture.png)


