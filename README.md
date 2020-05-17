# Prostate Cancer Grade Assessment

## Overview
Prostate Cancer is causing 1 million new diagnoses and 350,000 deaths annually. More precise diagnostics is key to decrease the mortality rate. Prostate tissue biopsies are graded in Gleason patterns based on the growth pattern of the tumor, and then converted into an ISUP grade from 1 to 5. This score is then used to decide the treatment for the patient. A more precise grading system will help to avoid both the risk of missing cancers and of unnecessary treatment.

![Grading Illustration](/images/grade.png)

Above is an illustration of the Gleason grading process.

### Data
The training set contains 11,000 images of digitized stained biopsies. This is the largest public whole-slide image dataset available. 

### Metric
In this competition, submisisons are scored based on the quadratic weighted kappa, which measures the agreement between two ISUP ratings - predicted vs. actual. A perfect score of 1 is graded when the both the predicted and actual ratings are the same.

Assuming there are 10 diasnoses with the predicted and actual scores as follow:
| ISUP grade - Predicted | ISUP grade - Actual |
| ---------------------- | ------------------- |
|           3            |          0          | 
