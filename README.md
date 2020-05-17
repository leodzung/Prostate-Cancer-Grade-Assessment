# Prostate Cancer Grade Assessment

## Overview
Prostate Cancer is causing 1 million new diagnoses and 350,000 deaths annually. More precise diagnostics is key to decrease the mortality rate. Prostate tissue biopsies are graded in Gleason patterns based on the growth pattern of the tumor, and then converted into an ISUP grade from 1 to 5. This score is then used to decide the treatment for the patient. A more precise grading system will help to avoid both the risk of missing cancers and of unnecessary treatment.

![Grading Illustration](/images/grade.png)

Above is an illustration of the Gleason grading process.

### Data
The training set contains 11,000 images of digitized stained biopsies. This is the largest public whole-slide image dataset available. 

### Metric
In this competition, submisisons are scored based on the quadratic weighted kappa - qwk, which measures the agreement between two ISUP ratings - predicted vs. actual. A perfect score of 1 is graded when the both the predicted and actual ratings are the same.

Assuming there are 10 diasnoses with the predicted and actual scores as follow:

| ISUP grade - Predicted | ISUP grade - Actual |
| ---------------------- | ------------------- |
|           3            |          0          | 
|           1            |          2          | 
|           0            |          2          | 
|           2            |          1          | 
|           3            |          0          | 
|           2            |          3          | 
|           1            |          2          | 
|           3            |          0          | 
|           1            |          2          | 
|           0            |          1          | 

To calculate the qwk score:

1. Create the confusion matrix. The columns represent predicted scores while the rows represent actual scores. The value in the matrix is the count of the number of instances with the according combination of predicted and actual score. For example, there are 3 instances where the predicted score is 1, and the actual score is 2 (row 2, 7 and 9 in the table above). The diagonal indicates the number of cases where the prediction is exactly the same as actual.

|     | `0` | `1` | `2` | `3` |
| --- | --- | --- | --- | --- |
| `0` |  0  |  0  |  0  |  0  |
| `1` |  1  |  0  |  1  |  0  |
| `2` |  1  |  3  |  0  |  0  |
| `3` |  0  |  0  |  1  |  0  |

2. Use the weighted matrix below. Predictions that are further away from actuals are penalized more harshly than the ones that are closer to actuals. For example, prediction of 4 will result in better score than prediction of 5 when the actual score is 3. This matrix is calculated based on the difference between actual and predicted rating scores, formular below with N = 4.

wi,j = (i−j)^2 / (N−1)^2

|     |   `0`  |   `1`  |   `2`  |   `3`  |
| --- | ------ | ------ | ------ | ------ |
| `0` |  0.00  |  0.11  |  0.44  |  1.00  |
| `1` |  0.11  |  0.00  |  0.11  |  0.44  |
| `2` |  0.44  |  0.11  |  0.00  |  0.11  |
| `3` |  1.00  |  0.44  |  0.11  |  0.00  |

3. Caculate histograms of predicted and actual scores

| Histogram of Actual  |   
| -------------------- | 
| `0` |      0.00      |
| `1` |      0.11      |
| `2` |      0.44      |
| `3` |      1.00      | 

4. Calculate expected value (outer product of the two histograms)

