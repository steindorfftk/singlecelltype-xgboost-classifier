# singlecelltype-xgboost-classifier
XGBoost-Based Cell Type Classification Pipeline

About the data: I used a glioblastoma scRNA-seq atlas composed of 307k cells (samples) and 13112 genes (features). The cells are classified with 6 classes: tumor cells,
myeloid cells, lymphoid cells, oligodendrocytes, mesenchymal cells and endothelial cells. Counts are log1p normalized (to 10000) and scaled to mean = 0 and sd = 1.

Step 1 - Initial Hyperparameter Optimization (01_Pipeline_Step_1.ipynb):
First, 20% of samples were assigned to the test group and the remaining 80% were assigned to calibrate hyperparameters.
Next, I encoded the labels and performed an initial randomized hyperparameter search using XGBoost with cross-validation to find the best combination of learning rate, 
max depth, and number of estimators.
The initial 20 iterations of RandomizedSearchCV yielded the following optimal hyperparameters: learning_rate = 0.2296, max_depth = 7, and n_estimators = 70. 
These parameters were applied to an initial classification test, which produced remarkably high performance metrics: an accuracy of 98.8%, precision of 98.5%, 
recall of 98.3%, and an F1-score of 98.3%.

Step 2 - 20-fold validation (02_Pipeline_Step_2.ipynb):
To validate these results, 20-fold validation was conducted. To account for class unbalance, in each of the 20 rounds the samples were subsetted to 2000 samples 
per class, divided in a 70/30 manne. This hyperparameter combination demonstrated consistent performance across all folds, with mean metrics as follows: 
Precision = 98.3% ± 0.002%, Recall = 98.3% ± 0.002%, and F1-score = 98.3% ± 0.002%.

Step 3 - Feature selection (03_Pipeline_Step_3.ipynb):
In order to simplify the model and allow for exploration of more hyperparameters, I order features using the built-in XGBoost feature_importance function and tested
the performance of the model as features were removed. In each round I removed half of the features and tested metrics in 20-folds like in the previous step. This way,
I reduced the initial 13112 features to 102 features that still achieved a high performance. With 51 features, all metrics droped to ~96%.


















