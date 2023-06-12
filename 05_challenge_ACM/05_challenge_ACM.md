  # Challenges MCA

## 1. Import required libraries


```R
if(!is.element("FactoMineR", installed.packages()[,1])){
  install.packages("FactoMineR", dependencies=TRUE)
}

if(!is.element("factoextra", installed.packages()[,1])){
  install.packages("factoextra", dependencies=TRUE)
}

if(!is.element("mongolite", installed.packages()[,1]) ){
  install.packages("mongolite", dependencies = T)
}

library(FactoMineR)
library(factoextra)
library(mongolite)
```

    Loading required package: ggplot2
    
    Welcome! Want to learn more? See two factoextra-related books at https://goo.gl/ve3WBa
    
    

## 2. Load data from Database using NoSQL query


```R
con <- mongo(collection = "BD_OPER_PROC_AUTH",db = "bandit-nbs",url = "mongodb://bandit:KQVYJnxcMXvQ5joLef524V97zRkMMT3N@localhost:27018")
challenges <- con$find()
rownames(challenges) <- challenges$Id

challenges[,"ChallengeType"] = factor(challenges[,"ChallengeType"])
challenges[,"Gender"] = factor(challenges[,"Gender"])
challenges[,"Decision"] = factor(challenges[,"Decision"])
challenges[,"AttemptCount"] = factor(challenges[,"AttemptCount"])
challenges[,"MaxAttemptsReached"] = factor(challenges[,"MaxAttemptsReached"])
challenges[,"Protocol"] = factor(challenges[,"Protocol"])
challenges[,"PhonePrefix"] = factor(challenges[,"PhonePrefix"])

summary(challenges)
```


          Id            ChallengeId           BankId            ClientId        
     Length:7980        Length:7980        Length:7980        Length:7980       
     Class :character   Class :character   Class :character   Class :character  
     Mode  :character   Mode  :character   Mode  :character   Mode  :character  
                                                                                
                                                                                
                                                                                
                                                                                
     ClientBirthDate                         Age        ChallengeType
     Min.   :1930-01-12 11:30:13.0000   Min.   :18.00   EMAIL:1630   
     1st Qu.:1948-09-19 18:33:09.0000   1st Qu.:34.00   ID   :1877   
     Median :1968-01-18 04:45:12.0000   Median :50.00   OTP  :2906   
     Mean   :1968-01-05 17:30:58.8862   Mean   :49.93   SMS  :1567   
     3rd Qu.:1987-01-27 06:51:36.5000   3rd Qu.:64.00                
     Max.   :2005-12-28 08:43:51.0000   Max.   :99.00                
     NA's   :1                                                       
      RequestTime                      ResponseTime                   
     Min.   :2023-01-01 00:18:41.73   Min.   :2023-01-01 00:21:01.30  
     1st Qu.:2023-02-03 02:30:43.97   1st Qu.:2023-02-03 02:32:43.70  
     Median :2023-03-08 09:11:10.23   Median :2023-03-08 09:12:37.90  
     Mean   :2023-03-08 12:04:10.86   Mean   :2023-03-08 12:06:43.35  
     3rd Qu.:2023-04-11 00:53:13.00   3rd Qu.:2023-04-11 00:55:05.07  
     Max.   :2023-05-14 14:04:52.05   Max.   :2023-05-14 14:06:33.90  
                                                                      
      DecisionTime                       Gender         Decision    AttemptCount
     Min.   :2023-01-01 00:21:03.71   Female:4135   Accepted:6138   1:2581      
     1st Qu.:2023-02-03 02:32:48.11   Male  :3845   Refused :1842   2:2168      
     Median :2023-03-08 09:12:42.02                                 3:2135      
     Mean   :2023-03-08 12:06:46.34                                 4:1096      
     3rd Qu.:2023-04-11 00:55:08.15                                             
     Max.   :2023-05-14 14:06:37.55                                             
                                                                                
     MaxAttemptsReached PhonePrefix  Protocol   
     FALSE:6884         +212: 315   HTTP : 175  
     TRUE :1096         +213: 331   HTTPS:1455  
                        +32 : 295   NA's :6350  
                        +33 : 298               
                        +34 : 328               
                        NA's:6413               
                                                


After a little bit of casting, our data is correctly parsed and can be used to do an ACM

## 3. Launch the MCA algorithm

The ACM algorithm is quite long to process (~20min)


```R
acm <- MCA(challenges, graph=FALSE, quanti.sup  =c("Age"), quali.sup=c("Id", "ChallengeId", "BankId","ClientId","ClientBirthDate","RequestTime","ResponseTime","DecisionTime","PhonePrefix"))
```

First, we can check for the eigen values to see what is the repartition of explanation between our dimensions


```R
acm$eig
```


<table class="dataframe">
<caption>A matrix: 11 × 3 of type dbl</caption>
<thead>
	<tr><th></th><th scope=col>eigenvalue</th><th scope=col>percentage of variance</th><th scope=col>cumulative percentage of variance</th></tr>
</thead>
<tbody>
	<tr><th scope=row>dim 1</th><td>4.493104e-01</td><td>2.450784e+01</td><td> 24.50784</td></tr>
	<tr><th scope=row>dim 2</th><td>3.351525e-01</td><td>1.828104e+01</td><td> 42.78888</td></tr>
	<tr><th scope=row>dim 3</th><td>2.212541e-01</td><td>1.206840e+01</td><td> 54.85728</td></tr>
	<tr><th scope=row>dim 4</th><td>1.752977e-01</td><td>9.561694e+00</td><td> 64.41898</td></tr>
	<tr><th scope=row>dim 5</th><td>1.698890e-01</td><td>9.266671e+00</td><td> 73.68565</td></tr>
	<tr><th scope=row>dim 6</th><td>1.660056e-01</td><td>9.054850e+00</td><td> 82.74050</td></tr>
	<tr><th scope=row>dim 7</th><td>1.634301e-01</td><td>8.914370e+00</td><td> 91.65487</td></tr>
	<tr><th scope=row>dim 8</th><td>1.242869e-01</td><td>6.779283e+00</td><td> 98.43415</td></tr>
	<tr><th scope=row>dim 9</th><td>2.870718e-02</td><td>1.565846e+00</td><td>100.00000</td></tr>
	<tr><th scope=row>dim 10</th><td>3.338695e-28</td><td>1.821106e-26</td><td>100.00000</td></tr>
	<tr><th scope=row>dim 11</th><td>9.482952e-29</td><td>5.172519e-27</td><td>100.00000</td></tr>
</tbody>
</table>



We can also plot those repartitions


```R
barplot(acm$eig[,2], names=paste("d",1:nrow(acm$eig)))
```


    
![png](output_12_0.png)
    


## 4. MCA variables factor map

First let's plot our acm with the following options
- **cex=0.8** : Size of the points
- **invisible=c("ind","quali.sup","quanti.sup")** : We only want to see variables


```R
plot(acm, invisible = c("ind", "quali.sup", "quanti.sup"), cex = 0.8, autoLab="yes")
```


    
![png](output_15_0.png)
    


With this graph, we can clearly see a distinction between the Accepted and Refused challenge attempts. We can further investigate
this observation with the next graph


```R
plot(acm, choix="var")
```

    Warning message:
    "ggrepel: 7 unlabeled data points (too many overlaps). Consider increasing max.overlaps"
    


    
![png](output_17_1.png)
    


This graph is showing us that the vertical axis is built based on the **ChallengeType** and the **Protocol** variables, the horizontal axis is built based
on the variables **Decision**, **AttemptCount**, **MaxAttemptsReached**. We can't say much about the vertical axis, but the horizontal one is much more
interesting

As we can see, Decision, AttemptCount and MaxAttemptsReached seems highly correlated, which seems coherent.
We can try to use the plotellipses function in order to better understand this correlation


```R
plotellipses(acm, keepvar = c("MaxAttemptsReached", "Decision", "AttemptCount"), label="none")
```


    
![png](output_19_0.png)
    


With a little bit of interpretation, we could guess that when the AttemptCount goes over 3, then the MaxAttempts may be reached and the Decision should be refused.
We can confirm this observation with a NoSQL Query


```R
decision_result <- con$find('{"AttemptCount": {"$gt": 3}}', fields = '{"AttemptCount": 1, "MaxAttemptsReached": 1, "Decision": 1}', limit=10)
print(decision_result)
```

                            _id Decision AttemptCount MaxAttemptsReached
    1  6460d9aa532da4b75a07f919  Refused            4               TRUE
    2  6460d9aa532da4b75a07f922  Refused            4               TRUE
    3  6460d9aa532da4b75a07f929  Refused            4               TRUE
    4  6460d9aa532da4b75a07f92a  Refused            4               TRUE
    5  6460d9aa532da4b75a07f92c  Refused            4               TRUE
    6  6460d9aa532da4b75a07f940  Refused            4               TRUE
    7  6460d9aa532da4b75a07f946  Refused            4               TRUE
    8  6460d9aa532da4b75a07f94c  Refused            4               TRUE
    9  6460d9aa532da4b75a07f953  Refused            4               TRUE
    10 6460d9aa532da4b75a07f961  Refused            4               TRUE
    

This definitely confirm our hypothesis

Unfortunately this finding could have been deducted without the ACM, but we are not done yet.
**Let's try to see if the age has any impact on the results**, for that, we will divide the data in age classes


```R
age_groups <- c("<20", "20-24", "25-29", "30-34", "35-39", "40-44", "45-49", "50-54", "55-59", ">=60")
bounds <- c(0, 20, 24, 29, 34, 39, 44, 49, 54, 59, Inf)
AgeGroup <- unlist(lapply(seq_len(nrow(challenges)), function(i) { cut(challenges$Age[i], breaks = bounds, labels = age_groups) }))
challenges$AgeGroup <- AgeGroup
```

Expect about 2 hours to run the analysis (seriously)


```R
acm <- MCA(challenges, graph=FALSE, quanti.sup=c("Age"), quali.sup=c("Id", "ChallengeId", "BankId","ClientId","ClientBirthDate","RequestTime","ResponseTime","DecisionTime","PhonePrefix","Protocol"))
```


```R
acm$eig
```


<table class="dataframe">
<caption>A matrix: 18 × 3 of type dbl</caption>
<thead>
	<tr><th></th><th scope=col>eigenvalue</th><th scope=col>percentage of variance</th><th scope=col>cumulative percentage of variance</th></tr>
</thead>
<tbody>
	<tr><th scope=row>dim 1</th><td>4.569301e-01</td><td>1.523100e+01</td><td> 15.23100</td></tr>
	<tr><th scope=row>dim 2</th><td>3.149520e-01</td><td>1.049840e+01</td><td> 25.72940</td></tr>
	<tr><th scope=row>dim 3</th><td>2.056056e-01</td><td>6.853520e+00</td><td> 32.58292</td></tr>
	<tr><th scope=row>dim 4</th><td>1.785454e-01</td><td>5.951513e+00</td><td> 38.53444</td></tr>
	<tr><th scope=row>dim 5</th><td>1.745789e-01</td><td>5.819297e+00</td><td> 44.35373</td></tr>
	<tr><th scope=row>dim 6</th><td>1.716000e-01</td><td>5.720001e+00</td><td> 50.07373</td></tr>
	<tr><th scope=row>dim 7</th><td>1.667722e-01</td><td>5.559075e+00</td><td> 55.63281</td></tr>
	<tr><th scope=row>dim 8</th><td>1.667183e-01</td><td>5.557277e+00</td><td> 61.19009</td></tr>
	<tr><th scope=row>dim 9</th><td>1.666667e-01</td><td>5.555556e+00</td><td> 66.74564</td></tr>
	<tr><th scope=row>dim 10</th><td>1.662981e-01</td><td>5.543269e+00</td><td> 72.28891</td></tr>
	<tr><th scope=row>dim 11</th><td>1.638909e-01</td><td>5.463030e+00</td><td> 77.75194</td></tr>
	<tr><th scope=row>dim 12</th><td>1.623337e-01</td><td>5.411124e+00</td><td> 83.16307</td></tr>
	<tr><th scope=row>dim 13</th><td>1.543920e-01</td><td>5.146401e+00</td><td> 88.30947</td></tr>
	<tr><th scope=row>dim 14</th><td>1.322138e-01</td><td>4.407126e+00</td><td> 92.71659</td></tr>
	<tr><th scope=row>dim 15</th><td>1.251477e-01</td><td>4.171589e+00</td><td> 96.88818</td></tr>
	<tr><th scope=row>dim 16</th><td>5.400933e-02</td><td>1.800311e+00</td><td> 98.68849</td></tr>
	<tr><th scope=row>dim 17</th><td>3.934518e-02</td><td>1.311506e+00</td><td>100.00000</td></tr>
	<tr><th scope=row>dim 18</th><td>4.048844e-28</td><td>1.349615e-26</td><td>100.00000</td></tr>
</tbody>
</table>




```R
barplot(acm$eig[,2], names=paste("d",1:nrow(acm$eig)))
```


    
![png](output_28_0.png)
    



```R
plot(acm, invisible = c("ind", "quali.sup", "quanti.sup"), cex = 0.8, autoLab="yes")
```


    
![png](output_29_0.png)
    


Fortunately, the pain of waiting the process to complete was very rewarding because the results here are incredible.

As we can see, we have still horizontally the distinction between authentication successes and failues.
But now we can definitely see that the vertical axis is mainly related to the age of the person

We can confirm this with the next graph


```R
plot(acm, choix="var")
```

    Warning message:
    "ggrepel: 7 unlabeled data points (too many overlaps). Consider increasing max.overlaps"
    


    
![png](output_31_1.png)
    


As we can see, the observations for the horizontal axis are quite similar than what we had previously,
and we can confirm what we saw, the vertical axis is mainly built based on age.

But here the fact that we have the age as the vertical axis allows us to see something that we didn't saw
before: it seems that there's a link between the **ChallengeType**, the **Age** and the **AttemptCount**

Let's use the plotellipses function to evaluate this observation


```R
plotellipses(acm, keepvar = c("ChallengeType", "AgeGroup", "AttemptCount"), label="none")
```


    
![png](output_33_0.png)
    


As we can see, there's definitely a link between the Age, the ChallengeType, and the AttemptCount

In order to quantify theses correlations, let's use ANOVA


```R
boxplot(challenges$Age~challenges$ChallengeType)
```


    
![png](output_35_0.png)
    



```R
model <- lm(challenges$Age~challenges$ChallengeType)
anova(model)
```


<table class="dataframe">
<caption>A anova: 2 × 5</caption>
<thead>
	<tr><th></th><th scope=col>Df</th><th scope=col>Sum Sq</th><th scope=col>Mean Sq</th><th scope=col>F value</th><th scope=col>Pr(&gt;F)</th></tr>
	<tr><th></th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>challenges$ChallengeType</th><td>   3</td><td> 556745.4</td><td>185581.7896</td><td>641.3258</td><td> 0</td></tr>
	<tr><th scope=row>Residuals</th><td>7976</td><td>2308031.9</td><td>   289.3721</td><td>      NA</td><td>NA</td></tr>
</tbody>
</table>




```R
pairwise.t.test(challenges$Age, challenges$ChallengeType, p.adjust.method = "none", pool.sd = T)
```


    
    	Pairwise comparisons using t tests with pooled SD 
    
    data:  challenges$Age and challenges$ChallengeType 
    
        EMAIL  ID     OTP   
    ID  <2e-16 -      -     
    OTP <2e-16 <2e-16 -     
    SMS 4e-13  <2e-16 <2e-16
    
    P value adjustment method: none 



```R
library(ggplot2)
library(dplyr)
counts <- challenges %>% count(Age, ChallengeType)
ggplot(counts, aes(x = Age, y = n, fill = ChallengeType)) + geom_col()
```


    
![png](output_38_0.png)
    



```R
boxplot(challenges$Age~challenges$AttemptCount)
```


    
![png](output_39_0.png)
    



```R
model <- lm(challenges$Age~challenges$AttemptCount)
anova(model)
```


<table class="dataframe">
<caption>A anova: 2 × 5</caption>
<thead>
	<tr><th></th><th scope=col>Df</th><th scope=col>Sum Sq</th><th scope=col>Mean Sq</th><th scope=col>F value</th><th scope=col>Pr(&gt;F)</th></tr>
	<tr><th></th><th scope=col>&lt;int&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><th scope=row>challenges$AttemptCount</th><td>   3</td><td>1094407</td><td>364802.2670</td><td>1643.533</td><td> 0</td></tr>
	<tr><th scope=row>Residuals</th><td>7976</td><td>1770370</td><td>   221.9622</td><td>      NA</td><td>NA</td></tr>
</tbody>
</table>




```R
pairwise.t.test(challenges$Age, challenges$AttemptCount, p.adjust.method = "none", pool.sd = T)
```


    
    	Pairwise comparisons using t tests with pooled SD 
    
    data:  challenges$Age and challenges$AttemptCount 
    
      1      2      3   
    2 <2e-16 -      -   
    3 <2e-16 <2e-16 -   
    4 <2e-16 <2e-16 0.24
    
    P value adjustment method: none 



```R
library(ggplot2)
library(dplyr)
counts <- challenges %>% count(Age, AttemptCount)
ggplot(counts, aes(x = Age, y = n, fill = AttemptCount)) + geom_col()
```


    
![png](output_42_0.png)
    

