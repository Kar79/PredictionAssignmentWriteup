---
title: "Practical Machine Learning: Prediction Assignment Writeup"
output: 
  html_document:
    keep_md: true
---

## Loading the data
The two csv-files are loaded.

```r
trainLift <- read.csv("pml-training.csv")
testLift <- read.csv("pml-testing.csv")
```

## Preprocessing the data: data type modification
The variables that are later used for the model fit are set to numeric type.

```r
trainLift[, c(8:11,37:49,60:68,84:86,102,113:124,151:159)] <- sapply(trainLift[, c(8:11,37:49,60:68,84:86,102,113:124,151:159)], as.numeric)
testLift[, c(8:11,37:49,60:68,84:86,102,113:124,151:159)] <- sapply(testLift[, c(8:11,37:49,60:68,84:86,102,113:124,151:159)], as.numeric)
```

## Splitting the data for cross validation
For cross vallidation porpose the big data set is split into 70% that are uses for training and 30% that are used for testing.

```r
library(caret)
```

```
## Loading required package: lattice
```

```
## Loading required package: ggplot2
```

```r
set.seed(100)
inTrain <- createDataPartition(y=trainLift$classe, p=0.7, list=FALSE)
training <- trainLift[inTrain,]
testing <- trainLift[-inTrain,]
dim(trainLift)
```

```
## [1] 19622   160
```

```r
dim(training)
```

```
## [1] 13737   160
```

```r
dim(testing)
```

```
## [1] 5885  160
```

## Model fitting for cross validation
The linear discriminant analysis (lda) is used as model. The "classe" variable has to be predicted. It indicates the manner in which the participants did their exercise. Data set columns that have large percentages of missing values are not considered as variables to predict with.

```r
modFit <- train(classe ~ roll_belt + pitch_belt + yaw_belt + total_accel_belt + gyros_belt_x + gyros_belt_y + gyros_belt_z + accel_belt_x + accel_belt_y + accel_belt_z + magnet_belt_x + magnet_belt_y + magnet_belt_z + roll_arm + pitch_arm + yaw_arm + total_accel_arm + gyros_arm_x + gyros_arm_y + gyros_arm_z + accel_arm_x + accel_arm_y + accel_arm_z + magnet_arm_x + magnet_arm_y + magnet_arm_z + roll_dumbbell + pitch_dumbbell + yaw_dumbbell + total_accel_dumbbell + gyros_dumbbell_x + gyros_dumbbell_y + gyros_dumbbell_z + accel_dumbbell_x + accel_dumbbell_y + accel_dumbbell_z + magnet_dumbbell_x + magnet_dumbbell_y + magnet_dumbbell_z + roll_forearm + pitch_forearm + yaw_forearm + total_accel_forearm + gyros_forearm_x + gyros_forearm_y + gyros_forearm_z + accel_forearm_x + accel_forearm_y + accel_forearm_z + magnet_forearm_x + magnet_forearm_y + magnet_forearm_z, method="lda", data=training)
```

## Calculation of expected error rate 
The expected error rate can be calculated by dividing the correct predictions by all data sets for that a prediction was done.

```r
solu <- predict(modFit,testing)
testing$prediction <- solu
dim( testing[ which(testing$classe != testing$prediction), ] )[1] / dim( testing[ , ] )[1]
```

```
## [1] 0.3016143
```

## Model fitting for the whole training set
Now the model is fitted for the whole big data set trainLift.

```r
modFit <- train(classe ~ roll_belt + pitch_belt + yaw_belt + total_accel_belt + gyros_belt_x + gyros_belt_y + gyros_belt_z + accel_belt_x + accel_belt_y + accel_belt_z + magnet_belt_x + magnet_belt_y + magnet_belt_z + roll_arm + pitch_arm + yaw_arm + total_accel_arm + gyros_arm_x + gyros_arm_y + gyros_arm_z + accel_arm_x + accel_arm_y + accel_arm_z + magnet_arm_x + magnet_arm_y + magnet_arm_z + roll_dumbbell + pitch_dumbbell + yaw_dumbbell + total_accel_dumbbell + gyros_dumbbell_x + gyros_dumbbell_y + gyros_dumbbell_z + accel_dumbbell_x + accel_dumbbell_y + accel_dumbbell_z + magnet_dumbbell_x + magnet_dumbbell_y + magnet_dumbbell_z + roll_forearm + pitch_forearm + yaw_forearm + total_accel_forearm + gyros_forearm_x + gyros_forearm_y + gyros_forearm_z + accel_forearm_x + accel_forearm_y + accel_forearm_z + magnet_forearm_x + magnet_forearm_y + magnet_forearm_z, method="lda", data=trainLift)
solu <- predict(modFit,testLift)
```

## Prediction for the 20 different test cases
Based on this model a prediction for the 20 test cases is calculated.

```r
solu <- predict(modFit,testLift)
solu
```

```
##  [1] B A B C C E D D A A D A B A E A A B B B
## Levels: A B C D E
```
