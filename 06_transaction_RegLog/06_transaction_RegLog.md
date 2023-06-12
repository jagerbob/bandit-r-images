  # Transactions Logistic Regression

## 1. Import required libraries


```R
if(!is.element("devtools", installed.packages()[,1])){
  install.packages("devtools")
}

if(!is.element("DBI", installed.packages()[,1])){
  devtools::install_github("rstats-db/DBI")
}

if(!is.element("RPostgres", installed.packages()[,1])){
  devtools::install_github("rstats-db/RPostgres")
}

library(RPostgres)
library(DBI)
```

## 2. Load data from Database using SQL query


```R
con <- dbConnect(RPostgres::Postgres(), host='localhost', port='5433', dbname='bandit-nbs', user='bandit', password="orF9YuPWVajej5tC6cfiro94BoxrzsoE")
transactions <- dbGetQuery(con, 'SELECT * FROM "Transaction"')
rownames(transactions) <- transactions$Id

transactions[,"DebitBank"] = factor(transactions[,"DebitBank"])
transactions[,"CreditBank"] = factor(transactions[,"CreditBank"])
transactions[,"ClientGender"] = factor(transactions[,"ClientGender"])
transactions[,"ClientMaritalStatus"] = factor(transactions[,"ClientMaritalStatus"])
transactions[,"MerchantActivity"] = factor(transactions[,"MerchantActivity"])
transactions[,"AuthenticationMethod"] = factor(transactions[,"AuthenticationMethod"])

summary(transactions)
```


          Id                        DebitBank                CreditBank  
     Length:7980        bandit-donsaluste:2423   bandit-donsaluste:2421  
     Class :character   bandit-picsou    :1566   bandit-picsou    :1849  
     Mode  :character   bandit-profit    :3197   bandit-profit    :2544  
                        bandit-radinou   : 794   bandit-radinou   :1166  
                                                                         
                                                                         
                                                                         
       ClientId         ClientGender  ClientBirthDate                   
     Length:7980        Female:4135   Min.   :1930-01-01 19:31:36.0000  
     Class :character   Male  :3845   1st Qu.:1948-03-27 04:38:18.2500  
     Mode  :character                 Median :1968-12-05 14:08:24.5000  
                                      Mean   :1967-12-18 04:27:29.8788  
                                      3rd Qu.:1986-10-16 14:52:00.2500  
                                      Max.   :2005-12-28 06:31:49.0000  
                                      NA's   :2                         
       ClientAge     ClientMaritalStatus ClientMonthlySalary
     Min.   :18.00   Divorced: 953       Min.   :1208       
     1st Qu.:34.00   Married :3515       1st Qu.:3520       
     Median :50.00   Single  :2926       Median :4271       
     Mean   :49.93   Widowed : 586       Mean   :4137       
     3rd Qu.:64.00                       3rd Qu.:4847       
     Max.   :99.00                       Max.   :6392       
                                                            
     TransactionDate                       MerchantActivity AuthenticationMethod
     Min.   :2022-12-31 23:01:21.96   Brass Animals:1515    EMAIL:1630          
     1st Qu.:2023-02-05 13:02:13.25   Dachshunds   :3753    ID   :1877          
     Median :2023-03-12 17:07:34.56   Trading Cards:2712    OTP  :2906          
     Mean   :2023-03-12 01:26:57.95                         SMS  :1567          
     3rd Qu.:2023-04-15 15:11:12.55                                             
     Max.   :2023-05-19 05:52:42.13                                             
                                                                                
     TransferredAmount
     Min.   : 12.0    
     1st Qu.: 56.0    
     Median :120.0    
     Mean   :224.8    
     3rd Qu.:396.0    
     Max.   :653.0    
                      


## 3. Logistic regression model creation


```R
# Remove disturbing values
transactions_sample <- transactions
transactions_sample$ClientId <- NULL
transactions_sample$Id <- NULL
```


```R
model<-glm(MerchantActivity~., data=transactions_sample, family=binomial)
summary(model)
```


    
    Call:
    glm(formula = MerchantActivity ~ ., family = binomial, data = transactions_sample)
    
    Coefficients:
                                 Estimate Std. Error z value Pr(>|z|)    
    (Intercept)                -5.377e+00  1.620e+01  -0.332   0.7400    
    DebitBankbandit-picsou      6.839e-02  9.632e-02   0.710   0.4777    
    DebitBankbandit-profit      2.051e-02  8.032e-02   0.255   0.7985    
    DebitBankbandit-radinou     3.493e-03  1.209e-01   0.029   0.9770    
    CreditBankbandit-picsou    -1.574e-01  9.539e-02  -1.651   0.0988 .  
    CreditBankbandit-profit     9.492e-02  8.319e-02   1.141   0.2538    
    CreditBankbandit-radinou    2.028e-01  1.208e-01   1.679   0.0932 .  
    ClientGenderMale            2.916e-03  6.671e-02   0.044   0.9651    
    ClientBirthDate             3.920e-11  4.766e-11   0.822   0.4108    
    ClientAge                   1.908e-03  3.724e-03   0.512   0.6083    
    ClientMaritalStatusMarried  7.158e-01  1.013e-01   7.068 1.57e-12 ***
    ClientMaritalStatusSingle   2.169e+00  1.281e-01  16.929  < 2e-16 ***
    ClientMaritalStatusWidowed -1.217e+00  1.458e-01  -8.343  < 2e-16 ***
    ClientMonthlySalary        -4.928e-05  7.074e-05  -0.697   0.4860    
    TransactionDate             2.729e-09  9.649e-09   0.283   0.7773    
    AuthenticationMethodID      2.924e-02  1.063e-01   0.275   0.7833    
    AuthenticationMethodOTP     7.493e-02  9.697e-02   0.773   0.4397    
    AuthenticationMethodSMS    -1.068e-02  1.114e-01  -0.096   0.9236    
    TransferredAmount           9.060e-03  3.187e-04  28.424  < 2e-16 ***
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    (Dispersion parameter for binomial family taken to be 1)
    
        Null deviance: 7752.9  on 7977  degrees of freedom
    Residual deviance: 5685.0  on 7959  degrees of freedom
      (2 observations deleted due to missingness)
    AIC: 5723
    
    Number of Fisher Scoring iterations: 6
    



```R
model<-glm(AuthenticationMethod~., data=transactions_sample, family=binomial)
summary(model)
```


    
    Call:
    glm(formula = AuthenticationMethod ~ ., family = binomial, data = transactions_sample)
    
    Coefficients:
                                    Estimate Std. Error z value Pr(>|z|)    
    (Intercept)                    4.207e+00  1.387e+01   0.303   0.7616    
    DebitBankbandit-picsou        -1.190e-01  8.157e-02  -1.459   0.1445    
    DebitBankbandit-profit        -1.137e-02  6.916e-02  -0.164   0.8694    
    DebitBankbandit-radinou       -5.615e-02  1.030e-01  -0.545   0.5858    
    CreditBankbandit-picsou        1.087e-01  7.961e-02   1.366   0.1720    
    CreditBankbandit-profit       -4.351e-02  7.675e-02  -0.567   0.5708    
    CreditBankbandit-radinou       1.579e-01  9.197e-02   1.717   0.0859 .  
    ClientGenderMale              -5.953e-02  5.689e-02  -1.046   0.2953    
    ClientBirthDate                2.269e-11  4.094e-11   0.554   0.5795    
    ClientAge                      3.214e-02  3.585e-03   8.966   <2e-16 ***
    ClientMaritalStatusMarried     6.965e-02  9.790e-02   0.711   0.4768    
    ClientMaritalStatusSingle      1.898e-01  1.112e-01   1.707   0.0878 .  
    ClientMaritalStatusWidowed    -1.948e-01  1.646e-01  -1.184   0.2366    
    ClientMonthlySalary            5.731e-05  6.110e-05   0.938   0.3483    
    TransactionDate               -2.801e-09  8.259e-09  -0.339   0.7345    
    MerchantActivityDachshunds    -1.140e-01  1.754e-01  -0.650   0.5159    
    MerchantActivityTrading Cards  4.081e-02  9.237e-02   0.442   0.6586    
    TransferredAmount              3.847e-04  5.170e-04   0.744   0.4569    
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    (Dispersion parameter for binomial family taken to be 1)
    
        Null deviance: 8078.9  on 7977  degrees of freedom
    Residual deviance: 7711.7  on 7960  degrees of freedom
      (2 observations deleted due to missingness)
    AIC: 7747.7
    
    Number of Fisher Scoring iterations: 4
    



```R
model<-glm(ClientMaritalStatus~., data=transactions_sample, family=binomial)
summary(model)
```


    
    Call:
    glm(formula = ClientMaritalStatus ~ ., family = binomial, data = transactions_sample)
    
    Coefficients:
                                    Estimate Std. Error z value Pr(>|z|)    
    (Intercept)                    9.215e+00  1.734e+01   0.531 0.595080    
    DebitBankbandit-picsou         3.201e-02  1.072e-01   0.299 0.765283    
    DebitBankbandit-profit        -1.964e-01  8.536e-02  -2.300 0.021420 *  
    DebitBankbandit-radinou       -7.066e-02  1.325e-01  -0.533 0.593751    
    CreditBankbandit-picsou        4.157e-01  1.081e-01   3.845 0.000121 ***
    CreditBankbandit-profit        1.428e-01  8.361e-02   1.707 0.087732 .  
    CreditBankbandit-radinou       5.320e-01  1.396e-01   3.811 0.000138 ***
    ClientGenderMale              -1.642e-03  7.123e-02  -0.023 0.981606    
    ClientBirthDate               -2.959e-11  5.145e-11  -0.575 0.565160    
    ClientAge                      1.042e-02  3.532e-03   2.951 0.003167 ** 
    ClientMonthlySalary           -6.967e-04  7.583e-05  -9.189  < 2e-16 ***
    TransactionDate               -3.105e-09  1.033e-08  -0.301 0.763622    
    MerchantActivityDachshunds    -4.817e-01  1.916e-01  -2.514 0.011941 *  
    MerchantActivityTrading Cards  1.115e+00  1.190e-01   9.366  < 2e-16 ***
    AuthenticationMethodID         8.689e-03  1.121e-01   0.078 0.938199    
    AuthenticationMethodOTP       -6.759e-02  1.031e-01  -0.655 0.512191    
    AuthenticationMethodSMS        2.528e-01  1.309e-01   1.931 0.053477 .  
    TransferredAmount              1.314e-03  5.769e-04   2.278 0.022701 *  
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    (Dispersion parameter for binomial family taken to be 1)
    
        Null deviance: 5837.3  on 7977  degrees of freedom
    Residual deviance: 5376.8  on 7960  degrees of freedom
      (2 observations deleted due to missingness)
    AIC: 5412.8
    
    Number of Fisher Scoring iterations: 5
    



```R
model<-glm(CreditBank~., data=transactions_sample, family=binomial)
summary(model)
```


    
    Call:
    glm(formula = CreditBank ~ ., family = binomial, data = transactions_sample)
    
    Coefficients:
                                    Estimate Std. Error z value Pr(>|z|)    
    (Intercept)                    9.362e+00  1.197e+01   0.782 0.434092    
    DebitBankbandit-picsou         7.032e-02  7.137e-02   0.985 0.324515    
    DebitBankbandit-profit         4.235e-02  5.898e-02   0.718 0.472731    
    DebitBankbandit-radinou       -4.811e-05  8.944e-02  -0.001 0.999571    
    ClientGenderMale              -1.791e-02  4.918e-02  -0.364 0.715795    
    ClientBirthDate                3.559e-11  3.534e-11   1.007 0.313927    
    ClientAge                     -3.493e-03  2.705e-03  -1.291 0.196564    
    ClientMaritalStatusMarried     1.883e-01  7.689e-02   2.449 0.014318 *  
    ClientMaritalStatusSingle      3.095e-01  9.185e-02   3.369 0.000755 ***
    ClientMaritalStatusWidowed     4.128e-01  1.202e-01   3.434 0.000595 ***
    ClientMonthlySalary           -1.871e-04  5.187e-05  -3.606 0.000311 ***
    TransactionDate               -4.676e-09  7.129e-09  -0.656 0.511843    
    MerchantActivityDachshunds     2.509e-01  1.513e-01   1.659 0.097147 .  
    MerchantActivityTrading Cards  2.753e-03  7.777e-02   0.035 0.971764    
    AuthenticationMethodID         3.029e-02  7.812e-02   0.388 0.698201    
    AuthenticationMethodOTP        2.681e-02  7.060e-02   0.380 0.704081    
    AuthenticationMethodSMS        2.570e-02  8.000e-02   0.321 0.748021    
    TransferredAmount             -4.638e-04  4.482e-04  -1.035 0.300696    
    ---
    Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    
    (Dispersion parameter for binomial family taken to be 1)
    
        Null deviance: 9789.9  on 7977  degrees of freedom
    Residual deviance: 9662.1  on 7960  degrees of freedom
      (2 observations deleted due to missingness)
    AIC: 9698.1
    
    Number of Fisher Scoring iterations: 4
    

