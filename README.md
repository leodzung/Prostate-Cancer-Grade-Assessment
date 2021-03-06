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

1. Create the confusion matrix O. The columns represent predicted scores while the rows represent actual scores. The value in the matrix is the count of the number of instances with the according combination of predicted and actual score. For example, there are 3 instances where the predicted score is 1, and the actual score is 2 (row 2, 7 and 9 in the table above). The diagonal indicates the number of cases where the prediction is exactly the same as actual. 

|     | `0` | `1` | `2` | `3` |
| --- | --- | --- | --- | --- |
| `0` |  0  |  0  |  0  |  0  |
| `1` |  1  |  0  |  1  |  0  |
| `2` |  1  |  3  |  0  |  0  |
| `3` |  0  |  0  |  1  |  0  |

2. Use the weighted matrix W below. Predictions that are further away from actuals are penalized more harshly than the ones that are closer to actuals. For example, prediction of 2 will result in better score than prediction of 3 when the actual score is 1. This matrix W is calculated based on the difference between actual and predicted rating scores, formular below with N = 4.

wi,j = (i−j)^2 / (N−1)^2

|     |   `0`  |   `1`  |   `2`  |   `3`  |
| --- | ------ | ------ | ------ | ------ |
| `0` |  0.00  |  0.11  |  0.44  |  1.00  |
| `1` |  0.11  |  0.00  |  0.11  |  0.44  |
| `2` |  0.44  |  0.11  |  0.00  |  0.11  |
| `3` |  1.00  |  0.44  |  0.11  |  0.00  |

3. Caculate histograms of predicted and actual scores

| Histogram |  Actual  | Predicted | 
| --------- | -------- | --------- | 
|     `0`   |     3    |      2    |
|     `1`   |     2    |      3    |
|     `2`   |     4    |      2    |
|     `3`   |     1    |      3    | 

4. Calculate expected value (outer product of the above two histograms) - matrix E

|   Matrix  |   E  |   Matrix  |   E    |
| --------- | ---- | --------- | ------ |
|   0.60    | 0.90 |    0.60   |  0.90  |
|   0.40    | 0.60 |    0.40   |  0.60  |
|   0.80    | 1.20 |    0.80   |  1.20  |
|   0.20    | 0.30 |    0.20   |  0.30  |

5. Calculate element-wise prorduct of matrix W and matrix O (num), and element-wise product of matrix W and matrix E (den).
qwk = 1 - (num / den) = -0.5811 (negative means that this is worse than what can be expected by chance).

|   Matrix  |    W   |    X   |    O   |
| --------- | ------ | ------ | ------ |
|   0.00    |  0.00  |  0.00  |  3.00  |
|   0.11    |  0.00  |  0.11  |  0.00  |
|   0.44    |  0.33  |  0.00  |  0.00  |
|   0.00    |  0.00  |  0.11  |  0.00  |

|   Matrix  |    W   |    X   |    E   |
| --------- | ------ | ------ | ------ |
|   0.00    |  0.10  |  0.27  |  0.90  |
|   0.04    |  0.00  |  0.04  |  0.27  |
|   0.36    |  0.13  |  0.00  |  0.13  |
|   0.00    |  0.13  |  0.02  |  0.00  |

### Preprocessing

One of the biggest challenges of this competition is that the imput images are of extremely high resolution and large areas of empty space. That calls for the need of an effective preprocessing to identify and focus on the interesting area. The straightforward approach of simply rescale the input images to square is not very efficient because the input images come with a wide range of sizes. Simply squared-shaped transform images became distorted and is not in a consistent manner. Moreover, the large blank areas in the images would consume GPU memory and processing power.

I decided to use the approach of concatenate tiles of interest that is proposed here. This approach selects N=12 tiles from each image simply based on the number of tissue pixels.

https://www.kaggle.com/iafoss/panda-concat-tile-pooling-starter-0-79-lb
