# Student-Loans

For this project I will be trying to predict a college cohert's overall default rate on student loans based upon feature variables in a dataset. The data that I will be using is already collected on the U.S. Department of Education Federal Student Aid website. 

The dataset to be used for this project can be found here:

https://www2.ed.gov/offices/OSFAP/defaultmanagement/cdr.html

https://www2.ed.gov/offices/OSFAP/defaultmanagement/instructions.html

### Overview

The dataset contains data on more than 4,800 schools from 2013-2015. Some of the variables include school name, length of the longest program offered by the institution, school type, number of borrowers in default, number of borrowers in repayment, institution default rate, congressional district and institution’s department of education regional code.

![.](https://github.com/bill22290/Student-Loans/blob/master/images/Key.PNG)

## Exploratory Data Analysis
```
Loans <- read.csv("peps300(1).csv", header = TRUE, stringsAsFactors = FALSE)
> str(Loans)
'data.frame':	4874 obs. of  30 variables:
 $ ï..OPEID                  : int  1002 1003 1004 1005 1007 1008 1009 1012 1013 1015 ...
 $ Name                      : chr  "ALABAMA AGRICULTURAL & MECHANICAL UNIVERSITY                     " "FAULKNER UNIVERSITY                                              " "UNIVERSITY OF MONTEVALLO                                         " "ALABAMA STATE UNIVERSITY                                         " ...
 $ Address                   : chr  "4900 MERIDIAN STREET                              " "5345 ATLANTA HIGHWAY                              " "PALMER CIRCLE                                     " "915 SOUTH JACKSON STREET                          " ...
 $ City                      : chr  "NORMAL              " "MONTGOMERY          " "MONTEVALLO          " "MONTGOMERY          " ...
 $ State                     : chr  "AL" "AL" "AL" "AL" ...
 $ State.Desc                : chr  "ALABAMA                                                     " "ALABAMA                                                     " "ALABAMA                                                     " "ALABAMA                                                     " ...
 $ Zip.Code                  : chr  "35762" "36109" "35115" "36104" ...
 $ Zip.Ext                   : chr  "1357" "3398" "6000" "5714" ...
 $ Prog.Length               : int  8 8 8 8 5 8 8 6 5 5 ...
 $ School.Type               : int  1 2 1 1 1 1 1 2 1 1 ...
 $ Year.1                    : int  2015 2015 2015 2015 2015 2015 2015 2015 2015 2015 ...
 $ X.Num.1                   : chr  "378" "180" "72" "466" ...
 $ X.Denom.1                 : chr  "1902" "1471" "784" "2208" ...
 $ DRateNew                  : chr  "0.20" "0.12" "0.09" "0.21" ...
 $ DRate.1                   : chr  "19.8" "12.2" "9.1" "21.1" ...
 $ PRate.1                   : chr  "A" "A" "A" "P" ...
 $ Ethnic.Code               : int  2 5 5 2 5 5 5 5 5 5 ...
 $ Cong.Dis                  : chr  "5" "2" "6" "3" ...
 $ Region                    : int  4 4 4 4 4 4 4 4 4 4 ...
 $ Average.or.Greater.than.30: int  0 0 0 0 0 0 0 0 0 0 ...
 $ Year.2                    : int  2014 2014 2014 2014 2014 2014 2014 2014 2014 2014 ...
 $ X.Num.2                   : chr  "332" "192" "64" "471" ...
 $ X.Denom.2                 : chr  "1753" "1470" "793" "2419" ...
 $ DRate.2                   : chr  "18.9" "13" "8" "19.4" ...
 $ PRate.2                   : chr  "A" "A" "A" "P" ...
 $ Year.3                    : int  2013 2013 2013 2013 2013 2013 2013 2013 2013 2013 ...
 $ X.Num.3                   : chr  "300" "143" "57" "367" ...
 $ X.Denom.3                 : chr  "1812" "1491" "744" "2196" ...
 $ DRate.3                   : chr  "16.5" "9.5" "7.6" "16.7" ...
 $ PRate.3                   : chr  "A" "A" "A" "P" ...
 
 > plot(Loans$DRate.1, Loans$Prog.Length)
 ```
 ![.](https://github.com/bill22290/Student-Loans/blob/master/images/ProgLength_DRate1_plot.PNG)
 ```
 > plot(Loans$DRate.1, Loans$School.Type)
 ```
 ![.](https://github.com/bill22290/Student-Loans/blob/master/images/SchoolType_DRate1_plot.PNG)
 
 There appears to be a bit of a negative correlation between the program length (the length of the longest program offered by the institution) and the cohert's default rate. It is also interesting to note that proprietary (for profit) institutions appear to have relatively high rates of cohert defaults. 
 
### Data Engineering
I want to engineer the dataset into a structure that will work for a decision tree model and a Random Forest model as those are the models that I intend to use. I am going to start by creating a dataframe only containing variables that I am interested in analyzing for 2015:
```
> str(Loans15)
'data.frame':	4874 obs. of  13 variables:
 $ Name                      : chr  "ALABAMA AGRICULTURAL & MECHANICAL UNIVERSITY                     " "FAULKNER UNIVERSITY                                              " "UNIVERSITY OF MONTEVALLO                                         " "ALABAMA STATE UNIVERSITY                                         " ...
 $ Zip.Code                  : int  35762 36109 35115 36104 35010 35611 36849 35254 35671 36331 ...
 $ Zip.Ext                   : int  1357 3398 6000 5714 0 1999 5113 2 0 1300 ...
 $ Prog.Length               : int  8 8 8 8 5 8 8 6 5 5 ...
 $ School.Type               : int  1 2 1 1 1 1 1 2 1 1 ...
 $ X.Num.1                   : int  378 180 72 466 131 66 114 15 433 79 ...
 $ X.Denom.1                 : int  1902 1471 784 2208 579 924 3750 227 2489 377 ...
 $ DRate.1                   : num  19.8 12.2 9.1 21.1 22.6 7.1 3 6.6 17.3 20.9 ...
 $ PRate.1                   : chr  "A" "A" "A" "P" ...
 $ Ethnic.Code               : int  2 5 5 2 5 5 5 5 5 5 ...
 $ Cong.Dis                  : chr  "5" "2" "6" "3" ...
 $ Region                    : int  4 4 4 4 4 4 4 4 4 4 ...
 $ Average.or.Greater.than.30: int  0 0 0 0 0 0 0 0 0 0 ...
 ```
 ### Decision Tree(Rpart)
 ```
> train <- sample(nrow(Loans15), 0.7*nrow(Loans15), replace = FALSE)
> train.15L <- Loans15[train,]
> test.15L <- Loans15[-train,]
> library(rpart)
> library(rpart.plot)
> tree15 <- rpart(train.15L$DRate.1~train.15L$Zip.Code + train.15L$Prog.Length + train.15L$Zip.Ext + train.15L$School.Type + train.15L$X.Num.1 + train.15L$X.Denom.1 + train.15L$PRate.1 + train.15L$Ethnic.Code + train.15L$Cong.Dis + train.15L$Region + train.15L$Average.or.Greater.than.30, data = train.15L, method = "anova", na.action = na.exclude)
> printcp(tree15)

Regression tree:
rpart(formula = train.15L$DRate.1 ~ train.15L$Zip.Code + train.15L$Prog.Length + 
    train.15L$Zip.Ext + train.15L$School.Type + train.15L$X.Num.1 + 
    train.15L$X.Denom.1 + train.15L$PRate.1 + train.15L$Ethnic.Code + 
    train.15L$Cong.Dis + train.15L$Region + train.15L$Average.or.Greater.than.30, 
    data = train.15L, na.action = na.exclude, method = "anova")

Variables actually used in tree construction:
[1] train.15L$Ethnic.Code train.15L$Prog.Length train.15L$X.Denom.1   train.15L$X.Num.1    

Root node error: 177835/3176 = 55.993

n=3176 (235 observations deleted due to missingness)

         CP nsplit rel error  xerror     xstd
1  0.221924      0   1.00000 1.00092 0.032694
2  0.190069      1   0.77808 0.77916 0.028997
3  0.048757      2   0.58801 0.59443 0.025777
4  0.032075      3   0.53925 0.54467 0.022205
5  0.031726      5   0.47510 0.52182 0.021717
6  0.019542      6   0.44338 0.46823 0.020487
7  0.019123      8   0.40429 0.44254 0.019746
8  0.015906      9   0.38517 0.42146 0.019120
9  0.012558     10   0.36926 0.39168 0.018396
10 0.010333     11   0.35671 0.37798 0.017714
11 0.010000     12   0.34637 0.37160 0.017603
> rpart.plot(tree15)
```
The first split improves the model fit the most based upon how much relative error has decreased (0.221924) as nsplit goes from 0 to 1. The last split adds a little value in decreasing relative error.

A tree with 11 splits minimizes the relative error for the tree15 model. I could look to apply the 1 standard error rule, however I do not think that is necessary since the dataset that I am working with isn't too large so computational constraints are not an issue for this exercise.
```
> rpart.plot(tree15)
```
![.](https://github.com/bill22290/Student-Loans/blob/master/images/rpartplot15.png)

 ### Random Forest
```
> rfmodel15 <- randomForest(rftrain.15L$DRate.1 ~., data = rftrain.15L, na.action = na.exclude, importance = TRUE)
> rfmodel15

Call:
 randomForest(formula = rftrain.15L$DRate.1 ~ ., data = rftrain.15L,      importance = TRUE, na.action = na.exclude) 
               Type of random forest: regression
                     Number of trees: 500
No. of variables tried at each split: 3

          Mean of squared residuals: 9.507475
                    % Var explained: 83.12
> varImpPlot(rfmodel15)
```
![.](https://github.com/bill22290/Student-Loans/blob/master/images/Rfmodel15.png)

It is surprising to me that the School type variable is not higher up on the charts considering the initial plots that I ran. I now want to do some feature engineering on the School Type variable.

 ### Feature Engineering
I want to deconstruct the categorical variable school type.  I know based upon the exploratory data analysis that there appears to be a significant relationship between school type and a cohert's default rate, specifically when it comes to proprietary institutions.  However, my models might not be fully capturing the importance of school's with a School Type entry of 3 (proprietary).

To better exploit the differences in school types, I want to transform the School type variable into a binary feature where proprietary institutions have a school type value of 1 and all other institutions have a school type value of 0. I will create a duplicate copy of the dataframe used in my tree15 model to do so (Loans15_Stype):
```
> Loans15_Stype$School.Type[Loans15_Stype$School.Type == 1] <- 0
> Loans15_Stype$School.Type[Loans15_Stype$School.Type == 2] <- 0
> Loans15_Stype$School.Type[Loans15_Stype$School.Type == 5] <- 0
> Loans15_Stype$School.Type[Loans15_Stype$School.Type == 7] <- 0
> Loans15_Stype$School.Type[Loans15_Stype$School.Type == 3] <- 1
> treeST15 <- rpart(Loans15_Stype$DRate.1~Loans15_Stype$Zip.Code + Loans15_Stype$Prog.Length + Loans15_Stype$Zip.Ext + Loans15_Stype$School.Type + Loans15_Stype$X.Num.1 + Loans15_Stype$X.Denom.1 + Loans15_Stype$PRate.1 + Loans15_Stype$Ethnic.Code + Loans15_Stype$Cong.Dis + Loans15_Stype$Region + Loans15_Stype$Average.or.Greater.than.30, data = Loans15_Stype, method = "anova", na.action = na.exclude)
> printcp(treeST15)

Regression tree:
rpart(formula = Loans15_Stype$DRate.1 ~ Loans15_Stype$Zip.Code + 
    Loans15_Stype$Prog.Length + Loans15_Stype$Zip.Ext + Loans15_Stype$School.Type + 
    Loans15_Stype$X.Num.1 + Loans15_Stype$X.Denom.1 + Loans15_Stype$PRate.1 + 
    Loans15_Stype$Ethnic.Code + Loans15_Stype$Cong.Dis + Loans15_Stype$Region + 
    Loans15_Stype$Average.or.Greater.than.30, data = Loans15_Stype, 
    na.action = na.exclude, method = "anova")

Variables actually used in tree construction:
[1] Loans15_Stype$Ethnic.Code Loans15_Stype$Prog.Length Loans15_Stype$X.Denom.1   Loans15_Stype$X.Num.1    

Root node error: 248420/4542 = 54.694

n=4542 (332 observations deleted due to missingness)

         CP nsplit rel error  xerror     xstd
1  0.222827      0   1.00000 1.00038 0.027131
2  0.187999      1   0.77717 0.77749 0.024194
3  0.045421      2   0.58917 0.58979 0.021621
4  0.031030      3   0.54375 0.54966 0.019133
5  0.029870      5   0.48169 0.49565 0.017853
6  0.022477      6   0.45182 0.46621 0.017400
7  0.019216      7   0.42934 0.44179 0.017555
8  0.015664      9   0.39091 0.40464 0.016536
9  0.012317     10   0.37525 0.38800 0.016343
10 0.010316     11   0.36293 0.37732 0.016097
11 0.010000     12   0.35262 0.36534 0.015631
```
Compared to the original decision tree15, the newly created decision tree treeST15 has a lower Root node error and lower xstd for each corresponding CP.

### Cross-validation Results
 ```
 > plotcp(treeST15)
 ```
 ![.](https://github.com/bill22290/Student-Loans/blob/master/images/rpartcrossval.png)
The plotcp() function gives us the relative cross-validation error for different tree sizes, and it appears that n = 11 minimizes the relative error with respect to the geometric mean.  One could perhaps interpret this visual as n = 12 minimizing the relative error, but that would add additional computation in exchange for little marginal improvement in minimizing relative error.

### Rpart Model Accuracy

I now want to build a prediction table and a confusion matrix to assess how well the model performed predicting a school's overall default rate. The U.S. Department of Education makes decisions on allocating Pell Grant funds using whole numbers (30% default rate for Pell Grant eligibility) so I want to round to whole numbers before building:
```
> predictions <- predict(treeST15, type = "matrix")
> rounded_y <- round(Loans15_Stype$DRate.1)
> rounded_predictions <- round(predictions)
> CM <- table(rounded_y, rounded_predictions)
```
I can now determine the accuracy by dividing the sum of the diagonal of the confusion matrix just built (CM), which are the correct predictions, by the total sum of the confusion matrix:
```
> accuracyrpart <- sum(diag(CM))/sum(CM)
> accuracyrpart
[1] 0.04997798
```
As we can see above, unfortunately the model's predictions were not very accurate at all. 

### Random Forest Model Accuracy

Let's now take a look at a random forest model with this newly created binary school type variable:
```
> rfmodel15ST <- randomForest(rfL15ST$DRate.1~., data = rfL15ST, na.action = na.exclude, importance = TRUE)
> rfmodel15ST

Call:
 randomForest(formula = rfL15ST$DRate.1 ~ ., data = rfL15ST, importance = TRUE,      na.action = na.exclude) 
               Type of random forest: regression
                     Number of trees: 500
No. of variables tried at each split: 3

          Mean of squared residuals: 7.759169
                    % Var explained: 85.77
```
This model has a lower Mean of squared residuals value and a higher % of Var explained compared to the first Random Forest Model that I created. 

```
> rfrounded_y <- round(rfmodel15ST$y)
> rfrounded_predictions <- round(rfmodel15ST$predicted)
> RFCM <- table(rfrounded_y, rfrounded_predictions)
> accuracyrf <- sum(diag(RFCM))/sum(RFCM)
> accuracyrf
[1] 0.2297235
```
The Random Forest Model accuracy was significantly better than the rpart model accuracy, however still underwhelming.

# Conclusion
Even though the accuracy of the different machine learning models was less than ideal, this still was a worthwhile exercise for a couple of different reasons.  Based upon the models, we could see that program length (the length of the longest program offered by the institution) and school type, specifically if a school is proprietary or not, were important features in the data with respect to the default rate.

This also was an important lesson in understanding that a machine learning model is only as good as the quality of the data.  Sometimes, there  isn't much of a recognizable pattern or predictive relationship between different variables within a dataset.  In situations like this, it is important for a data scientist not to attempt to overfit or extrapolate too much from a model that simply doesn't have much predictive value. 

### Sources
```
> citation()
To cite R in publications use:

  R Core Team (2019). R: A language and environment for statistical computing. R Foundation
  for Statistical Computing, Vienna, Austria. URL https://www.R-project.org/.
```


