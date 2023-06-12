  # Challenges PCA

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

## 2. Load data from Database using NoSQL query


```R
con <- mongo(collection = "BD_OPER_PROC_AUTH",db = "bandit-nbs",url = "mongodb://bandit:KQVYJnxcMXvQ5joLef524V97zRkMMT3N@localhost:27018")
challenges <- con$find()
rownames(challenges) <- challenges$Id
summary(challenges)
```


          Id            ChallengeId           BankId            ClientId        
     Length:7980        Length:7980        Length:7980        Length:7980       
     Class :character   Class :character   Class :character   Class :character  
     Mode  :character   Mode  :character   Mode  :character   Mode  :character  
                                                                                
                                                                                
                                                                                
     ClientBirthDate                         Age        ChallengeType     
     Min.   :1930-01-05 22:48:04.0000   Min.   :18.00   Length:7980       
     1st Qu.:1949-05-19 03:22:55.7500   1st Qu.:34.00   Class :character  
     Median :1968-01-25 18:00:16.5000   Median :50.00   Mode  :character  
     Mean   :1967-12-27 13:14:09.8179   Mean   :49.93                     
     3rd Qu.:1986-09-08 13:37:09.2500   3rd Qu.:64.00                     
     Max.   :2005-12-27 19:59:59.0000   Max.   :99.00                     
      RequestTime                      ResponseTime                   
     Min.   :2023-01-01 00:34:01.88   Min.   :2023-01-01 00:36:31.47  
     1st Qu.:2023-02-01 10:45:25.60   1st Qu.:2023-02-01 10:48:10.69  
     Median :2023-03-05 15:59:14.92   Median :2023-03-05 16:01:41.28  
     Mean   :2023-03-05 04:43:49.27   Mean   :2023-03-05 04:46:22.59  
     3rd Qu.:2023-04-05 19:41:07.99   3rd Qu.:2023-04-05 19:43:39.42  
     Max.   :2023-05-07 13:56:30.30   Max.   :2023-05-07 13:59:02.25  
      DecisionTime                       Gender            Decision        
     Min.   :2023-01-01 00:36:35.02   Length:7980        Length:7980       
     1st Qu.:2023-02-01 10:48:13.83   Class :character   Class :character  
     Median :2023-03-05 16:01:43.97   Mode  :character   Mode  :character  
     Mean   :2023-03-05 04:46:25.59                                        
     3rd Qu.:2023-04-05 19:43:41.53                                        
     Max.   :2023-05-07 13:59:03.72                                        
      AttemptCount   MaxAttemptsReached   Protocol         PhonePrefix       
     Min.   :1.000   Length:7980        Length:7980        Length:7980       
     1st Qu.:1.000   Class :character   Class :character   Class :character  
     Median :2.000   Mode  :character   Mode  :character   Mode  :character  
     Mean   :2.206                                                           
     3rd Qu.:3.000                                                           
     Max.   :4.000                                                           


As we can see in the summary, the columns are correctly interpreted, we can continue

## 3. Launch the PCA algorithm

Here we will launch the PCA Algorithm for FactoMineR, as the PCA only handle quantitative values, we need to specify which columns must be considered qualitative.
This way, we will be able to use them has an "habillage" for the graph of individuals in order to detect some correlation between the axis and our data.


```R
acp <- PCA(challenges, graph=FALSE, quali.sup=c("Id", "ChallengeId", "BankId", "ClientId", "ClientBirthDate", "ChallengeType", "RequestTime", "ResponseTime", "DecisionTime", "Gender", "Decision", "MaxAttemptsReached", "Protocol", "PhonePrefix"))
```

    Warning message in PCA(challenges, graph = FALSE, quali.sup = c("Id", "ChallengeId", :
    "Missing values are imputed by the mean of the variable: you should use the imputePCA function of the missMDA package"
    


```R
acp$eig
```


<table class="dataframe">
<caption>A matrix: 2 × 3 of type dbl</caption>
<thead>
	<tr><th></th><th scope=col>eigenvalue</th><th scope=col>percentage of variance</th><th scope=col>cumulative percentage of variance</th></tr>
</thead>
<tbody>
	<tr><th scope=row>comp 1</th><td>1.0247604</td><td>51.23802</td><td> 51.23802</td></tr>
	<tr><th scope=row>comp 2</th><td>0.9752396</td><td>48.76198</td><td>100.00000</td></tr>
</tbody>
</table>



Unfortunately, we can see that the PCA could in this case only work with two variables.
This will lead to poor analysis, and we may choose another technique to get relevant information from our data

## 4. PCA Individuals factor map


```R
plot(acp, cex=0.6, choix="ind", invisible="quali", label="none")
```


    
![png](output_12_0.png)
    


## 5. PCA variables factor map


```R
plot(acp, cex=0.6, choix="var")
```


    
![png](output_14_0.png)
    

