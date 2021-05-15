# Patient Selection for Diabetes Drug Testing

#### *Introduction:*

For the pharmaceutical industry and regulators, electronic health record (EHR) data is a key source of real world evidence. Clinical trials can be costly and time-consuming. The goal of this project is to use machine learning to find appropriate patients to include in the diabetes drug testing clinical trial.

#### *Goal:*

Build an Expected Hospitalization Time Regression Model, utilizing the [UCI Diabetes readmission dataset](https://archive.ics.uci.edu/ml/datasets/Diabetes+130-US+hospitals+for+years+1999-2008). The model will predict the expected days of hospitalization time and then convert the result into a binary prediction of whether to include or exclude that patient from the clinical trial.

#### *Exploratory Data Analysis:*

##### Step1: Determine Level of Dataset:

It’s very important to know what level is the EHR data at. It’s common to have duplication of encounter information in an EHR dataset. There are three levels of EHR dataset:

- Line level – It’s the most granular level. For example, one doctor visit can have many line levels, one for medication, one for diagnosis, one for an order for lab test.
- Encounter level – Visit level, include aggregated information from the line level.
- Longitudinal level – Patient level. It aggregates patient visit histories, can show how the cumulation of visits/encounters lead to some clinical impacts.

This dataset is at the line level.

------

##### Step 2: Fields with Missing / Zeros Values:

There are quite a few fields with high amount of missing or zero values. These fields need to be identified so that we can be aware about it when choosing features to build our model. The fields with high missing values are weight(97%), medical specialty(48%), payer code(37%), ndc_code(16%), max_glu_serum(95%), and A1C result(82%). The fields with high zero values are number of outpatient(83%), number of inpatient(67%), number of emergency(88%), and num procedures(45%).

------

##### Step 3: Visualize Data Distribution:

Next, I want to plot some histograms to see if each field has a Gaussian(normal) distribution shape. ![img](https://github.com/HUA1846/Patient-Selection-for-Diabetes-Drug-Testing/blob/main/visualization/distribution.png?raw=true)

------

##### Step 4: Reduce Dimensionality of the NDC Code Feature:

NDC codes are a common format to represent the wide variety of drugs that are prescribed for patient care in the United States. The challenge is that there are many codes that map to the same or similar drug. I grouped the ndc_codes based on their generic drug name to to reduce the dimensionality of the field. Reference: [National Drug Codes List](https://ndclist.com/)

------

##### Step 5: Aggregate Dataset to Encounter Level for Modeling:

Only the first encounter is selected because the goal is to predict length of hospital stay. After selecting the first encounters, we need to aggregate the line level data to encounter levels before training the model.

#### *Feature Selection*

The numerical features I select are ‘num_lab_procedures’, ‘number_diagnoses’, ‘num_medications’, ‘num_procedures’. They show positive relationships with time_in_hospital. Categorical feature selected are age, gender, admission_type_id, primary_diagnosis_code, other_diagnosis_codes.

![img](https://github.com/HUA1846/Patient-Selection-for-Diabetes-Drug-Testing/blob/main/visualization/output_66_0.png?raw=true)

#### *Build Model*

The curated dataset is randomly split into approximately 60% train, 20% validation, and 20% test. After the split, I check to see the distribution of key features/groups and make sure that there is representative samples across the partitions. See the [jupyter notebook for data visualization. ](https://github.com/HUA1846/Patient-Selection-for-Diabetes-Drug-Testing/blob/main/build_train_model/student_project_submission.ipynb)And then convert the split data into tensoflow dataset for training.

#### *Result and Potential Model Biases*

![img](https://github.com/HUA1846/Patient-Selection-for-Diabetes-Drug-Testing/blob/main/visualization/precision-recall.PNG?raw=true) ![img](https://github.com/HUA1846/Patient-Selection-for-Diabetes-Drug-Testing/blob/main/visualization/roc-curve.png?raw=true)

Roc_auc score: 0.71, Accuracy score: 0.68.

Bias: Analyze demographic bias with Aequitas:

- Race
  - False Negative Rate disparity magnitude across race is medium. It shows the FPR disparity relatively close to 1. A disparity of 1 indicates no disparity.
  - False Positive Rate disparity magnitude across race is high. The the disparities between the Hispanic reference population group and both the Caucasion and African-American Group are 1.5, and 2.56 respectively.
- Gender: There is low disparity between male (reference) and female. FPR and FPR disparity magnitude are 1.09 and 1.02 repectively.