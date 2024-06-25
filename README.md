# crossfitTMLE: An R Package Apply Double Cross-fit Approach to TMLE in Causal Inference 
---
Author: Momenul Haque Mondol & Mohammad Ehsanul Karim
---

## How to install

```{r}
remotes::install_github("momenulhaque/crossfitTMLE") # it will install the package
library(crossfitTMLE) 
```
Now the package is ready to use. 

## Install the required R packages
The package primarily requires 'SuperLearner'. Additionally, it requires all the R packages associated with advanced machine learning algorithms.
```{r}
require(SuperLearner)
# require(randomForest) for SL.randoForest.
```
## Generating Data set 
We generated a data set using Kang at al. (2007).

```{r}
library(tmle)
library(SuperLearner)
library(tidyverse)
gen.data <- function(n, do.pop=F, do.misp=F, my.transform=F){
  
  C1 <- rnorm(n,0,1)
  C2 <- rnorm(n,0,1)
  C3 <- rnorm(n,0,1)
  C4 <- rnorm(n,0,1)
  
  pscore <- plogis(-1 +log(1.75)*(C1+C2+C3+C4) )
  X <- rbinom(n, 1, pscore)
  
  eps <- rnorm(n,0,6)
  get.Y <- function(C1,C2,C3,C4,X,eps){
    120+6*X+3*(C1+C2+C3+C4)+eps
  }
  Y1 <- get.Y(C1,C2,C3,C4,X=1,eps)
  Y0 <- get.Y(C1,C2,C3,C4,X=0,eps)
  Y <- get.Y(C1,C2,C3,C4,X,eps)
  
  if(do.pop){
    # return the pscore & counterfactual outcomes
    yay <- data.frame(cbind(pscore, Y1,Y0))
  } else {
    
    if(do.misp & my.transform){
      # BALZER & WESTLING transformation of the confounders
      C1 <- exp(C1/2)
      C2 <- C2/(1+exp(C1)) + 10
      C3 <- (C1*C3/25 + 0.6)^3
      C4 <- (C2 + C4 + 20)^2
      
    } else if (do.misp & !my.transform){
      # NAIMI et al. transformation of the confounders
      Z1 <- C1
      Z2 <- C2
      Z3 <- C3
      Z4 <- C4
      
      C1 <- exp(Z1/2)
      C2 <- Z2/(1+exp(Z1)) + 10
      C3 <- (Z1*Z3/25 + 0.6)^3
      C4 <- (Z2 + Z4 + 20)^2
    }
    # return observed data
    yay <- data.frame(cbind(C1,C2,C3,C4,X,Y))
    
  }
  yay
}

## Defining Learners
SL.randomForest.dcTMLE <- function(...){
  SL.randomForest(...,ntree=500, mtry = 2, nodesize=30)}
SL.xgboost.dcTMLE <- function(...){
  SL.xgboost(..., ntrees = 500, max_depth = 4, shrinkage = 0.1, minobspernode = 30)}
SL.glm.dcTMLE <- function(...){
  SL.glm(...)}
SL.gam4.dcTMLE <- function(...){
  SL.gam(..., deg.gam=4)}
SL.mean.dcTMLE <- function(...){
  SL.mean(...)}

set.seed(236)
ObsData = gen.data(n=1200, do.pop=F, do.misp=T, my.transform=T)
```
 
## Apply the crossfitTMLE function
 
```{r}
fit = crossfitTMLE(data=ObsData_FF,
                          exposure="X",
                          outcome="Y",
                          covarsT=c("C1", "C2", "C3", "C4"),
                          covarsO=c("C1", "C2", "C3", "C4"),
                          family.y="gaussian",
                          learners=c("SL.glm.dcTMLE", "SL.gam4.dcTMLE", "SL.randomForest.dcTMLE", "SL.xgboost.dcTMLE", "SL.mean.dcTMLE"),
                          control=list(V = 3, stratifyCV = FALSE, shuffle = TRUE, validRows = NULL),
                          num_cf=5,
                          n_split=3,
                          rand_split=FALSE,
                          Qbounds = 1-0.9995,
                          gbounds = NULL,
                          seed=236,
                          conf.level=0.96,
                          stat = "median")

fit
# A tibble: 1 × 4


```
# References
Kang, J. D. Y., & Schafer, J. L. (2007). Demystifying Double Robustness: A Comparison of Alternative Strategies for Estimating a Population Mean from Incomplete Data. Statistical Science, 22(4). https://doi.org/10.1214/07-STS227
Mondol M, Karim M. "Towards Robust Causal Inference in Epidemiological Research: Employing Double Cross-fit TMLE in Right Heart Catheterization Data." (Under review)

# Funding
Natural Sciences and Engineering Research Council of Canada (NSERC) Discovery Grant (PG#: 20R01603, PI: Karim) as well as UBC Work Learn program (Summer 2023, PI: Karim).

# Acknowledgements
This research was partially supported by the computational resources and services provided by Advanced Research Computing at the University of British Columbia. We extend our sincere gratitude to Paul N. Zivich and Alexander Breskin for their R codes available at [this GitHub repository](https://github.com/pzivich/publications-code/tree/master/DoubleCrossFit).

