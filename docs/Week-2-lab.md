Week 2 Lab
=============

Confidence intervals
-----------------------

Before getting too far, we need to formally define a confidence interval. 

A 95th percentile confidence interval say “If I repeat this procedure 100 times using 100 different datasets, 95% of the time my confidence intervals will capture the true parameter”. It does NOT say that there is a 95% chance that the parameter is in the interval.

**Quiz time! (Don't worry, not a real quiz)**

*Important note*: This is an area where Aho is **WRONG**. Aho is correct on only one point. It is true that *once the 95th CI has been constructed*, it is no longer possible to assign a $\%$ to the probability that that CI contains the true value or not. Because that CI, once created, either DOES or DOES NOT contain the true value. However, we often talk about the interval in the abstract. **<span style="color: orangered;">When we say "There is a 95$\%$ chance that the interval contains the true value" what we mean is that there is a 95$\%$ probability that a CI created using that methodology would contain the true value.</span>**

Do not let Week 2 pass by without fundamentally understanding the interpretation of a confidence interval. 

So now we know the general idea behind confidence intervals but we don't yet know how to calculate them. To do that, we'll actually walk through an example of bootstrap using pennies. Each of you should have gathered the ages of ten pennies. (If a penny was made in 2021, that penny would be 1 year old, etc.)

*Data*: 10 pennies that the students have

*Test statistic*: Median

Lets say we are trying to find the median age of all pennies in circulation. We can't figure this out exactly, because we can't collect all the pennies in circulation, but we each have a sample of 10 pennies. The median age of the pennies in our sample is a reasonable estimate for the median age of all pennies in circulation. 

What is our uncertainty about that number? How far might our estimate of the median age be from the true median age? In this case, we don't know the underlying distribution of penny ages. (Let's brainstorm this for a bit. Do we have any guesses what this distribution might look like? What might be a reasonable distribution to describe the shape of penny age?) 

Let’s use bootstrapped samples to calculate the s.e. associated with that estimate.

Procedure: 
1. Sample WITH REPLACEMENT a group of 10 pennies. (To sample with replacement you will have to sample one penny, write down the age, and repeat that 10 times.)
2. Calculate the median age from that sample of pennies.
3. Repeat

Do this a few times with your actual physical pennies, and then once you get the idea, you can make a vector in R of your penny ages and use R to speed up the sampling. Don't forget to sample with replacement.

Gather a collection of 100 median estimates, each one calculated using a different bootstrapped dataset. Calculate the Bias and the Variance of the estimator for the Median.

We now want to take this one step further and estimate the confidence intervals for the median age of a penny in circulation. We actually have two primary mechanisms for generating confidence intervals for the statistic.

**Method #1**: Simply take the quantiles directly from the distribution of $\hat{\theta}^{*}$:

$$
\theta_{LL} = \mbox{2.5th percentile of } \hat{\theta}^{*}
$$
$$
\theta_{UL} = \mbox{97.5th percentile of } \hat{\theta}^{*}
$$

Notice that (by construction) 95$%$ of the $\hat{\theta}^{*}$ values fall in the interval $(\theta_{LL},\theta_{UL})$. <span style="color: orangered;">This is the very definition of the 95th percentile confidence interval.</span>

**OR** 

**Method #2**: We can use the Normal approximation:

We have a second method that won't make 100\% sense until next week, but it turns out that if we assume the bootstrapped estimates follow a Normal distribution, 

$$
\hat{\theta^{*}} \sim N(\hat{\theta},\hat{se}^{2})
$$

we can use the fact that the 95th percentile confidence interval is approximately given by:

$$
\hat{\theta}_{LL}=\hat{\theta}-1.96*\hat{se}
$$
$$
\hat{\theta}_{UL}=\hat{\theta}+1.96*\hat{se}
$$

It turns out that 95$\%$ of the probability for a Standard Normal distribution lies between (-1.96$\sigma$,+1.96$\sigma$). (We will show this more formally next week.) 

NB: If you are going to go through the trouble of doing the bootstrap sampling, I don’t know why you would make a Normal approximation at the very end to construct the CIs. I recommend Method #1.

**<span style="color: green;">Checkpoint #1: Use your penny data to calculate the 95th percentile confidence interval using Method #1 and Method #2. What did you get?</span>**

Testing hypotheses through permutation
------------------------------------

These examples use data on the speeds of the top 20 racing pigeons from a race in Alma, GA on February 7,2021. 

**Example #1**: Use permutation methods to test whether Cock or Hen birds fly at different speeds (speeds are in meters-per-minute) (in other word: $H_{0}$: No difference in speeds between the C and H groups):

C=$\{1359.8,1355.3,1355.1,1353.0,1349.8,1348.8,1345.2\}$

H=$\{1357.5,1356.4,1355.1,1353.5,1353.2,1352.5,1350.0,1349.8,1346.2,1344.9,1344.4,1343.9,1342.6\}$

**<span style="color: green;">Checkpoint #2: Is this a one-tailed or a two-tailed test?</span>**

Make sure that you understand what is being done here, as this example is very closely related to the problem set.


**Example #2**: Using the same data, provide a 95% confidence interval for the difference in mean speed based on 1000 bootstrap samples

Note that these two approaches are very closely related. Do you see why either approach can be used to test the null hypothesis? **<span style="color: green;">Checkpoint #3: What is the null hypothesis here?</span>**

**Example #3**: Now we will do one slightly more complicated example from Phillip Good's book "Permutation tests: A practical guide to resampling methods and testing hypotheses":

Holmes and Williams (1954) studied tonsil size in children to verify a possible association with the virus \textit{S. pyrogenes}. Test for an association between \textit{S. pyrogenes} status and tonsil size. (Note that you will need to come up with a reasonable test statistic.)

<div class="figure" style="text-align: center">
<img src="Table2categories.png" alt="Data on tonsil size and S. pyrogenes status. Source: Good (1994)" width="40%" />
<p class="caption">(\#fig:unnamed-chunk-1)Data on tonsil size and S. pyrogenes status. Source: Good (1994)</p>
</div>

Now lets consider the full dataset, where tonsil size is divided into three categories. How would we do the test now? **<span style="color: green;">Checkpoint #4: What is the new test statistic? (There are many options.)</span>** What 'labels' do you permute?

<div class="figure" style="text-align: center">
<img src="Table3categories.png" alt="Fill dataset on tonsil size and S. pyrogenes status. Source: Good (1994)" width="50%" />
<p class="caption">(\#fig:unnamed-chunk-2)Fill dataset on tonsil size and S. pyrogenes status. Source: Good (1994)</p>
</div>

Basics of bootstrap and jackknife
------------------------------------

To get started with bootstrap and jackknife techniques, we start by working through a very simple example. First we simulate some data


```r
x<-seq(0,9,by=1)
```

This will constutute our "data". Let's print the result of sampling with replacement to get a sense for it...


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 3 4 6 7 
## 1 1 2 3 1 1 1
```

Now we will write a little script to take bootstrap samples and calculate the means of each of these bootstrap samples


```r
xmeans<-vector(length=1000)
for (i in 1:1000)
  {
  xmeans[i]<-mean(sample(x,replace=T))
  }
```

The actual number of bootstrapped samples is arbitrary *at this point* but there are ways of characterizing the precision of the bootstrap (jackknife-after-bootstrap) which might inform the number of bootstrap samples needed. *In practice*, people tend to pick some arbitrary but large number of bootstrap samples because computers are so fast that it is often easy to draw far more samples than are actually needed. When calculation of the statistic is slow (as might be the case if you are using the samples to construct a phylogeny, for example), then you would need to be more concerned with the number of bootstrap samples. 

First, lets just look at a histogram of the bootstrapped means and plot the actual sample mean on the histogram for comparison



```r
hist(xmeans,breaks=30,col="pink")
abline(v=mean(x),lwd=2)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-6-1.png" width="672" />

Calculating bias and standard error
-----------------------------------

From these we can calculate the bias and standard deviation for the mean (which is the "statistic"):

$$
\widehat{Bias_{boot}} = \left(\frac{1}{k}\sum^{k}_{i=1}\theta^{*}_{i}\right)-\hat{\theta}
$$


```r
bias.boot<-mean(xmeans)-mean(x)
bias.boot
```

```
## [1] 0.0169
```

```r
hist(xmeans,breaks=30,col="pink")
abline(v=mean(x),lwd=5,col="black")
abline(v=mean(xmeans),lwd=2,col="yellow")
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-7-1.png" width="672" />

$$
\widehat{s.e._{boot}} = \sqrt{\frac{1}{k-1}\sum^{k}_{i=1}(\theta^{*}_{i}-\bar{\theta^{*}})^{2}}
$$


```r
se.boot<-sd(xmeans)
```

We can find the confidence intervals in two ways:

Method #1: Assume the bootstrap statistics are normally distributed


```r
LL.boot<-mean(xmeans)-1.96*se.boot #where did 1.96 come from?
UL.boot<-mean(xmeans)+1.96*se.boot
LL.boot
```

```
## [1] 2.749639
```

```r
UL.boot
```

```
## [1] 6.284161
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.8   6.4
```

Let's compare this to what we would have gotten if we had used normal distribution theory. First we have to calculate the standard error:


```r
se.normal<-sqrt(var(x)/length(x))
LL.normal<-mean(x)-qt(0.975,length(x)-1)*se.normal
UL.normal<-mean(x)+qt(0.975,length(x)-1)*se.normal
LL.normal
```

```
## [1] 2.334149
```

```r
UL.normal
```

```
## [1] 6.665851
```

In this case, the confidence intervals we got from the normal distribution theory are too wide.

**<span style="color: green;">Checkpoint #6: Does it make sense why the normal distribution theory intervals are too wide?</span>** Because the original were were uniformly distributed, the data has higher variance than would be expected and therefore the standard error is higher than would be expected.

There are two packages that provide functions for bootstrapping, 'boot' and 'boostrap'. We will start by using the 'bootstrap' package, which was originally designed for Efron and Tibshirani's monograph on the bootstrap. 

To test the main functionality of the 'bootstrap' package, we will use the data we already have. The 'bootstrap' function requires the input of a user-defined function to calculate the statistic of interest. Here I will write a function that calculates the mean of the input values.


```r
library(bootstrap)
theta<-function(x)
  {
    mean(x)
  }
results<-bootstrap(x=x,nboot=1000,theta=theta)
results
```

```
## $thetastar
##    [1] 5.6 3.5 4.8 5.1 4.7 5.1 3.7 5.5 4.1 4.0 4.8 5.8 3.9 5.5 4.7 4.9 5.4 4.4
##   [19] 3.2 4.1 4.7 3.6 4.0 4.7 4.4 5.3 5.1 4.5 2.3 4.3 5.7 6.1 4.6 5.1 4.2 5.7
##   [37] 5.6 5.5 4.6 4.2 5.3 6.6 5.1 5.8 4.2 4.6 3.2 3.9 4.2 2.7 6.1 5.7 4.7 5.5
##   [55] 4.9 3.5 5.2 3.6 6.0 4.8 4.2 5.1 4.5 3.3 4.8 3.7 5.2 4.8 4.9 5.8 3.5 4.8
##   [73] 5.5 4.2 3.2 4.2 4.1 4.0 5.7 6.0 5.1 4.1 4.3 3.5 6.5 4.0 4.5 3.6 4.1 4.8
##   [91] 5.6 4.1 3.9 5.4 4.7 4.9 3.7 4.8 3.0 4.7 3.8 5.1 4.0 2.0 4.9 4.7 3.9 5.7
##  [109] 6.0 4.8 4.3 4.0 2.3 3.5 3.6 3.8 4.2 5.6 4.4 3.0 4.6 5.7 5.8 4.1 6.0 5.4
##  [127] 2.9 4.2 4.0 5.5 4.9 6.1 5.6 4.3 4.4 6.0 4.8 7.0 3.6 5.3 4.0 4.2 4.0 3.4
##  [145] 3.8 5.8 3.2 5.1 4.5 5.0 3.8 2.6 4.1 2.5 3.3 5.8 2.0 4.8 4.1 3.8 4.3 4.8
##  [163] 4.2 5.7 4.7 5.6 2.6 4.9 5.1 4.0 4.4 5.2 4.4 2.8 5.7 3.3 4.6 4.6 5.3 3.6
##  [181] 4.5 3.6 3.8 4.8 3.5 3.6 4.6 4.3 4.7 3.9 3.5 4.0 5.3 4.7 3.0 4.1 6.4 4.8
##  [199] 4.8 5.2 4.4 5.1 6.5 4.7 4.3 3.4 3.7 4.2 4.4 4.9 3.4 4.0 4.9 6.5 4.4 3.9
##  [217] 3.5 4.7 4.9 2.4 4.3 5.2 2.8 3.7 6.2 4.5 5.7 4.3 4.7 3.1 6.0 5.0 5.1 6.1
##  [235] 5.3 5.8 4.8 5.5 5.2 4.9 5.7 5.3 4.6 4.5 3.9 3.7 5.0 4.7 4.1 6.5 3.8 4.0
##  [253] 5.7 5.5 3.3 6.1 5.8 4.3 3.8 4.4 5.8 4.6 4.8 2.9 5.3 5.2 5.0 6.3 3.6 4.8
##  [271] 2.7 3.4 4.7 5.8 6.0 3.5 4.0 4.9 3.8 5.0 2.8 4.7 2.5 5.2 3.7 3.4 4.8 4.0
##  [289] 5.1 5.5 3.0 4.9 5.2 4.5 3.5 5.2 5.3 6.1 3.3 6.7 4.0 4.6 5.1 5.2 4.7 5.2
##  [307] 6.2 5.5 3.3 5.4 3.5 5.8 4.2 3.6 4.4 5.5 4.7 4.0 4.3 5.6 3.8 3.4 2.1 3.9
##  [325] 3.8 4.1 4.6 3.3 3.3 4.5 3.4 5.2 3.5 4.0 4.0 6.0 4.2 4.5 4.0 3.6 3.4 5.1
##  [343] 4.1 3.4 5.8 4.5 3.9 4.2 4.2 3.4 5.3 4.4 6.3 4.0 5.5 5.3 4.3 4.7 2.7 5.5
##  [361] 6.7 2.7 5.8 3.9 4.3 3.3 5.5 5.7 4.8 5.0 6.7 5.1 4.8 5.4 3.5 5.7 3.6 2.5
##  [379] 4.3 4.0 5.5 4.1 5.0 4.8 3.9 4.8 4.6 4.0 6.4 3.2 3.9 4.9 3.6 5.0 3.0 4.4
##  [397] 5.4 4.9 5.3 5.0 4.5 4.1 4.7 3.9 4.3 4.4 5.6 4.4 3.9 5.5 4.4 3.9 2.7 5.6
##  [415] 4.7 3.7 5.5 4.6 4.2 4.3 4.2 3.5 4.3 5.2 5.3 3.8 4.4 4.8 4.3 3.1 5.2 4.3
##  [433] 5.8 4.8 4.0 3.8 4.5 4.7 5.1 5.4 4.4 4.8 3.0 3.6 4.6 3.9 4.6 6.1 3.9 4.3
##  [451] 4.6 3.2 4.9 4.0 3.7 4.6 4.9 3.6 3.2 5.2 5.6 5.9 4.2 4.3 4.5 3.3 3.6 6.9
##  [469] 4.9 4.9 5.9 5.4 4.9 6.7 5.2 4.9 6.1 3.7 2.6 4.7 6.0 4.2 4.6 5.4 3.8 3.5
##  [487] 4.2 5.0 5.0 4.6 5.2 5.8 4.0 6.4 2.8 3.8 3.5 4.7 3.9 5.8 3.4 6.0 4.0 5.8
##  [505] 3.8 3.9 5.1 4.5 5.3 5.0 5.6 3.5 4.6 3.9 5.2 5.5 8.2 4.8 5.2 2.9 4.3 7.2
##  [523] 4.7 4.5 7.2 2.8 3.7 5.6 4.7 5.3 3.9 3.2 4.4 4.6 4.5 5.0 3.4 5.1 3.7 3.6
##  [541] 5.0 5.3 3.5 3.7 3.5 4.3 4.3 4.9 3.1 3.6 5.1 4.9 3.9 4.6 4.9 4.5 5.3 4.5
##  [559] 3.8 2.2 4.9 2.8 3.0 5.7 4.2 4.7 4.9 4.8 5.4 5.4 2.6 4.5 5.7 4.3 4.3 4.5
##  [577] 4.1 5.0 5.9 5.3 4.3 4.7 3.7 5.0 5.0 4.8 4.0 4.8 2.9 5.5 2.3 3.1 3.5 5.4
##  [595] 4.8 4.2 4.0 3.8 5.3 3.8 4.2 4.4 5.8 4.7 5.3 4.8 4.2 5.7 4.4 4.9 4.6 5.9
##  [613] 6.2 4.8 6.3 5.0 4.9 5.0 3.6 4.3 3.9 4.8 4.1 3.1 4.2 5.5 6.4 4.8 3.8 3.7
##  [631] 4.0 3.6 4.0 5.1 6.9 3.3 3.7 5.2 6.2 4.4 4.0 4.0 5.6 3.7 4.3 6.8 4.5 4.6
##  [649] 3.7 3.7 4.2 4.5 3.5 4.0 4.1 4.6 4.8 4.4 3.9 4.9 4.7 4.8 4.9 6.3 3.6 5.6
##  [667] 4.6 4.9 5.7 2.7 4.9 4.9 4.5 3.2 3.2 3.1 4.7 4.2 5.7 5.0 5.0 2.3 4.4 5.4
##  [685] 3.7 4.4 3.4 4.7 5.2 6.3 4.6 4.9 6.3 4.4 4.6 4.5 3.9 3.3 5.7 4.0 4.9 3.7
##  [703] 4.5 5.5 4.1 4.7 4.3 5.0 4.5 5.2 4.0 5.3 3.1 4.8 3.8 3.9 2.1 5.7 3.6 3.3
##  [721] 3.9 5.0 3.2 3.6 5.0 3.9 5.3 4.7 5.4 5.8 6.1 3.5 3.6 6.1 6.7 4.5 3.4 3.9
##  [739] 3.6 3.1 3.6 4.9 3.8 5.8 4.8 4.3 3.2 3.5 4.1 5.0 3.9 4.3 4.5 6.5 5.9 3.3
##  [757] 3.5 2.7 5.3 3.3 3.7 4.6 4.6 3.5 4.1 3.1 3.4 4.5 4.2 4.4 4.6 5.3 3.5 4.8
##  [775] 4.8 4.9 3.6 5.3 3.1 5.6 3.9 4.8 5.0 4.1 5.9 6.0 4.3 4.8 5.6 3.8 5.0 5.8
##  [793] 3.6 5.4 4.1 4.6 4.3 3.6 4.5 5.0 5.3 4.2 3.2 5.6 4.8 5.3 3.5 3.4 4.7 3.3
##  [811] 3.8 3.2 5.1 4.1 5.1 3.0 4.5 6.5 5.1 4.0 5.0 3.3 3.6 5.4 5.4 3.6 4.6 3.7
##  [829] 4.5 4.1 3.0 4.8 4.1 4.1 4.0 5.1 5.8 4.4 3.7 3.1 4.7 4.1 5.8 5.1 3.7 5.9
##  [847] 5.0 4.0 4.7 4.9 4.4 3.8 5.1 4.3 4.7 3.9 4.1 3.1 4.9 4.6 5.0 4.3 6.9 3.2
##  [865] 4.0 3.8 5.9 5.1 5.3 5.0 4.9 4.7 4.5 4.0 4.7 4.1 3.8 5.2 5.4 5.0 4.1 4.3
##  [883] 5.5 6.6 3.8 5.6 4.6 3.1 5.6 5.7 5.0 5.1 4.0 4.5 5.0 3.0 5.5 3.0 3.5 4.5
##  [901] 3.1 4.0 5.3 3.4 6.0 3.6 6.7 5.0 5.6 4.9 4.1 2.1 4.9 4.2 3.6 4.1 5.1 3.9
##  [919] 5.1 5.8 5.3 2.8 2.9 4.5 5.0 3.9 4.3 5.2 6.1 3.3 5.4 3.5 4.2 4.5 3.7 3.7
##  [937] 3.9 2.7 3.9 5.0 3.4 2.6 3.8 6.1 5.5 5.9 7.1 4.4 5.2 3.6 4.8 4.3 3.7 2.8
##  [955] 2.2 6.1 4.7 5.9 3.7 5.6 4.5 4.8 3.7 3.9 6.0 6.5 3.1 5.1 3.6 4.1 5.6 5.7
##  [973] 5.0 5.5 5.9 4.6 6.3 5.2 5.3 4.8 5.0 4.4 4.8 3.5 4.7 5.9 4.3 5.3 2.5 5.0
##  [991] 3.9 5.1 5.2 3.5 3.6 2.2 5.5 4.9 4.7 5.1
## 
## $func.thetastar
## NULL
## 
## $jack.boot.val
## NULL
## 
## $jack.boot.se
## NULL
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta)
```

```r
quantile(results$thetastar,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.4
```

Notice that we get exactly what we got last time. This illustrates an important point, which is that the bootstrap functions are often no easier to use than something you could write yourself.

You can also define a function of the bootstrapped statistics (we have been calling this theta) to pull out immediately any summary statistics you are interested in from the bootstrapped thetas.

Here I will write a function that calculates the bias of my estimate of the mean (which is 4.5 [i.e. the mean of the number 0,1,2,3,4,5,6,7,8,9])


```r
bias<-function(x)
  {
  mean(x)-4.5
  }
results<-bootstrap(x=x,nboot=1000,theta=theta,func=bias)
results
```

```
## $thetastar
##    [1] 5.1 3.6 3.5 4.2 5.1 4.6 4.9 3.5 4.5 6.9 2.7 4.1 3.6 5.0 5.3 3.3 5.3 3.5
##   [19] 5.1 5.6 6.0 3.6 2.8 5.5 3.2 3.9 3.8 4.9 3.2 3.4 3.4 5.9 6.6 4.4 3.6 6.2
##   [37] 5.1 5.7 4.6 5.0 4.9 5.0 5.4 5.1 3.1 3.2 4.3 3.9 5.9 3.3 3.7 3.4 3.8 3.7
##   [55] 4.9 4.4 5.6 3.9 6.2 4.2 2.4 5.3 3.9 4.7 5.0 4.3 4.6 3.3 4.5 3.9 4.6 4.9
##   [73] 4.5 4.4 4.3 4.8 4.0 4.9 4.2 5.7 5.4 4.2 4.4 5.6 3.7 1.9 5.2 4.2 4.5 5.8
##   [91] 5.2 7.0 3.5 3.4 4.9 5.0 4.2 4.0 4.4 4.3 4.8 4.4 4.4 2.4 5.6 5.8 4.9 4.4
##  [109] 4.9 4.1 4.2 4.6 3.8 4.7 3.8 4.3 4.5 4.8 5.1 3.7 4.7 3.9 4.8 4.1 5.2 4.5
##  [127] 4.7 4.2 5.5 2.9 3.9 4.5 5.8 4.7 3.6 6.0 4.9 3.7 4.8 4.4 3.9 4.8 4.7 3.8
##  [145] 4.0 3.8 4.2 4.4 6.3 3.7 4.7 2.9 3.7 4.6 4.1 4.8 4.2 4.9 5.3 2.2 4.7 5.5
##  [163] 3.8 4.2 5.1 2.8 3.3 3.6 4.0 4.0 4.8 4.5 4.2 5.7 3.5 3.9 6.0 4.0 4.9 3.2
##  [181] 4.2 4.6 4.7 2.5 5.1 2.9 6.2 4.3 4.2 5.0 5.4 4.5 4.8 3.6 3.4 3.3 4.6 4.1
##  [199] 3.1 4.3 5.1 4.0 3.3 5.7 4.7 6.4 3.3 5.6 5.0 4.7 3.9 4.5 3.8 6.3 4.7 3.7
##  [217] 4.8 2.6 5.4 3.7 5.6 3.8 5.6 6.1 3.0 3.8 6.4 5.7 3.9 4.7 3.6 4.0 4.5 4.2
##  [235] 4.1 3.7 5.1 6.1 4.1 5.0 6.0 3.8 4.4 6.4 5.8 5.3 5.4 2.7 4.2 4.6 4.2 4.4
##  [253] 4.8 5.3 5.1 4.5 6.5 2.9 4.1 4.1 3.5 5.1 5.5 5.4 5.4 4.6 3.5 4.3 4.9 4.5
##  [271] 5.1 4.4 3.7 4.9 3.4 6.1 5.2 4.5 5.1 5.6 3.7 4.7 2.8 5.4 3.5 4.8 4.9 4.2
##  [289] 4.5 5.5 5.2 4.6 4.4 5.3 4.9 6.8 3.8 4.8 4.2 4.6 4.6 4.3 4.8 5.1 4.6 5.3
##  [307] 3.6 3.3 5.2 3.6 4.5 5.3 4.4 4.2 5.3 4.8 3.4 4.4 3.7 3.7 3.9 3.4 3.6 5.5
##  [325] 6.2 5.9 6.4 3.2 5.6 4.8 5.0 4.0 6.1 4.4 3.7 4.4 3.6 4.4 3.5 4.9 6.6 4.7
##  [343] 2.5 4.7 4.9 5.8 5.3 5.3 4.6 4.2 5.0 4.6 5.3 5.2 4.1 3.1 4.5 3.9 5.0 4.3
##  [361] 3.3 5.3 3.7 5.0 3.8 3.8 4.2 5.2 3.3 4.4 5.2 4.9 3.6 5.9 3.8 5.0 4.2 5.2
##  [379] 2.8 4.7 5.2 4.8 5.3 4.2 4.6 5.5 4.3 4.8 6.9 3.1 4.5 2.9 4.5 4.7 4.2 5.0
##  [397] 2.9 6.1 5.1 4.7 5.2 3.9 5.3 3.6 4.3 5.2 4.6 2.5 6.1 3.1 4.7 5.3 4.6 3.4
##  [415] 4.0 4.7 4.2 5.5 5.2 5.2 4.8 3.9 5.7 2.1 5.2 5.6 5.8 5.5 4.8 5.1 3.9 4.4
##  [433] 3.3 2.9 5.8 5.9 3.2 5.4 4.8 4.2 5.1 4.9 6.0 4.7 5.6 4.3 4.6 4.5 5.0 5.8
##  [451] 4.1 3.1 4.9 5.6 4.5 5.4 4.8 3.4 5.1 4.7 6.2 3.6 6.3 4.2 5.4 5.3 6.9 4.2
##  [469] 3.5 4.2 3.4 5.2 3.6 4.3 5.2 4.8 4.4 3.8 5.3 6.0 5.5 4.8 5.0 4.6 5.3 4.7
##  [487] 4.6 5.1 3.3 4.3 5.7 5.0 5.0 6.5 3.8 4.4 4.7 3.3 5.2 4.7 5.5 4.1 4.3 2.8
##  [505] 4.9 3.9 5.1 5.2 4.3 3.0 5.3 4.6 3.7 4.1 3.1 4.3 4.6 4.9 3.4 5.0 4.4 4.3
##  [523] 4.7 4.9 4.2 4.3 6.4 3.9 3.5 3.7 5.3 5.8 4.0 3.5 4.9 5.4 5.2 4.6 3.6 5.4
##  [541] 4.0 2.2 5.1 4.0 4.3 4.2 4.8 4.5 3.6 5.5 5.1 5.8 6.1 4.7 4.8 3.7 4.6 5.5
##  [559] 3.8 4.1 4.8 6.2 5.0 4.2 6.8 5.2 3.1 4.3 6.0 6.0 5.2 5.6 3.4 5.3 5.1 3.1
##  [577] 4.9 5.4 5.4 4.7 4.0 4.4 3.8 2.8 6.1 5.6 5.2 4.1 3.3 4.1 3.3 5.4 4.9 3.8
##  [595] 4.7 4.5 3.2 5.5 3.9 5.3 5.6 3.9 4.8 5.0 4.0 3.5 4.6 5.2 3.6 2.5 7.6 5.6
##  [613] 4.2 4.0 5.4 5.7 3.1 4.1 4.6 5.1 4.7 7.5 5.4 4.5 5.1 4.4 6.9 5.4 5.1 5.4
##  [631] 4.9 5.5 4.1 4.2 4.2 3.9 5.0 4.9 5.4 5.4 4.0 3.5 5.7 2.5 4.4 4.9 4.7 5.0
##  [649] 4.0 5.2 5.3 3.7 3.8 4.4 4.4 3.9 4.1 3.4 4.9 4.8 5.1 5.1 2.4 3.6 4.7 4.6
##  [667] 4.4 3.9 5.1 2.6 3.3 4.1 3.8 3.1 4.1 4.8 3.3 3.9 5.2 4.1 5.9 5.5 3.9 5.7
##  [685] 4.9 3.0 3.3 5.2 4.3 3.6 5.5 4.3 4.0 4.3 4.8 4.9 4.0 5.4 3.9 5.5 4.7 3.8
##  [703] 3.8 5.1 3.0 5.7 5.4 4.6 4.0 3.3 4.3 4.5 3.6 4.8 3.5 5.7 6.1 6.1 5.2 4.2
##  [721] 4.6 4.8 5.3 4.7 3.5 4.4 4.7 4.5 2.7 4.3 4.2 5.2 4.4 3.7 3.0 4.4 4.2 5.3
##  [739] 4.0 3.9 5.3 3.7 5.8 2.5 5.3 5.2 4.4 4.5 4.4 4.8 4.6 4.4 2.9 4.6 5.3 5.6
##  [757] 3.8 4.4 4.2 6.0 2.4 3.6 4.7 4.3 5.4 5.6 5.5 5.0 5.3 2.9 4.4 5.0 4.5 5.6
##  [775] 4.2 4.1 4.0 5.8 5.2 6.0 5.4 4.4 4.0 4.9 4.0 4.1 4.2 3.9 3.9 4.4 3.8 3.6
##  [793] 4.5 5.1 3.9 5.5 4.1 5.3 4.2 4.1 4.8 5.7 5.6 5.5 4.8 5.7 4.3 6.2 5.5 5.2
##  [811] 5.1 5.6 6.1 3.6 3.5 4.1 4.3 4.4 5.6 6.5 5.2 4.1 2.4 5.0 3.3 4.2 4.2 4.5
##  [829] 4.0 4.6 5.2 5.2 4.3 5.4 5.1 4.5 2.4 5.7 4.2 2.1 5.7 3.2 5.2 4.6 3.0 2.9
##  [847] 3.5 3.6 4.1 4.9 6.3 4.1 3.0 3.9 4.7 4.6 5.8 5.0 5.0 4.8 3.9 3.2 4.8 4.4
##  [865] 5.3 3.4 5.5 3.9 5.8 5.0 3.1 4.6 5.6 4.7 4.8 5.0 3.3 3.7 5.7 3.4 3.0 4.1
##  [883] 4.8 4.0 3.8 4.0 2.6 4.1 4.5 4.3 3.6 5.6 3.2 4.6 3.7 2.7 3.1 5.0 4.3 4.3
##  [901] 5.6 4.6 5.5 3.9 4.5 4.2 5.7 5.0 2.6 5.3 4.2 5.1 4.1 5.6 4.2 3.9 5.3 3.2
##  [919] 5.1 4.9 4.4 6.1 4.2 4.1 4.6 4.5 4.8 3.9 2.7 4.4 3.5 4.1 6.0 4.7 5.3 3.7
##  [937] 4.9 6.0 4.4 4.0 3.4 6.0 4.7 4.9 4.4 4.6 5.1 5.8 3.4 3.6 5.0 5.5 3.1 4.9
##  [955] 4.7 3.8 4.9 5.1 4.5 4.2 3.9 4.6 5.3 5.3 3.6 4.8 5.0 4.1 2.9 6.6 4.7 4.3
##  [973] 5.2 4.2 4.9 4.6 3.8 4.2 4.3 4.3 3.7 4.9 5.1 3.6 2.5 3.0 4.7 4.4 4.7 5.0
##  [991] 4.8 3.0 5.1 3.6 4.6 3.6 5.6 4.2 4.5 5.0
## 
## $func.thetastar
## [1] 0.0278
## 
## $jack.boot.val
##  [1]  0.46507042  0.36285714  0.29859155  0.21342466  0.09784946 -0.01480363
##  [7] -0.12140845 -0.23506849 -0.38765060 -0.46676136
## 
## $jack.boot.se
## [1] 0.9129539
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta, func = bias)
```

Compare this to 'bias.boot' (our result from above). Why might it not be the same? Try running the same section of code several times. See how the value of the bias ($func.thetastar) jumps around? We should not be surprised by this because we can look at the jackknife-after-bootstrap estimate of the standard error of the function (in this case, that function is the bias) and we can see that it is not so small that we wouldn't expect some variation in these values.

Remember, everything we have discussed today are estimates. The statistic as applied to your data will change with new data, as will the standard error, the confidence intervals - everything! All of these values have sampling distributions and are subject to change if you repeated the procedure with new data.

Note that we can calculate any function of $\theta^{*}$. A simple example would be the 72nd percentile:


```r
perc72<-function(x)
  {
  quantile(x,probs=c(0.72))
  }
results<-bootstrap(x=x,nboot=1000,theta=theta,func=perc72)
results
```

```
## $thetastar
##    [1] 5.2 4.6 4.8 4.8 4.0 4.1 4.5 4.1 3.6 4.3 2.5 5.2 2.4 5.5 4.5 4.4 4.9 6.3
##   [19] 6.1 5.9 4.1 4.9 3.5 5.3 4.3 5.8 5.5 4.8 3.2 4.9 4.4 4.6 5.9 4.9 4.7 4.3
##   [37] 4.8 5.1 5.1 4.0 5.0 4.1 5.3 4.9 4.5 4.1 5.1 4.2 4.1 5.1 4.2 6.4 2.9 4.2
##   [55] 5.0 4.4 3.8 5.8 4.0 4.4 4.6 3.8 4.3 4.5 4.4 4.9 5.3 2.9 5.5 5.1 4.5 5.8
##   [73] 3.1 4.8 4.6 3.6 3.8 4.8 5.2 3.8 5.2 4.5 4.4 5.7 4.9 5.0 4.8 3.6 5.7 5.0
##   [91] 6.4 4.5 4.5 4.4 4.5 5.4 4.7 4.6 5.1 3.8 3.7 3.3 3.3 6.7 5.3 4.8 4.6 4.7
##  [109] 5.0 5.1 5.3 4.0 3.0 4.5 4.8 3.7 6.0 5.1 3.5 3.0 4.4 4.4 5.0 5.8 4.6 4.3
##  [127] 5.8 4.3 1.7 4.8 5.6 3.8 3.8 5.9 3.8 6.0 4.0 4.9 4.1 3.5 5.3 5.4 2.5 4.2
##  [145] 4.0 4.2 4.9 3.5 4.7 3.9 4.3 5.4 4.0 4.3 5.0 4.2 3.8 5.1 4.0 4.0 3.8 5.1
##  [163] 2.7 4.9 3.3 4.2 4.2 4.4 5.1 4.3 5.4 4.1 6.1 4.8 4.7 5.7 4.6 3.6 6.3 2.6
##  [181] 4.7 5.1 5.1 4.2 4.2 4.9 4.0 3.2 4.5 3.5 3.8 4.1 5.2 3.6 4.7 4.6 3.7 3.7
##  [199] 4.1 3.4 2.5 3.4 3.5 4.5 5.1 4.3 4.3 4.3 3.2 4.1 3.7 3.3 7.0 4.9 4.8 4.9
##  [217] 5.1 4.4 5.9 4.4 4.8 4.6 4.3 5.8 5.9 4.4 5.7 4.5 5.4 3.6 3.9 3.0 5.6 6.6
##  [235] 5.5 3.1 3.9 4.7 5.8 5.2 4.8 3.9 3.2 5.2 5.2 5.1 4.6 4.8 5.4 3.8 4.1 5.9
##  [253] 2.8 3.1 4.4 5.2 4.5 4.6 3.1 4.4 5.0 4.1 3.2 4.2 4.7 4.2 4.5 3.6 4.4 3.9
##  [271] 5.2 4.5 6.2 5.1 5.2 4.6 4.7 5.4 4.9 3.8 3.4 4.6 4.2 5.5 4.5 5.0 5.1 5.6
##  [289] 5.1 4.2 2.6 4.8 3.7 2.9 3.1 3.8 4.0 5.5 6.3 2.9 4.3 6.5 5.4 5.2 3.9 3.9
##  [307] 5.4 3.8 4.4 3.8 3.2 6.1 4.3 4.4 5.0 4.3 2.9 4.2 3.9 5.1 4.9 4.0 5.3 3.3
##  [325] 2.7 6.0 3.1 4.3 5.2 5.5 4.0 3.8 5.0 4.0 4.2 6.0 5.4 6.1 4.3 3.6 4.5 5.0
##  [343] 4.2 5.8 4.1 3.9 5.1 5.7 4.0 7.0 3.8 2.7 4.3 4.4 4.6 4.7 3.8 4.7 5.0 4.0
##  [361] 4.7 5.3 5.0 4.9 2.9 3.9 4.3 5.2 6.1 2.9 4.6 4.6 5.3 4.7 4.7 2.7 3.7 5.6
##  [379] 4.6 3.4 4.9 3.3 5.1 2.8 5.0 4.0 3.6 5.8 4.5 5.9 3.6 4.8 3.4 5.5 5.3 3.3
##  [397] 5.2 4.3 4.7 5.4 4.7 4.7 3.4 4.6 4.4 4.5 5.3 4.3 5.8 6.3 4.3 4.4 5.1 5.4
##  [415] 5.1 2.3 3.6 6.0 3.6 4.3 6.1 4.2 4.6 2.7 5.6 5.0 3.2 5.0 5.3 3.5 4.9 3.4
##  [433] 5.0 4.0 5.5 3.3 3.4 4.1 2.9 4.8 4.4 4.6 4.8 3.9 3.7 4.6 5.3 4.5 5.3 5.0
##  [451] 5.2 3.4 4.8 4.9 5.7 4.4 5.3 4.8 4.5 2.6 5.8 6.1 4.9 4.4 3.8 3.6 5.0 3.7
##  [469] 3.8 4.8 5.4 3.2 6.2 2.8 5.1 4.7 5.3 4.4 4.7 4.4 5.0 5.4 6.1 3.8 5.0 6.2
##  [487] 3.7 4.9 4.8 3.5 4.9 4.7 5.3 4.0 2.8 4.4 5.9 4.9 4.9 4.7 3.9 5.0 4.5 5.0
##  [505] 4.3 5.2 4.8 4.8 5.6 4.1 4.5 5.0 5.3 4.0 5.0 3.9 4.1 4.9 2.9 4.5 5.1 5.1
##  [523] 5.5 4.4 4.8 4.6 5.2 4.4 5.6 4.3 3.6 3.5 2.4 5.0 5.4 6.4 3.9 3.8 4.8 5.0
##  [541] 5.2 5.0 2.6 4.0 4.0 4.8 4.6 4.4 4.9 4.3 5.5 6.3 4.9 5.0 4.1 6.1 3.4 5.9
##  [559] 6.7 3.9 4.7 3.9 3.5 4.5 3.4 5.6 3.3 3.7 5.0 4.0 4.3 4.0 4.6 4.5 5.5 5.2
##  [577] 3.3 4.7 5.1 4.8 4.8 4.9 6.2 3.1 2.5 4.7 4.0 6.1 3.5 5.5 5.9 4.4 3.1 5.5
##  [595] 4.7 4.2 3.6 5.3 3.5 6.6 3.8 4.2 5.7 4.5 4.9 5.9 4.0 4.1 5.0 4.3 3.2 2.6
##  [613] 4.1 3.7 5.8 4.0 4.3 3.3 4.5 4.7 2.5 5.0 5.5 5.6 4.7 4.2 5.1 6.1 5.2 4.4
##  [631] 4.8 6.0 6.8 3.2 3.0 3.5 3.7 5.2 5.4 5.7 4.4 3.5 5.0 4.9 3.9 4.9 4.4 3.8
##  [649] 4.0 3.7 4.7 3.2 4.8 4.4 4.1 3.7 3.6 4.1 3.9 5.3 4.9 6.1 4.8 5.3 5.7 3.5
##  [667] 3.4 5.8 3.7 4.8 5.1 5.8 5.3 4.3 4.5 4.6 4.3 5.0 4.0 3.3 3.9 6.4 5.4 4.8
##  [685] 4.4 4.2 5.7 4.9 4.7 4.5 6.1 3.5 4.9 4.0 5.4 5.2 4.6 4.8 6.3 4.6 4.5 5.1
##  [703] 2.9 3.8 3.6 3.9 4.2 5.7 3.3 4.0 5.2 3.9 3.5 3.5 3.8 4.8 5.4 4.7 5.9 4.9
##  [721] 4.9 5.3 5.7 5.0 4.0 4.4 4.5 3.4 4.2 4.3 4.9 5.0 3.7 3.9 5.1 4.2 4.6 5.7
##  [739] 4.0 5.6 4.7 4.0 4.2 3.8 3.9 4.5 3.2 5.0 5.0 5.1 3.8 5.2 3.9 5.5 5.0 4.0
##  [757] 3.3 4.7 4.8 5.0 5.1 4.7 3.5 5.5 7.2 5.7 4.4 5.4 4.9 4.1 4.0 5.6 5.2 3.1
##  [775] 5.3 5.6 5.1 3.0 4.0 3.6 4.6 4.2 2.6 4.7 3.9 3.6 5.4 4.7 5.1 4.4 5.1 6.3
##  [793] 4.1 4.2 4.6 3.9 5.0 5.0 4.9 3.8 4.3 3.3 3.2 4.6 6.3 4.0 5.2 5.3 3.0 4.1
##  [811] 4.1 5.3 5.3 2.0 6.5 3.9 5.5 4.2 5.1 5.0 4.5 5.1 3.9 4.2 4.8 5.0 4.0 4.2
##  [829] 3.8 4.3 4.3 5.2 5.5 3.2 4.5 5.1 4.6 4.5 4.1 4.4 5.1 4.3 5.0 3.1 4.2 4.7
##  [847] 5.7 5.4 5.0 3.1 5.1 4.2 4.3 3.1 5.8 4.9 4.1 4.5 3.6 4.4 3.2 4.4 4.8 4.6
##  [865] 5.6 4.3 5.3 4.9 4.4 3.3 3.9 5.4 2.5 4.9 6.7 4.9 5.4 2.5 4.4 5.5 5.7 5.5
##  [883] 5.2 4.8 4.4 6.8 3.3 4.1 3.6 6.2 5.3 4.7 3.6 4.7 5.5 4.7 5.5 3.4 4.4 6.1
##  [901] 3.0 5.6 4.6 5.0 4.9 5.8 5.1 5.0 4.4 6.3 4.9 5.2 7.0 4.1 3.7 5.2 5.4 4.3
##  [919] 5.6 4.4 3.1 4.6 4.9 5.1 4.3 4.8 5.8 4.4 4.0 5.2 5.4 5.6 6.0 3.5 4.7 4.0
##  [937] 4.8 5.2 5.8 4.9 4.1 3.3 4.5 5.6 3.7 2.9 3.4 3.8 5.0 3.8 4.1 5.1 5.6 3.2
##  [955] 5.8 5.2 3.7 5.9 4.1 4.1 3.8 2.9 5.5 4.5 5.6 4.2 3.5 6.0 4.7 3.8 3.7 4.3
##  [973] 6.1 3.6 5.2 4.1 3.6 5.2 3.6 5.3 5.1 5.2 4.3 6.3 3.9 4.9 3.3 5.2 6.7 3.8
##  [991] 5.3 5.3 6.7 4.8 4.7 4.0 4.5 4.4 4.7 4.0
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.5 5.3 5.3 5.3 5.1 5.0 5.0 4.8 4.8 4.6
## 
## $jack.boot.se
## [1] 0.8055433
## 
## $call
## bootstrap(x = x, nboot = 1000, theta = theta, func = perc72)
```

On Tuesday we went over an example in which we bootstrapped the correlation coefficient between LSAT scores and GPA. To do that, we sampled pairs of (LSAT,GPA) data with replacement. Here is a little script that would do something like that using (X,Y) data that are independently drawn from the normal distribution


```r
xdata<-matrix(rnorm(30),ncol=2)
```

Everyone's data is going to be different. With such a small sample size, it would be easy to get a positive or negative correlation by random change, but on average across everyone's datasets, there should be zero correlation because the two columns are drawn independently.


```r
n<-15
theta<-function(x,xdata)
  {
  cor(xdata[x,1],xdata[x,2])
  }
results<-bootstrap(x=1:n,nboot=50,theta=theta,xdata=xdata) 
#NB: xdata is passed to the theta function, not needed for bootstrap function itself
```

Notice the parameters that get passed to the 'bootstrap' function are: (1) the indexes which will be sampled with replacement. This is different that the raw data but the end result is the same because both the indices and the raw data get passed to the function 'theta' (2) the number of bootrapped samples (in this case 50) (3) the function to calculate the statistic (4) the raw data.

Lets look at a histogram of the bootstrapped statistics $\theta^{*}$ and draw a vertical line for the statistic as applied to the original data.


```r
hist(results$thetastar,breaks=30,col="pink")
abline(v=cor(xdata[,1],xdata[,2]),lwd=2)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-17-1.png" width="672" />

Parametric bootstrap
---------------------

Let's do one quick example of a parametric bootstrap. We haven't introduced distributions yet (except for the Gaussian, or Normal, distribution, which is the most familiar), so lets spend a few minutes exploring the Gamma distribution, just so we have it to work with for testing out parametric bootstrap. All we need to know is that the Gamma distribution is a continuous, non-negative distribution that takes two parameters, which we call "shape" and "rate". Lets plot a few examples just to see what a Gamma distribution looks like. (Note that the Gamma distribution can be parameterized by "shape" and "rate" OR by "shape" and "scale", where "scale" is just 1/"rate". R will allow you to use either (shape,rate) or (shape,scale) as long as you specify which you are providing.

<img src="Week-2-lab_files/figure-html/unnamed-chunk-18-1.png" width="672" />


Let's generate some fairly sparse data from a Gamma distribution


```r
original.data<-rgamma(10,3,5)
```

and calculate the skew of the data using the R function 'skewness' from the 'moments' package. 


```r
library(moments)
theta<-skewness(original.data)
head(theta)
```

```
## [1] 0.3077723
```

What is skew? Skew describes how assymetric a distribution is. A distribution with a positive skew is a distribution that is "slumped over" to the right, with a right tail that is longer than the left tail. Alternatively, a distribution with negative skew has a longer left tail. Here we are just using it for illustration, as a property of a distribution that you may want to estimate using your data.

Lets use 'fitdistr' to fit a gamma distribution to these data. This function is an extremely handy function that takes in your data, the name of the distribution you are fitting, and some starting values (for the estimation optimizer under the hood), and it will return the parameter values (and their standard errors). We will learn in a couple weeks how R is doing this, but for now we will just use it out of the box. (Because we generated the data, we happen to know that the data are gamma distributed. In general we wouldn't know that, and we will see in a second that our assumption about the shape of the data really does make a difference.)


```r
library(MASS)
fit<-fitdistr(original.data,dgamma,list(shape=1,rate=1))
# fit<-fitdistr(original.data,"gamma")
# The second version would also work.
fit
```

```
##     shape       rate  
##   5.283117   7.539103 
##  (2.291972) (3.431214)
```

Now lets sample with replacement from this new distribution and calculate the skewness at each step:


```r
results<-c()
for (i in 1:1000)
  {
  x.star<-rgamma(length(original.data),shape=fit$estimate[1],rate=fit$estimate[2])
  results<-c(results,skewness(x.star))
  }
head(results)
```

```
## [1]  0.6885877  0.3319295  0.5648524  0.2368536  0.7570007 -0.4237212
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-22-1.png" width="672" />

Now we have the bootstrap distribution for skewness (the $\theta^{*}$ s), we can compare that to the equivalent non-parametric bootstrap:


```r
results2<-bootstrap(x=original.data,nboot=1000,theta=skewness)
results2
```

```
## $thetastar
##    [1]  0.5305767504 -0.1735203877 -0.3616149847  0.3068423181 -0.2081177163
##    [6] -0.1983326001 -0.1336832719  0.4184957772 -0.2429470462  0.0744700006
##   [11]  0.2831772563  0.4354527278  0.5025367028  0.8512821373  0.0418112653
##   [16]  0.3709227832  0.4810565003  0.2490107574 -0.4402391341  0.1614606897
##   [21]  0.2624594843  0.1219631635  0.1796410825  0.8414658148 -0.2601480994
##   [26] -0.1564328865  0.7657480985 -0.4161943741  0.4234672530 -0.4361319932
##   [31]  0.9793224458 -0.1755166212 -0.3711036777 -0.1388476376  0.1002345770
##   [36]  0.3875717201  0.3949547294  0.0973755472  0.5818931218 -0.1426849658
##   [41] -0.0131210278 -0.7040222709 -0.2066022444 -0.0250400905  0.0671822890
##   [46]  0.2626057418  0.5010951785  0.0731282758 -0.3566829232  0.7091544110
##   [51]  0.5107908776 -0.7861013693  0.1778363289 -0.2670225414  0.5943394497
##   [56] -0.7394057318  0.5317586558 -0.0009626858  0.1760503292  0.4485755157
##   [61] -0.2519477150  0.4604640019 -0.2263722779  0.1232455159  0.6439049232
##   [66]  0.4229786918  0.0877222880  0.4507745933  0.4148089514 -0.7713867284
##   [71]  0.2148494284 -0.4724388628 -1.2705806957 -0.3944539273  0.8229887835
##   [76] -1.3269326899 -0.2531710576 -0.2868095439  0.3872758159 -0.0820415127
##   [81]  0.0470604620 -0.1042691682  0.4553551635  0.4715625058 -0.1793512397
##   [86] -0.3576087686  0.5385376765  1.2184462948  0.1817882607  0.2839745598
##   [91]  1.3676202619  0.8799642721 -0.2145266972 -0.4908402136 -0.4262254993
##   [96] -0.0194961814 -0.7520349130 -0.0069521606  0.1750486096  0.2850979907
##  [101]  0.3703720236 -0.3843013231 -0.5498552360  0.1888517744  0.4960079727
##  [106]  0.1486141509  0.3096191690 -0.0944444770  0.1513287934  0.1107236763
##  [111]  0.9143890529  0.0686146156  0.6428806120  0.7644768738  0.0928715559
##  [116]  0.0151535009  0.7446709089  0.2044356023  0.2656020747  0.1738638354
##  [121] -0.3297456718  0.0743068716 -0.1694682899 -0.6503919994 -0.5642626170
##  [126]  0.8233802746 -0.6677157309  0.5817790964  0.1275946236  0.3490386291
##  [131] -0.3863966902  0.3390947190  0.2358768270 -0.3411280022 -0.2063508136
##  [136]  0.4453459112  0.4035971003 -0.4156895906  0.3447642358 -1.1521223915
##  [141]  0.1959852481 -0.3230647386  0.6166368227  1.3467971393  0.0743655115
##  [146] -0.0734130318 -0.1552119313  0.0261225746 -0.2359915317 -0.0225426133
##  [151] -0.3086770213 -0.3997242472  0.0811286973  0.0811102552 -0.1597552529
##  [156]  1.3351429858 -1.0896053145  0.2662357891 -0.6912508789  0.5222759443
##  [161] -0.0053468834 -0.0245799544  0.4743513404  0.1125256298  0.5371889386
##  [166] -0.0409961637 -0.1296540085 -0.3136924041 -2.2393664247  0.7931658825
##  [171] -0.2286126437  0.3882024322  1.3351429858  0.8820347791 -2.4562667019
##  [176] -0.0951419435  0.8574699603  0.3145336997  1.4538881083 -0.2913417914
##  [181]  0.1235461214  0.2391234793 -0.0068074917  0.3620600813 -1.7786046623
##  [186] -0.0900808451 -0.6943990824  0.6296381834  0.4330887137 -0.3663782759
##  [191]  0.8769143907 -0.0862644846 -0.0365983217  0.0758894512 -0.7855641028
##  [196] -0.4062958147  0.4377658510  0.6629810353  0.3744582507  0.6334446754
##  [201]  0.1235461214  0.4723885092  0.4371059729  0.1538248837  0.0396334737
##  [206]  0.0476174151  0.0918576968  0.0302006239  0.2351149024  0.0954241983
##  [211]  0.8612413938  0.7991425200  0.5746623762  0.4771781906 -0.7155057515
##  [216]  0.5266619829  0.7690194685  1.5746740205 -0.0137077829  0.3499535588
##  [221]  0.5534793054  0.3310358513  0.2539043299 -0.7072309903  0.0214601991
##  [226] -0.3633324988  0.8308798245  0.4192254425 -0.1562291350  0.3678609779
##  [231]  0.2513792265  1.4272979018  0.3119498824  0.4492662694  0.5179936207
##  [236]  0.3608623472  0.4202528996 -0.8271740951  0.1959779302 -0.7761750032
##  [241] -1.1223358227  1.0401225865  0.0311802119  0.2444757231  1.3953788467
##  [246]  0.2539519365  0.3920035146  0.7459609125  1.3760426783  0.1425784118
##  [251]  0.2798531547  0.2974176402  0.3215650494  0.1416012996  0.2437577612
##  [256]  0.9338378443  0.0196450608  0.6345126660 -0.2675386730 -0.0761297164
##  [261] -0.0072772820  0.0002335416  0.4518073296  1.0760389633 -0.1253663608
##  [266]  0.3716102380  0.0130048434 -0.4210719585  0.0200562987  0.2189712538
##  [271]  0.8006585754  0.1598898206 -0.0383099353  0.3654462956  0.3789616123
##  [276] -0.6330735337  1.3594938674  0.4563330876  0.4150651845  0.4168996618
##  [281] -0.3615426272  0.2994476291 -0.2271484397  0.7842544780 -0.3978910402
##  [286]  0.4303870214 -0.1945204614  0.4715477971 -0.3383524575  0.0467068140
##  [291] -0.0385520533  0.5857429353  0.0311802119 -1.3180463788  0.7424513048
##  [296]  0.3666655177 -0.5044476198 -0.7314542497  0.7060387762 -0.0099347656
##  [301]  0.3352092332  0.4988968813  0.2383777536 -0.2355964910  0.5305238005
##  [306]  0.3451211560 -0.6929253619  0.3298520836  0.5410378890  0.7586898920
##  [311] -0.2582992277  0.3164548447 -0.3081542534  0.1122434245  0.1854901548
##  [316] -0.2702656882 -0.4274772445 -0.0714467492  0.2407007331  0.4525136467
##  [321] -0.2794150349  0.6076621703 -0.1791372007  0.4295304148 -0.3135610810
##  [326] -0.7910694876  0.3912664448  0.0361396911 -0.0405201111 -0.7077200632
##  [331]  0.7340077559 -0.1836477070  0.3988163741 -0.0640495568  0.2184793254
##  [336]  0.7873635135  0.5579088904  0.1164629193 -0.3366105559 -0.4234236082
##  [341]  0.3751423063  0.8313018570 -0.2800262519 -0.6819994785  0.5603279698
##  [346] -0.2100697712  0.2433282267  0.1273655362  0.0903681705  0.3041103408
##  [351]  0.5063044384 -0.1546120489  0.6759981818  0.6143395124  0.3598785540
##  [356]  0.7378821365 -0.1563034333  0.2013586143  0.7382318005  0.1558716600
##  [361]  0.5095401029  0.1897121880  0.0136540434  0.0329151288  0.1190065377
##  [366] -0.1902061883  0.4377550444  0.9404286458  0.4510331784 -0.2681284898
##  [371]  0.1774978389 -0.0312007462 -0.4558813274 -0.3261318836 -0.7609898987
##  [376]  0.2575149416  0.9289649057  0.2043401001  0.6766597224 -1.0314962949
##  [381]  0.4981616698  0.4009365677  0.6507354713 -0.2094431394  0.8892428511
##  [386]  0.1546653798 -0.7453715769 -0.2424721383  0.2116153300  0.1862713873
##  [391]  0.5347320878  0.4047889975 -1.1234316495 -0.3004478926  0.5502574444
##  [396]  0.0086775635  0.3693025525  0.0699910085  0.4688219386  1.3539006971
##  [401] -0.4166819761 -0.1628239790 -0.2099979324  0.3115970864 -0.1870311268
##  [406]  0.7197256993  0.3187623275  0.1461415976  0.0503806150  0.4549982363
##  [411]  0.9205479726  1.3594938674  0.1423844102  0.1893185981 -0.8176251566
##  [416] -0.0403916576  0.5450856814  1.0717747014 -0.1114551063  1.0287284443
##  [421]  0.7863735454 -0.4577735950  0.5275286099  0.0507512824  0.2888157410
##  [426] -0.2960020778  0.9519642738  0.3107924248  0.3036823206  0.3715401161
##  [431]  0.1224544211  0.0025508820  0.5405647832  0.5220961593 -0.1980835913
##  [436]  1.0903475242 -0.0536598434  1.2217361043  0.1270070659  0.4111989680
##  [441] -0.6729020837  0.4040362232  0.1112814373 -0.1388476376 -0.3349357889
##  [446]  0.7699349065  0.1616069429  0.2518842586  0.2153321023  0.2156991732
##  [451]  0.7934094017  0.2304781641  0.8711622568  0.3337911748 -0.3397244234
##  [456]  0.2419738280 -0.0399110622  0.0489077373 -1.0817790782  0.8618346444
##  [461] -0.0821923032  0.4937760854 -0.6910796825  0.7531807629 -0.0215863869
##  [466]  0.0416332093 -0.2055492675  0.1217110977 -0.1508435547 -0.3731659794
##  [471]  0.1849683203 -0.2107040397  1.0040964444 -0.3006098525 -0.7121435733
##  [476]  1.1541400690  0.0427448818 -0.6776836615  0.1306933022  0.8951521845
##  [481]  0.3348468411 -0.7385118259  1.3454440366 -0.7808686021  0.5058329954
##  [486] -0.3191303426  0.8004489380  0.0717315773  0.0364513825  0.4269996800
##  [491] -0.0401632110  0.0673397316  0.3745371540 -0.2481388154 -0.4450883147
##  [496]  0.3474469720 -0.0163460086 -0.0378928776 -0.2014833639  0.8340839048
##  [501]  0.1635767355  0.3495119195 -1.6920341707  0.0214964395 -0.0117442925
##  [506]  0.6032683627 -0.4129781491  0.3461483744 -0.7009528000  0.2220162124
##  [511]  0.3940723871  0.6507354713  0.1871908729 -0.6307774474 -0.3845677796
##  [516]  0.8303505575  0.5168533624 -0.1944145842  0.2501965798  0.3825477005
##  [521]  0.5269271876  0.8260029398 -0.0163460086  0.3574815023 -0.5559164157
##  [526] -0.7375714314  0.1299062784 -1.2027300887  0.2371404651 -0.4146099521
##  [531] -0.2627327617  0.7191448801  0.0088358583 -0.8500215780  0.8625546962
##  [536] -0.0908362574  0.1021151486  0.1114572601  0.2637312710 -0.1545703810
##  [541] -0.0291942528 -0.3329747525 -0.0470061797 -0.6294417200  0.5179936207
##  [546]  0.1175079966 -0.3421525205  0.3934932957  0.1971624329  0.1024025343
##  [551] -0.2117575148  0.3296678224  0.0821069115  0.5655571250 -0.0214276778
##  [556]  0.9092917047  0.1273278149  0.2250563249  0.1222411806  0.4198156460
##  [561]  1.4629071862  0.3489650227  0.2143420084 -0.7027569637  0.2496753572
##  [566]  0.0144109898  1.1899353272  0.3009356945  0.5079693164  0.2162350470
##  [571] -0.2771241141  0.2888614428  0.1065641625 -0.0016871091  0.0581893963
##  [576]  0.9699654254 -0.0225780559 -0.0598538244  1.1870719326  0.5436213624
##  [581] -0.6837963045  0.1871068290  1.1727941745  0.5197184904 -0.2065165763
##  [586]  0.3699510656  0.0281659842  0.0538063207  0.8303505575 -0.3401556813
##  [591]  0.5028159961  0.7696278169  0.3190979257  0.3322730675  0.4489981169
##  [596]  0.2144875136  1.0423759438  1.2560818060 -0.4192135638 -0.3825296497
##  [601]  0.0825017184  0.0623434426  0.1346219544 -0.4288400171  0.3946488482
##  [606]  0.3327672921 -0.2812667205  1.1487811682 -0.2014175742  0.0624193478
##  [611]  0.5069679645  0.0922160363 -0.2349000235 -0.1722647066  0.6028025756
##  [616]  0.3700854765  0.3345667145  0.9369820737  0.4806013201  0.8651250368
##  [621] -0.2324077128  0.9337013464  0.0022737188  0.9045446712 -0.0310056270
##  [626]  0.1261879072 -0.7966000888  0.3420052123 -0.2159017488  0.3808045484
##  [631]  0.1639567696  0.4310673850  0.1323173041 -0.3310942252 -0.8296047258
##  [636]  0.2618988793 -0.4374119948  0.4126961584 -0.4332839465 -0.5838908201
##  [641]  1.6981900674 -0.4400268263  0.1008020357  0.2013586143  0.5059498853
##  [646]  0.4540141057 -0.8241426245 -0.6135479280 -0.2960032340  0.8438760518
##  [651]  0.0740452279 -0.3412017914  0.1896976881 -0.3395023379  0.1482374566
##  [656] -0.2546309507 -0.7244871643  0.4474278720 -0.7788496122  0.4730573212
##  [661]  0.0440308139  0.7959449054  0.7861788920  0.0467360682 -0.4102149917
##  [666]  0.4000790963 -0.1942718477 -0.4042023177  0.7035314090  0.3138600703
##  [671] -0.6851806146  0.9200928789  0.0022290240 -0.3530108329  0.0463395202
##  [676]  0.4081113757  0.7826003338 -1.2324079348  0.4523251451  0.4933418589
##  [681] -0.0266264501  0.0347667723 -0.2232068665  0.1705228514  0.8807624021
##  [686]  0.2742877153  0.3448399765 -0.3543064347 -0.3712327177 -0.1644535835
##  [691]  0.9465071686  0.0132273540 -0.0177019957 -0.4767755649  0.6957768041
##  [696]  0.0415264429  0.2334988935 -0.0286049278 -0.2080375034  0.2769375208
##  [701] -0.2208591771  0.3583115175  0.4544450784  0.1422448704 -0.7102149695
##  [706]  1.0342977557 -0.0135001444  0.8483691300  0.8010923768  0.3883308700
##  [711] -0.7245880541  0.3541012206  1.9468628009  0.2170438614  0.5142024677
##  [716] -0.7855304279 -1.3811455947  1.3074710154 -0.0791839630 -1.1192753944
##  [721] -0.1760466965  0.4255673765  0.5225382085  0.2274130475  0.3476441082
##  [726]  0.3396442183  0.2632917473  0.8757177815  0.6129035542 -1.2815684192
##  [731] -0.2045575340  0.8837454140  0.0677611306  1.7062476489  1.2389808326
##  [736] -0.1433426893  0.5652044521  0.3358012996 -0.0485401691  0.3039106978
##  [741]  0.4887157624 -0.3428856144  0.4725439217  0.7353947687  0.8209086787
##  [746]  1.4398190971  0.9289349873 -1.5157839774  0.5951422733 -0.4184015204
##  [751] -0.3397011213  0.6247282174  0.3258737359  1.3060418159  0.6272567505
##  [756] -0.5851115133  0.5193130817  0.0107940064 -0.2531995739 -0.8984524029
##  [761] -0.2507613484  0.1015772240  0.4946697237 -0.6707601859 -0.3435861266
##  [766]  1.0328232212 -0.4492756259 -0.2577172060  0.5054347762 -1.3689365613
##  [771]  1.7569799032  0.6202562451 -0.1106863784  0.8315526928  0.4207190196
##  [776]  0.3366910217  0.2618797886  0.2529386065  0.8716799267  0.4662193258
##  [781] -0.3467062130 -0.3380278482 -0.8403041622  0.0562630038 -0.0228401512
##  [786]  0.1872222976  0.2300433847 -0.0458663078  0.5269271876  0.3099341229
##  [791]  0.0788633393  0.4252124935  0.0703459093 -0.2504467070  0.1565519960
##  [796]  0.4186659079  0.1218472381  0.0440568947 -0.1137985046 -0.8192191379
##  [801]  0.1004504477  0.2263759638 -0.7279712797  0.1080882493  0.4860394631
##  [806] -0.2152984964  0.5545544797  0.2635877514  0.7537033915 -0.1478598173
##  [811]  0.3548779327  0.0847312859 -0.8484894768 -0.0455787124 -0.1483631531
##  [816] -0.3149515845  0.5052702719 -0.3230264647 -0.3301555258 -0.2607094039
##  [821]  0.0644847372  0.2345801218 -0.0904295185 -0.3155465190  0.2252452936
##  [826] -0.4192256861  0.0966800974  0.4510582457  0.5096285771  0.0237085144
##  [831]  0.9260987934  0.2748786552  0.0610328674  0.4749333152  0.8686210148
##  [836]  0.0344108381 -0.1150873879  0.0168047978  0.5177978168  0.2603695086
##  [841] -0.3095198931  0.2983994183 -0.2563031278  0.5427618272 -0.6488702365
##  [846] -0.0183502121  0.5763723427 -0.3547182472  1.5185516751  0.4293206689
##  [851] -0.2676827009  0.1905734064  0.4586370417 -0.3920032476 -1.3667567143
##  [856]  1.3620863632 -0.7393223453 -0.3459376672 -0.3975227801  0.1907001967
##  [861]  0.5728160817 -0.7948318767 -0.2122148167 -0.4266898862 -0.0629104312
##  [866] -0.0615205928 -0.0602340022  0.2076697386  0.0343317845  0.0308201530
##  [871]  0.1479503604 -0.0701457204  0.4579212903 -0.4382259404  0.1375710510
##  [876]  0.4017355755  0.2778042436 -0.2048814945  0.2674514941 -0.0434520160
##  [881]  0.3446084981 -1.2084046523 -0.8066383826  0.4048027377 -1.2684224351
##  [886] -1.1616906294  0.3402263982  0.0235771132  0.0541505789 -0.0583409991
##  [891]  0.5456985085  0.0182726528 -0.7388717130  0.0212598800  1.3498298731
##  [896]  0.2662357891 -0.2652372994  0.3208081351  0.7586898920 -0.2775897697
##  [901] -1.2585725127  0.2513769440  0.1021137992  1.0863759419  0.3338261541
##  [906]  0.0105517160  1.2454895188  0.3487794410  0.1353968995  0.1011348766
##  [911] -0.3652554570 -0.7893745008  0.5067174427  0.3886986939 -0.1866687467
##  [916]  0.5294006268  0.3495109462  0.8386283843 -0.2245882505 -0.0200995047
##  [921] -0.0518804613  1.3857193270  0.3880389987 -0.4000195932 -0.1333931086
##  [926]  0.7234428656 -0.6054729765 -0.3417551070 -0.1225299806  0.3670611478
##  [931]  0.9219124569 -0.1573221230  0.9795802911  0.3360035175  0.1650870288
##  [936]  0.8984258680  0.3934219255  0.6143395124  0.5950344226  0.8607423795
##  [941]  0.0737375809  0.2425924706  0.2330400424  0.5681084902 -0.7999049923
##  [946]  1.3484059386  0.0985882152  0.2168172268  0.0232764359  0.4625191987
##  [951]  0.4918931600  0.3203804980 -0.2209332252 -0.0301088343 -0.8434499159
##  [956] -0.4356532820  0.5254276630  0.5275286099  0.1845500243  0.9657555848
##  [961]  0.5640837015 -0.1801577558  0.3400692949  0.4088755792  0.4655728774
##  [966]  0.5967963218 -0.2342282514  0.4255233179 -0.0088205066  0.2854464887
##  [971] -0.8215869795  0.3077722996 -0.3526263913  0.0044851405  0.0239750978
##  [976]  0.2290024390  0.2700252152  0.4857822081 -0.2051296634  0.4156508991
##  [981]  0.5733460387  0.4368854630  0.6862051102  0.0387495217  0.8597590105
##  [986]  0.4418920118  0.4485295054 -0.0255976073  0.4086403377  0.0889672738
##  [991]  0.2748622213  0.8339733922  0.3825628126 -1.1439706837  0.4614999541
##  [996]  0.2269209823  0.6295344951 -0.3640624544  0.4091822380  0.3082555428
## 
## $func.thetastar
## NULL
## 
## $jack.boot.val
## NULL
## 
## $jack.boot.se
## NULL
## 
## $call
## bootstrap(x = original.data, nboot = 1000, theta = skewness)
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
hist(results2$thetastar,breaks=30,border="purple",add=T,density=20,col="purple",freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-23-1.png" width="672" />

What would have happened if we would have fit a normal distribution instead of a gamma distribution?


```r
fit2<-fitdistr(original.data,dnorm,start=list(mean=1,sd=1))
```

```
## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
fit2
```

```
##       mean          sd    
##   0.70076041   0.29599552 
##  (0.09360200) (0.06618299)
```

```r
results.norm<-c()
for (i in 1:1000)
  {
  x.star<-rnorm(length(original.data),mean=fit2$estimate[1],sd=fit2$estimate[2])
  results.norm<-c(results.norm,skewness(x.star))
  }
head(results.norm)
```

```
## [1] -0.5092193  0.1777011  0.0509649 -0.7838533 -1.0032547  0.6980294
```

```r
hist(results,breaks=30,col="pink",ylim=c(0,1),freq=F)
hist(results.norm,breaks=30,col="lightgreen",freq=F,add=T)
hist(results2$thetastar,breaks=30,border="purple",add=T,density=20,col="purple",freq=F)
```

<img src="Week-2-lab_files/figure-html/unnamed-chunk-24-1.png" width="672" />

All three methods (two parametric and one non-parametric) really do give different distributions for the bootstrapped statistic, so the choice of which method is best depends a lot on the situation, how much data you have, and what you might already know about the underlying distribution.

Jackknifing is just as easy at bootstrapping. Here we will do a trivial example for illustration. We will write a little function for the mean even though you could put the function in directly with 'jackknife(x,mean)'


```r
theta<-function(x)
  {
  mean(x)
  }
x<-seq(0,9,by=1)
results<-jackknife(x=x,theta=theta)
results
```

```
## $jack.se
## [1] 0.9574271
## 
## $jack.bias
## [1] 0
## 
## $jack.values
##  [1] 5.000000 4.888889 4.777778 4.666667 4.555556 4.444444 4.333333 4.222222
##  [9] 4.111111 4.000000
## 
## $call
## jackknife(x = x, theta = theta)
```

**<span style="color: green;">Checkpoint #7: Why do we not have to tell the 'jackknife' function how many replicates to do?</span>**

Let's compare this with what we would have obtained from bootstrapping


```r
results2<-bootstrap(x,1000,theta)
mean(results2$thetastar)-mean(x)  #this is the bias
```

```
## [1] -0.0282
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9246187
```


Everything we have done to this point used the R package 'bootstrap' - now lets compare that with the R package 'boot'. To avoid any confusion (a.k.a. masking) between the two packages, I recommend detaching the bootstrap package from the workspace with


```r
detach("package:bootstrap")
```


The 'boot' package is now recommended over the 'bootstrap' package, but they give the same answers and to some extent it is personal preference which one prefers to use.

We will still use the mean as the statistic of interest, but we will have to write a new function for it because the syntax of the 'boot' package is slightly different:


```r
library(boot)
theta<-function(x,index)
  {
  mean(x[index])
  }
boot(x,theta,R=999)
```

```
## 
## ORDINARY NONPARAMETRIC BOOTSTRAP
## 
## 
## Call:
## boot(data = x, statistic = theta, R = 999)
## 
## 
## Bootstrap Statistics :
##     original      bias    std. error
## t1*      4.5 0.002502503   0.9047214
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 3 4 5 9 
## 1 5 1 3
```

```r
xmeans<-vector(length=1000)
for (i in 1:1000)
  {
  xmeans[i]<-mean(sample(x,replace=T))
  }
mean(x)
```

```
## [1] 4.5
```

```r
bias<-mean(xmeans)-mean(x)
se.boot<-sd(xmeans)
bias
```

```
## [1] -0.0284
```

```r
se.boot
```

```
## [1] 0.8934045
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

