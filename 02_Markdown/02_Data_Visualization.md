---
title: "02_Data_Visualization"
author: "Matt Dube"
date: "12/4/2018"
output:
  html_document:
    keep_md: yes
    toc: yes
  word_document:
    toc: yes
---

Load libraries and set theme

```r
library(ggplot2)
library(dplyr)
library(ggthemes)
library(here)
library(readr)
library(purrr)
library(tidyr)
library(vcd)
library(corrplot)
```

```r
theme_set(theme_light())
```

Load data

```r
customer <- read_csv(here("00_Data/raw", "WA_Fn-UseC_-Telco-Customer-Churn.csv"))
```

Functions to assist with plotting

```r
plotNumData <- function(varList, inputData, numCols=2) {
    gather(data=inputData, varList, key = "var", value = "value") %>%
        ggplot(aes(x=Churn, y=value, fill=Churn)) +
        geom_boxplot() +
        facet_wrap(~ var, scales = "free", ncol = numCols) +
        scale_fill_brewer(palette = "Paired")
}

plotCharData <- function(varList, inputData, numCols=2) {
    gather(data=inputData, varList, key = "var", value = "value") %>%
        ggplot(aes(x=value, fill=Churn)) +
        geom_bar() +
        coord_flip() +
        facet_wrap(~ var, scales="free", ncol = numCols) +
        scale_fill_brewer(palette = "Paired")
}

plotHistograms <- function(varList, inputData, numCols=2) {
    gather(data=inputData, varList, key = "var", value = "value") %>%
        ggplot(aes(x=value, fill=Churn)) +
        geom_histogram() +
        facet_wrap(~ var, scales = "free", ncol = numCols) +
        scale_fill_brewer(palette = "Paired")
}
```


Boxplots of numeric features

```r
numVarToPlot <- c("tenure", "MonthlyCharges", "TotalCharges")
plotNumData(numVarToPlot, customer)
```

```
## Warning: Removed 11 rows containing non-finite values (stat_boxplot).
```

![](02_Data_Visualization_files/figure-html/unnamed-chunk-5-1.png)<!-- -->
Histograms of numeric features

```r
plotHistograms(numVarToPlot, customer)
```

```
## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
```

```
## Warning: Removed 11 rows containing non-finite values (stat_bin).
```

![](02_Data_Visualization_files/figure-html/unnamed-chunk-6-1.png)<!-- -->


Notes on numeric features:

<<<<<<< HEAD
* customers with smaller tenures and higher monthly charges are more likely to churn.
* there are a few long tenured customers who have churned, and appear as outliers in the boxplot.
* total charges has a few potential outliers as well that need to be examined.

Bar plots of character features

```r
varToPlot <- c("gender", "Partner", "Dependents", "PhoneService")
plotCharData(varToPlot, customer)
```

![](02_Data_Visualization_files/figure-html/unnamed-chunk-7-1.png)<!-- -->


```r
varToPlot <- c("MultipleLines", "InternetService", "OnlineSecurity", "OnlineBackup")
plotCharData(varToPlot, customer)
```

![](02_Data_Visualization_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

```r
varToPlot <- c("DeviceProtection", "TechSupport", "StreamingTV", "StreamingMovies")
plotCharData(varToPlot, customer)
```

![](02_Data_Visualization_files/figure-html/unnamed-chunk-9-1.png)<!-- -->

```r
varToPlot <- c("Contract", "PaperlessBilling", "PaymentMethod")
plotCharData(varToPlot, customer)
```

![](02_Data_Visualization_files/figure-html/unnamed-chunk-10-1.png)<!-- -->


* customers will smaller tenures and higher monthly charges are more likely to churn.
* there are a few long tenured customers who have churned, and appear as outliers in the boxplot.
* total charges has a few potential outliers as well that need to be examined.

Review correlation between tenure, TotalCharges and MonthlyCharges.

```r
cust_numeric <- 
    customer %>%
    select(-SeniorCitizen) %>%
    keep(is.numeric)
```


```r
M <- cor(cust_numeric,use="complete.obs")
```


```r
corrplot(M, method="number", type="lower")
```

![](02_Data_Visualization_files/figure-html/unnamed-chunk-13-1.png)<!-- -->

Tenure and TotalCharges have a strong correlation.  MonthlyCharges and TotalCharges are also strongly correlated.  When we get to data cleaning TotalCharges should be removed - or at the minimum not used as a predictor in our models.
