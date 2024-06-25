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
 
## Apply the crossfitTMLE function
 
```{r}
# Read the data set that you want to use. An example data set "statin_sim_data" can be found in this package.
L <- names(dplyr::select(statin_sim_data, !c(Y, statin)))
DC_tmle <- crossfitTMLE(data = statin_sim_data,
                        exposure = "statin",
                        outcome = "Y",
                        covarsT = L,
                        covarsO = L,
                        family.y = "binomial",
                        learners = "SL.glm",
                        control = list(V = 5, stratifyCV = FALSE,
                                       shuffle = TRUE, validRows = NULL),
                        num_cf = 5,
                        n_split = 3,
                        seed = 2575,
                        conf.level = 0.95, stat = "median")

DC_tmle
# A tibble: 1 × 4
     ATE     se lower.ci upper.ci
   <dbl>  <dbl>    <dbl>    <dbl>
1 -0.125 0.0188   -0.162  -0.0884

```
# References
Zivich PN, and Breskin A. "Machine learning for causal inference: on the use of cross-fit estimators." Epidemiology 32.3 (2021): 393-401
Mondol M, Karim M. "Towards Robust Causal Inference in Epidemiological Research: Employing Double Cross-fit TMLE in Right Heart Catheterization Data." (Under review)

# Funding
Natural Sciences and Engineering Research Council of Canada (NSERC) Discovery Grant (PG#: 20R01603, PI: Karim) as well as UBC Work Learn program (Summer 2023, PI: Karim).

# Acknowledgements
This research was partially supported by the computational resources and services provided by Advanced Research Computing at the University of British Columbia. We extend our sincere gratitude to Paul N. Zivich and Alexander Breskin for their R codes available at [this GitHub repository](https://github.com/pzivich/publications-code/tree/master/DoubleCrossFit).

