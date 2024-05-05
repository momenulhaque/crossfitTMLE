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

 1. Install the required R packages
The package primarily requires 'SuperLearner'. Additionally, it requires all the R packages associated with advanced machine learning algorithms.
```{r}
require(SuperLearner)
# require(randomForest) for SL.randoForest.
```
 
 2. Defining the data you want to use
 
```{r}
# Read the data set that you want to use. An example data set "statin_sim_data" can be found in this package.
data = statin_sim_data 


