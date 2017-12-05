---
title: "R Codes for Simulation Study and Data Analysis for Manuscript: 

*A semiparametric model for wearable sensor-based physical activity monitoring data with informative device wear*"
output: github_document
---

## Objective

This document includes R codes to reproduce the simulation study and data analysis results presented in the manuscript *A semiparametric model for wearable sensor-based physical activity monitoring data with informative device wear*.

## Simulation Study

All results can be reproduced using built-in functions in our R package *acc*. While, the package is maintained in CRAN (https://cran.r-project.org/web/packages/acc), latest versions of the package are available at: https://github.com/github-js/acc. The latest version of the *acc* package can be installed directly from the github repository.

```
install.packages("devtools")
library(devtools)
install_github("github-js/acc")
```

The package manual is available at https://github.com/github-js/semiparametric. Simulation study can be reproduced using fuction *simStudy*. In our simulation study presented in the manuscript *A semiparametric model for wearable sensor-based physical activity monitoring data with informative device wear*, we evaluated the impact of informative observation times. Specifically, the simulation study assessed bias that may occur when estimating associations between a single predictor and minutes of moderate to vigorous physical activity in real time bouts, measured using wearable sensors. 

Data were simulated for a 7-day physical activity monitoring period (total of 10080 minutes) under two scenarios. In scenario 1, we assumed that the observation patterns were independent of the physical activity process. In scenario 2, we assume that the observation patterns were dependent on the physical activity process. The performance of the semiparametric approach introduced in this manuscript was compared to the conventional method of generalized estimating equations. Such simulation study can be reproduced using the function *simStudy* in package *acc* as the follwing.

```
# After installing the acc package from https://github.com/github-js/acc
# Load the package
library(acc)

# For illustration purposes, we present simulation results when *n=50*
# In the manuscript, we also showed simulation study results when *n=100* and *n=200*
# Please specify (n=100 and n=200) to reproduce all the results as in our manuscript

# Simulation study is conducted using R function 'simStudy'
# Please see package manual in https://github.com/github-js/semiparametric for details of usage

# First, we conduct simulation study for scenario 1 
# (when the observation and censoring patterns were independent of the physical activity process)
# Such scenario is specified by argument 'inf=FALSE'
simStudy_n50_ind <- simStudy(n=50,numsim=1000,beta=-.4,nu=5,mu=12,inf=FALSE,nobs=7)

# Second, we conduct simulation study for scenario 2 
# (when the observation and censoring patterns were dependent of the physical activity process)
# Such scenario is specified by argument 'inf=TRUE', and by varying 'nobs' from 'nobs=c(7,6)' to 'nobs=c(7,2)'
# Specifying 'nobs=c(7,6)' will generate differential observation and censoring patterns
# dependent on the predictor (X), and the unobserved latent variable (Z), 
# with the expected number of physical activity events being 7 for when X=1 and Z>=1, and 6 otherwise.
# Specifying smaller number of expected number of physical activity events 
# for when X=0 or Z<1 will impose stronger information on the observation and censoring patterns

simStudy_n50_inf_7_6 <- simStudy(n=50,numsim=1000,beta=-.4,nu=5,mu=12,inf=TRUE,nobs=c(7,6))
simStudy_n50_inf_7_5 <- simStudy(n=50,numsim=1000,beta=-.4,nu=5,mu=12,inf=TRUE,nobs=c(7,5))
simStudy_n50_inf_7_4 <- simStudy(n=50,numsim=1000,beta=-.4,nu=5,mu=12,inf=TRUE,nobs=c(7,4))
simStudy_n50_inf_7_3 <- simStudy(n=50,numsim=1000,beta=-.4,nu=5,mu=12,inf=TRUE,nobs=c(7,3))
simStudy_n50_inf_7_2 <- simStudy(n=50,numsim=1000,beta=-.4,nu=5,mu=12,inf=TRUE,nobs=c(7,2))
```

We can summarize the bias as the following.

```
sim_bias_n50 <- rbind(
simStudy_n50_ind$bias,
simStudy_n50_inf_7_6$bias,
simStudy_n50_inf_7_5$bias,
simStudy_n50_inf_7_4$bias,
simStudy_n50_inf_7_3$bias,
simStudy_n50_inf_7_2$bias)

rownames(sim_bias_n50) <- c('nobs(7,7) (i.e., delta1=6 and delta2=6 in manuscript)',
                            'nobs(7,6) (i.e., delta1=6 and delta2=5 in manuscript)',
                            'nobs(7,5) (i.e., delta1=6 and delta2=4 in manuscript)',
                            'nobs(7,4) (i.e., delta1=6 and delta2=3 in manuscript)',
                            'nobs(7,3) (i.e., delta1=6 and delta2=2 in manuscript)',
                            'nobs(7,2) (i.e., delta1=6 and delta2=1 in manuscript)')
```                            
```                          
> sim_bias_n50 
                                                         gee.bias    aeex.bias
nobs(7,7) (i.e., delta1=6 and delta2=6 in manuscript) -0.02972338 -0.026111755
nobs(7,6) (i.e., delta1=6 and delta2=5 in manuscript) -0.03271849  0.008100274
nobs(7,5) (i.e., delta1=6 and delta2=4 in manuscript) -0.03266916  0.006982480
nobs(7,4) (i.e., delta1=6 and delta2=3 in manuscript) -0.05012691 -0.011554433
nobs(7,3) (i.e., delta1=6 and delta2=2 in manuscript) -0.06262936 -0.004016261
nobs(7,2) (i.e., delta1=6 and delta2=1 in manuscript) -0.08932496 -0.007346101                          
```

Similarly, we can summarize the coverage as the following,
```
sim_coverage_n50 <- rbind(
simStudy_n50_ind$coverage,
simStudy_n50_inf_7_6$coverage,
simStudy_n50_inf_7_5$coverage,
simStudy_n50_inf_7_4$coverage,
simStudy_n50_inf_7_3$coverage,
simStudy_n50_inf_7_2$coverage)

rownames(sim_coverage_n50) <- c('nobs(7,7) (i.e., delta1=6 and delta2=6 in manuscript)',
                            'nobs(7,6) (i.e., delta1=6 and delta2=5 in manuscript)',
                            'nobs(7,5) (i.e., delta1=6 and delta2=4 in manuscript)',
                            'nobs(7,4) (i.e., delta1=6 and delta2=3 in manuscript)',
                            'nobs(7,3) (i.e., delta1=6 and delta2=2 in manuscript)',
                            'nobs(7,2) (i.e., delta1=6 and delta2=1 in manuscript)')
                            
```
```
> sim_coverage_n50
                                                      gee.coverage aeex.coverage
nobs(7,7) (i.e., delta1=6 and delta2=6 in manuscript)         0.90          0.99
nobs(7,6) (i.e., delta1=6 and delta2=5 in manuscript)         0.89          0.99
nobs(7,5) (i.e., delta1=6 and delta2=4 in manuscript)         0.92          1.00
nobs(7,4) (i.e., delta1=6 and delta2=3 in manuscript)         0.89          0.93
nobs(7,3) (i.e., delta1=6 and delta2=2 in manuscript)         0.90          0.97
nobs(7,2) (i.e., delta1=6 and delta2=1 in manuscript)         0.93          1.00                        
```

where *'AEEX'* refers to the semiparametric model.


## Real Data Analysis

In the NHANES 2003-04, there were data for 4,518 participants who were at least 18 years of age and have participated in the physical activity monitoring component. Data analysis can be conducted using the function *aeexfit* in package *acc* as the follwing. Details of the accelerometer data processing procedures for this data were described in our manuscript *A semiparametric model for wearable sensor-based physical activity monitoring data with informative device wear*.

```
# After installing the acc package from https://github.com/github-js/acc
# Load the package
library(acc)

# For illustration purposes, we analyze data for 100 randomly sampled individuals
# (in the manuscript, we analyzed data for 4,518 participants)

# The sample data is available in the R package acc
data(nhanesSample)

# The sample data consists of four columns: ID, Time (time index),
# min (number of minutes engaged in moderate-vigorous physical activity),
# and Female (1=Female, 0=Male)
> head(nhanesSample)
     ID Time Min Female
1 21319 2333  11      0
2 21319 2347  17      0
3 22176 3936  10      0
4 22176 8364  79      0
5 22177  936  16      0
6 22177 6730  13      0

# For illustrative purposes, we fit a semiparametric model with function *aeex*,
# for a randomly sampled 100 individuals in this dataset
formula <- aee(ID, Time, Min) ~ Female
fitted2 <- aeexfit(formula=formula, data=nhanesSample, se="Sandwich") 
> summary(fitted2)

Call:
aee(ID, Time, Min) ~ Female

Coefficients:
          coef exp(coef) se(coef)     z Pr(>|z|)
Female -0.0735    0.9291   0.5724 -0.13      0.9

# For comparison, we fit a GEE model
gee.fit <- gee(Min ~ Female,data =nhanesSample, id = ID, 
              family = "poisson", corstr = "exchangeable")
> summary(gee.fit)$coefficients
              Estimate Naive S.E.    Naive z Robust S.E.   Robust z
(Intercept)  3.1304425  0.1249967 25.0441912   0.1329596 23.5443191
Female      -0.1523674  0.1880801 -0.8101196   0.1909157 -0.7980871              
```

