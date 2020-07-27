---
title: "Final Project: Investigating Thyroid Function"
author: "Dashyanng Kachru"
date: "5/14/2020"
output: 
  html_document:
    keep_md: true
---
    

         
# Dataset variables:     
     
subject_id : Unique identifier which specifies an individual patient   
hadm_id : Represents a single patient’s admission to the hospital. It is possible to have duplicate subject_id, indicating that a single patient had multiple admissions to the hospital     
gender : Sex of the patient    
dob : Date of birth of the given patient. Patients who are older than 89 years old at any time have had their date of birth shifted to obscure their age and comply with HIPAA   
itemid : Associated with lab measurements   
value : Contains the value measured for the concept identified by the itemid   
valuenum : Contains the same data as "value" variable in a numeric format. If this data is not numeric, valuenum is null   
valueuom : Unit of measurement for the value   
charttime : Records the time at which an observation was made, and is usually the closest proxy to the time the data was actually measured  
label : Describes the concept which is represented by the itemid   
fluid : Describes the substance on which the measurement was made   
icd9_list : Contains the actual code corresponding to the diagnosis assigned to the patient for the given row   
icd9_short_list : Provide a brief definition for the given diagnosis code   
icd9_long_list : Provide a slighly longer definition for the given diagnosis code   
drug_list : Drug prescribed to the patient   
drug_name : Representation of the drug prescribed to the patient   
generic_drug_name : Representation of the drug prescribed to the patient   
drug_type : Provides the type of drug prescribed   
formulary : Provide a representation of the drug in a coding system   
ndc : Provide a representation of the drug as in the National Drug Code   
gsn : Provide a representation of the drug as in the Generic Sequence Number   
    
# Loading Libraries     

```r
library(dplyr)
```

```
## Warning: package 'dplyr' was built under R version 3.6.3
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.6.3
```

```r
library(pscl)
```

```
## Warning: package 'pscl' was built under R version 3.6.3
```

```
## Classes and Methods for R developed in the
## Political Science Computational Laboratory
## Department of Political Science
## Stanford University
## Simon Jackman
## hurdle and zeroinfl functions by Achim Zeileis
```

```r
library(tidyverse)
```

```
## Warning: package 'tidyverse' was built under R version 3.6.3
```

```
## -- Attaching packages -------------------------------------------------------------------------- tidyverse 1.3.0 --
```

```
## v tibble  3.0.1     v purrr   0.3.4
## v tidyr   1.0.2     v stringr 1.4.0
## v readr   1.3.1     v forcats 0.5.0
```

```
## Warning: package 'tibble' was built under R version 3.6.3
```

```
## Warning: package 'tidyr' was built under R version 3.6.3
```

```
## Warning: package 'purrr' was built under R version 3.6.3
```

```
## Warning: package 'forcats' was built under R version 3.6.3
```

```
## -- Conflicts ----------------------------------------------------------------------------- tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
library(rpart.plot)
```

```
## Warning: package 'rpart.plot' was built under R version 3.6.3
```

```
## Loading required package: rpart
```

```r
library(pROC)
```

```
## Warning: package 'pROC' was built under R version 3.6.3
```

```
## Type 'citation("pROC")' for a citation.
```

```
## 
## Attaching package: 'pROC'
```

```
## The following objects are masked from 'package:stats':
## 
##     cov, smooth, var
```

```r
library(caret)
```

```
## Warning: package 'caret' was built under R version 3.6.3
```

```
## Loading required package: lattice
```

```
## 
## Attaching package: 'caret'
```

```
## The following object is masked from 'package:purrr':
## 
##     lift
```

```r
library(plyr)
```

```
## Warning: package 'plyr' was built under R version 3.6.3
```

```
## ------------------------------------------------------------------------------
```

```
## You have loaded plyr after dplyr - this is likely to cause problems.
## If you need functions from both plyr and dplyr, please load plyr first, then dplyr:
## library(plyr); library(dplyr)
```

```
## ------------------------------------------------------------------------------
```

```
## 
## Attaching package: 'plyr'
```

```
## The following object is masked from 'package:purrr':
## 
##     compact
```

```
## The following objects are masked from 'package:dplyr':
## 
##     arrange, count, desc, failwith, id, mutate, rename, summarise,
##     summarize
```

```r
library(corrplot)
```

```
## corrplot 0.84 loaded
```

```r
library(taRifx)
```

```
## Warning: package 'taRifx' was built under R version 3.6.3
```

```
## 
## Attaching package: 'taRifx'
```

```
## The following object is masked from 'package:purrr':
## 
##     rep_along
```

```
## The following objects are masked from 'package:dplyr':
## 
##     between, distinct, first, last
```

```r
library(Deducer)
```

```
## Warning: package 'Deducer' was built under R version 3.6.3
```

```
## Loading required package: JGR
```

```
## Warning: package 'JGR' was built under R version 3.6.3
```

```
## Loading required package: rJava
```

```
## Loading required package: JavaGD
```

```
## Warning: package 'JavaGD' was built under R version 3.6.3
```

```
## 
## Please type JGR() to launch console. Platform specific launchers (.exe and .app) can also be obtained at http://www.rforge.net/JGR/files/.
```

```
## Loading required package: car
```

```
## Warning: package 'car' was built under R version 3.6.3
```

```
## Loading required package: carData
```

```
## 
## Attaching package: 'car'
```

```
## The following object is masked from 'package:purrr':
## 
##     some
```

```
## The following object is masked from 'package:dplyr':
## 
##     recode
```

```
## Loading required package: MASS
```

```
## 
## Attaching package: 'MASS'
```

```
## The following object is masked from 'package:dplyr':
## 
##     select
```

```
## Registered S3 methods overwritten by 'lme4':
##   method                          from
##   cooks.distance.influence.merMod car 
##   influence.merMod                car 
##   dfbeta.influence.merMod         car 
##   dfbetas.influence.merMod        car
```

```
## Registered S3 method overwritten by 'Deducer':
##   method          from  
##   sort.data.frame taRifx
```

```
## 
## 
## Note Non-JGR console detected:
## 	Deducer is best used from within JGR (http://jgr.markushelbig.org/).
## 	To Bring up GUI dialogs, type deducer().
```

```
## 
## Attaching package: 'Deducer'
```

```
## The following object is masked from 'package:taRifx':
## 
##     sort.data.frame
```

```r
library(rattle)
```

```
## Warning: package 'rattle' was built under R version 3.6.3
```

```
## Rattle: A free graphical interface for data science with R.
## Version 5.3.0 Copyright (c) 2006-2018 Togaware Pty Ltd.
## Type 'rattle()' to shake, rattle, and roll your data.
```

```r
library(RColorBrewer)
logisticPseudoR2s <- function(LogModel) {
  
  dev <- LogModel$deviance 
  nullDev <- LogModel$null.deviance 
  modelN <-  length(LogModel$fitted.values)
  R.l <-  1 -  dev / nullDev
  R.cs <- 1- exp ( -(nullDev - dev) / modelN)
  R.n <- R.cs / ( 1 - ( exp (-(nullDev / modelN))))
  cat("Pseudo R^2 for logistic regression\n")
  cat("Hosmer and Lemeshow R^2  ", round(R.l, 3), "\n")
  cat("Cox and Snell R^2        ", round(R.cs, 3), "\n")
  cat("Nagelkerke R^2           ", round(R.n, 3),    "\n")
}
```
     
# Loading data    

```r
setwd("C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analytics")
thyroid_df <- read.csv("Thyroid_Dataset.csv")
head(thyroid_df,5)
```

```
##   subject_id hadm_id gender                 dob itemid value valuenum valueuom
## 1          3  145834      M 2025-04-11 00:00:00  50993   3.4      3.4    uU/ML
## 2          3  145834      M 2025-04-11 00:00:00  50994   6.6      6.6    uG/DL
## 3          4  185777      F 2143-05-12 00:00:00  50993  0.35     0.35   uIU/mL
## 4         13  143045      F 2127-02-27 00:00:00  50993   1.2      1.2   uIU/mL
## 5         19  109235      M 1808-08-05 00:00:00  50993   1.5      1.5   uIU/mL
##             charttime                       label fluid
## 1 2101-10-21 13:00:00 Thyroid Stimulating Hormone Blood
## 2 2101-10-21 13:00:00              Thyroxine (T4) Blood
## 3 2191-03-16 05:42:00 Thyroid Stimulating Hormone Blood
## 4 2167-01-09 07:11:00 Thyroid Stimulating Hormone Blood
## 5 2108-08-05 15:00:00 Thyroid Stimulating Hormone Blood
##                                          icd9_list
## 1   0389,78559,5849,4275,41071,4280,6826,4254,2639
## 2   0389,78559,5849,4275,41071,4280,6826,4254,2639
## 3    042,1363,7994,2763,7907,5715,04111,V090,E9317
## 4                       41401,4111,25000,4019,2720
## 5 80502,5990,5964,E8809,8220,73300,2948,4019,44321
##                                                                                                                                                                                    icd9_short_list
## 1                   Septicemia NOS,Shock w/o trauma NEC,Acute kidney failure NOS,Cardiac arrest,Subendo infarct, initial,CHF NOS,Cellulitis of leg,Prim cardiomyopathy NEC,Protein-cal malnutr NOS
## 2                   Septicemia NOS,Shock w/o trauma NEC,Acute kidney failure NOS,Cardiac arrest,Subendo infarct, initial,CHF NOS,Cellulitis of leg,Prim cardiomyopathy NEC,Protein-cal malnutr NOS
## 3                               Human immuno virus dis,Pneumocystosis,Cachexia,Alkalosis,Bacteremia,Cirrhosis of liver NOS,Mth sus Stph aur els/NOS,Inf mcrg rstn pncllins,Adv eff antiviral drugs
## 4                                                                                Crnry athrscl natve vssl,Intermed coronary synd,DMII wo cmp nt st uncntr,Hypertension NOS,Pure hypercholesterolem
## 5 Fx c2 vertebra-closed,Urin tract infection NOS,Atony of bladder,Fall on stair/step NEC,Fracture patella-closed,Osteoporosis NOS,Mental disor NEC oth dis,Hypertension NOS,Dissect carotid artery
##                                                                                                                                                                                                                                                                                                                                                     icd9_long_list
## 1                                        Unspecified septicemia,Other shock without mention of trauma,Acute kidney failure, unspecified,Cardiac arrest,Subendocardial infarction, initial episode of care,Congestive heart failure, unspecified,Cellulitis and abscess of leg, except foot,Other primary cardiomyopathies,Unspecified protein-calorie malnutrition
## 2                                        Unspecified septicemia,Other shock without mention of trauma,Acute kidney failure, unspecified,Cardiac arrest,Subendocardial infarction, initial episode of care,Congestive heart failure, unspecified,Cellulitis and abscess of leg, except foot,Other primary cardiomyopathies,Unspecified protein-calorie malnutrition
## 3 Human immunodeficiency virus [HIV] disease,Pneumocystosis,Cachexia,Alkalosis,Bacteremia,Cirrhosis of liver without mention of alcohol,Methicillin susceptible Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Infection with microorganisms resistant to penicillins,Antiviral drugs causing adverse effects in therapeutic use
## 4                                                                                                        Coronary atherosclerosis of native coronary artery,Intermediate coronary syndrome,Diabetes mellitus without mention of complication, type II or unspecified type, not stated as uncontrolled,Unspecified essential hypertension,Pure hypercholesterolemia
## 5          Closed fracture of second cervical vertebra,Urinary tract infection, site not specified,Atony of bladder,Accidental fall on or from other stairs or steps,Closed fracture of patella,Osteoporosis, unspecified,Other persistent mental disorders due to conditions classified elsewhere,Unspecified essential hypertension,Dissection of carotid artery
##                                                                                                                                                                                      drug_list
## 1                                                                                                                                                                                         NULL
## 2                                                                                                                                                                                         NULL
## 3 Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium
## 4                                                                                                                                                                                         NULL
## 5                                                                                                                                                                                         NULL
##                                                                                                                                                                                      drug_name
## 1                                                                                                                                                                                         NULL
## 2                                                                                                                                                                                         NULL
## 3 Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium
## 4                                                                                                                                                                                         NULL
## 5                                                                                                                                                                                         NULL
##                                                                                                                                                                              generic_drug_name
## 1                                                                                                                                                                                         NULL
## 2                                                                                                                                                                                         NULL
## 3 Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium
## 4                                                                                                                                                                                         NULL
## 5                                                                                                                                                                                         NULL
##                                      drug_type
## 1                                         NULL
## 2                                         NULL
## 3 MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN
## 4                                         NULL
## 5                                         NULL
##                                                        formulary
## 1                                                           NULL
## 2                                                           NULL
## 3 LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25
## 4                                                           NULL
## 5                                                           NULL
##                                                                                                           ndc
## 1                                                                                                        NULL
## 2                                                                                                        NULL
## 3 00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113
## 4                                                                                                        NULL
## 5                                                                                                        NULL
##                                                              gsn
## 1                                                           NULL
## 2                                                           NULL
## 3 006648,006648,006648,006648,006648,006648,006648,006648,006648
## 4                                                           NULL
## 5                                                           NULL
```

```r
str(thyroid_df)
```

```
## 'data.frame':	18132 obs. of  21 variables:
##  $ subject_id       : int  3 3 4 13 19 22 34 34 34 38 ...
##  $ hadm_id          : int  145834 145834 185777 143045 109235 165315 115799 144319 144319 185910 ...
##  $ gender           : Factor w/ 2 levels "F","M": 2 2 1 1 2 1 2 2 2 2 ...
##  $ dob              : Factor w/ 9850 levels "1800-07-02 00:00:00",..: 892 892 9338 8593 71 8824 592 592 592 5572 ...
##  $ itemid           : int  50993 50994 50993 50993 50993 50993 50993 50993 50995 50993 ...
##  $ value            : Factor w/ 545 levels ".07",".08",".09",..: 368 445 108 183 187 323 325 488 182 187 ...
##  $ valuenum         : Factor w/ 492 levels "0.02","0.021",..: 338 413 91 160 163 294 296 454 159 163 ...
##  $ valueuom         : Factor w/ 10 levels "IU/mL","ng/dl",..: 10 8 9 9 9 10 9 9 3 9 ...
##  $ charttime        : Factor w/ 14120 levels "2100-07-05 04:12:00",..: 176 176 12650 9255 1022 13342 11992 12637 12638 9191 ...
##  $ label            : Factor w/ 6 levels "Thyroglobulin",..: 3 4 3 3 3 3 3 3 5 3 ...
##  $ fluid            : Factor w/ 1 level "Blood": 1 1 1 1 1 1 1 1 1 1 ...
##  $ icd9_list        : Factor w/ 12155 levels "0031,51881,5566,2535,72888,5845,78552,5070,2762,9982,2851,00845,5770,570,1890,34839,42832,9972,99592,03819,3030"| __truncated__,..: 1069 1069 1125 4025 10477 11251 3726 5343 5343 9853 ...
##  $ icd9_short_list  : Factor w/ 12155 levels "35-36 comp wks gestation,NB obsrv suspct infect,Need prphyl vc vrl hepat,Single lb in-hosp w cs,Preterm NEC 250"| __truncated__,..: 10609 10609 6303 4553 5752 9231 11322 3106 3106 7454 ...
##  $ icd9_long_list   : Factor w/ 12155 levels "35-36 completed weeks of gestation,Observation for suspected infectious condition,Need for prophylactic vaccina"| __truncated__,..: 11620 11620 5593 4446 3752 9513 10717 8429 8429 12082 ...
##  $ drug_list        : Factor w/ 171 levels "Levothyroxine Sodium,Levothyroxine Sodium",..: 171 171 8 171 171 171 7 7 7 171 ...
##  $ drug_name        : Factor w/ 170 levels "Levothyroxine Sodium,Levothyroxine Sodium",..: 170 170 8 170 170 170 7 7 7 170 ...
##  $ generic_drug_name: Factor w/ 200 levels "*NF* Levothyroxine Sodium Brand Name,Levothyroxine Sodium,*NF* Levothyroxine Sodium Brand Name,Levothyroxine So"| __truncated__,..: 200 200 9 200 200 200 8 8 8 200 ...
##  $ drug_type        : Factor w/ 148 levels "MAIN,MAIN","MAIN,MAIN,MAIN",..: 148 148 8 148 148 148 7 7 7 148 ...
##  $ formulary        : Factor w/ 1427 levels "CYTO5T,LEVO88,CYTO5T,CYTO5T,CYTO5T,LEVO88,CYTO5T,CYTO5T,CYTO5T,LEVO88,CYTO5T,CYTO5T,CYTO5T,LEVO88,CYTO5T,CYTO5T"| __truncated__,..: 1427 1427 908 1427 1427 1427 9 1284 1284 1427 ...
##  $ ndc              : Factor w/ 1519 levels "00007521006,00007521006,00007521006,00007521006,00007521006,55390088010,00007521006,55390088010,55390088010,553"| __truncated__,..: 1519 1519 109 1519 1519 1519 755 490 490 1519 ...
##  $ gsn              : Factor w/ 1426 levels "006645,006645,006645,006645,006645,006645",..: 1426 1426 322 1426 1426 1426 898 709 709 1426 ...
```
       
# Exploratory Data Analysis       
    
#### Checking if data needs to be manipulated:         

```r
summary(thyroid_df)
```

```
##    subject_id       hadm_id       gender                    dob       
##  Min.   :    3   Min.   :100001   F:9450   2078-12-05 00:00:00:   40  
##  1st Qu.:12076   1st Qu.:124705   M:8682   2052-02-14 00:00:00:   28  
##  Median :24633   Median :149789            2130-07-08 00:00:00:   21  
##  Mean   :34134   Mean   :149679            2066-10-13 00:00:00:   18  
##  3rd Qu.:55085   3rd Qu.:174662            2071-06-27 00:00:00:   18  
##  Max.   :99957   Max.   :199993            2104-04-12 00:00:00:   17  
##                                            (Other)            :17990  
##      itemid          value          valuenum        valueuom    
##  Min.   :50991   1.1    :  782   1.1    :  782   uIU/mL :10803  
##  1st Qu.:50993   1.2    :  687   1.2    :  688   ng/dL  : 3747  
##  Median :50993   1.3    :  609   1.3    :  609   ug/dL  : 1555  
##  Mean   :50994   1.4    :  535   1.4    :  535   uU/ML  : 1259  
##  3rd Qu.:50994   1.5    :  502   1.5    :  502   ng/dl  :  336  
##  Max.   :51001   1.0    :  428   1      :  428   uG/DL  :  188  
##                  (Other):14589   (Other):14588   (Other):  244  
##                charttime                               label      
##  2129-03-12 13:40:00:    6   Thyroglobulin                :   47  
##  2153-04-15 21:25:00:    5   Thyroid Peroxidase Antibodies:   65  
##  2160-01-30 15:50:00:    5   Thyroid Stimulating Hormone  :12063  
##  2195-10-20 03:21:00:    5   Thyroxine (T4)               : 1743  
##  2104-05-22 20:31:00:    4   Thyroxine (T4), Free         : 3031  
##  2105-06-18 16:15:00:    4   Triiodothyronine (T3)        : 1183  
##  (Other)            :18103                                        
##    fluid      
##  Blood:18132  
##               
##               
##               
##               
##               
##               
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            icd9_list    
##  20280,20280,20280,20280,5845,5845,5845,5845,3363,3363,3363,3363,41519,41519,41519,41519,57400,57400,57400,57400,6822,6822,6822,6822,1363,1363,1363,1363,03843,03843,03843,03843,99591,99591,99591,99591,59080,59080,59080,59080,34830,34830,34830,34830,3220,3220,3220,3220,1125,1125,1125,1125,2841,2841,2841,2841,5781,5781,5781,5781,99811,99811,99811,99811,43491,43491,43491,43491,591,591,591,591,2761,2761,2761,2761,2930,2930,2930,2930,5848,5848,5848,5848,28800,28800,28800,28800,52809,52809,52809,52809,78061,78061,78061,78061,E9331,E9331,E9331,E9331,99931,99931,99931,99931,04112,04112,04112,04112,E8798,E8798,E8798,E8798,V6441,V6441,V6441,V6441,78559,78559,78559,78559,E8786,E8786,E8786,E8786,E9342,E9342,E9342,E9342,5933,5933,5933,5933,94224,94224,94224,94224,E8792,E8792,E8792,E8792,70705,70705,70705,70705,70722,70722,70722,70722,40390,40390,40390,40390,27542,27542,27542,27542:    6  
##  24291,2768,28529                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               :    6  
##  34510,5070,99731,51881,7802,E9380,24200,30423,V1581,V642,3051                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  :    6  
##  41071,42731,00845,27651,2761,44024,70714,138,2720,4019,53081,2449,412,25000                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    :    6  
##  4414,4414,4414,4233,4233,4233,42822,42822,42822,4239,4239,4239,2449,2449,2449,2948,2948,2948,45829,45829,45829,42789,42789,42789,4148,4148,4148,4019,4019,4019,3051,3051,3051,2809,2809,2809,41401,41401,41401,4280,4280,4280,4928,4928,4928,4439,4439,4439,412,412,412,V1581,V1581,V1581,V1301,V1301,V1301,V1083,V1083,V1083                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  :    6  
##  4870,41071,4280,42832,27651,5859,42731,2724,4019,25050,36201,25040,58381,V4581,51889,41401,2411,2270                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           :    6  
##  (Other)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        :18096  
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 icd9_short_list 
##  Abdom aortic aneurysm,Abdom aortic aneurysm,Abdom aortic aneurysm,Cardiac tamponade,Cardiac tamponade,Cardiac tamponade,Chr systolic hrt failure,Chr systolic hrt failure,Chr systolic hrt failure,Pericardial disease NOS,Pericardial disease NOS,Pericardial disease NOS,Hypothyroidism NOS,Hypothyroidism NOS,Hypothyroidism NOS,Mental disor NEC oth dis,Mental disor NEC oth dis,Mental disor NEC oth dis,Iatrogenc hypotnsion NEC,Iatrogenc hypotnsion NEC,Iatrogenc hypotnsion NEC,Cardiac dysrhythmias NEC,Cardiac dysrhythmias NEC,Cardiac dysrhythmias NEC,Chr ischemic hrt dis NEC,Chr ischemic hrt dis NEC,Chr ischemic hrt dis NEC,Hypertension NOS,Hypertension NOS,Hypertension NOS,Tobacco use disorder,Tobacco use disorder,Tobacco use disorder,Iron defic anemia NOS,Iron defic anemia NOS,Iron defic anemia NOS,Crnry athrscl natve vssl,Crnry athrscl natve vssl,Crnry athrscl natve vssl,CHF NOS,CHF NOS,CHF NOS,Emphysema NEC,Emphysema NEC,Emphysema NEC,Periph vascular dis NOS,Periph vascular dis NOS,Periph vascular dis NOS,Old myocardial infarct,Old myocardial infarct,Old myocardial infarct,Hx of past noncompliance,Hx of past noncompliance,Hx of past noncompliance,Prsnl hst urnr dsrd calc,Prsnl hst urnr dsrd calc,Prsnl hst urnr dsrd calc,Hx-skin malignancy NEC,Hx-skin malignancy NEC,Hx-skin malignancy NEC                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               :    6  
##  Gen cnv epil w/o intr ep,Food/vomit pneumonitis,Ventltr assoc pneumonia,Acute respiratry failure,Syncope and collapse,Adv eff cns muscl depres,Tox dif goiter no crisis,Cocaine depend-remiss,Hx of past noncompliance,No proc/patient decision,Tobacco use disorder                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   :    6  
##  Influenza with pneumonia,Subendo infarct, initial,CHF NOS,Chr diastolic hrt fail,Dehydration,Chronic kidney dis NOS,Atrial fibrillation,Hyperlipidemia NEC/NOS,Hypertension NOS,DMII ophth nt st uncntrl,Diabetic retinopathy NOS,DMII renl nt st uncntrld,Nephritis NOS in oth dis,Aortocoronary bypass,Other lung disease NEC,Crnry athrscl natve vssl,Nontox multinodul goiter,Benign neoplasm adrenal                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              :    6  
##  Oth lymp unsp xtrndl org,Oth lymp unsp xtrndl org,Oth lymp unsp xtrndl org,Oth lymp unsp xtrndl org,Ac kidny fail, tubr necr,Ac kidny fail, tubr necr,Ac kidny fail, tubr necr,Ac kidny fail, tubr necr,Myelopathy in oth dis,Myelopathy in oth dis,Myelopathy in oth dis,Myelopathy in oth dis,Pulm embol/infarct NEC,Pulm embol/infarct NEC,Pulm embol/infarct NEC,Pulm embol/infarct NEC,Cholelith w ac cholecyst,Cholelith w ac cholecyst,Cholelith w ac cholecyst,Cholelith w ac cholecyst,Cellulitis of trunk,Cellulitis of trunk,Cellulitis of trunk,Cellulitis of trunk,Pneumocystosis,Pneumocystosis,Pneumocystosis,Pneumocystosis,Pseudomonas septicemia,Pseudomonas septicemia,Pseudomonas septicemia,Pseudomonas septicemia,Sepsis,Sepsis,Sepsis,Sepsis,Pyelonephritis NOS,Pyelonephritis NOS,Pyelonephritis NOS,Pyelonephritis NOS,Encephalopathy NOS,Encephalopathy NOS,Encephalopathy NOS,Encephalopathy NOS,Nonpyogenic meningitis,Nonpyogenic meningitis,Nonpyogenic meningitis,Nonpyogenic meningitis,Disseminated candidiasis,Disseminated candidiasis,Disseminated candidiasis,Disseminated candidiasis,Blood in stool,Blood in stool,Blood in stool,Blood in stool,Hemorrhage complic proc,Hemorrhage complic proc,Hemorrhage complic proc,Hemorrhage complic proc,Crbl art ocl NOS w infrc,Crbl art ocl NOS w infrc,Crbl art ocl NOS w infrc,Crbl art ocl NOS w infrc,Hydronephrosis,Hydronephrosis,Hydronephrosis,Hydronephrosis,Hyposmolality,Hyposmolality,Hyposmolality,Hyposmolality,Delirium d/t other cond,Delirium d/t other cond,Delirium d/t other cond,Delirium d/t other cond,Acute kidney failure NEC,Acute kidney failure NEC,Acute kidney failure NEC,Acute kidney failure NEC,Neutropenia NOS,Neutropenia NOS,Neutropenia NOS,Neutropenia NOS,Stomatits & mucosits NEC,Stomatits & mucosits NEC,Stomatits & mucosits NEC,Stomatits & mucosits NEC,Fever in other diseases,Fever in other diseases,Fever in other diseases,Fever in other diseases,Adv eff antineoplastic,Adv eff antineoplastic,Adv eff antineoplastic,Adv eff antineoplastic,Oth/uns inf-cen ven cath,Oth/uns inf-cen ven cath,Oth/uns inf-cen ven cath,Oth/uns inf-cen ven cath,MRSA elsewhere/NOS,MRSA elsewhere/NOS,MRSA elsewhere/NOS,MRSA elsewhere/NOS,Abn react-procedure NEC,Abn react-procedure NEC,Abn react-procedure NEC,Abn react-procedure NEC,Lap surg convert to open,Lap surg convert to open,Lap surg convert to open,Lap surg convert to open,Shock w/o trauma NEC,Shock w/o trauma NEC,Shock w/o trauma NEC,Shock w/o trauma NEC,Abn reac-organ rem NEC,Abn reac-organ rem NEC,Abn reac-organ rem NEC,Abn reac-organ rem NEC,Adv eff anticoagulants,Adv eff anticoagulants,Adv eff anticoagulants,Adv eff anticoagulants,Stricture of ureter,Stricture of ureter,Stricture of ureter,Stricture of ureter,2nd deg burn back,2nd deg burn back,2nd deg burn back,2nd deg burn back,Abn react-radiotherapy,Abn react-radiotherapy,Abn react-radiotherapy,Abn react-radiotherapy,Pressure ulcer, buttock,Pressure ulcer, buttock,Pressure ulcer, buttock,Pressure ulcer, buttock,Pressure ulcer, stage II,Pressure ulcer, stage II,Pressure ulcer, stage II,Pressure ulcer, stage II,Hy kid NOS w cr kid I-IV,Hy kid NOS w cr kid I-IV,Hy kid NOS w cr kid I-IV,Hy kid NOS w cr kid I-IV,Hypercalcemia,Hypercalcemia,Hypercalcemia,Hypercalcemia:    6  
##  Subendo infarct, initial,Atrial fibrillation,Int inf clstrdium dfcile,Dehydration,Hyposmolality,Ath ext ntv art gngrene,Ulcer of heel & midfoot,Late effect acute polio,Pure hypercholesterolem,Hypertension NOS,Esophageal reflux,Hypothyroidism NOS,Old myocardial infarct,DMII wo cmp nt st uncntr                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  :    6  
##  Thyrotox NOS w crisis,Hypopotassemia,Anemia-other chronic dis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          :    6  
##  (Other)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                :18096  
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      icd9_long_list 
##  Abdominal aneurysm without mention of rupture,Abdominal aneurysm without mention of rupture,Abdominal aneurysm without mention of rupture,Cardiac tamponade,Cardiac tamponade,Cardiac tamponade,Chronic systolic heart failure,Chronic systolic heart failure,Chronic systolic heart failure,Unspecified disease of pericardium,Unspecified disease of pericardium,Unspecified disease of pericardium,Unspecified acquired hypothyroidism,Unspecified acquired hypothyroidism,Unspecified acquired hypothyroidism,Other persistent mental disorders due to conditions classified elsewhere,Other persistent mental disorders due to conditions classified elsewhere,Other persistent mental disorders due to conditions classified elsewhere,Other iatrogenic hypotension,Other iatrogenic hypotension,Other iatrogenic hypotension,Other specified cardiac dysrhythmias,Other specified cardiac dysrhythmias,Other specified cardiac dysrhythmias,Other specified forms of chronic ischemic heart disease,Other specified forms of chronic ischemic heart disease,Other specified forms of chronic ischemic heart disease,Unspecified essential hypertension,Unspecified essential hypertension,Unspecified essential hypertension,Tobacco use disorder,Tobacco use disorder,Tobacco use disorder,Iron deficiency anemia, unspecified,Iron deficiency anemia, unspecified,Iron deficiency anemia, unspecified,Coronary atherosclerosis of native coronary artery,Coronary atherosclerosis of native coronary artery,Coronary atherosclerosis of native coronary artery,Congestive heart failure, unspecified,Congestive heart failure, unspecified,Congestive heart failure, unspecified,Other emphysema,Other emphysema,Other emphysema,Peripheral vascular disease, unspecified,Peripheral vascular disease, unspecified,Peripheral vascular disease, unspecified,Old myocardial infarction,Old myocardial infarction,Old myocardial infarction,Personal history of noncompliance with medical treatment, presenting hazards to health,Personal history of noncompliance with medical treatment, presenting hazards to health,Personal history of noncompliance with medical treatment, presenting hazards to health,Personal history of urinary calculi,Personal history of urinary calculi,Personal history of urinary calculi,Personal history of other malignant neoplasm of skin,Personal history of other malignant neoplasm of skin,Personal history of other malignant neoplasm of skin                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         :    6  
##  Generalized convulsive epilepsy, without mention of intractable epilepsy,Pneumonitis due to inhalation of food or vomitus,Ventilator associated pneumonia,Acute respiratory failure,Syncope and collapse,Central nervous system muscle-tone depressants causing adverse effects in therapeutic use,Toxic diffuse goiter without mention of thyrotoxic crisis or storm,Cocaine dependence, in remission,Personal history of noncompliance with medical treatment, presenting hazards to health,Surgical or other procedure not carried out because of patient's decision,Tobacco use disorder                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               :    6  
##  Influenza with pneumonia,Subendocardial infarction, initial episode of care,Congestive heart failure, unspecified,Chronic diastolic heart failure,Dehydration,Chronic kidney disease, unspecified,Atrial fibrillation,Other and unspecified hyperlipidemia,Unspecified essential hypertension,Diabetes with ophthalmic manifestations, type II or unspecified type, not stated as uncontrolled,Background diabetic retinopathy,Diabetes with renal manifestations, type II or unspecified type, not stated as uncontrolled,Nephritis and nephropathy, not specified as acute or chronic, in diseases classified elsewhere,Aortocoronary bypass status,Other diseases of lung, not elsewhere classified,Coronary atherosclerosis of native coronary artery,Nontoxic multinodular goiter,Benign neoplasm of adrenal gland                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    :    6  
##  Other malignant lymphomas, unspecified site, extranodal and solid organ sites,Other malignant lymphomas, unspecified site, extranodal and solid organ sites,Other malignant lymphomas, unspecified site, extranodal and solid organ sites,Other malignant lymphomas, unspecified site, extranodal and solid organ sites,Acute kidney failure with lesion of tubular necrosis,Acute kidney failure with lesion of tubular necrosis,Acute kidney failure with lesion of tubular necrosis,Acute kidney failure with lesion of tubular necrosis,Myelopathy in other diseases classified elsewhere,Myelopathy in other diseases classified elsewhere,Myelopathy in other diseases classified elsewhere,Myelopathy in other diseases classified elsewhere,Other pulmonary embolism and infarction,Other pulmonary embolism and infarction,Other pulmonary embolism and infarction,Other pulmonary embolism and infarction,Calculus of gallbladder with acute cholecystitis, without mention of obstruction,Calculus of gallbladder with acute cholecystitis, without mention of obstruction,Calculus of gallbladder with acute cholecystitis, without mention of obstruction,Calculus of gallbladder with acute cholecystitis, without mention of obstruction,Cellulitis and abscess of trunk,Cellulitis and abscess of trunk,Cellulitis and abscess of trunk,Cellulitis and abscess of trunk,Pneumocystosis,Pneumocystosis,Pneumocystosis,Pneumocystosis,Septicemia due to pseudomonas,Septicemia due to pseudomonas,Septicemia due to pseudomonas,Septicemia due to pseudomonas,Sepsis,Sepsis,Sepsis,Sepsis,Pyelonephritis, unspecified,Pyelonephritis, unspecified,Pyelonephritis, unspecified,Pyelonephritis, unspecified,Encephalopathy, unspecified,Encephalopathy, unspecified,Encephalopathy, unspecified,Encephalopathy, unspecified,Nonpyogenic meningitis,Nonpyogenic meningitis,Nonpyogenic meningitis,Nonpyogenic meningitis,Disseminated candidiasis,Disseminated candidiasis,Disseminated candidiasis,Disseminated candidiasis,Blood in stool,Blood in stool,Blood in stool,Blood in stool,Hemorrhage complicating a procedure,Hemorrhage complicating a procedure,Hemorrhage complicating a procedure,Hemorrhage complicating a procedure,Cerebral artery occlusion, unspecified with cerebral infarction,Cerebral artery occlusion, unspecified with cerebral infarction,Cerebral artery occlusion, unspecified with cerebral infarction,Cerebral artery occlusion, unspecified with cerebral infarction,Hydronephrosis,Hydronephrosis,Hydronephrosis,Hydronephrosis,Hyposmolality and/or hyponatremia,Hyposmolality and/or hyponatremia,Hyposmolality and/or hyponatremia,Hyposmolality and/or hyponatremia,Delirium due to conditions classified elsewhere,Delirium due to conditions classified elsewhere,Delirium due to conditions classified elsewhere,Delirium due to conditions classified elsewhere,Acute kidney failure with other specified pathological lesion in kidney,Acute kidney failure with other specified pathological lesion in kidney,Acute kidney failure with other specified pathological lesion in kidney,Acute kidney failure with other specified pathological lesion in kidney,Neutropenia, unspecified,Neutropenia, unspecified,Neutropenia, unspecified,Neutropenia, unspecified,Other stomatitis and mucositis (ulcerative),Other stomatitis and mucositis (ulcerative),Other stomatitis and mucositis (ulcerative),Other stomatitis and mucositis (ulcerative),Fever presenting with conditions classified elsewhere,Fever presenting with conditions classified elsewhere,Fever presenting with conditions classified elsewhere,Fever presenting with conditions classified elsewhere,Antineoplastic and immunosuppressive drugs causing adverse effects in therapeutic use,Antineoplastic and immunosuppressive drugs causing adverse effects in therapeutic use,Antineoplastic and immunosuppressive drugs causing adverse effects in therapeutic use,Antineoplastic and immunosuppressive drugs causing adverse effects in therapeutic use,Other and unspecified infection due to central venous catheter,Other and unspecified infection due to central venous catheter,Other and unspecified infection due to central venous catheter,Other and unspecified infection due to central venous catheter,Methicillin resistant Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Methicillin resistant Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Methicillin resistant Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Methicillin resistant Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Other specified procedures as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Other specified procedures as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Other specified procedures as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Other specified procedures as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Laparoscopic surgical procedure converted to open procedure,Laparoscopic surgical procedure converted to open procedure,Laparoscopic surgical procedure converted to open procedure,Laparoscopic surgical procedure converted to open procedure,Other shock without mention of trauma,Other shock without mention of trauma,Other shock without mention of trauma,Other shock without mention of trauma,Removal of other organ (partial) (total) causing abnormal patient reaction, or later complication, without mention of misadventure at time of operation,Removal of other organ (partial) (total) causing abnormal patient reaction, or later complication, without mention of misadventure at time of operation,Removal of other organ (partial) (total) causing abnormal patient reaction, or later complication, without mention of misadventure at time of operation,Removal of other organ (partial) (total) causing abnormal patient reaction, or later complication, without mention of misadventure at time of operation,Anticoagulants causing adverse effects in therapeutic use,Anticoagulants causing adverse effects in therapeutic use,Anticoagulants causing adverse effects in therapeutic use,Anticoagulants causing adverse effects in therapeutic use,Stricture or kinking of ureter,Stricture or kinking of ureter,Stricture or kinking of ureter,Stricture or kinking of ureter,Blisters, epidermal loss [second degree] of back [any part],Blisters, epidermal loss [second degree] of back [any part],Blisters, epidermal loss [second degree] of back [any part],Blisters, epidermal loss [second degree] of back [any part],Radiological procedure and radiotherapy as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Radiological procedure and radiotherapy as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Radiological procedure and radiotherapy as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Radiological procedure and radiotherapy as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Pressure ulcer, buttock,Pressure ulcer, buttock,Pressure ulcer, buttock,Pressure ulcer, buttock,Pressure ulcer, stage II,Pressure ulcer, stage II,Pressure ulcer, stage II,Pressure ulcer, stage II,Hypertensive chronic kidney disease, unspecified, with chronic kidney disease stage I through stage IV, or unspecified,Hypertensive chronic kidney disease, unspecified, with chronic kidney disease stage I through stage IV, or unspecified,Hypertensive chronic kidney disease, unspecified, with chronic kidney disease stage I through stage IV, or unspecified,Hypertensive chronic kidney disease, unspecified, with chronic kidney disease stage I through stage IV, or unspecified,Hypercalcemia,Hypercalcemia,Hypercalcemia,Hypercalcemia:    6  
##  Other tracheostomy complications,Atrial fibrillation,Chronic respiratory failure,Dependence on respirator, status,Diastolic heart failure, unspecified,Congestive heart failure, unspecified,Pure hypercholesterolemia,Other diseases of trachea and bronchus,Personal history of malignant neoplasm of thyroid,Gastrostomy status,Unspecified acquired hypothyroidism,Other chronic pulmonary heart diseases,Anemia, unspecified                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          :    6  
##  Other tracheostomy complications,Other tracheostomy complications,Congestive heart failure, unspecified,Congestive heart failure, unspecified,Chronic respiratory failure,Chronic respiratory failure,Chronic airway obstruction, not elsewhere classified,Chronic airway obstruction, not elsewhere classified,Other and unspecified angina pectoris,Other and unspecified angina pectoris,Urinary complications, not elsewhere classified,Urinary complications, not elsewhere classified,Urinary tract infection, site not specified,Urinary tract infection, site not specified,Infection and inflammatory reaction due to other vascular device, implant, and graft,Infection and inflammatory reaction due to other vascular device, implant, and graft,Bacteremia,Bacteremia,Methicillin susceptible pneumonia due to Staphylococcus aureus,Methicillin susceptible pneumonia due to Staphylococcus aureus,Hematoma complicating a procedure,Hematoma complicating a procedure,Other diseases of trachea and bronchus,Other diseases of trachea and bronchus,Nontoxic multinodular goiter,Nontoxic multinodular goiter,Other abnormal granulation tissue,Other abnormal granulation tissue,Morbid obesity,Morbid obesity,Diabetes mellitus without mention of complication, type II or unspecified type, not stated as uncontrolled,Diabetes mellitus without mention of complication, type II or unspecified type, not stated as uncontrolled,Coronary atherosclerosis of native coronary artery,Coronary atherosclerosis of native coronary artery,Friedl?nder's bacillus infection in conditions classified elsewhere and of unspecified site,Friedl?nder's bacillus infection in conditions classified elsewhere and of unspecified site,Proteus (mirabilis) (morganii) infection in conditions classified elsewhere and of unspecified site,Proteus (mirabilis) (morganii) infection in conditions classified elsewhere and of unspecified site,Methicillin susceptible Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Methicillin susceptible Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Infection with microorganisms resistant to penicillins,Infection with microorganisms resistant to penicillins,Ileostomy status,Ileostomy status                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              :    6  
##  (Other)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    :18096  
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                drug_list    
##  NULL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               :13038  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       :  377  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                          :  301  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                                                              :  173  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium:  147  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                    :  145  
##  (Other)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            : 3951  
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                drug_name    
##  NULL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               :13040  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       :  377  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                          :  302  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                                                              :  173  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium:  147  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                    :  145  
##  (Other)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            : 3948  
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            generic_drug_name
##  NULL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               :13040  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       :  377  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                          :  301  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                                                              :  173  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium:  146  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                    :  145  
##  (Other)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            : 3950  
##                                                                                                                                                                                drug_type    
##  NULL                                                                                                                                                                               :13038  
##  MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN                                                                                                                                       :  378  
##  MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN                                                                                          :  305  
##  MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN                                                                                                              :  177  
##  MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN:  151  
##  MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN                                                                                                    :  146  
##  (Other)                                                                                                                                                                            : 3937  
##                                                                    formulary    
##  NULL                                                                   :13038  
##  LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25         :   82  
##  LEVO50,LEVO50,LEVO50,LEVO50,LEVO50,LEVO50,LEVO50,LEVO50,LEVO50         :   69  
##  LEVO75,LEVO75,LEVO75,LEVO75,LEVO75,LEVO75,LEVO75,LEVO75,LEVO75         :   60  
##  LEVO100,LEVO100,LEVO100,LEVO100,LEVO100,LEVO100,LEVO100,LEVO100,LEVO100:   48  
##  LEVO125,LEVO125,LEVO125,LEVO125,LEVO125,LEVO125,LEVO125,LEVO125,LEVO125:   45  
##  (Other)                                                                : 4790  
##                                                                                                                                                                       ndc       
##  NULL                                                                                                                                                                   :13038  
##  00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113                                                            :   68  
##  00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211                                                            :   53  
##  00074518211,00074518211,00074518211,00074518211,00074518211,00074518211,00074518211,00074518211,00074518211                                                            :   44  
##  00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211:   37  
##  00074662411,00074662411,00074662411,00074662411,00074662411,00074662411,00074662411,00074662411,00074662411                                                            :   36  
##  (Other)                                                                                                                                                                : 4856  
##                                                              gsn       
##  NULL                                                          :13038  
##  006648,006648,006648,006648,006648,006648,006648,006648,006648:   82  
##  006649,006649,006649,006649,006649,006649,006649,006649,006649:   69  
##  006650,006650,006650,006650,006650,006650,006650,006650,006650:   60  
##  006651,006651,006651,006651,006651,006651,006651,006651,006651:   48  
##  006653,006653,006653,006653,006653,006653,006653,006653,006653:   45  
##  (Other)                                                       : 4790
```
     
#### Checking for NAs:      

```r
colSums(is.na(thyroid_df))
```

```
##        subject_id           hadm_id            gender               dob 
##                 0                 0                 0                 0 
##            itemid             value          valuenum          valueuom 
##                 0                 0                 0                 0 
##         charttime             label             fluid         icd9_list 
##                 0                 0                 0                 0 
##   icd9_short_list    icd9_long_list         drug_list         drug_name 
##                 0                 0                 0                 0 
## generic_drug_name         drug_type         formulary               ndc 
##                 0                 0                 0                 0 
##               gsn 
##                 0
```
       
#### Converting variables to proper type:     

```r
# First converting values that contain "NULL" (187 total) in column "valuenum" to NA
for(k in 1:nrow(thyroid_df)){
  if (thyroid_df$valuenum[k] == "NULL"){
    thyroid_df$value[k] <- NA
  }
}

# Then converting the string to numerics to get proper results otherwise when doing direct coercing to numeric, incorrect results were been displayed

thyroid_df$value <- destring(thyroid_df$value)
thyroid_df$value <- as.numeric(thyroid_df$value)
thyroid_df$gender <- as.factor(thyroid_df$gender)
thyroid_df$label <- as.factor(thyroid_df$label)
thyroid_df$valueuom <- as.factor(thyroid_df$valueuom)
str(thyroid_df)
```

```
## 'data.frame':	18132 obs. of  21 variables:
##  $ subject_id       : int  3 3 4 13 19 22 34 34 34 38 ...
##  $ hadm_id          : int  145834 145834 185777 143045 109235 165315 115799 144319 144319 185910 ...
##  $ gender           : Factor w/ 2 levels "F","M": 2 2 1 1 2 1 2 2 2 2 ...
##  $ dob              : Factor w/ 9850 levels "1800-07-02 00:00:00",..: 892 892 9338 8593 71 8824 592 592 592 5572 ...
##  $ itemid           : int  50993 50994 50993 50993 50993 50993 50993 50993 50995 50993 ...
##  $ value            : num  3.4 6.6 0.35 1.2 1.5 2.5 2.7 8.4 1.1 1.5 ...
##  $ valuenum         : Factor w/ 492 levels "0.02","0.021",..: 338 413 91 160 163 294 296 454 159 163 ...
##  $ valueuom         : Factor w/ 10 levels "IU/mL","ng/dl",..: 10 8 9 9 9 10 9 9 3 9 ...
##  $ charttime        : Factor w/ 14120 levels "2100-07-05 04:12:00",..: 176 176 12650 9255 1022 13342 11992 12637 12638 9191 ...
##  $ label            : Factor w/ 6 levels "Thyroglobulin",..: 3 4 3 3 3 3 3 3 5 3 ...
##  $ fluid            : Factor w/ 1 level "Blood": 1 1 1 1 1 1 1 1 1 1 ...
##  $ icd9_list        : Factor w/ 12155 levels "0031,51881,5566,2535,72888,5845,78552,5070,2762,9982,2851,00845,5770,570,1890,34839,42832,9972,99592,03819,3030"| __truncated__,..: 1069 1069 1125 4025 10477 11251 3726 5343 5343 9853 ...
##  $ icd9_short_list  : Factor w/ 12155 levels "35-36 comp wks gestation,NB obsrv suspct infect,Need prphyl vc vrl hepat,Single lb in-hosp w cs,Preterm NEC 250"| __truncated__,..: 10609 10609 6303 4553 5752 9231 11322 3106 3106 7454 ...
##  $ icd9_long_list   : Factor w/ 12155 levels "35-36 completed weeks of gestation,Observation for suspected infectious condition,Need for prophylactic vaccina"| __truncated__,..: 11620 11620 5593 4446 3752 9513 10717 8429 8429 12082 ...
##  $ drug_list        : Factor w/ 171 levels "Levothyroxine Sodium,Levothyroxine Sodium",..: 171 171 8 171 171 171 7 7 7 171 ...
##  $ drug_name        : Factor w/ 170 levels "Levothyroxine Sodium,Levothyroxine Sodium",..: 170 170 8 170 170 170 7 7 7 170 ...
##  $ generic_drug_name: Factor w/ 200 levels "*NF* Levothyroxine Sodium Brand Name,Levothyroxine Sodium,*NF* Levothyroxine Sodium Brand Name,Levothyroxine So"| __truncated__,..: 200 200 9 200 200 200 8 8 8 200 ...
##  $ drug_type        : Factor w/ 148 levels "MAIN,MAIN","MAIN,MAIN,MAIN",..: 148 148 8 148 148 148 7 7 7 148 ...
##  $ formulary        : Factor w/ 1427 levels "CYTO5T,LEVO88,CYTO5T,CYTO5T,CYTO5T,LEVO88,CYTO5T,CYTO5T,CYTO5T,LEVO88,CYTO5T,CYTO5T,CYTO5T,LEVO88,CYTO5T,CYTO5T"| __truncated__,..: 1427 1427 908 1427 1427 1427 9 1284 1284 1427 ...
##  $ ndc              : Factor w/ 1519 levels "00007521006,00007521006,00007521006,00007521006,00007521006,55390088010,00007521006,55390088010,55390088010,553"| __truncated__,..: 1519 1519 109 1519 1519 1519 755 490 490 1519 ...
##  $ gsn              : Factor w/ 1426 levels "006645,006645,006645,006645,006645,006645",..: 1426 1426 322 1426 1426 1426 898 709 709 1426 ...
```

```r
summary(thyroid_df)
```

```
##    subject_id       hadm_id       gender                    dob       
##  Min.   :    3   Min.   :100001   F:9450   2078-12-05 00:00:00:   40  
##  1st Qu.:12076   1st Qu.:124705   M:8682   2052-02-14 00:00:00:   28  
##  Median :24633   Median :149789            2130-07-08 00:00:00:   21  
##  Mean   :34134   Mean   :149679            2066-10-13 00:00:00:   18  
##  3rd Qu.:55085   3rd Qu.:174662            2071-06-27 00:00:00:   18  
##  Max.   :99957   Max.   :199993            2104-04-12 00:00:00:   17  
##                                            (Other)            :17990  
##      itemid          value             valuenum        valueuom    
##  Min.   :50991   Min.   :   0.020   1.1    :  782   uIU/mL :10803  
##  1st Qu.:50993   1st Qu.:   1.100   1.2    :  688   ng/dL  : 3747  
##  Median :50993   Median :   2.000   1.3    :  609   ug/dL  : 1555  
##  Mean   :50994   Mean   :   8.703   1.4    :  535   uU/ML  : 1259  
##  3rd Qu.:50994   3rd Qu.:   4.900   1.5    :  502   ng/dl  :  336  
##  Max.   :51001   Max.   :3890.000   1      :  428   uG/DL  :  188  
##                  NA's   :187        (Other):14588   (Other):  244  
##                charttime                               label      
##  2129-03-12 13:40:00:    6   Thyroglobulin                :   47  
##  2153-04-15 21:25:00:    5   Thyroid Peroxidase Antibodies:   65  
##  2160-01-30 15:50:00:    5   Thyroid Stimulating Hormone  :12063  
##  2195-10-20 03:21:00:    5   Thyroxine (T4)               : 1743  
##  2104-05-22 20:31:00:    4   Thyroxine (T4), Free         : 3031  
##  2105-06-18 16:15:00:    4   Triiodothyronine (T3)        : 1183  
##  (Other)            :18103                                        
##    fluid      
##  Blood:18132  
##               
##               
##               
##               
##               
##               
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            icd9_list    
##  20280,20280,20280,20280,5845,5845,5845,5845,3363,3363,3363,3363,41519,41519,41519,41519,57400,57400,57400,57400,6822,6822,6822,6822,1363,1363,1363,1363,03843,03843,03843,03843,99591,99591,99591,99591,59080,59080,59080,59080,34830,34830,34830,34830,3220,3220,3220,3220,1125,1125,1125,1125,2841,2841,2841,2841,5781,5781,5781,5781,99811,99811,99811,99811,43491,43491,43491,43491,591,591,591,591,2761,2761,2761,2761,2930,2930,2930,2930,5848,5848,5848,5848,28800,28800,28800,28800,52809,52809,52809,52809,78061,78061,78061,78061,E9331,E9331,E9331,E9331,99931,99931,99931,99931,04112,04112,04112,04112,E8798,E8798,E8798,E8798,V6441,V6441,V6441,V6441,78559,78559,78559,78559,E8786,E8786,E8786,E8786,E9342,E9342,E9342,E9342,5933,5933,5933,5933,94224,94224,94224,94224,E8792,E8792,E8792,E8792,70705,70705,70705,70705,70722,70722,70722,70722,40390,40390,40390,40390,27542,27542,27542,27542:    6  
##  24291,2768,28529                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               :    6  
##  34510,5070,99731,51881,7802,E9380,24200,30423,V1581,V642,3051                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  :    6  
##  41071,42731,00845,27651,2761,44024,70714,138,2720,4019,53081,2449,412,25000                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    :    6  
##  4414,4414,4414,4233,4233,4233,42822,42822,42822,4239,4239,4239,2449,2449,2449,2948,2948,2948,45829,45829,45829,42789,42789,42789,4148,4148,4148,4019,4019,4019,3051,3051,3051,2809,2809,2809,41401,41401,41401,4280,4280,4280,4928,4928,4928,4439,4439,4439,412,412,412,V1581,V1581,V1581,V1301,V1301,V1301,V1083,V1083,V1083                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  :    6  
##  4870,41071,4280,42832,27651,5859,42731,2724,4019,25050,36201,25040,58381,V4581,51889,41401,2411,2270                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           :    6  
##  (Other)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        :18096  
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 icd9_short_list 
##  Abdom aortic aneurysm,Abdom aortic aneurysm,Abdom aortic aneurysm,Cardiac tamponade,Cardiac tamponade,Cardiac tamponade,Chr systolic hrt failure,Chr systolic hrt failure,Chr systolic hrt failure,Pericardial disease NOS,Pericardial disease NOS,Pericardial disease NOS,Hypothyroidism NOS,Hypothyroidism NOS,Hypothyroidism NOS,Mental disor NEC oth dis,Mental disor NEC oth dis,Mental disor NEC oth dis,Iatrogenc hypotnsion NEC,Iatrogenc hypotnsion NEC,Iatrogenc hypotnsion NEC,Cardiac dysrhythmias NEC,Cardiac dysrhythmias NEC,Cardiac dysrhythmias NEC,Chr ischemic hrt dis NEC,Chr ischemic hrt dis NEC,Chr ischemic hrt dis NEC,Hypertension NOS,Hypertension NOS,Hypertension NOS,Tobacco use disorder,Tobacco use disorder,Tobacco use disorder,Iron defic anemia NOS,Iron defic anemia NOS,Iron defic anemia NOS,Crnry athrscl natve vssl,Crnry athrscl natve vssl,Crnry athrscl natve vssl,CHF NOS,CHF NOS,CHF NOS,Emphysema NEC,Emphysema NEC,Emphysema NEC,Periph vascular dis NOS,Periph vascular dis NOS,Periph vascular dis NOS,Old myocardial infarct,Old myocardial infarct,Old myocardial infarct,Hx of past noncompliance,Hx of past noncompliance,Hx of past noncompliance,Prsnl hst urnr dsrd calc,Prsnl hst urnr dsrd calc,Prsnl hst urnr dsrd calc,Hx-skin malignancy NEC,Hx-skin malignancy NEC,Hx-skin malignancy NEC                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               :    6  
##  Gen cnv epil w/o intr ep,Food/vomit pneumonitis,Ventltr assoc pneumonia,Acute respiratry failure,Syncope and collapse,Adv eff cns muscl depres,Tox dif goiter no crisis,Cocaine depend-remiss,Hx of past noncompliance,No proc/patient decision,Tobacco use disorder                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   :    6  
##  Influenza with pneumonia,Subendo infarct, initial,CHF NOS,Chr diastolic hrt fail,Dehydration,Chronic kidney dis NOS,Atrial fibrillation,Hyperlipidemia NEC/NOS,Hypertension NOS,DMII ophth nt st uncntrl,Diabetic retinopathy NOS,DMII renl nt st uncntrld,Nephritis NOS in oth dis,Aortocoronary bypass,Other lung disease NEC,Crnry athrscl natve vssl,Nontox multinodul goiter,Benign neoplasm adrenal                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              :    6  
##  Oth lymp unsp xtrndl org,Oth lymp unsp xtrndl org,Oth lymp unsp xtrndl org,Oth lymp unsp xtrndl org,Ac kidny fail, tubr necr,Ac kidny fail, tubr necr,Ac kidny fail, tubr necr,Ac kidny fail, tubr necr,Myelopathy in oth dis,Myelopathy in oth dis,Myelopathy in oth dis,Myelopathy in oth dis,Pulm embol/infarct NEC,Pulm embol/infarct NEC,Pulm embol/infarct NEC,Pulm embol/infarct NEC,Cholelith w ac cholecyst,Cholelith w ac cholecyst,Cholelith w ac cholecyst,Cholelith w ac cholecyst,Cellulitis of trunk,Cellulitis of trunk,Cellulitis of trunk,Cellulitis of trunk,Pneumocystosis,Pneumocystosis,Pneumocystosis,Pneumocystosis,Pseudomonas septicemia,Pseudomonas septicemia,Pseudomonas septicemia,Pseudomonas septicemia,Sepsis,Sepsis,Sepsis,Sepsis,Pyelonephritis NOS,Pyelonephritis NOS,Pyelonephritis NOS,Pyelonephritis NOS,Encephalopathy NOS,Encephalopathy NOS,Encephalopathy NOS,Encephalopathy NOS,Nonpyogenic meningitis,Nonpyogenic meningitis,Nonpyogenic meningitis,Nonpyogenic meningitis,Disseminated candidiasis,Disseminated candidiasis,Disseminated candidiasis,Disseminated candidiasis,Blood in stool,Blood in stool,Blood in stool,Blood in stool,Hemorrhage complic proc,Hemorrhage complic proc,Hemorrhage complic proc,Hemorrhage complic proc,Crbl art ocl NOS w infrc,Crbl art ocl NOS w infrc,Crbl art ocl NOS w infrc,Crbl art ocl NOS w infrc,Hydronephrosis,Hydronephrosis,Hydronephrosis,Hydronephrosis,Hyposmolality,Hyposmolality,Hyposmolality,Hyposmolality,Delirium d/t other cond,Delirium d/t other cond,Delirium d/t other cond,Delirium d/t other cond,Acute kidney failure NEC,Acute kidney failure NEC,Acute kidney failure NEC,Acute kidney failure NEC,Neutropenia NOS,Neutropenia NOS,Neutropenia NOS,Neutropenia NOS,Stomatits & mucosits NEC,Stomatits & mucosits NEC,Stomatits & mucosits NEC,Stomatits & mucosits NEC,Fever in other diseases,Fever in other diseases,Fever in other diseases,Fever in other diseases,Adv eff antineoplastic,Adv eff antineoplastic,Adv eff antineoplastic,Adv eff antineoplastic,Oth/uns inf-cen ven cath,Oth/uns inf-cen ven cath,Oth/uns inf-cen ven cath,Oth/uns inf-cen ven cath,MRSA elsewhere/NOS,MRSA elsewhere/NOS,MRSA elsewhere/NOS,MRSA elsewhere/NOS,Abn react-procedure NEC,Abn react-procedure NEC,Abn react-procedure NEC,Abn react-procedure NEC,Lap surg convert to open,Lap surg convert to open,Lap surg convert to open,Lap surg convert to open,Shock w/o trauma NEC,Shock w/o trauma NEC,Shock w/o trauma NEC,Shock w/o trauma NEC,Abn reac-organ rem NEC,Abn reac-organ rem NEC,Abn reac-organ rem NEC,Abn reac-organ rem NEC,Adv eff anticoagulants,Adv eff anticoagulants,Adv eff anticoagulants,Adv eff anticoagulants,Stricture of ureter,Stricture of ureter,Stricture of ureter,Stricture of ureter,2nd deg burn back,2nd deg burn back,2nd deg burn back,2nd deg burn back,Abn react-radiotherapy,Abn react-radiotherapy,Abn react-radiotherapy,Abn react-radiotherapy,Pressure ulcer, buttock,Pressure ulcer, buttock,Pressure ulcer, buttock,Pressure ulcer, buttock,Pressure ulcer, stage II,Pressure ulcer, stage II,Pressure ulcer, stage II,Pressure ulcer, stage II,Hy kid NOS w cr kid I-IV,Hy kid NOS w cr kid I-IV,Hy kid NOS w cr kid I-IV,Hy kid NOS w cr kid I-IV,Hypercalcemia,Hypercalcemia,Hypercalcemia,Hypercalcemia:    6  
##  Subendo infarct, initial,Atrial fibrillation,Int inf clstrdium dfcile,Dehydration,Hyposmolality,Ath ext ntv art gngrene,Ulcer of heel & midfoot,Late effect acute polio,Pure hypercholesterolem,Hypertension NOS,Esophageal reflux,Hypothyroidism NOS,Old myocardial infarct,DMII wo cmp nt st uncntr                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  :    6  
##  Thyrotox NOS w crisis,Hypopotassemia,Anemia-other chronic dis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          :    6  
##  (Other)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                :18096  
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      icd9_long_list 
##  Abdominal aneurysm without mention of rupture,Abdominal aneurysm without mention of rupture,Abdominal aneurysm without mention of rupture,Cardiac tamponade,Cardiac tamponade,Cardiac tamponade,Chronic systolic heart failure,Chronic systolic heart failure,Chronic systolic heart failure,Unspecified disease of pericardium,Unspecified disease of pericardium,Unspecified disease of pericardium,Unspecified acquired hypothyroidism,Unspecified acquired hypothyroidism,Unspecified acquired hypothyroidism,Other persistent mental disorders due to conditions classified elsewhere,Other persistent mental disorders due to conditions classified elsewhere,Other persistent mental disorders due to conditions classified elsewhere,Other iatrogenic hypotension,Other iatrogenic hypotension,Other iatrogenic hypotension,Other specified cardiac dysrhythmias,Other specified cardiac dysrhythmias,Other specified cardiac dysrhythmias,Other specified forms of chronic ischemic heart disease,Other specified forms of chronic ischemic heart disease,Other specified forms of chronic ischemic heart disease,Unspecified essential hypertension,Unspecified essential hypertension,Unspecified essential hypertension,Tobacco use disorder,Tobacco use disorder,Tobacco use disorder,Iron deficiency anemia, unspecified,Iron deficiency anemia, unspecified,Iron deficiency anemia, unspecified,Coronary atherosclerosis of native coronary artery,Coronary atherosclerosis of native coronary artery,Coronary atherosclerosis of native coronary artery,Congestive heart failure, unspecified,Congestive heart failure, unspecified,Congestive heart failure, unspecified,Other emphysema,Other emphysema,Other emphysema,Peripheral vascular disease, unspecified,Peripheral vascular disease, unspecified,Peripheral vascular disease, unspecified,Old myocardial infarction,Old myocardial infarction,Old myocardial infarction,Personal history of noncompliance with medical treatment, presenting hazards to health,Personal history of noncompliance with medical treatment, presenting hazards to health,Personal history of noncompliance with medical treatment, presenting hazards to health,Personal history of urinary calculi,Personal history of urinary calculi,Personal history of urinary calculi,Personal history of other malignant neoplasm of skin,Personal history of other malignant neoplasm of skin,Personal history of other malignant neoplasm of skin                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         :    6  
##  Generalized convulsive epilepsy, without mention of intractable epilepsy,Pneumonitis due to inhalation of food or vomitus,Ventilator associated pneumonia,Acute respiratory failure,Syncope and collapse,Central nervous system muscle-tone depressants causing adverse effects in therapeutic use,Toxic diffuse goiter without mention of thyrotoxic crisis or storm,Cocaine dependence, in remission,Personal history of noncompliance with medical treatment, presenting hazards to health,Surgical or other procedure not carried out because of patient's decision,Tobacco use disorder                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               :    6  
##  Influenza with pneumonia,Subendocardial infarction, initial episode of care,Congestive heart failure, unspecified,Chronic diastolic heart failure,Dehydration,Chronic kidney disease, unspecified,Atrial fibrillation,Other and unspecified hyperlipidemia,Unspecified essential hypertension,Diabetes with ophthalmic manifestations, type II or unspecified type, not stated as uncontrolled,Background diabetic retinopathy,Diabetes with renal manifestations, type II or unspecified type, not stated as uncontrolled,Nephritis and nephropathy, not specified as acute or chronic, in diseases classified elsewhere,Aortocoronary bypass status,Other diseases of lung, not elsewhere classified,Coronary atherosclerosis of native coronary artery,Nontoxic multinodular goiter,Benign neoplasm of adrenal gland                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    :    6  
##  Other malignant lymphomas, unspecified site, extranodal and solid organ sites,Other malignant lymphomas, unspecified site, extranodal and solid organ sites,Other malignant lymphomas, unspecified site, extranodal and solid organ sites,Other malignant lymphomas, unspecified site, extranodal and solid organ sites,Acute kidney failure with lesion of tubular necrosis,Acute kidney failure with lesion of tubular necrosis,Acute kidney failure with lesion of tubular necrosis,Acute kidney failure with lesion of tubular necrosis,Myelopathy in other diseases classified elsewhere,Myelopathy in other diseases classified elsewhere,Myelopathy in other diseases classified elsewhere,Myelopathy in other diseases classified elsewhere,Other pulmonary embolism and infarction,Other pulmonary embolism and infarction,Other pulmonary embolism and infarction,Other pulmonary embolism and infarction,Calculus of gallbladder with acute cholecystitis, without mention of obstruction,Calculus of gallbladder with acute cholecystitis, without mention of obstruction,Calculus of gallbladder with acute cholecystitis, without mention of obstruction,Calculus of gallbladder with acute cholecystitis, without mention of obstruction,Cellulitis and abscess of trunk,Cellulitis and abscess of trunk,Cellulitis and abscess of trunk,Cellulitis and abscess of trunk,Pneumocystosis,Pneumocystosis,Pneumocystosis,Pneumocystosis,Septicemia due to pseudomonas,Septicemia due to pseudomonas,Septicemia due to pseudomonas,Septicemia due to pseudomonas,Sepsis,Sepsis,Sepsis,Sepsis,Pyelonephritis, unspecified,Pyelonephritis, unspecified,Pyelonephritis, unspecified,Pyelonephritis, unspecified,Encephalopathy, unspecified,Encephalopathy, unspecified,Encephalopathy, unspecified,Encephalopathy, unspecified,Nonpyogenic meningitis,Nonpyogenic meningitis,Nonpyogenic meningitis,Nonpyogenic meningitis,Disseminated candidiasis,Disseminated candidiasis,Disseminated candidiasis,Disseminated candidiasis,Blood in stool,Blood in stool,Blood in stool,Blood in stool,Hemorrhage complicating a procedure,Hemorrhage complicating a procedure,Hemorrhage complicating a procedure,Hemorrhage complicating a procedure,Cerebral artery occlusion, unspecified with cerebral infarction,Cerebral artery occlusion, unspecified with cerebral infarction,Cerebral artery occlusion, unspecified with cerebral infarction,Cerebral artery occlusion, unspecified with cerebral infarction,Hydronephrosis,Hydronephrosis,Hydronephrosis,Hydronephrosis,Hyposmolality and/or hyponatremia,Hyposmolality and/or hyponatremia,Hyposmolality and/or hyponatremia,Hyposmolality and/or hyponatremia,Delirium due to conditions classified elsewhere,Delirium due to conditions classified elsewhere,Delirium due to conditions classified elsewhere,Delirium due to conditions classified elsewhere,Acute kidney failure with other specified pathological lesion in kidney,Acute kidney failure with other specified pathological lesion in kidney,Acute kidney failure with other specified pathological lesion in kidney,Acute kidney failure with other specified pathological lesion in kidney,Neutropenia, unspecified,Neutropenia, unspecified,Neutropenia, unspecified,Neutropenia, unspecified,Other stomatitis and mucositis (ulcerative),Other stomatitis and mucositis (ulcerative),Other stomatitis and mucositis (ulcerative),Other stomatitis and mucositis (ulcerative),Fever presenting with conditions classified elsewhere,Fever presenting with conditions classified elsewhere,Fever presenting with conditions classified elsewhere,Fever presenting with conditions classified elsewhere,Antineoplastic and immunosuppressive drugs causing adverse effects in therapeutic use,Antineoplastic and immunosuppressive drugs causing adverse effects in therapeutic use,Antineoplastic and immunosuppressive drugs causing adverse effects in therapeutic use,Antineoplastic and immunosuppressive drugs causing adverse effects in therapeutic use,Other and unspecified infection due to central venous catheter,Other and unspecified infection due to central venous catheter,Other and unspecified infection due to central venous catheter,Other and unspecified infection due to central venous catheter,Methicillin resistant Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Methicillin resistant Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Methicillin resistant Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Methicillin resistant Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Other specified procedures as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Other specified procedures as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Other specified procedures as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Other specified procedures as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Laparoscopic surgical procedure converted to open procedure,Laparoscopic surgical procedure converted to open procedure,Laparoscopic surgical procedure converted to open procedure,Laparoscopic surgical procedure converted to open procedure,Other shock without mention of trauma,Other shock without mention of trauma,Other shock without mention of trauma,Other shock without mention of trauma,Removal of other organ (partial) (total) causing abnormal patient reaction, or later complication, without mention of misadventure at time of operation,Removal of other organ (partial) (total) causing abnormal patient reaction, or later complication, without mention of misadventure at time of operation,Removal of other organ (partial) (total) causing abnormal patient reaction, or later complication, without mention of misadventure at time of operation,Removal of other organ (partial) (total) causing abnormal patient reaction, or later complication, without mention of misadventure at time of operation,Anticoagulants causing adverse effects in therapeutic use,Anticoagulants causing adverse effects in therapeutic use,Anticoagulants causing adverse effects in therapeutic use,Anticoagulants causing adverse effects in therapeutic use,Stricture or kinking of ureter,Stricture or kinking of ureter,Stricture or kinking of ureter,Stricture or kinking of ureter,Blisters, epidermal loss [second degree] of back [any part],Blisters, epidermal loss [second degree] of back [any part],Blisters, epidermal loss [second degree] of back [any part],Blisters, epidermal loss [second degree] of back [any part],Radiological procedure and radiotherapy as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Radiological procedure and radiotherapy as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Radiological procedure and radiotherapy as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Radiological procedure and radiotherapy as the cause of abnormal reaction of patient, or of later complication, without mention of misadventure at time of procedure,Pressure ulcer, buttock,Pressure ulcer, buttock,Pressure ulcer, buttock,Pressure ulcer, buttock,Pressure ulcer, stage II,Pressure ulcer, stage II,Pressure ulcer, stage II,Pressure ulcer, stage II,Hypertensive chronic kidney disease, unspecified, with chronic kidney disease stage I through stage IV, or unspecified,Hypertensive chronic kidney disease, unspecified, with chronic kidney disease stage I through stage IV, or unspecified,Hypertensive chronic kidney disease, unspecified, with chronic kidney disease stage I through stage IV, or unspecified,Hypertensive chronic kidney disease, unspecified, with chronic kidney disease stage I through stage IV, or unspecified,Hypercalcemia,Hypercalcemia,Hypercalcemia,Hypercalcemia:    6  
##  Other tracheostomy complications,Atrial fibrillation,Chronic respiratory failure,Dependence on respirator, status,Diastolic heart failure, unspecified,Congestive heart failure, unspecified,Pure hypercholesterolemia,Other diseases of trachea and bronchus,Personal history of malignant neoplasm of thyroid,Gastrostomy status,Unspecified acquired hypothyroidism,Other chronic pulmonary heart diseases,Anemia, unspecified                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          :    6  
##  Other tracheostomy complications,Other tracheostomy complications,Congestive heart failure, unspecified,Congestive heart failure, unspecified,Chronic respiratory failure,Chronic respiratory failure,Chronic airway obstruction, not elsewhere classified,Chronic airway obstruction, not elsewhere classified,Other and unspecified angina pectoris,Other and unspecified angina pectoris,Urinary complications, not elsewhere classified,Urinary complications, not elsewhere classified,Urinary tract infection, site not specified,Urinary tract infection, site not specified,Infection and inflammatory reaction due to other vascular device, implant, and graft,Infection and inflammatory reaction due to other vascular device, implant, and graft,Bacteremia,Bacteremia,Methicillin susceptible pneumonia due to Staphylococcus aureus,Methicillin susceptible pneumonia due to Staphylococcus aureus,Hematoma complicating a procedure,Hematoma complicating a procedure,Other diseases of trachea and bronchus,Other diseases of trachea and bronchus,Nontoxic multinodular goiter,Nontoxic multinodular goiter,Other abnormal granulation tissue,Other abnormal granulation tissue,Morbid obesity,Morbid obesity,Diabetes mellitus without mention of complication, type II or unspecified type, not stated as uncontrolled,Diabetes mellitus without mention of complication, type II or unspecified type, not stated as uncontrolled,Coronary atherosclerosis of native coronary artery,Coronary atherosclerosis of native coronary artery,Friedl?nder's bacillus infection in conditions classified elsewhere and of unspecified site,Friedl?nder's bacillus infection in conditions classified elsewhere and of unspecified site,Proteus (mirabilis) (morganii) infection in conditions classified elsewhere and of unspecified site,Proteus (mirabilis) (morganii) infection in conditions classified elsewhere and of unspecified site,Methicillin susceptible Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Methicillin susceptible Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Infection with microorganisms resistant to penicillins,Infection with microorganisms resistant to penicillins,Ileostomy status,Ileostomy status                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              :    6  
##  (Other)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    :18096  
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                drug_list    
##  NULL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               :13038  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       :  377  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                          :  301  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                                                              :  173  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium:  147  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                    :  145  
##  (Other)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            : 3951  
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                drug_name    
##  NULL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               :13040  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       :  377  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                          :  302  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                                                              :  173  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium:  147  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                    :  145  
##  (Other)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            : 3948  
##                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            generic_drug_name
##  NULL                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               :13040  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       :  377  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                          :  301  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                                                              :  173  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium:  146  
##  Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium                                                                                                                                                                                                                                                                                                                                                                                                                                    :  145  
##  (Other)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            : 3950  
##                                                                                                                                                                                drug_type    
##  NULL                                                                                                                                                                               :13038  
##  MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN                                                                                                                                       :  378  
##  MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN                                                                                          :  305  
##  MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN                                                                                                              :  177  
##  MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN:  151  
##  MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN                                                                                                    :  146  
##  (Other)                                                                                                                                                                            : 3937  
##                                                                    formulary    
##  NULL                                                                   :13038  
##  LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25         :   82  
##  LEVO50,LEVO50,LEVO50,LEVO50,LEVO50,LEVO50,LEVO50,LEVO50,LEVO50         :   69  
##  LEVO75,LEVO75,LEVO75,LEVO75,LEVO75,LEVO75,LEVO75,LEVO75,LEVO75         :   60  
##  LEVO100,LEVO100,LEVO100,LEVO100,LEVO100,LEVO100,LEVO100,LEVO100,LEVO100:   48  
##  LEVO125,LEVO125,LEVO125,LEVO125,LEVO125,LEVO125,LEVO125,LEVO125,LEVO125:   45  
##  (Other)                                                                : 4790  
##                                                                                                                                                                       ndc       
##  NULL                                                                                                                                                                   :13038  
##  00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113                                                            :   68  
##  00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211                                                            :   53  
##  00074518211,00074518211,00074518211,00074518211,00074518211,00074518211,00074518211,00074518211,00074518211                                                            :   44  
##  00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211,00074455211:   37  
##  00074662411,00074662411,00074662411,00074662411,00074662411,00074662411,00074662411,00074662411,00074662411                                                            :   36  
##  (Other)                                                                                                                                                                : 4856  
##                                                              gsn       
##  NULL                                                          :13038  
##  006648,006648,006648,006648,006648,006648,006648,006648,006648:   82  
##  006649,006649,006649,006649,006649,006649,006649,006649,006649:   69  
##  006650,006650,006650,006650,006650,006650,006650,006650,006650:   60  
##  006651,006651,006651,006651,006651,006651,006651,006651,006651:   48  
##  006653,006653,006653,006653,006653,006653,006653,006653,006653:   45  
##  (Other)                                                       : 4790
```
       
#### Getting age of patients:     

```r
for (i in 1:nrow(thyroid_df)){
  start_date = as.Date(thyroid_df$dob[i])
  end_date = as.Date(thyroid_df$charttime[i])
  thyroid_df$age[i] <- floor(lubridate::time_length(difftime(end_date, start_date), "years"))
}

# In MIMIC-III, patients with age > 89 are coded in as impossibile values
thyroid_df$age <- ifelse(thyroid_df$age > 89, 89, thyroid_df$age) 

# Since age cannot be 0, saving it as 1
thyroid_df$age <- ifelse(thyroid_df$age == 0, 1, thyroid_df$age)
head(thyroid_df,5)
```

```
##   subject_id hadm_id gender                 dob itemid value valuenum valueuom
## 1          3  145834      M 2025-04-11 00:00:00  50993  3.40      3.4    uU/ML
## 2          3  145834      M 2025-04-11 00:00:00  50994  6.60      6.6    uG/DL
## 3          4  185777      F 2143-05-12 00:00:00  50993  0.35     0.35   uIU/mL
## 4         13  143045      F 2127-02-27 00:00:00  50993  1.20      1.2   uIU/mL
## 5         19  109235      M 1808-08-05 00:00:00  50993  1.50      1.5   uIU/mL
##             charttime                       label fluid
## 1 2101-10-21 13:00:00 Thyroid Stimulating Hormone Blood
## 2 2101-10-21 13:00:00              Thyroxine (T4) Blood
## 3 2191-03-16 05:42:00 Thyroid Stimulating Hormone Blood
## 4 2167-01-09 07:11:00 Thyroid Stimulating Hormone Blood
## 5 2108-08-05 15:00:00 Thyroid Stimulating Hormone Blood
##                                          icd9_list
## 1   0389,78559,5849,4275,41071,4280,6826,4254,2639
## 2   0389,78559,5849,4275,41071,4280,6826,4254,2639
## 3    042,1363,7994,2763,7907,5715,04111,V090,E9317
## 4                       41401,4111,25000,4019,2720
## 5 80502,5990,5964,E8809,8220,73300,2948,4019,44321
##                                                                                                                                                                                    icd9_short_list
## 1                   Septicemia NOS,Shock w/o trauma NEC,Acute kidney failure NOS,Cardiac arrest,Subendo infarct, initial,CHF NOS,Cellulitis of leg,Prim cardiomyopathy NEC,Protein-cal malnutr NOS
## 2                   Septicemia NOS,Shock w/o trauma NEC,Acute kidney failure NOS,Cardiac arrest,Subendo infarct, initial,CHF NOS,Cellulitis of leg,Prim cardiomyopathy NEC,Protein-cal malnutr NOS
## 3                               Human immuno virus dis,Pneumocystosis,Cachexia,Alkalosis,Bacteremia,Cirrhosis of liver NOS,Mth sus Stph aur els/NOS,Inf mcrg rstn pncllins,Adv eff antiviral drugs
## 4                                                                                Crnry athrscl natve vssl,Intermed coronary synd,DMII wo cmp nt st uncntr,Hypertension NOS,Pure hypercholesterolem
## 5 Fx c2 vertebra-closed,Urin tract infection NOS,Atony of bladder,Fall on stair/step NEC,Fracture patella-closed,Osteoporosis NOS,Mental disor NEC oth dis,Hypertension NOS,Dissect carotid artery
##                                                                                                                                                                                                                                                                                                                                                     icd9_long_list
## 1                                        Unspecified septicemia,Other shock without mention of trauma,Acute kidney failure, unspecified,Cardiac arrest,Subendocardial infarction, initial episode of care,Congestive heart failure, unspecified,Cellulitis and abscess of leg, except foot,Other primary cardiomyopathies,Unspecified protein-calorie malnutrition
## 2                                        Unspecified septicemia,Other shock without mention of trauma,Acute kidney failure, unspecified,Cardiac arrest,Subendocardial infarction, initial episode of care,Congestive heart failure, unspecified,Cellulitis and abscess of leg, except foot,Other primary cardiomyopathies,Unspecified protein-calorie malnutrition
## 3 Human immunodeficiency virus [HIV] disease,Pneumocystosis,Cachexia,Alkalosis,Bacteremia,Cirrhosis of liver without mention of alcohol,Methicillin susceptible Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Infection with microorganisms resistant to penicillins,Antiviral drugs causing adverse effects in therapeutic use
## 4                                                                                                        Coronary atherosclerosis of native coronary artery,Intermediate coronary syndrome,Diabetes mellitus without mention of complication, type II or unspecified type, not stated as uncontrolled,Unspecified essential hypertension,Pure hypercholesterolemia
## 5          Closed fracture of second cervical vertebra,Urinary tract infection, site not specified,Atony of bladder,Accidental fall on or from other stairs or steps,Closed fracture of patella,Osteoporosis, unspecified,Other persistent mental disorders due to conditions classified elsewhere,Unspecified essential hypertension,Dissection of carotid artery
##                                                                                                                                                                                      drug_list
## 1                                                                                                                                                                                         NULL
## 2                                                                                                                                                                                         NULL
## 3 Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium
## 4                                                                                                                                                                                         NULL
## 5                                                                                                                                                                                         NULL
##                                                                                                                                                                                      drug_name
## 1                                                                                                                                                                                         NULL
## 2                                                                                                                                                                                         NULL
## 3 Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium
## 4                                                                                                                                                                                         NULL
## 5                                                                                                                                                                                         NULL
##                                                                                                                                                                              generic_drug_name
## 1                                                                                                                                                                                         NULL
## 2                                                                                                                                                                                         NULL
## 3 Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium
## 4                                                                                                                                                                                         NULL
## 5                                                                                                                                                                                         NULL
##                                      drug_type
## 1                                         NULL
## 2                                         NULL
## 3 MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN
## 4                                         NULL
## 5                                         NULL
##                                                        formulary
## 1                                                           NULL
## 2                                                           NULL
## 3 LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25
## 4                                                           NULL
## 5                                                           NULL
##                                                                                                           ndc
## 1                                                                                                        NULL
## 2                                                                                                        NULL
## 3 00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113
## 4                                                                                                        NULL
## 5                                                                                                        NULL
##                                                              gsn age
## 1                                                           NULL  76
## 2                                                           NULL  76
## 3 006648,006648,006648,006648,006648,006648,006648,006648,006648  47
## 4                                                           NULL  39
## 5                                                           NULL  89
```
    
#### Checking if the patient has been prescribed T4 and T3 medicine:    

```r
thyroid_df$t4_medication <- ifelse(str_detect(thyroid_df$drug_list, 'Levothyroxine') | str_detect(thyroid_df$drug_list, 'Synthroid'),"Yes","No") # T4
thyroid_df$t3_medication <- ifelse(str_detect(thyroid_df$drug_list, 'Liothyronine'),"Yes","No") # T3
head(thyroid_df,5)
```

```
##   subject_id hadm_id gender                 dob itemid value valuenum valueuom
## 1          3  145834      M 2025-04-11 00:00:00  50993  3.40      3.4    uU/ML
## 2          3  145834      M 2025-04-11 00:00:00  50994  6.60      6.6    uG/DL
## 3          4  185777      F 2143-05-12 00:00:00  50993  0.35     0.35   uIU/mL
## 4         13  143045      F 2127-02-27 00:00:00  50993  1.20      1.2   uIU/mL
## 5         19  109235      M 1808-08-05 00:00:00  50993  1.50      1.5   uIU/mL
##             charttime                       label fluid
## 1 2101-10-21 13:00:00 Thyroid Stimulating Hormone Blood
## 2 2101-10-21 13:00:00              Thyroxine (T4) Blood
## 3 2191-03-16 05:42:00 Thyroid Stimulating Hormone Blood
## 4 2167-01-09 07:11:00 Thyroid Stimulating Hormone Blood
## 5 2108-08-05 15:00:00 Thyroid Stimulating Hormone Blood
##                                          icd9_list
## 1   0389,78559,5849,4275,41071,4280,6826,4254,2639
## 2   0389,78559,5849,4275,41071,4280,6826,4254,2639
## 3    042,1363,7994,2763,7907,5715,04111,V090,E9317
## 4                       41401,4111,25000,4019,2720
## 5 80502,5990,5964,E8809,8220,73300,2948,4019,44321
##                                                                                                                                                                                    icd9_short_list
## 1                   Septicemia NOS,Shock w/o trauma NEC,Acute kidney failure NOS,Cardiac arrest,Subendo infarct, initial,CHF NOS,Cellulitis of leg,Prim cardiomyopathy NEC,Protein-cal malnutr NOS
## 2                   Septicemia NOS,Shock w/o trauma NEC,Acute kidney failure NOS,Cardiac arrest,Subendo infarct, initial,CHF NOS,Cellulitis of leg,Prim cardiomyopathy NEC,Protein-cal malnutr NOS
## 3                               Human immuno virus dis,Pneumocystosis,Cachexia,Alkalosis,Bacteremia,Cirrhosis of liver NOS,Mth sus Stph aur els/NOS,Inf mcrg rstn pncllins,Adv eff antiviral drugs
## 4                                                                                Crnry athrscl natve vssl,Intermed coronary synd,DMII wo cmp nt st uncntr,Hypertension NOS,Pure hypercholesterolem
## 5 Fx c2 vertebra-closed,Urin tract infection NOS,Atony of bladder,Fall on stair/step NEC,Fracture patella-closed,Osteoporosis NOS,Mental disor NEC oth dis,Hypertension NOS,Dissect carotid artery
##                                                                                                                                                                                                                                                                                                                                                     icd9_long_list
## 1                                        Unspecified septicemia,Other shock without mention of trauma,Acute kidney failure, unspecified,Cardiac arrest,Subendocardial infarction, initial episode of care,Congestive heart failure, unspecified,Cellulitis and abscess of leg, except foot,Other primary cardiomyopathies,Unspecified protein-calorie malnutrition
## 2                                        Unspecified septicemia,Other shock without mention of trauma,Acute kidney failure, unspecified,Cardiac arrest,Subendocardial infarction, initial episode of care,Congestive heart failure, unspecified,Cellulitis and abscess of leg, except foot,Other primary cardiomyopathies,Unspecified protein-calorie malnutrition
## 3 Human immunodeficiency virus [HIV] disease,Pneumocystosis,Cachexia,Alkalosis,Bacteremia,Cirrhosis of liver without mention of alcohol,Methicillin susceptible Staphylococcus aureus in conditions classified elsewhere and of unspecified site,Infection with microorganisms resistant to penicillins,Antiviral drugs causing adverse effects in therapeutic use
## 4                                                                                                        Coronary atherosclerosis of native coronary artery,Intermediate coronary syndrome,Diabetes mellitus without mention of complication, type II or unspecified type, not stated as uncontrolled,Unspecified essential hypertension,Pure hypercholesterolemia
## 5          Closed fracture of second cervical vertebra,Urinary tract infection, site not specified,Atony of bladder,Accidental fall on or from other stairs or steps,Closed fracture of patella,Osteoporosis, unspecified,Other persistent mental disorders due to conditions classified elsewhere,Unspecified essential hypertension,Dissection of carotid artery
##                                                                                                                                                                                      drug_list
## 1                                                                                                                                                                                         NULL
## 2                                                                                                                                                                                         NULL
## 3 Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium
## 4                                                                                                                                                                                         NULL
## 5                                                                                                                                                                                         NULL
##                                                                                                                                                                                      drug_name
## 1                                                                                                                                                                                         NULL
## 2                                                                                                                                                                                         NULL
## 3 Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium
## 4                                                                                                                                                                                         NULL
## 5                                                                                                                                                                                         NULL
##                                                                                                                                                                              generic_drug_name
## 1                                                                                                                                                                                         NULL
## 2                                                                                                                                                                                         NULL
## 3 Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium,Levothyroxine Sodium
## 4                                                                                                                                                                                         NULL
## 5                                                                                                                                                                                         NULL
##                                      drug_type
## 1                                         NULL
## 2                                         NULL
## 3 MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN,MAIN
## 4                                         NULL
## 5                                         NULL
##                                                        formulary
## 1                                                           NULL
## 2                                                           NULL
## 3 LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25,LEVO25
## 4                                                           NULL
## 5                                                           NULL
##                                                                                                           ndc
## 1                                                                                                        NULL
## 2                                                                                                        NULL
## 3 00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113,00074434113
## 4                                                                                                        NULL
## 5                                                                                                        NULL
##                                                              gsn age
## 1                                                           NULL  76
## 2                                                           NULL  76
## 3 006648,006648,006648,006648,006648,006648,006648,006648,006648  47
## 4                                                           NULL  39
## 5                                                           NULL  89
##   t4_medication t3_medication
## 1            No            No
## 2            No            No
## 3           Yes            No
## 4            No            No
## 5            No            No
```
      
#### Removing unwanted columns:     

```r
cleaned_thyroid <- subset(thyroid_df, select = -c(drug_list, generic_drug_name, drug_type, dob, subject_id,formulary, ndc, gsn, drug_name, fluid, itemid, icd9_list, icd9_short_list, icd9_long_list, charttime))

summary(cleaned_thyroid)
```

```
##     hadm_id       gender       value             valuenum        valueuom    
##  Min.   :100001   F:9450   Min.   :   0.020   1.1    :  782   uIU/mL :10803  
##  1st Qu.:124705   M:8682   1st Qu.:   1.100   1.2    :  688   ng/dL  : 3747  
##  Median :149789            Median :   2.000   1.3    :  609   ug/dL  : 1555  
##  Mean   :149679            Mean   :   8.703   1.4    :  535   uU/ML  : 1259  
##  3rd Qu.:174662            3rd Qu.:   4.900   1.5    :  502   ng/dl  :  336  
##  Max.   :199993            Max.   :3890.000   1      :  428   uG/DL  :  188  
##                            NA's   :187        (Other):14588   (Other):  244  
##                            label            age        t4_medication     
##  Thyroglobulin                :   47   Min.   : 1.00   Length:18132      
##  Thyroid Peroxidase Antibodies:   65   1st Qu.:54.00   Class :character  
##  Thyroid Stimulating Hormone  :12063   Median :67.00   Mode  :character  
##  Thyroxine (T4)               : 1743   Mean   :64.48                     
##  Thyroxine (T4), Free         : 3031   3rd Qu.:79.00                     
##  Triiodothyronine (T3)        : 1183   Max.   :89.00                     
##                                                                          
##  t3_medication     
##  Length:18132      
##  Class :character  
##  Mode  :character  
##                    
##                    
##                    
## 
```
     
#### Checking how many unique hadm_id is there out of 18132 observations:       

```r
data.table::uniqueN(cleaned_thyroid[['hadm_id']])
```

```
## [1] 12161
```
        
## Creating 6 different dataframes, one with all entries for each label, for eventually getting records of unique hadm_id      
    
#### 1. Dataframe with column "label" having just Thyroglobulin:        

```r
cleaned_thyroid_tg <- cleaned_thyroid
cleaned_thyroid_tg <- cleaned_thyroid_tg[cleaned_thyroid_tg$label == "Thyroglobulin", ]
head(cleaned_thyroid_tg,5)
```

```
##      hadm_id gender value valuenum valueuom         label age t4_medication
## 200   150352      M     8        8    ng/mL Thyroglobulin  56            No
## 241   114791      M     4        4    ng/mL Thyroglobulin  84            No
## 705   186403      F    13       13    ng/mL Thyroglobulin  64            No
## 987   112512      F    NA     NULL    ng/mL Thyroglobulin  69            No
## 2320  115026      M    27       27    ng/mL Thyroglobulin  70           Yes
##      t3_medication
## 200             No
## 241             No
## 705             No
## 987            Yes
## 2320            No
```
    
#### 2. Dataframe with column "label" having just Thyroid Peroxidase Antibodies:      

```r
cleaned_thyroid_tpa <- cleaned_thyroid
cleaned_thyroid_tpa <- cleaned_thyroid_tpa[cleaned_thyroid_tpa$label == "Thyroid Peroxidase Antibodies", ]
head(cleaned_thyroid_tpa,5)
```

```
##      hadm_id gender value valuenum valueuom                         label age
## 242   114791      M    NA     NULL    IU/mL Thyroid Peroxidase Antibodies  84
## 696   157668      F    NA     NULL    IU/mL Thyroid Peroxidase Antibodies  72
## 2409  100319      M    14       14    IU/mL Thyroid Peroxidase Antibodies  70
## 2718  142869      F    NA     NULL    IU/mL Thyroid Peroxidase Antibodies  30
## 2816  122241      F    NA     NULL    IU/mL Thyroid Peroxidase Antibodies  36
##      t4_medication t3_medication
## 242             No            No
## 696             No            No
## 2409            No            No
## 2718           Yes            No
## 2816            No            No
```
      
#### 3. Dataframe with column "label" having just Thyroid Stimulating Hormone:         

```r
cleaned_thyroid_tsh <- cleaned_thyroid
cleaned_thyroid_tsh <- cleaned_thyroid_tsh[cleaned_thyroid_tsh$label == "Thyroid Stimulating Hormone", ]
head(cleaned_thyroid_tsh,5)
```

```
##   hadm_id gender value valuenum valueuom                       label age
## 1  145834      M  3.40      3.4    uU/ML Thyroid Stimulating Hormone  76
## 3  185777      F  0.35     0.35   uIU/mL Thyroid Stimulating Hormone  47
## 4  143045      F  1.20      1.2   uIU/mL Thyroid Stimulating Hormone  39
## 5  109235      M  1.50      1.5   uIU/mL Thyroid Stimulating Hormone  89
## 6  165315      F  2.50      2.5    uU/ML Thyroid Stimulating Hormone  64
##   t4_medication t3_medication
## 1            No            No
## 3           Yes            No
## 4            No            No
## 5            No            No
## 6            No            No
```
       
#### 4. Dataframe with column "label" having just Thyroxine (T4):          

```r
cleaned_thyroid_t4 <- cleaned_thyroid
cleaned_thyroid_t4 <- cleaned_thyroid_t4[cleaned_thyroid_t4$label == "Thyroxine (T4)", ]
head(cleaned_thyroid_t4,5)
```

```
##    hadm_id gender value valuenum valueuom          label age t4_medication
## 2   145834      M   6.6      6.6    uG/DL Thyroxine (T4)  76            No
## 16  189535      M   4.4      4.4    ug/dL Thyroxine (T4)  55            No
## 43  190707      M   5.2      5.2    uG/DL Thyroxine (T4)  85            No
## 51  156461      M  10.3     10.3    uG/DL Thyroxine (T4)   1            No
## 61  135671      F   7.2      7.2    uG/DL Thyroxine (T4)  75            No
##    t3_medication
## 2             No
## 16            No
## 43            No
## 51            No
## 61            No
```
      
#### 5. Dataframe with column "label" having just Thyroxine (T4), Free:            

```r
cleaned_thyroid_t4_free <- cleaned_thyroid
cleaned_thyroid_t4_free <- cleaned_thyroid_t4_free[cleaned_thyroid_t4_free$label == "Thyroxine (T4), Free", ]
head(cleaned_thyroid_t4_free,5)
```

```
##    hadm_id gender value valuenum valueuom                label age
## 9   144319      M   1.1      1.1    ng/dL Thyroxine (T4), Free  89
## 13  181750      M   1.3      1.3    ng/dL Thyroxine (T4), Free  80
## 17  189535      M   1.0        1    ng/dL Thyroxine (T4), Free  55
## 26  161160      F   1.3      1.3    ng/dL Thyroxine (T4), Free  35
## 30  125288      F   1.2      1.2    ng/dL Thyroxine (T4), Free  24
##    t4_medication t3_medication
## 9            Yes            No
## 13            No            No
## 17            No            No
## 26            No            No
## 30            No            No
```
      
#### 6. Dataframe with column "label" having just Triiodothyronine (T3):           

```r
cleaned_thyroid_t3 <- cleaned_thyroid
cleaned_thyroid_t3 <- cleaned_thyroid_t3[cleaned_thyroid_t3$label == "Triiodothyronine (T3)", ]
head(cleaned_thyroid_t3,5)
```

```
##    hadm_id gender value valuenum valueuom                 label age
## 27  161160      F    49       49    ng/dL Triiodothyronine (T3)  35
## 62  135671      F    74       74    NG/DL Triiodothyronine (T3)  75
## 66  101148      F    58       58    ng/dL Triiodothyronine (T3)  85
## 71  197273      M    53       53    ng/dL Triiodothyronine (T3)  63
## 75  137006      F    30       30    ng/dL Triiodothyronine (T3)  68
##    t4_medication t3_medication
## 27            No            No
## 62            No            No
## 66           Yes            No
## 71           Yes            No
## 75            No            No
```
       
## Merging dataframes to get data in desirable format     
    
#### 1. Merging dataframe with column "label" having just Thyroglobulin and dataframe with column "label" having just Thyroid Peroxidase Antibodies:        

```r
merged_df <- merge(cleaned_thyroid_tg, cleaned_thyroid_tpa, by.x = "hadm_id", by.y = "hadm_id", all.x = TRUE, all.y = TRUE)

merged_df <- rename(merged_df, c("gender.x"="gender_tg", "value.x"="tg_value_inNumeric", "valuenum.x"="tg_value", "valueuom.x"="tg_value_unit", "age.x"="age_tg", "label.x"="label_tg", "t4_medication.x"="t4_medication_tg", "t3_medication.x"="t3_medication_tg","gender.y"="gender_tpa", "value.y"="tpa_value_inNumeric", "valuenum.y"="tpa_value", "valueuom.y"="tpa_value_unit", "label.y"="label_tpa", "age.y"="age_tpa", "t4_medication.y"="t4_medication_tpa", "t3_medication.y"="t3_medication_tpa"))

merged_df$age <-  ifelse(!is.na(merged_df$age_tg) & is.na(merged_df$age_tpa), merged_df$age_tg, merged_df$age_tpa)

merged_df$gender <-  ifelse(!is.na(merged_df$gender_tg) & is.na(merged_df$gender_tpa), merged_df$gender_tg, merged_df$gender_tpa)

merged_df$t3_medication <-  ifelse(!is.na(merged_df$t3_medication_tg) & is.na(merged_df$t3_medication_tpa), merged_df$t3_medication_tg, merged_df$t3_medication_tpa)

merged_df$t4_medication <-  ifelse(!is.na(merged_df$t4_medication_tg) & is.na(merged_df$t4_medication_tpa), merged_df$t4_medication_tg, merged_df$t4_medication_tpa)

merged_df <- subset(merged_df, select = -c(gender_tg, gender_tpa, age_tg, age_tpa, tg_value, tpa_value, t3_medication_tpa, t3_medication_tg, t4_medication_tg, t4_medication_tpa))

head(merged_df,5)
```

```
##   hadm_id tg_value_inNumeric tg_value_unit      label_tg tpa_value_inNumeric
## 1  100253                 NA          <NA>          <NA>                 289
## 2  100319                 NA          <NA>          <NA>                  14
## 3  100398               1530         ng/mL Thyroglobulin                  NA
## 4  101015                 66         ng/mL Thyroglobulin                  NA
## 5  101432               1990         ng/mL Thyroglobulin                  NA
##   tpa_value_unit                     label_tpa age gender t3_medication
## 1          IU/mL Thyroid Peroxidase Antibodies  26      1            No
## 2          IU/mL Thyroid Peroxidase Antibodies  70      2            No
## 3           <NA>                          <NA>  75      1            No
## 4           <NA>                          <NA>   1      1            No
## 5           <NA>                          <NA>  64      2            No
##   t4_medication
## 1            No
## 2            No
## 3           Yes
## 4           Yes
## 5            No
```

```r
str(merged_df)
```

```
## 'data.frame':	101 obs. of  11 variables:
##  $ hadm_id            : int  100253 100319 100398 101015 101432 103103 103198 104841 105316 109134 ...
##  $ tg_value_inNumeric : num  NA NA 1530 66 1990 NA NA NA NA NA ...
##  $ tg_value_unit      : Factor w/ 10 levels "IU/mL","ng/dl",..: NA NA 5 5 5 NA NA 5 NA NA ...
##  $ label_tg           : Factor w/ 6 levels "Thyroglobulin",..: NA NA 1 1 1 NA NA 1 NA NA ...
##  $ tpa_value_inNumeric: num  289 14 NA NA NA 39 30 NA 103 124 ...
##  $ tpa_value_unit     : Factor w/ 10 levels "IU/mL","ng/dl",..: 1 1 NA NA NA 1 1 NA 1 1 ...
##  $ label_tpa          : Factor w/ 6 levels "Thyroglobulin",..: 2 2 NA NA NA 2 2 NA 2 2 ...
##  $ age                : num  26 70 75 1 64 28 88 52 52 59 ...
##  $ gender             : int  1 2 1 1 2 2 2 2 2 1 ...
##  $ t3_medication      : chr  "No" "No" "No" "No" ...
##  $ t4_medication      : chr  "No" "No" "Yes" "Yes" ...
```
     
#### 2. Merging dataframe, got after merging in step 1 above, and dataframe with column "label" having just Thyroxine (T4):         

```r
merged_df2 <- merge(merged_df, cleaned_thyroid_t4, by.x = "hadm_id", by.y = "hadm_id", all.x = TRUE, all.y = TRUE)

merged_df2 <- rename(merged_df2, c("value"="t4_value_inNumeric", "valuenum"="t4_value", "valueuom"="t4_value_unit", "label"="label_t4"))

merged_df2$age <-  ifelse(!is.na(merged_df2$age.x) & is.na(merged_df2$age.y), merged_df2$age.x, merged_df2$age.y)

merged_df2$gender <-  ifelse(!is.na(merged_df2$gender.x) & is.na(merged_df2$gender.y), merged_df2$gender.x, merged_df2$gender.y)

merged_df2$t3_medication <-  ifelse(!is.na(merged_df2$t3_medication.x) & is.na(merged_df2$t3_medication.y), merged_df2$t3_medication.x, merged_df2$t3_medication.y)

merged_df2$t4_medication <-  ifelse(!is.na(merged_df2$t4_medication.x) & is.na(merged_df2$t4_medication.y), merged_df2$t4_medication.x, merged_df2$t4_medication.y)

merged_df2 <- subset(merged_df2, select = -c(gender.x, gender.y, age.x, age.y, t4_value, t3_medication.x, t3_medication.y, t4_medication.x, t4_medication.y))


head(merged_df2,5)
```

```
##   hadm_id tg_value_inNumeric tg_value_unit label_tg tpa_value_inNumeric
## 1  100045                 NA          <NA>     <NA>                  NA
## 2  100096                 NA          <NA>     <NA>                  NA
## 3  100156                 NA          <NA>     <NA>                  NA
## 4  100210                 NA          <NA>     <NA>                  NA
## 5  100253                 NA          <NA>     <NA>                 289
##   tpa_value_unit                     label_tpa t4_value_inNumeric t4_value_unit
## 1           <NA>                          <NA>               19.8         ug/dL
## 2           <NA>                          <NA>                4.5         ug/dL
## 3           <NA>                          <NA>                5.9         uG/DL
## 4           <NA>                          <NA>                5.1         ug/dL
## 5          IU/mL Thyroid Peroxidase Antibodies               16.2         ug/dL
##         label_t4 age gender t3_medication t4_medication
## 1 Thyroxine (T4)  69      1            No            No
## 2 Thyroxine (T4)   1      1            No           Yes
## 3 Thyroxine (T4)  69      2            No            No
## 4 Thyroxine (T4)  52      2            No            No
## 5 Thyroxine (T4)  26      1            No            No
```

```r
str(merged_df2)
```

```
## 'data.frame':	1792 obs. of  14 variables:
##  $ hadm_id            : int  100045 100096 100156 100210 100253 100262 100300 100302 100319 100343 ...
##  $ tg_value_inNumeric : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ tg_value_unit      : Factor w/ 10 levels "IU/mL","ng/dl",..: NA NA NA NA NA NA NA NA NA NA ...
##  $ label_tg           : Factor w/ 6 levels "Thyroglobulin",..: NA NA NA NA NA NA NA NA NA NA ...
##  $ tpa_value_inNumeric: num  NA NA NA NA 289 NA NA NA 14 NA ...
##  $ tpa_value_unit     : Factor w/ 10 levels "IU/mL","ng/dl",..: NA NA NA NA 1 NA NA NA 1 NA ...
##  $ label_tpa          : Factor w/ 6 levels "Thyroglobulin",..: NA NA NA NA 2 NA NA NA 2 NA ...
##  $ t4_value_inNumeric : num  19.8 4.5 5.9 5.1 16.2 6.9 5.4 7.4 NA 8.9 ...
##  $ t4_value_unit      : Factor w/ 10 levels "IU/mL","ng/dl",..: 7 7 8 7 7 7 7 8 NA 7 ...
##  $ label_t4           : Factor w/ 6 levels "Thyroglobulin",..: 4 4 4 4 4 4 4 4 NA 4 ...
##  $ age                : num  69 1 69 52 26 21 49 48 70 74 ...
##  $ gender             : int  1 1 2 2 1 2 2 1 2 1 ...
##  $ t3_medication      : chr  "No" "No" "No" "No" ...
##  $ t4_medication      : chr  "No" "Yes" "No" "No" ...
```
     
#### 3. Merging dataframe, got after merging in step 2 above, and dataframe with column "label" having just Triiodothyronine (T3):         

```r
merged_df3 <- merge(merged_df2, cleaned_thyroid_t3, by.x = "hadm_id", by.y = "hadm_id", all.x = TRUE, all.y = TRUE)

merged_df3 <- rename(merged_df3, c("value"="t3_value_inNumeric", "valuenum"="t3_value", "valueuom"="t3_value_unit", "label"="label_t3"))

merged_df3$age <- ifelse(!is.na(merged_df3$age.x) & is.na(merged_df3$age.y), merged_df3$age.x, merged_df3$age.y)

merged_df3$gender <-ifelse(!is.na(merged_df3$gender.x) & is.na(merged_df3$gender.y), merged_df3$gender.x, merged_df3$gender.y)

merged_df3$t3_medication <- ifelse(!is.na(merged_df3$t3_medication.x) & is.na(merged_df3$t3_medication.y), merged_df3$t3_medication.x, merged_df3$t3_medication.y)

merged_df3$t4_medication <- ifelse(!is.na(merged_df3$t4_medication.x) & is.na(merged_df3$t4_medication.y), merged_df3$t4_medication.x, merged_df3$t4_medication.y)

merged_df3 <- subset(merged_df3, select = -c(gender.x, gender.y, age.x, age.y, t3_value, t3_medication.x, t3_medication.y, t4_medication.x, t4_medication.y))

head(merged_df3,5)
```

```
##   hadm_id tg_value_inNumeric tg_value_unit label_tg tpa_value_inNumeric
## 1  100045                 NA          <NA>     <NA>                  NA
## 2  100096                 NA          <NA>     <NA>                  NA
## 3  100130                 NA          <NA>     <NA>                  NA
## 4  100156                 NA          <NA>     <NA>                  NA
## 5  100210                 NA          <NA>     <NA>                  NA
##   tpa_value_unit label_tpa t4_value_inNumeric t4_value_unit       label_t4
## 1           <NA>      <NA>               19.8         ug/dL Thyroxine (T4)
## 2           <NA>      <NA>                4.5         ug/dL Thyroxine (T4)
## 3           <NA>      <NA>                 NA          <NA>           <NA>
## 4           <NA>      <NA>                5.9         uG/DL Thyroxine (T4)
## 5           <NA>      <NA>                5.1         ug/dL Thyroxine (T4)
##   t3_value_inNumeric t3_value_unit              label_t3 age gender
## 1                 95         ng/dL Triiodothyronine (T3)  69      1
## 2                143         ng/dL Triiodothyronine (T3)   1      1
## 3                 98         NG/DL Triiodothyronine (T3)  56      1
## 4                 NA          <NA>                  <NA>  69      2
## 5                 85         ng/dL Triiodothyronine (T3)  52      2
##   t3_medication t4_medication
## 1            No            No
## 2            No           Yes
## 3            No            No
## 4            No            No
## 5            No            No
```

```r
str(merged_df3)
```

```
## 'data.frame':	2109 obs. of  17 variables:
##  $ hadm_id            : int  100045 100096 100130 100156 100210 100253 100262 100300 100302 100319 ...
##  $ tg_value_inNumeric : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ tg_value_unit      : Factor w/ 10 levels "IU/mL","ng/dl",..: NA NA NA NA NA NA NA NA NA NA ...
##  $ label_tg           : Factor w/ 6 levels "Thyroglobulin",..: NA NA NA NA NA NA NA NA NA NA ...
##  $ tpa_value_inNumeric: num  NA NA NA NA NA 289 NA NA NA 14 ...
##  $ tpa_value_unit     : Factor w/ 10 levels "IU/mL","ng/dl",..: NA NA NA NA NA 1 NA NA NA 1 ...
##  $ label_tpa          : Factor w/ 6 levels "Thyroglobulin",..: NA NA NA NA NA 2 NA NA NA 2 ...
##  $ t4_value_inNumeric : num  19.8 4.5 NA 5.9 5.1 16.2 6.9 5.4 7.4 NA ...
##  $ t4_value_unit      : Factor w/ 10 levels "IU/mL","ng/dl",..: 7 7 NA 8 7 7 7 7 8 NA ...
##  $ label_t4           : Factor w/ 6 levels "Thyroglobulin",..: 4 4 NA 4 4 4 4 4 4 NA ...
##  $ t3_value_inNumeric : num  95 143 98 NA 85 158 NA NA 55 NA ...
##  $ t3_value_unit      : Factor w/ 10 levels "IU/mL","ng/dl",..: 3 3 4 NA 3 3 NA NA 4 NA ...
##  $ label_t3           : Factor w/ 6 levels "Thyroglobulin",..: 6 6 6 NA 6 6 NA NA 6 NA ...
##  $ age                : num  69 1 56 69 52 26 21 49 48 70 ...
##  $ gender             : int  1 1 1 2 2 1 2 2 1 2 ...
##  $ t3_medication      : chr  "No" "No" "No" "No" ...
##  $ t4_medication      : chr  "No" "Yes" "No" "No" ...
```
       
#### 4. Merging dataframe, got after merging in step 3 above, and dataframe with column "label" having just Thyroxine (T4), Free:      

```r
merged_df4 <- merge(merged_df3, cleaned_thyroid_t4_free, by.x = "hadm_id", by.y = "hadm_id", all.x = TRUE, all.y = TRUE)

merged_df4 <- rename(merged_df4, c("value"="t4_free_value_inNumeric", "valuenum"="t4_free_value", "valueuom"="t4_free_value_unit", "label"="label_t4_free"))

merged_df4$age <- ifelse(!is.na(merged_df4$age.x) & is.na(merged_df4$age.y), merged_df4$age.x, merged_df4$age.y)

merged_df4$gender <- ifelse(!is.na(merged_df4$gender.x) & is.na(merged_df4$gender.y), merged_df4$gender.x, merged_df4$gender.y)

merged_df4$t3_medication <- ifelse(!is.na(merged_df4$t3_medication.x) & is.na(merged_df4$t3_medication.y), merged_df4$t3_medication.x, merged_df4$t3_medication.y)

merged_df4$t4_medication <- ifelse(!is.na(merged_df4$t4_medication.x) & is.na(merged_df4$t4_medication.y), merged_df4$t4_medication.x, merged_df4$t4_medication.y)

merged_df4 <- subset(merged_df4, select = -c(gender.x, gender.y, age.x, age.y, t4_free_value, t3_medication.x, t3_medication.y, t4_medication.x, t4_medication.y))

head(merged_df4,5)
```

```
##   hadm_id tg_value_inNumeric tg_value_unit label_tg tpa_value_inNumeric
## 1  100017                 NA          <NA>     <NA>                  NA
## 2  100045                 NA          <NA>     <NA>                  NA
## 3  100096                 NA          <NA>     <NA>                  NA
## 4  100130                 NA          <NA>     <NA>                  NA
## 5  100153                 NA          <NA>     <NA>                  NA
##   tpa_value_unit label_tpa t4_value_inNumeric t4_value_unit       label_t4
## 1           <NA>      <NA>                 NA          <NA>           <NA>
## 2           <NA>      <NA>               19.8         ug/dL Thyroxine (T4)
## 3           <NA>      <NA>                4.5         ug/dL Thyroxine (T4)
## 4           <NA>      <NA>                 NA          <NA>           <NA>
## 5           <NA>      <NA>                 NA          <NA>           <NA>
##   t3_value_inNumeric t3_value_unit              label_t3
## 1                 NA          <NA>                  <NA>
## 2                 95         ng/dL Triiodothyronine (T3)
## 3                143         ng/dL Triiodothyronine (T3)
## 4                 98         NG/DL Triiodothyronine (T3)
## 5                 NA          <NA>                  <NA>
##   t4_free_value_inNumeric t4_free_value_unit        label_t4_free age gender
## 1                    1.30              ng/dl Thyroxine (T4), Free  27      2
## 2                    1.50              ng/dL Thyroxine (T4), Free  69      1
## 3                    0.70              ng/dL Thyroxine (T4), Free   1      1
## 4                    0.80              ng/dl Thyroxine (T4), Free  56      1
## 5                    0.91              ng/dL Thyroxine (T4), Free  79      2
##   t3_medication t4_medication
## 1            No            No
## 2            No            No
## 3            No           Yes
## 4            No            No
## 5            No           Yes
```

```r
str(merged_df4)
```

```
## 'data.frame':	4130 obs. of  20 variables:
##  $ hadm_id                : int  100017 100045 100096 100130 100153 100156 100187 100210 100225 100247 ...
##  $ tg_value_inNumeric     : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ tg_value_unit          : Factor w/ 10 levels "IU/mL","ng/dl",..: NA NA NA NA NA NA NA NA NA NA ...
##  $ label_tg               : Factor w/ 6 levels "Thyroglobulin",..: NA NA NA NA NA NA NA NA NA NA ...
##  $ tpa_value_inNumeric    : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ tpa_value_unit         : Factor w/ 10 levels "IU/mL","ng/dl",..: NA NA NA NA NA NA NA NA NA NA ...
##  $ label_tpa              : Factor w/ 6 levels "Thyroglobulin",..: NA NA NA NA NA NA NA NA NA NA ...
##  $ t4_value_inNumeric     : num  NA 19.8 4.5 NA NA 5.9 NA 5.1 NA NA ...
##  $ t4_value_unit          : Factor w/ 10 levels "IU/mL","ng/dl",..: NA 7 7 NA NA 8 NA 7 NA NA ...
##  $ label_t4               : Factor w/ 6 levels "Thyroglobulin",..: NA 4 4 NA NA 4 NA 4 NA NA ...
##  $ t3_value_inNumeric     : num  NA 95 143 98 NA NA NA 85 NA NA ...
##  $ t3_value_unit          : Factor w/ 10 levels "IU/mL","ng/dl",..: NA 3 3 4 NA NA NA 3 NA NA ...
##  $ label_t3               : Factor w/ 6 levels "Thyroglobulin",..: NA 6 6 6 NA NA NA 6 NA NA ...
##  $ t4_free_value_inNumeric: num  1.3 1.5 0.7 0.8 0.91 NA 1.2 0.88 1.4 0.5 ...
##  $ t4_free_value_unit     : Factor w/ 10 levels "IU/mL","ng/dl",..: 2 3 3 2 3 NA 3 3 3 2 ...
##  $ label_t4_free          : Factor w/ 6 levels "Thyroglobulin",..: 5 5 5 5 5 NA 5 5 5 5 ...
##  $ age                    : num  27 69 1 56 79 69 64 52 89 58 ...
##  $ gender                 : int  2 1 1 1 2 2 1 2 1 1 ...
##  $ t3_medication          : chr  "No" "No" "No" "No" ...
##  $ t4_medication          : chr  "No" "No" "Yes" "No" ...
```
     
#### 5. Merging dataframe, got after merging in step 4 above, and dataframe with column "label" having just Thyroid Stimulating Hormone:           

```r
merged_df5 <- merge(cleaned_thyroid_tsh, merged_df4, by.x = "hadm_id", by.y = "hadm_id", all.x = TRUE, all.y = TRUE)

merged_df5 <- rename(merged_df5, c("value"="tsh_value_inNumeric", "valuenum"="tsh_value", "valueuom"="tsh_value_unit", "label"="label_tsh"))

merged_df5$age <- ifelse(!is.na(merged_df5$age.x) & is.na(merged_df5$age.y), merged_df5$age.x, merged_df5$age.y)

merged_df5$gender <- ifelse(!is.na(merged_df5$gender.x) & is.na(merged_df5$gender.y), merged_df5$gender.x, merged_df5$gender.y)

merged_df5$t3_medication <- ifelse(!is.na(merged_df5$t3_medication.x) & is.na(merged_df5$t3_medication.y), merged_df5$t3_medication.x, merged_df5$t3_medication.y)

merged_df5$t4_medication <- ifelse(!is.na(merged_df5$t4_medication.x) & is.na(merged_df5$t4_medication.y), merged_df5$t4_medication.x, merged_df5$t4_medication.y)

merged_df5 <- subset(merged_df5, select = -c(gender.x, gender.y, age.x, age.y, tsh_value, t3_medication.x, t3_medication.y, t4_medication.x, t4_medication.y))

head(merged_df5,5)
```

```
##   hadm_id tsh_value_inNumeric tsh_value_unit                   label_tsh
## 1  100001                3.80         uIU/mL Thyroid Stimulating Hormone
## 2  100006                  NA          uU/ML Thyroid Stimulating Hormone
## 3  100017                0.59          uU/ML Thyroid Stimulating Hormone
## 4  100018                0.87         uIU/mL Thyroid Stimulating Hormone
## 5  100021                3.00         uIU/mL Thyroid Stimulating Hormone
##   tg_value_inNumeric tg_value_unit label_tg tpa_value_inNumeric tpa_value_unit
## 1                 NA          <NA>     <NA>                  NA           <NA>
## 2                 NA          <NA>     <NA>                  NA           <NA>
## 3                 NA          <NA>     <NA>                  NA           <NA>
## 4                 NA          <NA>     <NA>                  NA           <NA>
## 5                 NA          <NA>     <NA>                  NA           <NA>
##   label_tpa t4_value_inNumeric t4_value_unit label_t4 t3_value_inNumeric
## 1      <NA>                 NA          <NA>     <NA>                 NA
## 2      <NA>                 NA          <NA>     <NA>                 NA
## 3      <NA>                 NA          <NA>     <NA>                 NA
## 4      <NA>                 NA          <NA>     <NA>                 NA
## 5      <NA>                 NA          <NA>     <NA>                 NA
##   t3_value_unit label_t3 t4_free_value_inNumeric t4_free_value_unit
## 1          <NA>     <NA>                      NA               <NA>
## 2          <NA>     <NA>                      NA               <NA>
## 3          <NA>     <NA>                     1.3              ng/dl
## 4          <NA>     <NA>                      NA               <NA>
## 5          <NA>     <NA>                      NA               <NA>
##          label_t4_free age gender t3_medication t4_medication
## 1                 <NA>  35      1            No            No
## 2                 <NA>  48      1            No            No
## 3 Thyroxine (T4), Free  27      2            No            No
## 4                 <NA>  55      2            No            No
## 5                 <NA>  54      2            No            No
```

```r
str(merged_df5)
```

```
## 'data.frame':	12161 obs. of  23 variables:
##  $ hadm_id                : int  100001 100006 100017 100018 100021 100045 100061 100065 100068 100088 ...
##  $ tsh_value_inNumeric    : num  3.8 NA 0.59 0.87 3 3.6 2.1 2 2.9 3.5 ...
##  $ tsh_value_unit         : Factor w/ 10 levels "IU/mL","ng/dl",..: 9 10 10 9 9 9 9 9 9 9 ...
##  $ label_tsh              : Factor w/ 6 levels "Thyroglobulin",..: 3 3 3 3 3 3 3 3 3 3 ...
##  $ tg_value_inNumeric     : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ tg_value_unit          : Factor w/ 10 levels "IU/mL","ng/dl",..: NA NA NA NA NA NA NA NA NA NA ...
##  $ label_tg               : Factor w/ 6 levels "Thyroglobulin",..: NA NA NA NA NA NA NA NA NA NA ...
##  $ tpa_value_inNumeric    : num  NA NA NA NA NA NA NA NA NA NA ...
##  $ tpa_value_unit         : Factor w/ 10 levels "IU/mL","ng/dl",..: NA NA NA NA NA NA NA NA NA NA ...
##  $ label_tpa              : Factor w/ 6 levels "Thyroglobulin",..: NA NA NA NA NA NA NA NA NA NA ...
##  $ t4_value_inNumeric     : num  NA NA NA NA NA 19.8 NA NA NA NA ...
##  $ t4_value_unit          : Factor w/ 10 levels "IU/mL","ng/dl",..: NA NA NA NA NA 7 NA NA NA NA ...
##  $ label_t4               : Factor w/ 6 levels "Thyroglobulin",..: NA NA NA NA NA 4 NA NA NA NA ...
##  $ t3_value_inNumeric     : num  NA NA NA NA NA 95 NA NA NA NA ...
##  $ t3_value_unit          : Factor w/ 10 levels "IU/mL","ng/dl",..: NA NA NA NA NA 3 NA NA NA NA ...
##  $ label_t3               : Factor w/ 6 levels "Thyroglobulin",..: NA NA NA NA NA 6 NA NA NA NA ...
##  $ t4_free_value_inNumeric: num  NA NA 1.3 NA NA 1.5 NA NA NA NA ...
##  $ t4_free_value_unit     : Factor w/ 10 levels "IU/mL","ng/dl",..: NA NA 2 NA NA 3 NA NA NA NA ...
##  $ label_t4_free          : Factor w/ 6 levels "Thyroglobulin",..: NA NA 5 NA NA 5 NA NA NA NA ...
##  $ age                    : num  35 48 27 55 54 69 62 59 74 74 ...
##  $ gender                 : int  1 1 2 2 2 1 1 2 1 1 ...
##  $ t3_medication          : chr  "No" "No" "No" "No" ...
##  $ t4_medication          : chr  "No" "No" "No" "No" ...
```
       
#### Checking for NAs in df_unique (which contains records for unique hadm_id):       

```r
df_unique <- merged_df5
colSums(is.na(df_unique))
```

```
##                 hadm_id     tsh_value_inNumeric          tsh_value_unit 
##                       0                     192                      98 
##               label_tsh      tg_value_inNumeric           tg_value_unit 
##                      98                   12121                   12114 
##                label_tg     tpa_value_inNumeric          tpa_value_unit 
##                   12114                   12127                   12096 
##               label_tpa      t4_value_inNumeric           t4_value_unit 
##                   12096                   10422                   10418 
##                label_t4      t3_value_inNumeric           t3_value_unit 
##                   10418                   11010                   10978 
##                label_t3 t4_free_value_inNumeric      t4_free_value_unit 
##                   10978                    9149                    9130 
##           label_t4_free                     age                  gender 
##                    9130                       0                       0 
##           t3_medication           t4_medication 
##                       0                       0
```
     
#### Removing unwanted columns:      

```r
df_unique <- subset(df_unique, select = -c(hadm_id, label_tsh, label_tg, label_tpa, label_t4,label_t3, label_t4_free))
head(df_unique,10)
```

```
##    tsh_value_inNumeric tsh_value_unit tg_value_inNumeric tg_value_unit
## 1                 3.80         uIU/mL                 NA          <NA>
## 2                   NA          uU/ML                 NA          <NA>
## 3                 0.59          uU/ML                 NA          <NA>
## 4                 0.87         uIU/mL                 NA          <NA>
## 5                 3.00         uIU/mL                 NA          <NA>
## 6                 3.60         uIU/mL                 NA          <NA>
## 7                 2.10         uIU/mL                 NA          <NA>
## 8                 2.00         uIU/mL                 NA          <NA>
## 9                 2.90         uIU/mL                 NA          <NA>
## 10                3.50         uIU/mL                 NA          <NA>
##    tpa_value_inNumeric tpa_value_unit t4_value_inNumeric t4_value_unit
## 1                   NA           <NA>                 NA          <NA>
## 2                   NA           <NA>                 NA          <NA>
## 3                   NA           <NA>                 NA          <NA>
## 4                   NA           <NA>                 NA          <NA>
## 5                   NA           <NA>                 NA          <NA>
## 6                   NA           <NA>               19.8         ug/dL
## 7                   NA           <NA>                 NA          <NA>
## 8                   NA           <NA>                 NA          <NA>
## 9                   NA           <NA>                 NA          <NA>
## 10                  NA           <NA>                 NA          <NA>
##    t3_value_inNumeric t3_value_unit t4_free_value_inNumeric t4_free_value_unit
## 1                  NA          <NA>                      NA               <NA>
## 2                  NA          <NA>                      NA               <NA>
## 3                  NA          <NA>                     1.3              ng/dl
## 4                  NA          <NA>                      NA               <NA>
## 5                  NA          <NA>                      NA               <NA>
## 6                  95         ng/dL                     1.5              ng/dL
## 7                  NA          <NA>                      NA               <NA>
## 8                  NA          <NA>                      NA               <NA>
## 9                  NA          <NA>                      NA               <NA>
## 10                 NA          <NA>                      NA               <NA>
##    age gender t3_medication t4_medication
## 1   35      1            No            No
## 2   48      1            No            No
## 3   27      2            No            No
## 4   55      2            No            No
## 5   54      2            No            No
## 6   69      1            No            No
## 7   62      1            No            No
## 8   59      2            No            No
## 9   74      1            No           Yes
## 10  74      1            No           Yes
```
     
#### Removing record with NULL tsh_value_unit:         

```r
df_unique <- df_unique[-c(10004), ]
df_unique$tsh_value_unit <- droplevels(df_unique$tsh_value_unit)
head(df_unique$tsh_value_unit,10)
```

```
##  [1] uIU/mL uU/ML  uU/ML  uIU/mL uIU/mL uIU/mL uIU/mL uIU/mL uIU/mL uIU/mL
## Levels: uIU/mL uU/ML
```
      
#### Assigning if a patient has hypothyroidism based on the TSH value:       

```r
for(l in 1:nrow(df_unique)){
  if(is.na(df_unique$tsh_value_inNumeric[l])){
    df_unique$has_hypothyroidism[l] <- NA
    }
  else if (df_unique$tsh_value_inNumeric[l] >= 4.5){
    df_unique$has_hypothyroidism[l] <- "Yes"
    }
  else{
    df_unique$has_hypothyroidism[l] <- "No"
    }
}
head(df_unique,10) # Check tsh value
```

```
##    tsh_value_inNumeric tsh_value_unit tg_value_inNumeric tg_value_unit
## 1                 3.80         uIU/mL                 NA          <NA>
## 2                   NA          uU/ML                 NA          <NA>
## 3                 0.59          uU/ML                 NA          <NA>
## 4                 0.87         uIU/mL                 NA          <NA>
## 5                 3.00         uIU/mL                 NA          <NA>
## 6                 3.60         uIU/mL                 NA          <NA>
## 7                 2.10         uIU/mL                 NA          <NA>
## 8                 2.00         uIU/mL                 NA          <NA>
## 9                 2.90         uIU/mL                 NA          <NA>
## 10                3.50         uIU/mL                 NA          <NA>
##    tpa_value_inNumeric tpa_value_unit t4_value_inNumeric t4_value_unit
## 1                   NA           <NA>                 NA          <NA>
## 2                   NA           <NA>                 NA          <NA>
## 3                   NA           <NA>                 NA          <NA>
## 4                   NA           <NA>                 NA          <NA>
## 5                   NA           <NA>                 NA          <NA>
## 6                   NA           <NA>               19.8         ug/dL
## 7                   NA           <NA>                 NA          <NA>
## 8                   NA           <NA>                 NA          <NA>
## 9                   NA           <NA>                 NA          <NA>
## 10                  NA           <NA>                 NA          <NA>
##    t3_value_inNumeric t3_value_unit t4_free_value_inNumeric t4_free_value_unit
## 1                  NA          <NA>                      NA               <NA>
## 2                  NA          <NA>                      NA               <NA>
## 3                  NA          <NA>                     1.3              ng/dl
## 4                  NA          <NA>                      NA               <NA>
## 5                  NA          <NA>                      NA               <NA>
## 6                  95         ng/dL                     1.5              ng/dL
## 7                  NA          <NA>                      NA               <NA>
## 8                  NA          <NA>                      NA               <NA>
## 9                  NA          <NA>                      NA               <NA>
## 10                 NA          <NA>                      NA               <NA>
##    age gender t3_medication t4_medication has_hypothyroidism
## 1   35      1            No            No                 No
## 2   48      1            No            No               <NA>
## 3   27      2            No            No                 No
## 4   55      2            No            No                 No
## 5   54      2            No            No                 No
## 6   69      1            No            No                 No
## 7   62      1            No            No                 No
## 8   59      2            No            No                 No
## 9   74      1            No           Yes                 No
## 10  74      1            No           Yes                 No
```
       
Assigned 'No' for not having hypothyroidism and 'Yes' for having hypothyroidism    
    
#### Checking how many NAs are present:    

```r
colSums(is.na(df_unique))
```

```
##     tsh_value_inNumeric          tsh_value_unit      tg_value_inNumeric 
##                     192                      98                   12120 
##           tg_value_unit     tpa_value_inNumeric          tpa_value_unit 
##                   12113                   12126                   12095 
##      t4_value_inNumeric           t4_value_unit      t3_value_inNumeric 
##                   10421                   10417                   11009 
##           t3_value_unit t4_free_value_inNumeric      t4_free_value_unit 
##                   10977                    9149                    9130 
##                     age                  gender           t3_medication 
##                       0                       0                       0 
##           t4_medication      has_hypothyroidism 
##                       0                     192
```
    
#### Removing NAs and unwanted variables:       

```r
df_unique <- df_unique[!is.na(df_unique$tsh_value_inNumeric),]

df_unique <- subset(df_unique, select = -c(tsh_value_unit, tg_value_inNumeric, tg_value_unit, tpa_value_inNumeric, tpa_value_unit))

df_unique <- df_unique[!is.na(df_unique$t4_free_value_inNumeric), ]
df_unique <- df_unique[!is.na(df_unique$t3_value_inNumeric),]
colSums(is.na(df_unique))
```

```
##     tsh_value_inNumeric      t4_value_inNumeric           t4_value_unit 
##                       0                     286                     286 
##      t3_value_inNumeric           t3_value_unit t4_free_value_inNumeric 
##                       0                       0                       0 
##      t4_free_value_unit                     age                  gender 
##                       0                       0                       0 
##           t3_medication           t4_medication      has_hypothyroidism 
##                       0                       0                       0
```
     
#### Creating final dataframe and dropping unused levels:        

```r
final_df <- df_unique
final_df$t4_value_unit <- droplevels(final_df$t4_value_unit)
final_df$t3_value_unit <- droplevels(final_df$t3_value_unit)
final_df$t4_free_value_unit <- droplevels(final_df$t4_free_value_unit)

final_df <- rename(final_df, c("t4_value_inNumeric"="t4_value", "t3_value_inNumeric"="t3_value", "t4_free_value_inNumeric"="t4_free_value"))

final_df <- subset(final_df, select = -c(tsh_value_inNumeric ,t3_value_unit, t4_free_value_unit, t4_value_unit))

final_df$gender <- as.factor(final_df$gender)
final_df$gender <- revalue(final_df$gender, c("1"="Female", "2"="Male"))

head(final_df,10)
```

```
##     t4_value t3_value t4_free_value age gender t3_medication t4_medication
## 6       19.8       95          1.50  69 Female            No            No
## 12       4.5      143          0.70   1 Female            No           Yes
## 16        NA       98          0.80  56 Female            No            No
## 27       5.1       85          0.88  52   Male            No            No
## 40       7.4       55          1.30  48 Female            No            No
## 46       8.9       56          2.10  74 Female            No            No
## 55        NA       32          0.80  57 Female            No            No
## 91        NA       83          1.70  84 Female            No            No
## 93        NA       97          1.50  29 Female            No            No
## 101     18.9      363          5.60  20 Female            No            No
##     has_hypothyroidism
## 6                   No
## 12                 Yes
## 16                  No
## 27                  No
## 40                  No
## 46                  No
## 55                  No
## 91                 Yes
## 93                  No
## 101                 No
```
     
#### Summary of final dataframe:    

```r
final_df$t3_medication <- factor(final_df$t3_medication)
final_df$t4_medication <- factor(final_df$t4_medication)
final_df$has_hypothyroidism <- factor(final_df$has_hypothyroidism)
summary(final_df)
```

```
##     t4_value         t3_value      t4_free_value        age           gender   
##  Min.   : 1.300   Min.   : 23.00   Min.   :0.170   Min.   : 1.00   Female:362  
##  1st Qu.: 4.350   1st Qu.: 49.00   1st Qu.:0.850   1st Qu.:52.00   Male  :287  
##  Median : 5.700   Median : 63.00   Median :1.100   Median :66.00               
##  Mean   : 5.942   Mean   : 68.74   Mean   :1.124   Mean   :63.12               
##  3rd Qu.: 7.100   3rd Qu.: 83.00   3rd Qu.:1.300   3rd Qu.:77.00               
##  Max.   :22.200   Max.   :363.00   Max.   :6.200   Max.   :89.00               
##  NA's   :286                                                                   
##  t3_medication t4_medication has_hypothyroidism
##  No :642       No :358       No :364           
##  Yes:  7       Yes:291       Yes:285           
##                                                
##                                                
##                                                
##                                                
## 
```
       
#### Correlation between variables using ranks for factor variables:      

```r
final_df_numeric <- final_df
final_df_numeric$has_hypothyroidism <- as.numeric(final_df_numeric$has_hypothyroidism)
final_df_numeric$gender <- as.numeric(final_df_numeric$gender)
final_df_numeric$t3_medication <- as.numeric(final_df_numeric$t3_medication)
final_df_numeric$t4_medication <- as.numeric(final_df_numeric$t4_medication)

c <- cor(final_df_numeric, use= "pairwise.complete.obs", method = "spearman")
c
```

```
##                       t4_value    t3_value t4_free_value          age
## t4_value            1.00000000  0.58161151    0.67166260 -0.015815016
## t3_value            0.58161151  1.00000000    0.36301347 -0.175462530
## t4_free_value       0.67166260  0.36301347    1.00000000  0.139524212
## age                -0.01581502 -0.17546253    0.13952421  1.000000000
## gender             -0.07315593 -0.00259187   -0.01631378 -0.023381097
## t3_medication      -0.02051961 -0.03957619   -0.05029692 -0.002230081
## t4_medication      -0.26299894 -0.23503402   -0.17557544  0.035772339
## has_hypothyroidism -0.15677556 -0.08155946   -0.22555479  0.089466104
##                          gender t3_medication t4_medication has_hypothyroidism
## t4_value           -0.073155931  -0.020519607   -0.26299894        -0.15677556
## t3_value           -0.002591870  -0.039576187   -0.23503402        -0.08155946
## t4_free_value      -0.016313782  -0.050296915   -0.17557544        -0.22555479
## age                -0.023381097  -0.002230081    0.03577234         0.08946610
## gender              1.000000000  -0.002869326   -0.10408907        -0.05646476
## t3_medication      -0.002869326   1.000000000    0.05582933         0.02783459
## t4_medication      -0.104089066   0.055829327    1.00000000         0.41334656
## has_hypothyroidism -0.056464756   0.027834589    0.41334656         1.00000000
```
      
t4_free_value and t4_value variables have a correlation of 0.67166260         
t3_value and t4_value variables have a correlation of 0.58161151       
      
# Data Visualization       

```r
corrplot((c),method = "number")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-31-1.png)<!-- -->
      
## Univariate distribution         

```r
g <- ggplot(final_df, aes(x = t4_value))
g + geom_density(color = "cyan") +
  ggtitle("Distribution of t4_value variable") +
  xlab("T4 value of patients (ug/dL)") + ylab("Density")
```

```
## Warning: Removed 286 rows containing non-finite values (stat_density).
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-32-1.png)<!-- -->
    
284 NAs are present.     
     
The distribution of T4 value of patients (ug/dL) present in this dataframe shows that most of the patients have values around the peak of 5ug/dL.     
         

```r
g1 <- ggplot(final_df, aes(x = t3_value))
g1 + geom_density(color = "red") +
  ggtitle("Distribution of t3_value variable") +
  xlab("T3 value of patients (ng/dL)") + ylab("Density")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-33-1.png)<!-- -->
     
The distribution of T3 value of patients (ng/dL) present in this dataframe shows that most of the patients have values around the peak of 50ng/dL.      
        

```r
g2 <- ggplot(final_df, aes(x = t4_free_value))
g2 + geom_density(color = "green") +
  ggtitle("Distribution of t4_free_value variable") +
  xlab("T4 (free) value of patients (ng/dL)") + ylab("Density")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-34-1.png)<!-- -->
        
The distribution of T4 (free) value of patients (ng/dL) present in this dataframe shows that most of the patients have values around the peak of 1.2ng/dL.       
      

```r
g3 <- ggplot(final_df, aes(x = age))
g3 + geom_histogram(binwidth = 2, color = "purple") +
  ggtitle("Distribution of age variable") +
  xlab("Age of patient (years)") + ylab("Count")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-35-1.png)<!-- -->
        
The distribution of age of patients (years) present in this dataframe shows that more than half of the patients are older than 50years of age.         
          

```r
g4 <- ggplot(final_df, aes(x = gender))
g4 + geom_bar(color = "cyan") +
  ggtitle("Distribution of gender variable") +
  xlab("Classifying patient as female or male") + ylab("Count")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-36-1.png)<!-- -->
       
The distribution of gender of patients present in this dataframe shows that there are more female patients than male patients.           
       

```r
g5 <- ggplot(final_df, aes(x = t3_medication))
g5 + geom_bar(color = "red") +
  ggtitle("Distribution of t3_medication variable") +
  xlab("Classifying whether patient has been prescribed T3 medication or not") + ylab("Count")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-37-1.png)<!-- -->
      
The distribution of whether patients present in this dataframe have been prescribed T3 medication or not shows that there are way more patients who have not been prescribed T3 medication than who have been prescribed T3 medication.        
         

```r
g6 <- ggplot(final_df, aes(x = t4_medication))
g6 + geom_bar(color = "green") +
  ggtitle("Distribution of t4_medication variable") +
  xlab("Classifying whether patient has been prescribed T4 medication or not") + ylab("Count")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-38-1.png)<!-- -->
         
The distribution of whether patients present in this dataframe have been prescribed T4 medication or not shows that there are slightly more patients who have not been prescribed T4 medication than who have been prescribed T4 medication.       
        

```r
g7 <- ggplot(final_df, aes(x = has_hypothyroidism))
g7 + geom_bar(color = "purple") +
  ggtitle("Distribution of has_hypothyroidism variable") +
  xlab("Classifying whether patient has hypothyroidism or not") + ylab("Count")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-39-1.png)<!-- -->
      
The distribution of whether patients present in this dataframe have hypothyroidism or not shows that there are slightly more patients who do not have hypothyroidism than who have hypothyroidism.    
      
## Bivariate Distribution      

```r
g8 <- ggplot(final_df)
g8 + geom_boxplot(aes(x = has_hypothyroidism, y = age, color = has_hypothyroidism))+
  ggtitle("Distribution of age variable with respect to whether patient has hypothyroidism or not") + xlab("Classifying whether patient has hypothyroidism or not") + ylab("Age of patient (years)")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-40-1.png)<!-- -->
        
For this dataframe, median age (years) for patients having hypothyroidism is higher than the median age (years) for patients not having hypothyroidism.     
        

```r
g9 <- ggplot(final_df)
g9 + geom_density(aes(x = t4_value, color = t3_medication))+
  ggtitle("Distribution of t4_value variable with respect to whether patient has been prescribed T3 medication or not") + xlab("T4 value of patients (ug/dL)") + ylab("Density") 
```

```
## Warning: Removed 286 rows containing non-finite values (stat_density).
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-41-1.png)<!-- -->
     
For this dataframe, T4 value for patients (ug/dL) that have been prescribed T3 medication have a peak around 7.5ug/dL while T4 value for patients (ug/dL) that have not been prescribed T3 medication have a peak around 5ug/dL.      
     

```r
g10 <- ggplot(final_df)
g10 + geom_bar(aes(x = gender, fill = has_hypothyroidism), position = "dodge") + ggtitle("Distribution of gender and has_hypothyroidism variable") +
  xlab("Gender of patients") + ylab("Count")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-42-1.png)<!-- -->
      
For this dataframe, we have more male and female patients who do not have hypothyroidism than who have hypothyroidism repectively.       
      

```r
g11 <- ggplot(final_df)
g11 + geom_bar(aes(x = t4_medication, fill = has_hypothyroidism), position = "dodge") + ggtitle("Distribution of t3_medication and has_hypothyroidism variable") +
  xlab("Classification of patients whether they have been prescribed T3 medication or not") + ylab("Count")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-43-1.png)<!-- -->
     
For this dataframe, number of patients who have not been prescribed T3 medication and do not have hypothyroidism are more than double the number of patients who have not been prescribed T3 medication but have hypothyroidism. On the contrary, number of patients who have been prescribed T3 medication and have hypothyroidism are more than double the number of patients who have been prescribed T3 medication but do not have hypothyroidism.      
      
## Trivariate Distribution

```r
g12 <- ggplot(final_df)
g12 + geom_point(aes(x = age, y = t4_free_value, color = t4_medication)) +
  ggtitle("Distribution of t3_value and t4_free_value variable with respect to t4_medication variable") + xlab("T3 value of patients (ng/dL)") + ylab("T4 (free) value of patients (ng/dL)")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-44-1.png)<!-- -->
   
For this dataframe, patients tend to have T3 value (ng/dL) greater than 50ng/dL for more than half of the patients irrespective of whether they have been prescribed T4 medication or not. Also, patients tend to have T4 (free) value (ng/dL) greater than 1ng/dL for more than half of the patients who have not been prescribed T4 medication while there seems to be around equal number of patients having been prescribed t4_medication above and below T4 (free) value (ng/dL) of 1ng/dL.     
    
#### Converting response variable into 0 and 1:     

```r
final_df$has_hypothyroidism <- ifelse(final_df$has_hypothyroidism == "Yes", 1, 0)
final_df$has_hypothyroidism <- as.factor(final_df$has_hypothyroidism)
```
    
# Splitting the data into training and testing data      

```r
set.seed(400) 
intrain <- createDataPartition(y = final_df$has_hypothyroidism, p= 0.7, list = FALSE)
training <- final_df[intrain,]
testing <- final_df[-intrain,] 
dim(intrain); dim(training); dim(testing)
```

```
## [1] 455   1
```

```
## [1] 455   8
```

```
## [1] 194   8
```
         
## Imputation by median for training and testing data    

```r
training$t4_value[is.na(training$t4_value)] <- median(training$t4_value, na.rm = TRUE)
testing$t4_value[is.na(testing$t4_value)] <- median(testing$t4_value, na.rm = TRUE)
colSums(is.na(training))
```

```
##           t4_value           t3_value      t4_free_value                age 
##                  0                  0                  0                  0 
##             gender      t3_medication      t4_medication has_hypothyroidism 
##                  0                  0                  0                  0
```

```r
colSums(is.na(testing))
```

```
##           t4_value           t3_value      t4_free_value                age 
##                  0                  0                  0                  0 
##             gender      t3_medication      t4_medication has_hypothyroidism 
##                  0                  0                  0                  0
```
          
#### Table for seeing how many patients have or do not have hypothyroidism in training and testing data:           

```r
table(training$has_hypothyroidism)
```

```
## 
##   0   1 
## 255 200
```

```r
table(testing$has_hypothyroidism)
```

```
## 
##   0   1 
## 109  85
```
    
# Logistic Regression     
     
## Using step function     

```r
fit_null <- glm(has_hypothyroidism ~ 1, family = binomial(), data = training)
summary(fit_null)
```

```
## 
## Call:
## glm(formula = has_hypothyroidism ~ 1, family = binomial(), data = training)
## 
## Deviance Residuals: 
##    Min      1Q  Median      3Q     Max  
## -1.076  -1.076  -1.076   1.282   1.282  
## 
## Coefficients:
##             Estimate Std. Error z value Pr(>|z|)  
## (Intercept) -0.24295    0.09445  -2.572   0.0101 *
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 624.1  on 454  degrees of freedom
## Residual deviance: 624.1  on 454  degrees of freedom
## AIC: 626.1
## 
## Number of Fisher Scoring iterations: 3
```
    
### Main effects and second order interactions     

```r
fit_main_step <- glm(has_hypothyroidism ~ (.)^2, family = binomial(), data = training)

fit_step1 <- step(fit_null, scope=list(lower=fit_null,upper=fit_main_step),direction="both")
```

```
## Start:  AIC=626.1
## has_hypothyroidism ~ 1
## 
##                 Df Deviance    AIC
## + t4_medication  1   561.58 565.58
## + t4_free_value  1   602.93 606.93
## + t4_value       1   617.21 621.21
## + gender         1   619.90 623.90
## + age            1   621.60 625.60
## <none>               624.10 626.10
## + t3_value       1   622.17 626.17
## + t3_medication  1   624.04 628.04
## 
## Step:  AIC=565.58
## has_hypothyroidism ~ t4_medication
## 
##                 Df Deviance    AIC
## + t4_free_value  1   548.83 554.83
## <none>               561.58 565.58
## + age            1   560.33 566.33
## + t4_value       1   560.40 566.40
## + gender         1   560.79 566.79
## + t3_value       1   561.18 567.18
## + t3_medication  1   561.53 567.53
## - t4_medication  1   624.10 626.10
## 
## Step:  AIC=554.83
## has_hypothyroidism ~ t4_medication + t4_free_value
## 
##                               Df Deviance    AIC
## + t3_value                     1   545.55 553.55
## + age                          1   545.97 553.97
## <none>                             548.83 554.83
## + t4_value                     1   548.13 556.13
## + gender                       1   548.14 556.14
## + t4_free_value:t4_medication  1   548.35 556.35
## + t3_medication                1   548.79 556.79
## - t4_free_value                1   561.58 565.58
## - t4_medication                1   602.93 606.93
## 
## Step:  AIC=553.55
## has_hypothyroidism ~ t4_medication + t4_free_value + t3_value
## 
##                               Df Deviance    AIC
## + age                          1   539.72 549.72
## <none>                             545.55 553.55
## - t3_value                     1   548.83 554.83
## + gender                       1   545.01 555.01
## + t4_free_value:t4_medication  1   545.20 555.20
## + t4_value                     1   545.42 555.42
## + t3_value:t4_free_value       1   545.42 555.42
## + t3_medication                1   545.51 555.51
## + t3_value:t4_medication       1   545.52 555.52
## - t4_free_value                1   561.18 567.18
## - t4_medication                1   602.91 608.91
## 
## Step:  AIC=549.72
## has_hypothyroidism ~ t4_medication + t4_free_value + t3_value + 
##     age
## 
##                               Df Deviance    AIC
## + t3_value:age                 1   537.68 549.68
## <none>                             539.72 549.72
## + t4_free_value:age            1   538.82 550.82
## + gender                       1   539.07 551.07
## + t4_free_value:t4_medication  1   539.31 551.31
## + t3_value:t4_free_value       1   539.57 551.57
## + t4_value                     1   539.59 551.59
## + age:t4_medication            1   539.60 551.60
## + t3_medication                1   539.62 551.62
## + t3_value:t4_medication       1   539.69 551.69
## - age                          1   545.55 553.55
## - t3_value                     1   545.97 553.97
## - t4_free_value                1   559.41 567.41
## - t4_medication                1   596.85 604.85
## 
## Step:  AIC=549.68
## has_hypothyroidism ~ t4_medication + t4_free_value + t3_value + 
##     age + t3_value:age
## 
##                               Df Deviance    AIC
## <none>                             537.68 549.68
## - t3_value:age                 1   539.72 549.72
## + t4_free_value:t4_medication  1   537.23 551.23
## + gender                       1   537.24 551.24
## + t4_free_value:age            1   537.35 551.35
## + t4_value                     1   537.53 551.53
## + t3_value:t4_medication       1   537.57 551.57
## + t3_medication                1   537.58 551.58
## + t3_value:t4_free_value       1   537.58 551.58
## + age:t4_medication            1   537.68 551.68
## - t4_free_value                1   557.04 567.04
## - t4_medication                1   594.50 604.50
```

```r
summary(fit_step1)
```

```
## 
## Call:
## glm(formula = has_hypothyroidism ~ t4_medication + t4_free_value + 
##     t3_value + age + t3_value:age, family = binomial(), data = training)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -1.8711  -0.8940  -0.6063   0.9567   2.2968  
## 
## Coefficients:
##                    Estimate Std. Error z value Pr(>|z|)    
## (Intercept)      -2.4808627  1.0604000  -2.340   0.0193 *  
## t4_medicationYes  1.5771901  0.2175366   7.250 4.16e-13 ***
## t4_free_value    -1.3277137  0.3212050  -4.134 3.57e-05 ***
## t3_value          0.0280174  0.0122278   2.291   0.0219 *  
## age               0.0355704  0.0162857   2.184   0.0290 *  
## t3_value:age     -0.0002851  0.0001997  -1.428   0.1534    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 624.10  on 454  degrees of freedom
## Residual deviance: 537.68  on 449  degrees of freedom
## AIC: 549.68
## 
## Number of Fisher Scoring iterations: 4
```

```r
pR2(fit_step1)
```

```
## fitting null model for pseudo-r2
```

```
##          llh      llhNull           G2     McFadden         r2ML         r2CU 
## -268.8387848 -312.0496483   86.4217271    0.1384743    0.1729895    0.2317927
```
       
#### Removing insignifcant variable age:t3_value which has p-value equal to 0.1534  (Significance level = 0.05):       

```r
fit_main_step2 <- glm(formula = has_hypothyroidism ~ t4_medication + t4_free_value + 
    age + t3_value, family = binomial(), data = training)

summary(fit_main_step2)
```

```
## 
## Call:
## glm(formula = has_hypothyroidism ~ t4_medication + t4_free_value + 
##     age + t3_value, family = binomial(), data = training)
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -1.7350  -0.8867  -0.6084   0.9669   2.1414  
## 
## Coefficients:
##                   Estimate Std. Error z value Pr(>|z|)    
## (Intercept)      -1.211778   0.578497  -2.095   0.0362 *  
## t4_medicationYes  1.576917   0.217015   7.266 3.69e-13 ***
## t4_free_value    -1.329716   0.319465  -4.162 3.15e-05 ***
## age               0.013972   0.005886   2.374   0.0176 *  
## t3_value          0.011998   0.004836   2.481   0.0131 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 624.10  on 454  degrees of freedom
## Residual deviance: 539.72  on 450  degrees of freedom
## AIC: 549.72
## 
## Number of Fisher Scoring iterations: 4
```

```r
pR2(fit_main_step2)
```

```
## fitting null model for pseudo-r2
```

```
##          llh      llhNull           G2     McFadden         r2ML         r2CU 
## -269.8586864 -312.0496483   84.3819238    0.1352059    0.1692736    0.2268137
```
       
McFadden: 0.1352059    
r2CU: 0.2268137     
AIC: 549.72      
       
## Equation for fitted coefficients      
      
logit(has_hypothyroidism) = (t4_medicationYes * 1.576917) + (t4_free_value * -1.329716) + (age * 0.013972) + (t3_value * 0.011998) - 1.211778    
      
## Effect of significant variables on has_hypothyroidism variable     
     
For every one-unit (ng/dL) increase in T4 (free) value, the odds of having hypothyroidism is multiplied by exp(-1.329716) = 0.2645 (ie: a decrease of 26.45%) on average, when all other variables in the model are held constant.       
       
For every one-unit (years) increase in age, the odds of having hypothyroidism is multiplied by exp(0.013972) = 1.01407 (ie: an increase of 1.41%) on average, when all other variables in the model are held constant.       
        
For every one-unit (ng/dL) increase in T3 value, the odds of having hypothyroidism is multiplied by exp(0.011998) = 1.01207 (ie: an increase of 1.21%) on average, when all other variables in the model are held constant.         
         
For been prescribed to take T4 medication as compared with not been prescribed to take T4 medication, the odds of having hypothyroidism is multiplied by exp(1.576917) = 4.840011 (ie: an increase of 84%) on average when all other variables in the model are held constant.  
    
#### Predicting on test data:      

```r
pred_log <- predict(fit_main_step2, testing, type="response")
print(pred_log)
```

```
##          6         46         55         93        101        142        151 
## 0.24928037 0.09126014 0.25064125 0.16282623 0.01757650 0.34236846 0.60985332 
##        159        265        368        371        468        490        542 
## 0.22994972 0.60291005 0.34600802 0.50497974 0.65886800 0.24901124 0.28277249 
##        671        683        808        810        827        891        902 
## 0.33648561 0.23254085 0.68667928 0.62722734 0.26013993 0.53805200 0.09934576 
##        936        971       1228       1230       1274       1299       1423 
## 0.18095306 0.53282135 0.70541860 0.25715089 0.12537288 0.20195143 0.53604755 
##       1492       1529       1587       1621       1810       1844       1888 
## 0.57188914 0.72469223 0.41737710 0.70101149 0.20517687 0.35162314 0.63190555 
##       1967       1991       2164       2242       2253       2293       2396 
## 0.71888894 0.75041540 0.47989004 0.52273027 0.29140316 0.68484049 0.74925196 
##       2426       2475       2489       2524       2565       2600       2648 
## 0.76553624 0.55706718 0.23780049 0.51399213 0.57681150 0.57499226 0.24354043 
##       2762       2766       2777       2846       2864       2945       3051 
## 0.15244018 0.46212368 0.29676275 0.16469611 0.13394034 0.75812974 0.23487915 
##       3070       3075       3176       3191       3192       3391       3431 
## 0.68819797 0.31741115 0.21360804 0.27491231 0.21009697 0.35135639 0.28787558 
##       3443       3448       3699       3767       3858       3884       4020 
## 0.72522310 0.74437553 0.78632455 0.29610939 0.49164006 0.59088833 0.28217846 
##       4067       4076       4117       4159       4176       4186       4207 
## 0.73882537 0.31513608 0.38080711 0.71512125 0.66895352 0.75321280 0.28171192 
##       4251       4284       4372       4403       4443       4502       4507 
## 0.29508040 0.67749922 0.26216753 0.17411571 0.24236459 0.27824058 0.32312557 
##       4536       4537       4615       4654       4816       4852       4909 
## 0.75430462 0.34830564 0.81706475 0.33668725 0.30953354 0.18782014 0.63545021 
##       4958       5032       5061       5125       5133       5215       5228 
## 0.72991635 0.37459801 0.34821616 0.78468922 0.73379551 0.70868759 0.46250488 
##       5232       5252       5276       5306       5596       5762       5786 
## 0.26620094 0.68386637 0.18680558 0.39227155 0.70629939 0.56402900 0.10481295 
##       5858       5917       6005       6030       6097       6175       6221 
## 0.72611327 0.27738715 0.78714741 0.51003970 0.36702715 0.32585595 0.14721670 
##       6246       6297       6566       6606       6835       6880       6956 
## 0.78727741 0.14489887 0.44189231 0.46635122 0.62760516 0.56822120 0.42657576 
##       7101       7195       7204       7211       7261       7347       7362 
## 0.59708040 0.25162097 0.25855427 0.61520399 0.73439437 0.70603514 0.18656731 
##       7381       7452       7553       7602       7677       7704       7726 
## 0.15776920 0.21337226 0.20887998 0.19510210 0.36211726 0.20083708 0.35141738 
##       7764       7854       7879       8024       8045       8110       8271 
## 0.77502975 0.69390853 0.27148814 0.42511531 0.17402037 0.41601945 0.33875577 
##       8272       8288       8303       8363       8381       8387       8434 
## 0.78834194 0.35239322 0.73229755 0.10702634 0.34345041 0.56592931 0.48631624 
##       8456       8549       8564       8621       8755       8798       8843 
## 0.43951500 0.64270055 0.30616291 0.32558743 0.32672143 0.34835470 0.58965926 
##       8868       8998       9046       9128       9162       9399       9451 
## 0.55069370 0.70128359 0.70486527 0.12795528 0.86831554 0.25203635 0.27013530 
##       9659       9674       9750       9769       9794       9832       9908 
## 0.33714289 0.23678854 0.20887585 0.25586341 0.67841690 0.70691462 0.31489150 
##       9946      10093      10098      10255      10360      10376      10450 
## 0.14672325 0.29041781 0.76041374 0.60100526 0.69426832 0.80294107 0.57618659 
##      10573      10853      10890      10990      11040      11081      11085 
## 0.53102028 0.65676381 0.60919888 0.82210796 0.27237592 0.62221641 0.66819174 
##      11200      11225      11268      11296      11454      11482      11527 
## 0.53586326 0.60523444 0.23838062 0.22392474 0.23753757 0.22222906 0.65177353 
##      11621      12047      12057      12104      12128 
## 0.43267438 0.21072761 0.55527034 0.42873931 0.53129942
```
     
#### Displaying confusion matrix:       

```r
confusionMatrix(data = as.factor(as.numeric(pred_log > 0.5)), reference = testing$has_hypothyroidism, positive = "1")
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction  0  1
##          0 86 24
##          1 23 61
##                                           
##                Accuracy : 0.7577          
##                  95% CI : (0.6912, 0.8162)
##     No Information Rate : 0.5619          
##     P-Value [Acc > NIR] : 1.112e-08       
##                                           
##                   Kappa : 0.5073          
##                                           
##  Mcnemar's Test P-Value : 1               
##                                           
##             Sensitivity : 0.7176          
##             Specificity : 0.7890          
##          Pos Pred Value : 0.7262          
##          Neg Pred Value : 0.7818          
##              Prevalence : 0.4381          
##          Detection Rate : 0.3144          
##    Detection Prevalence : 0.4330          
##       Balanced Accuracy : 0.7533          
##                                           
##        'Positive' Class : 1               
## 
```
      
Accuracy : 0.7577    
Sensitivity : 0.7176               
Specificity : 0.7890      
     
#### ROC:  

```r
logROC <- roc(testing$has_hypothyroidism, predict(fit_main_step2, testing, type = "response"))
```

```
## Setting levels: control = 0, case = 1
```

```
## Setting direction: controls < cases
```

```r
plot.roc(logROC, print.auc=TRUE, legacy.axes=TRUE) 
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-54-1.png)<!-- -->
    
AUC = 0.831   
      
#### Predicted vs Actual values plot:    

```r
g13 <- ggplot(testing)
g13 + geom_boxplot(aes(x = has_hypothyroidism, y = pred_log, color = has_hypothyroidism))+
  ggtitle("Distribution of predicted values and actual values") +
  xlab("Actual Values") + ylab("Predicted Values")
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-55-1.png)<!-- -->
      
For the testing data, we can see a median predicted value of around 0.25 for people who do not have hypothyroidism (actual value is 0) while we can see a median predicted value of around 0.65 for people who do have hypothyroidism (actual value is 1). Also, we see false positives and false negatives as the threshold is 0.5 for predicted values (if predicted value >0.5 patient has hypothyroidism and if predicted value <=0.5 patient does not have hypothyroidism).   
    
     
# SVM    
    
#### Converting response variable into "No" for 0 and "Yes" for 1:     

```r
levels(training$has_hypothyroidism) <- c("No", "Yes")
levels(testing$has_hypothyroidism) <- c("No", "Yes")
```
     
### With Linear kernel     

```r
trctrl <- trainControl(summaryFunction=twoClassSummary,classProbs = TRUE, method = "repeatedcv", number = 10, repeats = 10)

grid <- expand.grid(C = c(0.005,0.01, 0.05, 0.1, 0.25, 0.5, 0.75, 1, 1.25, 1.5, 1.75, 2,5))

set.seed(300)
svm_Linear <- train(has_hypothyroidism ~., data = training, 
                    method = "svmLinear",
                    trControl=trctrl,
                    preProcess = c("center", "scale"),
                    metric="ROC",
                    tuneGrid = grid,
                    tuneLength = 10)
print(svm_Linear)
```

```
## Support Vector Machines with Linear Kernel 
## 
## 455 samples
##   7 predictor
##   2 classes: 'No', 'Yes' 
## 
## Pre-processing: centered (7), scaled (7) 
## Resampling: Cross-Validated (10 fold, repeated 10 times) 
## Summary of sample sizes: 410, 409, 409, 410, 409, 410, ... 
## Resampling results across tuning parameters:
## 
##   C      ROC        Sens       Spec
##   0.005  0.7149754  0.7140462  0.65
##   0.010  0.7184446  0.7175538  0.65
##   0.050  0.7025715  0.7175538  0.65
##   0.100  0.7089746  0.7175538  0.65
##   0.250  0.7082777  0.7175538  0.65
##   0.500  0.7166877  0.7175538  0.65
##   0.750  0.7125508  0.7175538  0.65
##   1.000  0.7118654  0.7175538  0.65
##   1.250  0.7104662  0.7175538  0.65
##   1.500  0.7146715  0.7175538  0.65
##   1.750  0.7175877  0.7175538  0.65
##   2.000  0.7126338  0.7175538  0.65
##   5.000  0.7111708  0.7175538  0.65
## 
## ROC was used to select the optimal model using the largest value.
## The final value used for the model was C = 0.01.
```
     

```r
plot(svm_Linear)
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-58-1.png)<!-- -->
   
#### Prediction on testing data:         

```r
pred_svmLinear <- predict(svm_Linear, newdata = testing)
print(pred_svmLinear)
```

```
##   [1] No  No  No  No  No  No  Yes Yes Yes No  No  Yes No  No  No  No  Yes Yes
##  [19] No  Yes No  No  Yes Yes No  No  No  Yes Yes Yes No  Yes No  No  Yes Yes
##  [37] Yes No  Yes No  Yes Yes Yes Yes No  Yes Yes Yes No  No  No  No  No  No 
##  [55] Yes No  Yes No  No  No  No  No  No  Yes Yes Yes No  Yes Yes No  Yes No 
##  [73] No  Yes Yes Yes No  No  Yes No  No  No  No  No  Yes No  Yes No  No  Yes
##  [91] Yes Yes No  No  Yes Yes Yes Yes No  Yes No  No  Yes Yes No  Yes No  Yes
## [109] Yes No  No  No  Yes No  Yes Yes Yes Yes No  Yes No  No  Yes Yes Yes No 
## [127] No  No  No  No  No  No  No  Yes Yes No  No  No  Yes No  Yes No  Yes No 
## [145] No  Yes Yes No  Yes No  No  No  No  Yes Yes Yes Yes No  No  No  No  No 
## [163] No  No  No  Yes Yes No  No  No  Yes Yes Yes Yes Yes Yes Yes Yes Yes No 
## [181] Yes Yes Yes Yes No  No  No  No  Yes No  No  No  No  Yes
## Levels: No Yes
```
     
#### Displaying confusion matrix:       

```r
confusionMatrix(pred_svmLinear, testing$has_hypothyroidism, positive = "Yes")
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction No Yes
##        No  84  21
##        Yes 25  64
##                                           
##                Accuracy : 0.7629          
##                  95% CI : (0.6967, 0.8209)
##     No Information Rate : 0.5619          
##     P-Value [Acc > NIR] : 4.449e-09       
##                                           
##                   Kappa : 0.5209          
##                                           
##  Mcnemar's Test P-Value : 0.6583          
##                                           
##             Sensitivity : 0.7529          
##             Specificity : 0.7706          
##          Pos Pred Value : 0.7191          
##          Neg Pred Value : 0.8000          
##              Prevalence : 0.4381          
##          Detection Rate : 0.3299          
##    Detection Prevalence : 0.4588          
##       Balanced Accuracy : 0.7618          
##                                           
##        'Positive' Class : Yes             
## 
```
   
Accuracy : 0.7629     
Sensitivity : 0.7529     
Specificity : 0.7706   
    
#### ROC:   

```r
lin_Probs <- predict(svm_Linear, testing, type = "prob")
lin_ROC <- roc(testing$has_hypothyroidism, lin_Probs[, "Yes"])
```

```
## Setting levels: control = No, case = Yes
```

```
## Setting direction: controls < cases
```

```r
plot.roc(lin_ROC, print.auc=TRUE, legacy.axes=TRUE)   
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-61-1.png)<!-- -->
     
AUC = 0.824   
     
### With Radial kernel    

```r
trctrl2 <- trainControl(summaryFunction=twoClassSummary,classProbs = TRUE, savePredictions = T, method = "repeatedcv", number = 10, repeats = 10)

svmGrid <- expand.grid(sigma= 2^c(-15,-10, -5, 0), C= 2^c(0:5))

set.seed(300)
svm_Radial <- train(has_hypothyroidism ~., data = training, 
              method = "svmRadial",
              trControl = trctrl2,
              preProcess = c("center", "scale"),
              metric = "ROC",
              tuneGrid = svmGrid,
              tuneLength = 10) 
print(svm_Radial)
```

```
## Support Vector Machines with Radial Basis Function Kernel 
## 
## 455 samples
##   7 predictor
##   2 classes: 'No', 'Yes' 
## 
## Pre-processing: centered (7), scaled (7) 
## Resampling: Cross-Validated (10 fold, repeated 10 times) 
## Summary of sample sizes: 410, 409, 409, 410, 409, 410, ... 
## Resampling results across tuning parameters:
## 
##   sigma         C   ROC        Sens       Spec  
##   3.051758e-05   1  0.7140677  0.7140462  0.6500
##   3.051758e-05   2  0.7143538  0.7140462  0.6500
##   3.051758e-05   4  0.7149800  0.7140462  0.6500
##   3.051758e-05   8  0.7146454  0.7140462  0.6500
##   3.051758e-05  16  0.7148785  0.7140462  0.6500
##   3.051758e-05  32  0.7149154  0.7140462  0.6500
##   9.765625e-04   1  0.7145892  0.7140462  0.6500
##   9.765625e-04   2  0.7147085  0.7140462  0.6500
##   9.765625e-04   4  0.7166692  0.7171692  0.6500
##   9.765625e-04   8  0.7180308  0.7175538  0.6500
##   9.765625e-04  16  0.7133331  0.7175538  0.6500
##   9.765625e-04  32  0.7082608  0.7175538  0.6500
##   3.125000e-02   1  0.7120162  0.7199385  0.6380
##   3.125000e-02   2  0.7087215  0.7187846  0.6320
##   3.125000e-02   4  0.7122154  0.7211231  0.6260
##   3.125000e-02   8  0.7079869  0.7203538  0.6220
##   3.125000e-02  16  0.7007946  0.7235077  0.6010
##   3.125000e-02  32  0.6975962  0.7317231  0.5875
##   1.000000e+00   1  0.6844792  0.7964000  0.5345
##   1.000000e+00   2  0.6689446  0.7881231  0.5180
##   1.000000e+00   4  0.6491254  0.7835385  0.4540
##   1.000000e+00   8  0.6302077  0.8050308  0.3735
##   1.000000e+00  16  0.6117269  0.8203385  0.3480
##   1.000000e+00  32  0.6070785  0.8415077  0.3040
## 
## ROC was used to select the optimal model using the largest value.
## The final values used for the model were sigma = 0.0009765625 and C = 8.
```
       

```r
plot(svm_Radial)
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-63-1.png)<!-- -->
    
#### Predicting on test data:    

```r
pred_Radial <- predict(svm_Radial, newdata = testing)
print(pred_Radial)
```

```
##   [1] No  No  No  No  No  No  Yes Yes Yes No  No  Yes No  No  No  No  Yes Yes
##  [19] No  Yes No  No  Yes Yes No  No  No  Yes Yes Yes No  Yes No  No  Yes Yes
##  [37] Yes No  Yes No  Yes Yes Yes Yes No  Yes Yes Yes No  No  No  No  No  No 
##  [55] Yes No  Yes No  No  No  No  No  No  Yes Yes Yes No  Yes Yes No  Yes No 
##  [73] No  Yes Yes Yes No  No  Yes No  No  No  No  No  Yes No  Yes No  No  Yes
##  [91] Yes Yes No  No  Yes Yes Yes Yes No  Yes No  No  Yes Yes No  Yes No  Yes
## [109] Yes No  No  No  Yes No  Yes Yes Yes Yes No  Yes No  No  Yes Yes Yes No 
## [127] No  No  No  No  No  No  No  Yes Yes No  No  No  Yes No  Yes No  Yes No 
## [145] No  Yes Yes No  Yes No  No  No  No  Yes Yes Yes Yes No  No  No  No  No 
## [163] No  No  No  Yes Yes No  No  No  Yes Yes Yes Yes Yes Yes Yes Yes Yes No 
## [181] Yes Yes Yes Yes No  No  No  No  Yes No  No  No  No  Yes
## Levels: No Yes
```
      
#### Displaying Confusion Matrix:    

```r
confusionMatrix(pred_Radial, testing$has_hypothyroidism, positive = "Yes")
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction No Yes
##        No  84  21
##        Yes 25  64
##                                           
##                Accuracy : 0.7629          
##                  95% CI : (0.6967, 0.8209)
##     No Information Rate : 0.5619          
##     P-Value [Acc > NIR] : 4.449e-09       
##                                           
##                   Kappa : 0.5209          
##                                           
##  Mcnemar's Test P-Value : 0.6583          
##                                           
##             Sensitivity : 0.7529          
##             Specificity : 0.7706          
##          Pos Pred Value : 0.7191          
##          Neg Pred Value : 0.8000          
##              Prevalence : 0.4381          
##          Detection Rate : 0.3299          
##    Detection Prevalence : 0.4588          
##       Balanced Accuracy : 0.7618          
##                                           
##        'Positive' Class : Yes             
## 
```
     
Accuracy : 0.7629    
Sensitivity : 0.7529               
Specificity : 0.7706    
    
#### ROC:       

```r
rad_Probs <- predict(svm_Radial, testing, type = "prob")
rad_ROC <- roc(testing$has_hypothyroidism, rad_Probs[, "Yes"])
```

```
## Setting levels: control = No, case = Yes
```

```
## Setting direction: controls < cases
```

```r
plot.roc(rad_ROC, print.auc=TRUE, legacy.axes=TRUE)    
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-66-1.png)<!-- -->
      
AUC = 0.802    
     
### With Polynomial kernel    

```r
trctrl3 <- trainControl(summaryFunction=twoClassSummary,classProbs = TRUE, savePredictions = T, method = "cv", number = 5)

svmGrid_poly <- expand.grid(degree = c(2,3),
                            scale = seq(0.1, 1, by = .2),
                            C = c(0.005,0.01, 0.05, 0.1, 0.25, 0.5,
                                  0.75, 1, 1.25, 1.5, 1.75, 2,5))

set.seed(300)
svm_Poly <- train(has_hypothyroidism ~., data = training, 
              method = "svmPoly",
              trControl = trctrl3,
              preProcess = c("center", "scale"),
              metric = "ROC",
              tuneGrid = svmGrid_poly,
              tuneLength = 10) 
print(svm_Poly)
```

```
## Support Vector Machines with Polynomial Kernel 
## 
## 455 samples
##   7 predictor
##   2 classes: 'No', 'Yes' 
## 
## Pre-processing: centered (7), scaled (7) 
## Resampling: Cross-Validated (5 fold) 
## Summary of sample sizes: 364, 364, 364, 364, 364 
## Resampling results across tuning parameters:
## 
##   degree  scale  C      ROC        Sens       Spec 
##   2       0.1    0.005  0.7077451  0.7098039  0.650
##   2       0.1    0.010  0.7083333  0.7137255  0.650
##   2       0.1    0.050  0.7206863  0.7176471  0.650
##   2       0.1    0.100  0.7167647  0.7176471  0.645
##   2       0.1    0.250  0.7075490  0.7176471  0.645
##   2       0.1    0.500  0.7040196  0.7215686  0.645
##   2       0.1    0.750  0.7041176  0.7215686  0.635
##   2       0.1    1.000  0.7016667  0.7215686  0.630
##   2       0.1    1.250  0.6993137  0.7215686  0.630
##   2       0.1    1.500  0.6973529  0.7215686  0.625
##   2       0.1    1.750  0.6967647  0.7215686  0.620
##   2       0.1    2.000  0.6960784  0.7254902  0.620
##   2       0.1    5.000  0.6953922  0.7294118  0.615
##   2       0.3    0.005  0.7069608  0.7254902  0.640
##   2       0.3    0.010  0.7109804  0.7215686  0.640
##   2       0.3    0.050  0.7116667  0.7215686  0.640
##   2       0.3    0.100  0.7097059  0.7254902  0.620
##   2       0.3    0.250  0.7010784  0.7333333  0.620
##   2       0.3    0.500  0.6965686  0.7294118  0.610
##   2       0.3    0.750  0.6913725  0.7333333  0.610
##   2       0.3    1.000  0.6948039  0.7372549  0.610
##   2       0.3    1.250  0.6866667  0.7333333  0.590
##   2       0.3    1.500  0.6922549  0.7294118  0.605
##   2       0.3    1.750  0.6890196  0.7333333  0.585
##   2       0.3    2.000  0.6889216  0.7333333  0.590
##   2       0.3    5.000  0.6902941  0.7372549  0.595
##   2       0.5    0.005  0.7069608  0.7333333  0.620
##   2       0.5    0.010  0.7032353  0.7333333  0.615
##   2       0.5    0.050  0.7072549  0.7254902  0.620
##   2       0.5    0.100  0.6976471  0.7333333  0.610
##   2       0.5    0.250  0.6945098  0.7372549  0.610
##   2       0.5    0.500  0.6899020  0.7294118  0.605
##   2       0.5    0.750  0.6922549  0.7372549  0.585
##   2       0.5    1.000  0.6951961  0.7333333  0.605
##   2       0.5    1.250  0.6936275  0.7333333  0.600
##   2       0.5    1.500  0.6948039  0.7372549  0.585
##   2       0.5    1.750  0.6950980  0.7333333  0.585
##   2       0.5    2.000  0.6925490  0.7372549  0.585
##   2       0.5    5.000  0.6927451  0.7372549  0.590
##   2       0.7    0.005  0.7046078  0.7490196  0.605
##   2       0.7    0.010  0.7037255  0.7372549  0.610
##   2       0.7    0.050  0.6968627  0.7294118  0.610
##   2       0.7    0.100  0.7002941  0.7411765  0.600
##   2       0.7    0.250  0.6930392  0.7294118  0.585
##   2       0.7    0.500  0.6930392  0.7372549  0.600
##   2       0.7    0.750  0.6899020  0.7372549  0.590
##   2       0.7    1.000  0.6908824  0.7372549  0.595
##   2       0.7    1.250  0.6842157  0.7411765  0.575
##   2       0.7    1.500  0.6868627  0.7372549  0.600
##   2       0.7    1.750  0.6919608  0.7411765  0.585
##   2       0.7    2.000  0.6913725  0.7372549  0.580
##   2       0.7    5.000  0.6914706  0.7450980  0.545
##   2       0.9    0.005  0.7015686  0.7490196  0.605
##   2       0.9    0.010  0.7071569  0.7333333  0.615
##   2       0.9    0.050  0.6972549  0.7372549  0.605
##   2       0.9    0.100  0.6933333  0.7333333  0.600
##   2       0.9    0.250  0.6928431  0.7450980  0.585
##   2       0.9    0.500  0.6946078  0.7411765  0.580
##   2       0.9    0.750  0.6892157  0.7333333  0.585
##   2       0.9    1.000  0.6946078  0.7411765  0.580
##   2       0.9    1.250  0.6873529  0.7372549  0.575
##   2       0.9    1.500  0.6914706  0.7333333  0.580
##   2       0.9    1.750  0.6933333  0.7411765  0.580
##   2       0.9    2.000  0.6916667  0.7529412  0.545
##   2       0.9    5.000  0.6933333  0.7333333  0.580
##   3       0.1    0.005  0.7114706  0.7176471  0.645
##   3       0.1    0.010  0.7143137  0.7176471  0.645
##   3       0.1    0.050  0.7225490  0.7176471  0.645
##   3       0.1    0.100  0.7210784  0.7215686  0.645
##   3       0.1    0.250  0.7158824  0.7215686  0.625
##   3       0.1    0.500  0.7121569  0.7254902  0.620
##   3       0.1    0.750  0.7096078  0.7294118  0.595
##   3       0.1    1.000  0.7101961  0.7294118  0.590
##   3       0.1    1.250  0.7105882  0.7333333  0.595
##   3       0.1    1.500  0.7098039  0.7529412  0.590
##   3       0.1    1.750  0.7108824  0.7490196  0.595
##   3       0.1    2.000  0.7089216  0.7568627  0.585
##   3       0.1    5.000  0.7079412  0.7764706  0.560
##   3       0.3    0.005  0.7172549  0.7215686  0.630
##   3       0.3    0.010  0.7166667  0.7333333  0.620
##   3       0.3    0.050  0.7111765  0.7411765  0.590
##   3       0.3    0.100  0.7102941  0.7607843  0.580
##   3       0.3    0.250  0.7049020  0.7843137  0.545
##   3       0.3    0.500  0.6998039  0.8235294  0.485
##   3       0.3    0.750  0.6925490  0.8196078  0.420
##   3       0.3    1.000  0.6887255  0.9176471  0.190
##   3       0.3    1.250  0.6850980  0.8509804  0.285
##   3       0.3    1.500  0.6800980  0.9176471  0.140
##   3       0.3    1.750  0.6776471  0.8980392  0.240
##   3       0.3    2.000  0.6742157  0.9254902  0.170
##   3       0.3    5.000  0.6608824  0.9058824  0.165
##   3       0.5    0.005  0.7151961  0.7372549  0.605
##   3       0.5    0.010  0.7134314  0.7411765  0.600
##   3       0.5    0.050  0.7033333  0.7764706  0.570
##   3       0.5    0.100  0.6994118  0.8392157  0.410
##   3       0.5    0.250  0.6857843  0.8745098  0.275
##   3       0.5    0.500  0.6746078  0.8313725  0.400
##   3       0.5    0.750  0.6693137  0.9176471  0.165
##   3       0.5    1.000  0.6633333  0.9333333  0.095
##   3       0.5    1.250  0.5779412  0.9725490  0.050
##   3       0.5    1.500  0.6600980  0.9019608  0.180
##   3       0.5    1.750  0.5997059  0.9411765  0.130
##   3       0.5    2.000  0.6588235  0.9254902  0.120
##   3       0.5    5.000  0.6618627  0.9490196  0.070
##   3       0.7    0.005  0.7121569  0.7568627  0.595
##   3       0.7    0.010  0.7097059  0.7725490  0.560
##   3       0.7    0.050  0.5950980  0.8352941  0.400
##   3       0.7    0.100  0.6861765  0.8431373  0.365
##   3       0.7    0.250  0.6718627  0.9333333  0.160
##   3       0.7    0.500  0.6621569  0.9568627  0.090
##   3       0.7    0.750  0.6582353  0.9058824  0.195
##   3       0.7    1.000  0.6619608  0.9568627  0.100
##   3       0.7    1.250  0.6634314  0.9137255  0.145
##   3       0.7    1.500  0.6645098  0.9490196  0.100
##   3       0.7    1.750  0.6629412  0.9607843  0.060
##   3       0.7    2.000  0.6623529  0.9607843  0.035
##   3       0.7    5.000  0.6584314  0.9843137  0.010
##   3       0.9    0.005  0.7100980  0.7686275  0.575
##   3       0.9    0.010  0.6994118  0.7803922  0.555
##   3       0.9    0.050  0.6881373  0.8509804  0.355
##   3       0.9    0.100  0.6767647  0.8470588  0.360
##   3       0.9    0.250  0.6630392  0.9176471  0.165
##   3       0.9    0.500  0.6635294  0.9215686  0.115
##   3       0.9    0.750  0.6639216  0.9333333  0.110
##   3       0.9    1.000  0.6629412  0.9803922  0.015
##   3       0.9    1.250  0.6604902  0.9490196  0.070
##   3       0.9    1.500  0.6600000  0.9176471  0.115
##   3       0.9    1.750  0.5775490  0.9647059  0.040
##   3       0.9    2.000  0.6604902  0.9882353  0.020
##   3       0.9    5.000  0.6607843  0.9764706  0.045
## 
## ROC was used to select the optimal model using the largest value.
## The final values used for the model were degree = 3, scale = 0.1 and C = 0.05.
```
    

```r
plot(svm_Poly)
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-68-1.png)<!-- -->
     
#### Predicting on test data:     

```r
pred_Poly <- predict(svm_Poly, newdata = testing)
print(pred_Poly)
```

```
##   [1] No  No  No  No  Yes No  Yes Yes Yes No  No  Yes No  No  No  No  Yes Yes
##  [19] No  Yes No  No  Yes Yes No  No  No  Yes Yes Yes No  Yes No  No  Yes Yes
##  [37] Yes No  Yes No  Yes Yes Yes Yes No  Yes Yes Yes No  No  No  No  No  Yes
##  [55] Yes No  Yes No  No  No  No  No  No  Yes Yes Yes No  Yes Yes No  Yes No 
##  [73] No  Yes Yes Yes No  No  Yes No  No  No  No  No  Yes No  Yes No  No  Yes
##  [91] Yes Yes No  No  Yes Yes Yes Yes No  Yes No  No  Yes Yes No  Yes No  Yes
## [109] Yes No  No  No  Yes No  Yes Yes Yes Yes No  Yes No  No  Yes Yes Yes No 
## [127] No  No  No  No  No  No  No  Yes Yes No  No  No  Yes No  Yes No  Yes No 
## [145] No  Yes Yes No  Yes No  No  No  No  Yes Yes Yes Yes No  No  No  No  No 
## [163] No  No  No  Yes Yes No  No  No  Yes Yes Yes Yes Yes Yes Yes Yes Yes No 
## [181] Yes Yes Yes Yes No  No  No  No  Yes No  No  No  No  Yes
## Levels: No Yes
```
      
#### Displaying Confusion Matrix:     

```r
confusionMatrix(pred_Poly, testing$has_hypothyroidism, positive = "Yes")
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction No Yes
##        No  82  21
##        Yes 27  64
##                                           
##                Accuracy : 0.7526          
##                  95% CI : (0.6857, 0.8116)
##     No Information Rate : 0.5619          
##     P-Value [Acc > NIR] : 2.706e-08       
##                                           
##                   Kappa : 0.5013          
##                                           
##  Mcnemar's Test P-Value : 0.4705          
##                                           
##             Sensitivity : 0.7529          
##             Specificity : 0.7523          
##          Pos Pred Value : 0.7033          
##          Neg Pred Value : 0.7961          
##              Prevalence : 0.4381          
##          Detection Rate : 0.3299          
##    Detection Prevalence : 0.4691          
##       Balanced Accuracy : 0.7526          
##                                           
##        'Positive' Class : Yes             
## 
```
   
Accuracy : 0.7526   
Sensitivity : 0.7529          
Specificity : 0.7523     
    
#### ROC:    

```r
pol_Probs <- predict(svm_Poly, testing, type = "prob")
pol_ROC <- roc(testing$has_hypothyroidism, pol_Probs[, "Yes"])
```

```
## Setting levels: control = No, case = Yes
```

```
## Setting direction: controls < cases
```

```r
plot.roc(pol_ROC, print.auc=TRUE, legacy.axes=TRUE)    
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-71-1.png)<!-- -->
     
AUC = 0.813            
      
      
# Gradient Boosting       

```r
gbmGrid <-  expand.grid(interaction.depth = c(1, 5), 
                        n.trees = c(100,250,500,1000,5000), 
                        shrinkage = c(0.01, 0.1),
                        n.minobsinnode = c(10,20))

set.seed(300)
gbmFit1 <- train(has_hypothyroidism ~ ., data = training, 
                 method = "gbm", 
                 metric="ROC",
                 trControl = trctrl2,
                 verbose = FALSE,
                 tuneGrid = gbmGrid)
print(gbmFit1)
```

```
## Stochastic Gradient Boosting 
## 
## 455 samples
##   7 predictor
##   2 classes: 'No', 'Yes' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold, repeated 10 times) 
## Summary of sample sizes: 410, 409, 409, 410, 409, 410, ... 
## Resampling results across tuning parameters:
## 
##   shrinkage  interaction.depth  n.minobsinnode  n.trees  ROC        Sens     
##   0.01       1                  10               100     0.7155269  0.7183385
##   0.01       1                  10               250     0.7157792  0.7359538
##   0.01       1                  10               500     0.7133715  0.7449231
##   0.01       1                  10              1000     0.7048015  0.7379231
##   0.01       1                  10              5000     0.6727308  0.7092769
##   0.01       1                  20               100     0.7148354  0.7183385
##   0.01       1                  20               250     0.7190719  0.7363692
##   0.01       1                  20               500     0.7167154  0.7453231
##   0.01       1                  20              1000     0.7087569  0.7378615
##   0.01       1                  20              5000     0.6775815  0.7132615
##   0.01       5                  10               100     0.7060462  0.7642308
##   0.01       5                  10               250     0.6992700  0.7413846
##   0.01       5                  10               500     0.6874892  0.7320308
##   0.01       5                  10              1000     0.6695108  0.7222615
##   0.01       5                  10              5000     0.6317954  0.6653692
##   0.01       5                  20               100     0.7123854  0.7579231
##   0.01       5                  20               250     0.7053008  0.7335538
##   0.01       5                  20               500     0.6916300  0.7234154
##   0.01       5                  20              1000     0.6763285  0.7136154
##   0.01       5                  20              5000     0.6370046  0.6724154
##   0.10       1                  10               100     0.7024281  0.7375385
##   0.10       1                  10               250     0.6875754  0.7139231
##   0.10       1                  10               500     0.6700700  0.7081077
##   0.10       1                  10              1000     0.6502062  0.6923385
##   0.10       1                  10              5000     0.5933646  0.6338462
##   0.10       1                  20               100     0.7039992  0.7382769
##   0.10       1                  20               250     0.6923454  0.7205846
##   0.10       1                  20               500     0.6764315  0.7097231
##   0.10       1                  20              1000     0.6569185  0.6944615
##   0.10       1                  20              5000     0.6003208  0.6326154
##   0.10       5                  10               100     0.6678554  0.7074615
##   0.10       5                  10               250     0.6431385  0.6783846
##   0.10       5                  10               500     0.6281600  0.6670769
##   0.10       5                  10              1000     0.6134231  0.6473538
##   0.10       5                  10              5000     0.5972769  0.6341077
##   0.10       5                  20               100     0.6724223  0.7077077
##   0.10       5                  20               250     0.6547869  0.6891385
##   0.10       5                  20               500     0.6362392  0.6728308
##   0.10       5                  20              1000     0.6210631  0.6584308
##   0.10       5                  20              5000     0.6007154  0.6369231
##   Spec  
##   0.6470
##   0.6175
##   0.5800
##   0.5695
##   0.5385
##   0.6455
##   0.6175
##   0.5870
##   0.5745
##   0.5465
##   0.5245
##   0.5375
##   0.5315
##   0.5330
##   0.5235
##   0.5360
##   0.5405
##   0.5370
##   0.5265
##   0.5270
##   0.5685
##   0.5370
##   0.5390
##   0.5380
##   0.4800
##   0.5700
##   0.5595
##   0.5450
##   0.5265
##   0.4825
##   0.5470
##   0.5255
##   0.5080
##   0.5045
##   0.4925
##   0.5300
##   0.5355
##   0.5180
##   0.5030
##   0.4970
## 
## ROC was used to select the optimal model using the largest value.
## The final values used for the model were n.trees = 250, interaction.depth =
##  1, shrinkage = 0.01 and n.minobsinnode = 20.
```
     
#### Variable Importance         

```r
par(mar = c(5, 8, 1, 1))
summary(object = gbmFit1, las = 2)
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-73-1.png)<!-- -->

```
##                               var    rel.inf
## t4_medicationYes t4_medicationYes 64.4029446
## t4_free_value       t4_free_value 18.1217636
## age                           age  9.2176559
## t4_value                 t4_value  5.3743552
## t3_value                 t3_value  2.3957773
## genderMale             genderMale  0.4875034
## t3_medicationYes t3_medicationYes  0.0000000
```
   
    

```r
plot(gbmFit1)
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-74-1.png)<!-- -->
     
#### Predicting on test data:    

```r
pred_boost <- predict(gbmFit1, newdata = testing)
print(pred_boost)
```

```
##   [1] No  No  No  No  No  No  Yes Yes Yes No  No  Yes No  No  No  No  Yes Yes
##  [19] No  Yes No  No  Yes Yes No  No  No  Yes Yes Yes No  Yes No  No  Yes Yes
##  [37] Yes No  No  No  Yes Yes Yes Yes No  No  No  Yes No  No  No  No  No  No 
##  [55] Yes No  Yes No  No  No  No  No  No  Yes Yes Yes No  Yes Yes No  Yes No 
##  [73] No  Yes Yes Yes No  No  Yes No  No  No  No  No  Yes No  Yes No  No  No 
##  [91] Yes Yes No  No  Yes Yes Yes Yes No  Yes No  No  Yes Yes No  Yes No  Yes
## [109] No  No  No  No  Yes No  Yes Yes Yes Yes No  Yes No  No  Yes Yes Yes No 
## [127] No  No  No  No  No  No  No  Yes Yes No  No  No  Yes No  Yes No  Yes No 
## [145] No  Yes Yes No  Yes No  No  No  No  Yes Yes Yes Yes No  No  No  No  No 
## [163] No  No  No  Yes Yes No  No  No  Yes Yes Yes Yes Yes Yes Yes Yes Yes No 
## [181] Yes Yes Yes Yes No  No  No  No  Yes No  No  No  No  Yes
## Levels: No Yes
```
      
#### Displaying Confusion Matrix:     

```r
confusionMatrix(pred_boost, testing$has_hypothyroidism, positive = "Yes")
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction No Yes
##        No  86  24
##        Yes 23  61
##                                           
##                Accuracy : 0.7577          
##                  95% CI : (0.6912, 0.8162)
##     No Information Rate : 0.5619          
##     P-Value [Acc > NIR] : 1.112e-08       
##                                           
##                   Kappa : 0.5073          
##                                           
##  Mcnemar's Test P-Value : 1               
##                                           
##             Sensitivity : 0.7176          
##             Specificity : 0.7890          
##          Pos Pred Value : 0.7262          
##          Neg Pred Value : 0.7818          
##              Prevalence : 0.4381          
##          Detection Rate : 0.3144          
##    Detection Prevalence : 0.4330          
##       Balanced Accuracy : 0.7533          
##                                           
##        'Positive' Class : Yes             
## 
```
      
Accuracy : 0.7577     
Sensitivity : 0.7176             
Specificity : 0.7890       
     
#### ROC:      

```r
boost_Probs <- predict(gbmFit1, testing, type = "prob")
boostROC <- roc(testing$has_hypothyroidism, boost_Probs[, "Yes"]) 
```

```
## Setting levels: control = No, case = Yes
```

```
## Setting direction: controls < cases
```

```r
plot.roc(boostROC, asp = NA, print.auc=TRUE, legacy.axes=TRUE) 
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-77-1.png)<!-- -->
     
AUC = 0.817    
      
# Random Forest         

```r
grid_rf <- expand.grid(.mtry = (1:7)) 

set.seed(300)
rf <- train(has_hypothyroidism ~., data = training, method = "rf",
                    trControl=trctrl2,
                    metric="ROC",
                    tuneGrid = grid_rf) 
print(rf) 
```

```
## Random Forest 
## 
## 455 samples
##   7 predictor
##   2 classes: 'No', 'Yes' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold, repeated 10 times) 
## Summary of sample sizes: 410, 409, 409, 410, 409, 410, ... 
## Resampling results across tuning parameters:
## 
##   mtry  ROC        Sens       Spec  
##   1     0.7042550  0.7819846  0.5170
##   2     0.6834596  0.7375385  0.5530
##   3     0.6582369  0.6911692  0.5265
##   4     0.6533331  0.6906923  0.5305
##   5     0.6496469  0.6856769  0.5260
##   6     0.6467565  0.6845231  0.5230
##   7     0.6440162  0.6800769  0.5235
## 
## ROC was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 1.
```
    

```r
plot(rf)
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-79-1.png)<!-- -->
      
#### Variable Importance        

```r
rfImp <- varImp(rf, scale = FALSE)
rfImp
```

```
## rf variable importance
## 
##                  Overall
## t4_medicationYes 16.4347
## t4_free_value    11.4457
## age              11.1838
## t3_value         11.1671
## t4_value         10.0989
## genderMale        2.2961
## t3_medicationYes  0.5755
```
      

```r
plot(rfImp)
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-81-1.png)<!-- -->
      
#### Predicting on test data:    

```r
pred_rf <- predict(rf, newdata = testing)
print(pred_rf)
```

```
##   [1] No  No  No  No  No  No  Yes Yes Yes No  No  Yes No  No  No  No  Yes Yes
##  [19] No  Yes No  No  Yes Yes No  No  No  Yes Yes No  No  Yes No  No  Yes Yes
##  [37] Yes No  No  No  Yes Yes Yes Yes No  No  No  Yes No  No  No  No  No  No 
##  [55] Yes No  Yes No  No  No  No  No  No  Yes Yes Yes No  No  No  No  Yes No 
##  [73] No  Yes Yes Yes No  No  Yes No  No  No  No  No  Yes No  Yes No  No  No 
##  [91] Yes Yes No  No  Yes Yes Yes Yes No  Yes No  No  Yes Yes No  Yes No  Yes
## [109] No  No  No  No  Yes No  No  No  Yes Yes No  Yes No  No  Yes Yes Yes No 
## [127] No  No  No  No  No  No  No  Yes Yes No  No  No  No  No  Yes No  Yes No 
## [145] No  Yes Yes No  Yes No  No  No  No  Yes Yes Yes Yes No  No  No  No  No 
## [163] No  No  No  Yes Yes No  No  No  Yes Yes Yes Yes Yes Yes Yes Yes Yes No 
## [181] Yes Yes No  Yes No  No  No  No  Yes No  No  No  No  Yes
## Levels: No Yes
```
     
#### Dispalying confusion matrix:     

```r
confusionMatrix(pred_rf, testing$has_hypothyroidism, positive="Yes")
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction No Yes
##        No  89  28
##        Yes 20  57
##                                           
##                Accuracy : 0.7526          
##                  95% CI : (0.6857, 0.8116)
##     No Information Rate : 0.5619          
##     P-Value [Acc > NIR] : 2.706e-08       
##                                           
##                   Kappa : 0.4922          
##                                           
##  Mcnemar's Test P-Value : 0.3123          
##                                           
##             Sensitivity : 0.6706          
##             Specificity : 0.8165          
##          Pos Pred Value : 0.7403          
##          Neg Pred Value : 0.7607          
##              Prevalence : 0.4381          
##          Detection Rate : 0.2938          
##    Detection Prevalence : 0.3969          
##       Balanced Accuracy : 0.7436          
##                                           
##        'Positive' Class : Yes             
## 
```
     
Accuracy : 0.7526    
Sensitivity : 0.6706             
Specificity : 0.8165       
    
#### ROC curve:     

```r
rfProbs <- predict(rf, testing, type = "prob")
rfROC <- roc(testing$has_hypothyroidism, rfProbs[, "Yes"]) 
```

```
## Setting levels: control = No, case = Yes
```

```
## Setting direction: controls < cases
```

```r
plot.roc(rfROC, asp = NA, print.auc=TRUE, legacy.axes=TRUE) 
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-84-1.png)<!-- -->
    
AUC = 0.791     

# XGBoost     

#### Converting factor variables to numeric for training data

```r
training_num <- training

training_num$gender <- as.numeric(training_num$gender)
training_num$t3_medication <- as.numeric(training_num$t3_medication)
training_num$t4_medication <- as.numeric(training_num$t4_medication)

training_num$gender <- ifelse(training_num$gender == 2, 1, 0)
training_num$t3_medication <- ifelse(training_num$t3_medication == 2, 1, 0)
training_num$t4_medication <- ifelse(training_num$t4_medication == 2, 1, 0)

levels(training_num$has_hypothyroidism) <- c("No", "Yes")
levels(training_num$has_hypothyroidism) <- c("No", "Yes")

head(training_num)
```

```
##     t4_value t3_value t4_free_value age gender t3_medication t4_medication
## 12       4.5      143          0.70   1      0             0             1
## 16       5.6       98          0.80  56      0             0             0
## 27       5.1       85          0.88  52      1             0             0
## 40       7.4       55          1.30  48      0             0             0
## 91       5.6       83          1.70  84      0             0             0
## 120      2.0       35          1.50  64      0             0             1
##     has_hypothyroidism
## 12                 Yes
## 16                  No
## 27                  No
## 40                  No
## 91                 Yes
## 120                Yes
```

#### Converting factor variables to numeric for testing data

```r
testing_num <- testing

testing_num$gender <- as.numeric(testing_num$gender)
testing_num$t3_medication <- as.numeric(testing_num$t3_medication)
testing_num$t4_medication <- as.numeric(testing_num$t4_medication)

testing_num$gender <- ifelse(testing_num$gender == 2, 1, 0)
testing_num$t3_medication <- ifelse(testing_num$t3_medication == 2, 1, 0)
testing_num$t4_medication <- ifelse(testing_num$t4_medication == 2, 1, 0)

levels(testing_num$has_hypothyroidism) <- c("No", "Yes")
levels(testing_num$has_hypothyroidism) <- c("No", "Yes")

head(testing_num)
```

```
##     t4_value t3_value t4_free_value age gender t3_medication t4_medication
## 6       19.8       95           1.5  69      0             0             0
## 46       8.9       56           2.1  74      0             0             0
## 55       5.9       32           0.8  57      0             0             0
## 93       5.9       97           1.5  29      0             0             0
## 101     18.9      363           5.6  20      0             0             0
## 142      8.2      122           1.5  78      0             0             0
##     has_hypothyroidism
## 6                   No
## 46                  No
## 55                  No
## 93                  No
## 101                 No
## 142                 No
```


```r
set.seed(300)

ctrl <- trainControl(method = "repeatedcv", 
                     number = 10, repeats = 10,                        
                     allowParallel = FALSE)

xgb_model <- train(x = data.matrix(training_num[,-8]), y = training_num$has_hypothyroidism,
                  method = "xgbTree",
                  trControl = ctrl,
                  verbose=TRUE)
xgb_model
```

```
## eXtreme Gradient Boosting 
## 
## 455 samples
##   7 predictor
##   2 classes: 'No', 'Yes' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold, repeated 10 times) 
## Summary of sample sizes: 410, 409, 409, 410, 409, 410, ... 
## Resampling results across tuning parameters:
## 
##   eta  max_depth  colsample_bytree  subsample  nrounds  Accuracy   Kappa    
##   0.3  1          0.6               0.50        50      0.6524444  0.2873074
##   0.3  1          0.6               0.50       100      0.6381449  0.2581787
##   0.3  1          0.6               0.50       150      0.6367971  0.2552848
##   0.3  1          0.6               0.75        50      0.6557150  0.2936415
##   0.3  1          0.6               0.75       100      0.6515169  0.2863073
##   0.3  1          0.6               0.75       150      0.6464734  0.2754819
##   0.3  1          0.6               1.00        50      0.6697440  0.3234125
##   0.3  1          0.6               1.00       100      0.6559082  0.2953829
##   0.3  1          0.6               1.00       150      0.6499324  0.2828514
##   0.3  1          0.8               0.50        50      0.6539662  0.2908270
##   0.3  1          0.8               0.50       100      0.6414396  0.2651229
##   0.3  1          0.8               0.50       150      0.6355121  0.2531781
##   0.3  1          0.8               0.75        50      0.6603285  0.3045977
##   0.3  1          0.8               0.75       100      0.6482850  0.2788981
##   0.3  1          0.8               0.75       150      0.6425024  0.2661272
##   0.3  1          0.8               1.00        50      0.6662415  0.3160978
##   0.3  1          0.8               1.00       100      0.6539179  0.2913708
##   0.3  1          0.8               1.00       150      0.6481932  0.2791212
##   0.3  2          0.6               0.50        50      0.6328696  0.2465134
##   0.3  2          0.6               0.50       100      0.6247150  0.2313695
##   0.3  2          0.6               0.50       150      0.6169952  0.2172483
##   0.3  2          0.6               0.75        50      0.6414879  0.2634385
##   0.3  2          0.6               0.75       100      0.6258841  0.2350182
##   0.3  2          0.6               0.75       150      0.6184106  0.2194910
##   0.3  2          0.6               1.00        50      0.6445169  0.2703626
##   0.3  2          0.6               1.00       100      0.6280483  0.2376204
##   0.3  2          0.6               1.00       150      0.6155362  0.2133951
##   0.3  2          0.8               0.50        50      0.6419227  0.2654740
##   0.3  2          0.8               0.50       100      0.6225507  0.2273254
##   0.3  2          0.8               0.50       150      0.6258502  0.2360400
##   0.3  2          0.8               0.75        50      0.6396425  0.2594930
##   0.3  2          0.8               0.75       100      0.6234251  0.2291372
##   0.3  2          0.8               0.75       150      0.6197101  0.2222669
##   0.3  2          0.8               1.00        50      0.6405845  0.2621180
##   0.3  2          0.8               1.00       100      0.6287005  0.2395179
##   0.3  2          0.8               1.00       150      0.6170821  0.2168887
##   0.3  3          0.6               0.50        50      0.6129227  0.2080134
##   0.3  3          0.6               0.50       100      0.6035024  0.1904352
##   0.3  3          0.6               0.50       150      0.6023333  0.1890838
##   0.3  3          0.6               0.75        50      0.6292029  0.2391650
##   0.3  3          0.6               0.75       100      0.6130966  0.2086264
##   0.3  3          0.6               0.75       150      0.6036522  0.1900469
##   0.3  3          0.6               1.00        50      0.6210048  0.2227212
##   0.3  3          0.6               1.00       100      0.6089469  0.2010720
##   0.3  3          0.6               1.00       150      0.6020870  0.1874047
##   0.3  3          0.8               0.50        50      0.6163961  0.2146421
##   0.3  3          0.8               0.50       100      0.6080386  0.1996807
##   0.3  3          0.8               0.50       150      0.6029324  0.1901229
##   0.3  3          0.8               0.75        50      0.6187778  0.2194598
##   0.3  3          0.8               0.75       100      0.6066957  0.1966532
##   0.3  3          0.8               0.75       150      0.6016715  0.1872454
##   0.3  3          0.8               1.00        50      0.6205024  0.2222683
##   0.3  3          0.8               1.00       100      0.6119614  0.2072308
##   0.3  3          0.8               1.00       150      0.6005604  0.1858733
##   0.4  1          0.6               0.50        50      0.6515024  0.2849120
##   0.4  1          0.6               0.50       100      0.6412222  0.2650077
##   0.4  1          0.6               0.50       150      0.6310628  0.2437754
##   0.4  1          0.6               0.75        50      0.6521498  0.2866391
##   0.4  1          0.6               0.75       100      0.6396377  0.2619221
##   0.4  1          0.6               0.75       150      0.6337536  0.2507638
##   0.4  1          0.6               1.00        50      0.6599034  0.3035448
##   0.4  1          0.6               1.00       100      0.6484010  0.2794800
##   0.4  1          0.6               1.00       150      0.6415894  0.2650900
##   0.4  1          0.8               0.50        50      0.6440918  0.2702714
##   0.4  1          0.8               0.50       100      0.6401304  0.2630870
##   0.4  1          0.8               0.50       150      0.6280821  0.2387208
##   0.4  1          0.8               0.75        50      0.6533043  0.2892578
##   0.4  1          0.8               0.75       100      0.6407488  0.2630468
##   0.4  1          0.8               0.75       150      0.6387778  0.2600971
##   0.4  1          0.8               1.00        50      0.6625314  0.3089812
##   0.4  1          0.8               1.00       100      0.6485942  0.2800502
##   0.4  1          0.8               1.00       150      0.6405169  0.2632154
##   0.4  2          0.6               0.50        50      0.6265266  0.2357776
##   0.4  2          0.6               0.50       100      0.6130966  0.2080956
##   0.4  2          0.6               0.50       150      0.6056135  0.1947101
##   0.4  2          0.6               0.75        50      0.6361353  0.2539957
##   0.4  2          0.6               0.75       100      0.6282126  0.2395455
##   0.4  2          0.6               0.75       150      0.6123768  0.2056081
##   0.4  2          0.6               1.00        50      0.6343961  0.2506919
##   0.4  2          0.6               1.00       100      0.6247150  0.2314626
##   0.4  2          0.6               1.00       150      0.6158937  0.2144189
##   0.4  2          0.8               0.50        50      0.6275266  0.2363426
##   0.4  2          0.8               0.50       100      0.6150870  0.2135825
##   0.4  2          0.8               0.50       150      0.6071836  0.1981414
##   0.4  2          0.8               0.75        50      0.6350918  0.2526113
##   0.4  2          0.8               0.75       100      0.6203430  0.2234651
##   0.4  2          0.8               0.75       150      0.6109130  0.2052172
##   0.4  2          0.8               1.00        50      0.6343768  0.2507584
##   0.4  2          0.8               1.00       100      0.6214783  0.2259738
##   0.4  2          0.8               1.00       150      0.6100338  0.2046378
##   0.4  3          0.6               0.50        50      0.6145266  0.2119230
##   0.4  3          0.6               0.50       100      0.6080628  0.2013885
##   0.4  3          0.6               0.50       150      0.6056522  0.1960177
##   0.4  3          0.6               0.75        50      0.6140870  0.2106184
##   0.4  3          0.6               0.75       100      0.6036184  0.1922067
##   0.4  3          0.6               0.75       150      0.5877778  0.1591881
##   0.4  3          0.6               1.00        50      0.6163961  0.2147916
##   0.4  3          0.6               1.00       100      0.6029614  0.1894459
##   0.4  3          0.6               1.00       150      0.5987488  0.1816732
##   0.4  3          0.8               0.50        50      0.6113043  0.2066575
##   0.4  3          0.8               0.50       100      0.5950097  0.1760471
##   0.4  3          0.8               0.50       150      0.5886570  0.1625161
##   0.4  3          0.8               0.75        50      0.6099420  0.2019519
##   0.4  3          0.8               0.75       100      0.6051256  0.1947455
##   0.4  3          0.8               0.75       150      0.5923961  0.1700184
##   0.4  3          0.8               1.00        50      0.6203188  0.2236360
##   0.4  3          0.8               1.00       100      0.6012415  0.1867616
##   0.4  3          0.8               1.00       150      0.5925749  0.1697580
## 
## Tuning parameter 'gamma' was held constant at a value of 0
## Tuning
##  parameter 'min_child_weight' was held constant at a value of 1
## Accuracy was used to select the optimal model using the largest value.
## The final values used for the model were nrounds = 50, max_depth = 1, eta
##  = 0.3, gamma = 0, colsample_bytree = 0.6, min_child_weight = 1 and subsample
##  = 1.
```


```r
plot(xgb_model)
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-88-1.png)<!-- -->![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-88-2.png)<!-- -->
     
#### Variable Importance      

```r
xgbImp <- varImp(xgb_model)
xgbImp
```

```
## xgbTree variable importance
## 
##                Overall
## t4_medication 100.0000
## t4_free_value  34.8805
## age            23.6259
## t4_value       11.9606
## t3_value       10.8896
## gender          0.7711
## t3_medication   0.0000
```
       

```r
plot(varImp(xgb_model))
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-90-1.png)<!-- -->
     
#### Predicting on test data:    

```r
pred_xgb <- predict(xgb_model, newdata = data.matrix(testing_num))
print(pred_xgb)
```

```
##   [1] No  No  No  No  No  No  Yes No  Yes No  No  Yes No  No  No  No  Yes Yes
##  [19] No  Yes No  No  Yes Yes No  No  No  Yes Yes Yes No  Yes No  No  Yes Yes
##  [37] Yes No  No  No  Yes Yes Yes Yes No  No  No  Yes No  No  No  No  No  No 
##  [55] Yes No  Yes No  No  No  No  No  No  Yes Yes Yes No  No  Yes No  Yes No 
##  [73] No  Yes Yes Yes No  No  Yes No  No  No  No  No  Yes No  Yes No  No  No 
##  [91] Yes Yes No  No  Yes Yes Yes No  No  Yes No  No  Yes Yes No  Yes No  Yes
## [109] No  No  No  No  Yes No  No  No  No  Yes Yes No  No  No  Yes Yes Yes No 
## [127] No  No  No  No  No  No  No  Yes Yes No  No  No  Yes No  Yes No  Yes No 
## [145] No  Yes Yes No  Yes No  No  No  No  Yes Yes Yes Yes No  Yes No  No  No 
## [163] No  No  No  Yes Yes No  No  No  Yes Yes Yes Yes Yes Yes Yes Yes Yes No 
## [181] No  Yes No  Yes No  No  No  No  Yes No  No  Yes No  Yes
## Levels: No Yes
```
      
#### Displaying Confusion Matrix:     

```r
confusionMatrix(pred_xgb, testing_num$has_hypothyroidism, positive = "Yes")
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction No Yes
##        No  86  30
##        Yes 23  55
##                                           
##                Accuracy : 0.7268          
##                  95% CI : (0.6584, 0.7882)
##     No Information Rate : 0.5619          
##     P-Value [Acc > NIR] : 1.554e-06       
##                                           
##                   Kappa : 0.44            
##                                           
##  Mcnemar's Test P-Value : 0.4098          
##                                           
##             Sensitivity : 0.6471          
##             Specificity : 0.7890          
##          Pos Pred Value : 0.7051          
##          Neg Pred Value : 0.7414          
##              Prevalence : 0.4381          
##          Detection Rate : 0.2835          
##    Detection Prevalence : 0.4021          
##       Balanced Accuracy : 0.7180          
##                                           
##        'Positive' Class : Yes             
## 
```
      
Accuracy: 0.7268      
Sensitivity: 0.6471           
Specificity: 0.7890       

#### ROC:      

```r
xgb_Probs <- predict(xgb_model, testing_num, type = "prob")
xgbROC <- roc(testing_num$has_hypothyroidism, xgb_Probs[, "Yes"]) 
```

```
## Setting levels: control = No, case = Yes
```

```
## Setting direction: controls < cases
```

```r
plot.roc(xgbROC, asp = NA, print.auc=TRUE, legacy.axes=TRUE) 
```

![](C:/Users/DELL/Desktop/Clinical Decision Support and Health Data Analyticsunnamed-chunk-93-1.png)<!-- -->
      
AUC: 0.812        
        
         
# Conclusion:   
    
Hence, we observe that, judging by area under the curve (AUC), we get the best results from Logistic Regression (0.831), followed by SVM with linear kernel (0.824), Gradient Boosting (0.817), SVM with polynomial kernel (0.813), XGBoost (0.812), SVM with radial kernel (0.802), and finally Random Forest (0.791).      
    
