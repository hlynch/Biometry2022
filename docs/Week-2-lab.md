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
## 1 2 3 5 6 
## 3 1 1 1 4
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
## [1] 0.0129
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
## [1] 2.747273
```

```r
UL.boot
```

```
## [1] 6.278527
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.8   6.3
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
##    [1] 4.9 4.8 5.7 2.1 5.3 5.5 4.5 4.6 3.6 5.4 3.6 4.3 4.7 4.8 4.4 2.7 6.3 4.8
##   [19] 4.1 5.1 4.7 3.4 3.8 4.1 4.3 5.3 4.2 5.1 4.6 5.0 4.3 4.2 5.6 4.4 5.2 3.6
##   [37] 6.0 3.8 4.8 5.2 2.9 3.6 5.0 3.0 5.8 5.3 4.2 6.9 4.2 5.0 1.6 6.6 5.1 4.7
##   [55] 5.3 5.7 5.3 3.2 3.7 4.8 4.0 4.5 4.4 2.9 5.4 5.5 4.3 5.7 4.6 3.3 4.3 4.4
##   [73] 4.7 3.5 5.6 4.1 3.4 4.3 6.9 5.5 4.9 3.5 5.1 3.9 3.4 6.6 3.4 3.0 3.2 5.0
##   [91] 3.6 4.6 5.6 3.6 4.7 6.0 3.9 3.6 4.9 5.7 3.1 3.7 3.8 3.6 5.7 3.5 3.4 4.4
##  [109] 5.0 5.0 4.6 3.3 5.8 3.3 4.5 2.6 3.7 4.1 4.6 4.7 4.1 7.1 5.6 4.7 3.2 5.2
##  [127] 4.3 4.8 4.5 3.9 5.6 3.5 5.3 4.9 3.0 5.3 4.2 4.7 4.0 5.0 3.7 4.4 4.9 5.4
##  [145] 5.0 3.7 5.0 4.0 5.6 3.9 3.2 4.5 4.4 5.4 5.2 5.1 4.8 4.9 3.5 3.6 4.7 4.2
##  [163] 4.2 4.4 4.9 4.0 4.7 5.3 4.3 2.9 1.6 5.2 5.5 5.5 4.2 4.5 3.0 3.9 5.9 3.4
##  [181] 6.9 2.9 5.3 4.5 5.6 5.8 4.6 3.9 5.8 5.1 5.6 4.5 4.2 3.6 3.3 5.4 4.3 6.0
##  [199] 4.3 6.1 4.8 3.9 3.8 5.0 3.2 3.3 4.5 4.9 3.5 4.1 4.5 4.0 4.3 5.8 4.6 3.5
##  [217] 4.3 4.7 5.5 4.1 6.3 5.9 5.6 4.6 5.6 5.0 3.5 5.7 4.6 3.5 4.4 5.1 5.0 3.7
##  [235] 4.5 3.7 4.4 4.7 4.4 3.8 5.4 3.6 3.8 5.6 4.4 4.4 4.2 5.0 4.1 3.4 5.8 3.9
##  [253] 2.5 4.4 4.0 4.5 5.2 3.9 4.9 2.4 3.6 4.1 3.8 5.4 5.2 5.3 3.3 4.0 4.2 2.9
##  [271] 5.0 5.6 4.6 5.2 3.2 5.2 5.4 4.3 6.1 5.3 4.8 5.4 4.7 2.6 4.0 4.0 5.0 6.4
##  [289] 4.3 4.4 3.7 4.2 2.9 3.9 5.0 4.8 4.1 4.0 3.5 6.0 4.1 5.5 4.1 4.2 4.0 5.8
##  [307] 4.4 5.9 4.5 4.5 4.8 5.0 3.9 4.6 5.1 6.9 3.9 5.9 4.7 4.4 3.9 5.3 3.3 4.8
##  [325] 3.9 4.7 5.9 4.1 4.6 4.1 5.4 5.3 3.9 4.5 4.5 5.7 3.4 5.5 3.2 4.5 5.4 5.1
##  [343] 4.2 6.1 3.7 5.0 4.1 4.0 4.4 3.4 4.5 3.6 5.3 5.4 3.8 4.8 5.3 3.0 4.0 3.4
##  [361] 2.2 3.6 5.1 4.9 5.4 3.7 4.1 6.4 6.3 4.8 6.1 5.5 3.7 5.8 4.7 3.7 4.4 5.1
##  [379] 3.6 4.5 4.3 3.5 3.9 4.5 4.0 3.8 3.4 5.5 4.8 4.6 3.1 4.6 4.9 3.3 4.3 4.1
##  [397] 3.9 2.9 3.7 5.1 6.3 4.1 5.0 4.2 3.4 4.5 5.3 3.9 5.9 3.6 6.4 4.4 3.6 6.2
##  [415] 5.3 4.3 4.6 6.1 5.2 4.3 4.9 3.2 3.9 4.4 3.8 5.2 4.4 4.2 5.7 4.7 4.6 2.7
##  [433] 5.6 6.0 3.9 3.9 4.6 5.2 5.3 3.9 4.8 3.8 5.7 4.2 5.0 3.8 4.4 4.7 4.1 5.6
##  [451] 4.7 4.9 4.7 3.1 4.6 4.3 4.8 4.1 6.0 5.1 3.9 6.3 5.1 3.5 3.8 3.7 6.8 5.2
##  [469] 3.8 3.7 3.9 4.9 4.3 3.0 3.5 4.6 3.9 3.8 5.0 4.8 4.8 5.4 4.4 3.3 5.8 4.4
##  [487] 4.3 5.0 6.3 4.0 4.9 5.7 3.2 4.6 3.6 4.4 5.0 3.6 4.0 5.4 5.3 5.1 5.1 4.3
##  [505] 6.2 4.0 5.1 5.1 5.2 5.8 4.4 4.1 5.6 3.1 4.6 5.4 5.6 4.4 5.1 3.4 4.4 3.6
##  [523] 4.7 5.3 3.5 4.5 4.4 4.9 3.4 4.9 5.0 5.0 3.8 5.1 5.5 2.1 6.0 3.9 5.7 5.1
##  [541] 4.2 4.3 4.5 4.8 3.5 5.3 6.6 6.4 5.6 4.1 5.3 4.0 3.8 4.2 4.1 3.8 4.1 3.1
##  [559] 3.6 4.2 4.6 5.7 4.8 5.1 3.0 6.4 4.1 5.0 3.4 2.9 3.1 4.3 6.6 2.8 2.8 4.3
##  [577] 4.0 4.5 2.6 5.2 4.9 4.9 4.3 3.6 4.9 3.3 4.2 5.0 4.7 5.8 4.8 3.5 5.7 3.0
##  [595] 5.5 3.5 2.8 3.9 3.6 4.5 4.9 2.8 4.6 4.6 4.0 3.7 4.7 3.7 3.6 4.6 5.8 3.9
##  [613] 6.1 3.3 5.4 5.8 3.9 4.1 4.1 5.3 4.2 3.4 6.5 3.7 5.4 4.9 3.2 4.1 4.5 4.5
##  [631] 3.6 4.4 4.0 4.5 3.7 3.7 4.5 5.9 4.4 4.0 3.6 4.2 4.9 4.9 5.5 3.8 6.2 4.1
##  [649] 5.3 3.1 5.6 3.3 3.9 4.4 2.8 4.0 4.3 2.6 4.9 2.7 4.1 4.5 6.2 5.4 4.1 3.9
##  [667] 6.4 4.4 4.8 5.6 3.8 6.2 3.0 5.8 4.4 3.4 5.3 4.2 4.1 4.7 4.6 6.3 4.2 5.5
##  [685] 6.0 5.6 4.1 4.0 4.5 4.2 4.5 5.3 4.6 5.2 5.3 4.8 3.9 4.2 2.3 4.6 5.3 5.9
##  [703] 4.0 6.2 4.6 3.5 5.9 3.2 4.6 3.2 4.7 4.5 3.3 4.8 3.7 4.1 5.2 2.4 5.0 3.0
##  [721] 5.5 3.8 5.0 3.6 3.7 6.0 3.7 3.9 5.2 4.4 3.3 3.7 5.9 4.4 3.5 4.4 3.7 4.4
##  [739] 5.7 4.3 2.7 3.5 3.9 5.2 3.9 3.6 4.9 6.2 4.5 2.8 3.6 5.5 5.8 5.2 4.7 5.7
##  [757] 4.4 6.8 5.0 4.3 5.9 5.4 5.9 4.2 4.9 4.4 5.2 3.2 5.3 4.9 4.1 3.1 3.9 5.4
##  [775] 5.1 5.4 4.9 5.4 2.6 4.4 5.7 3.3 6.1 4.4 4.5 4.9 4.8 4.0 5.3 4.5 3.7 5.6
##  [793] 3.4 4.0 4.1 6.1 3.9 5.7 4.1 5.1 5.1 3.9 3.5 4.2 4.0 3.2 3.9 4.1 4.2 5.3
##  [811] 3.4 4.0 3.3 4.2 4.7 3.7 3.2 5.1 3.9 4.9 4.2 3.6 3.3 3.2 2.9 4.6 4.5 3.1
##  [829] 4.4 3.0 2.8 4.6 4.0 3.3 4.7 3.8 3.3 5.1 4.3 5.1 4.5 4.3 5.5 3.0 4.2 2.9
##  [847] 5.9 3.8 3.8 5.8 5.3 5.5 4.6 6.0 4.7 4.3 5.3 4.2 3.7 4.3 5.3 4.7 4.9 2.9
##  [865] 6.2 4.6 4.2 4.3 3.5 2.9 4.8 4.0 3.6 4.1 4.2 4.2 4.0 5.3 5.6 3.1 5.3 5.9
##  [883] 3.5 4.7 3.6 3.3 4.4 4.0 3.7 4.1 5.3 4.7 4.5 5.0 4.7 4.5 2.4 4.1 3.5 4.7
##  [901] 4.5 5.2 4.7 3.1 4.3 4.6 2.1 5.4 5.6 4.7 4.7 3.3 5.4 6.1 4.5 4.4 4.9 5.0
##  [919] 6.1 5.1 4.2 5.5 6.0 4.9 5.5 3.4 3.2 4.7 4.2 4.0 4.8 5.4 5.7 3.1 4.9 4.3
##  [937] 4.6 3.7 5.3 4.1 4.7 5.6 3.4 4.3 5.2 4.2 5.9 5.2 4.4 6.1 3.2 3.3 4.0 3.5
##  [955] 3.4 3.7 3.9 4.7 5.5 5.3 4.5 3.3 5.4 4.5 4.8 5.2 4.7 4.8 4.2 4.7 5.6 5.5
##  [973] 2.6 2.1 3.8 4.8 1.9 4.5 4.0 4.8 4.9 3.6 5.0 4.1 3.1 2.8 5.6 4.9 3.5 3.5
##  [991] 5.8 4.0 3.6 4.8 4.1 3.3 3.5 5.9 5.6 4.2
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
##   2.5%  97.5% 
## 2.8000 6.2025
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
##    [1] 6.3 4.2 5.0 5.8 3.8 5.8 4.1 4.5 5.4 3.3 5.3 4.7 2.9 5.0 3.4 4.3 5.0 4.2
##   [19] 3.9 3.2 5.7 4.0 3.7 4.4 2.9 5.5 4.7 4.0 2.9 5.4 2.4 3.9 5.8 3.4 5.0 5.4
##   [37] 4.7 4.1 4.0 5.4 3.8 3.5 2.6 3.6 5.5 3.7 5.0 5.4 6.0 5.5 3.8 3.9 3.6 5.1
##   [55] 3.8 5.4 3.6 5.3 4.9 2.6 4.4 3.9 4.7 4.0 3.8 3.7 3.2 3.9 4.2 3.7 3.7 5.6
##   [73] 5.4 3.8 3.8 4.8 4.0 4.6 5.4 4.4 4.5 4.6 3.9 3.2 4.4 4.6 4.1 5.2 5.0 5.0
##   [91] 4.8 5.3 5.3 4.8 4.7 4.9 4.2 4.0 4.0 4.5 6.5 4.3 4.0 3.0 6.6 3.5 4.5 3.6
##  [109] 3.0 4.3 5.5 5.0 4.6 4.3 5.7 5.0 5.4 3.8 5.7 5.5 4.5 4.5 2.4 4.8 4.7 5.5
##  [127] 4.7 5.0 4.6 4.9 5.5 4.1 5.7 3.7 7.2 5.5 6.4 2.9 3.9 5.5 4.7 4.9 4.9 4.1
##  [145] 3.6 6.5 4.6 3.6 4.8 4.6 4.6 3.2 4.0 3.5 3.9 5.7 4.7 2.7 3.5 4.2 4.7 5.2
##  [163] 4.8 5.1 3.8 3.8 5.4 4.0 4.2 3.6 4.8 4.4 4.6 3.3 5.5 7.2 5.3 4.8 5.0 5.3
##  [181] 4.1 5.4 3.4 4.6 4.1 4.3 5.7 5.0 3.4 5.6 4.1 2.5 2.9 3.8 3.6 6.7 4.2 4.2
##  [199] 3.2 3.5 2.5 4.7 5.8 5.9 5.5 5.1 5.0 3.4 5.1 5.7 4.6 3.3 5.5 4.2 4.6 3.0
##  [217] 5.0 4.5 3.8 4.7 4.7 5.7 6.0 4.8 4.9 4.7 5.2 3.2 3.8 4.5 4.7 6.0 4.6 4.0
##  [235] 5.2 3.5 4.6 6.5 3.3 4.7 3.8 3.7 4.9 4.8 4.0 4.8 5.4 5.2 4.7 4.6 5.9 2.5
##  [253] 5.1 5.4 4.9 6.4 3.7 5.9 6.1 3.9 5.7 4.7 4.7 5.5 4.2 4.4 4.4 5.1 5.6 4.6
##  [271] 5.9 3.9 4.6 4.1 4.9 5.4 5.5 2.5 4.4 5.4 3.5 5.0 5.7 5.1 6.2 4.4 2.6 4.3
##  [289] 5.1 4.8 4.5 5.4 4.2 4.3 5.0 4.9 4.0 3.7 4.8 4.7 3.1 4.1 5.6 4.5 5.2 3.4
##  [307] 4.7 3.7 2.1 5.5 5.7 2.5 3.5 5.7 4.8 3.3 3.9 6.0 2.7 4.1 3.6 3.0 4.1 4.9
##  [325] 3.9 3.4 4.0 6.4 3.8 4.9 4.1 5.3 4.8 5.4 4.2 4.5 4.8 6.5 6.1 5.7 5.4 4.0
##  [343] 4.9 3.7 4.8 4.1 4.3 3.9 3.5 5.6 2.9 4.3 5.9 4.0 4.8 4.2 3.6 5.3 3.6 5.1
##  [361] 3.9 3.9 3.2 3.5 3.6 4.6 4.5 6.6 5.3 4.0 5.7 2.8 4.3 3.7 4.5 4.6 4.7 5.6
##  [379] 2.4 4.0 4.7 4.3 5.1 5.2 4.9 5.7 6.5 5.0 5.5 4.2 4.8 5.8 4.1 5.6 5.4 3.9
##  [397] 5.5 6.4 4.7 2.9 7.0 3.7 4.5 4.1 5.3 3.6 2.6 4.6 5.3 4.4 3.6 6.3 3.8 5.1
##  [415] 4.8 4.5 4.8 4.2 4.7 4.0 4.1 4.8 3.6 4.0 4.2 4.1 3.9 4.3 4.7 4.1 4.2 6.9
##  [433] 5.9 3.9 4.7 5.7 5.5 3.2 4.0 5.2 5.5 5.6 5.9 5.2 5.9 3.9 4.7 6.1 5.2 3.1
##  [451] 4.1 4.1 5.3 4.6 2.6 6.1 3.1 5.6 5.2 4.6 5.0 4.3 5.0 3.6 5.1 3.7 3.7 5.3
##  [469] 6.1 4.1 4.9 5.5 5.2 4.5 3.9 4.1 4.0 3.5 3.3 3.6 3.3 2.8 5.7 5.2 5.1 4.6
##  [487] 3.8 2.8 4.7 5.6 4.3 4.0 2.9 4.2 4.9 3.8 6.0 3.4 4.0 5.1 4.9 3.6 6.1 3.6
##  [505] 3.3 5.5 3.4 4.7 4.1 2.8 5.7 5.7 4.3 5.6 5.6 3.0 4.2 4.0 5.9 6.0 4.4 5.2
##  [523] 5.3 4.9 3.1 4.2 6.0 4.8 4.3 3.0 4.1 5.0 4.4 3.6 4.2 4.5 4.8 5.3 3.7 6.0
##  [541] 3.6 6.0 3.3 5.8 5.0 4.5 5.2 4.4 3.2 5.6 4.6 4.7 4.6 3.5 5.8 3.4 5.1 4.5
##  [559] 5.0 4.1 4.4 5.8 4.2 5.0 5.1 4.7 3.2 5.3 3.2 5.3 5.4 5.0 3.8 5.6 4.6 4.7
##  [577] 5.8 4.1 3.9 4.3 5.7 3.1 4.6 5.0 5.7 3.4 4.5 5.0 2.8 2.7 5.0 3.2 4.4 2.9
##  [595] 5.8 4.1 3.2 3.6 4.4 3.6 4.3 5.1 4.3 4.2 4.4 5.1 3.9 5.0 5.6 4.8 3.6 5.6
##  [613] 4.4 2.4 4.2 5.1 3.5 4.4 3.1 3.7 5.0 5.2 3.0 3.4 4.9 4.8 5.7 4.8 4.3 5.7
##  [631] 5.7 4.9 5.4 5.2 4.0 5.5 4.9 4.2 3.6 2.2 3.6 5.4 5.4 5.5 3.1 4.4 3.6 3.4
##  [649] 3.2 5.6 4.4 5.1 4.7 5.7 3.3 3.5 3.8 2.7 4.9 5.6 4.3 3.8 4.6 5.3 5.7 3.5
##  [667] 4.6 6.0 3.5 6.1 5.2 4.0 3.0 3.8 3.9 6.2 4.1 4.9 3.5 4.9 4.4 4.1 4.6 6.0
##  [685] 3.9 5.2 4.4 4.3 5.1 3.6 4.9 4.4 2.8 4.2 4.3 4.3 3.6 6.0 4.6 3.3 4.6 5.2
##  [703] 5.3 6.0 3.1 4.6 4.1 5.8 5.0 4.8 5.5 4.0 2.7 3.0 4.8 4.6 4.1 6.3 5.7 3.8
##  [721] 4.4 3.7 6.3 5.0 4.4 4.2 3.2 5.8 5.2 4.2 3.9 3.7 3.3 4.4 3.7 4.8 4.5 3.2
##  [739] 3.0 6.4 6.5 5.3 4.8 3.4 4.8 3.8 4.3 4.6 5.0 5.6 5.7 5.1 4.3 4.3 5.4 4.3
##  [757] 2.3 3.4 4.8 4.4 4.1 6.9 6.1 4.6 6.3 4.0 5.4 4.8 4.3 4.4 4.9 5.1 3.3 3.4
##  [775] 5.7 3.4 4.3 3.9 5.8 5.3 4.8 6.3 4.7 5.8 2.4 3.6 2.8 5.8 3.7 5.4 5.2 5.3
##  [793] 5.8 4.0 5.0 5.6 4.0 4.5 4.0 3.4 4.8 3.8 5.2 5.3 4.2 4.5 3.9 4.6 2.8 5.7
##  [811] 4.6 3.5 4.0 5.7 3.6 5.2 3.7 5.0 4.2 2.9 3.7 5.3 4.4 2.8 5.3 4.1 5.3 5.1
##  [829] 4.2 3.3 4.5 5.4 4.4 4.2 4.5 4.2 5.6 3.7 3.9 2.4 5.4 5.1 4.2 4.3 4.7 5.3
##  [847] 4.1 3.1 5.1 5.2 5.7 6.2 4.9 5.6 7.2 3.7 4.1 4.3 3.8 4.9 5.2 6.4 4.2 4.4
##  [865] 3.8 4.9 5.4 6.8 5.3 3.7 4.8 3.7 4.5 6.6 4.6 4.6 2.6 4.5 3.2 4.3 5.4 5.2
##  [883] 6.1 3.8 4.3 5.2 4.3 4.3 5.0 4.3 4.2 3.8 4.5 4.0 2.7 5.0 3.4 4.4 5.5 6.5
##  [901] 4.4 5.7 5.6 3.8 4.1 4.1 5.3 6.8 4.9 3.8 4.7 3.8 4.6 4.7 4.0 4.6 4.4 4.8
##  [919] 4.1 3.7 2.7 5.1 5.4 5.1 6.0 5.5 3.7 2.7 5.9 6.0 4.7 4.6 5.7 3.4 3.9 5.0
##  [937] 5.0 4.0 2.8 3.4 4.9 5.1 5.4 5.3 3.4 4.1 6.6 2.1 4.9 5.0 4.6 3.2 4.6 5.2
##  [955] 4.9 5.4 4.3 5.7 5.9 5.9 4.1 3.8 3.4 5.8 4.7 2.7 3.5 4.8 4.3 3.9 4.1 3.3
##  [973] 6.0 4.3 3.5 4.1 4.8 5.0 3.8 3.9 6.0 6.2 3.3 4.1 4.4 5.0 3.9 5.9 4.6 5.3
##  [991] 5.1 2.2 5.4 3.1 2.2 4.9 3.9 4.1 4.0 6.0
## 
## $func.thetastar
## [1] 0.0334
## 
## $jack.boot.val
##  [1]  0.52697947  0.40762943  0.35117647  0.19501466  0.16279762  0.01620879
##  [7] -0.16118598 -0.24434783 -0.42060440 -0.53726115
## 
## $jack.boot.se
## [1] 1.028107
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
##    [1] 6.1 4.9 5.1 4.9 6.4 5.2 4.7 2.2 4.6 4.1 4.0 5.1 5.3 4.0 4.0 4.7 3.8 5.9
##   [19] 4.0 5.7 3.4 3.9 5.8 3.2 4.2 6.1 5.1 6.0 4.9 5.2 2.7 6.1 4.1 5.9 4.3 5.9
##   [37] 4.9 4.1 4.1 5.5 2.8 5.7 4.1 3.3 5.3 6.0 5.3 4.7 5.1 5.4 4.6 4.3 2.7 5.9
##   [55] 4.0 4.2 5.9 4.4 4.9 3.5 3.3 3.8 3.9 4.7 5.0 3.8 5.0 4.6 4.6 5.5 4.3 4.3
##   [73] 4.8 2.5 2.4 5.3 5.9 4.3 1.6 4.5 3.3 3.6 3.6 4.9 4.8 5.3 2.7 3.7 4.2 5.1
##   [91] 4.6 3.3 2.7 5.4 3.9 5.3 5.8 5.0 4.6 3.7 5.9 4.1 5.2 5.8 2.8 6.7 4.9 4.0
##  [109] 3.8 3.8 2.9 5.7 5.5 4.5 4.5 5.3 4.9 2.4 5.2 3.2 3.8 4.6 3.3 5.1 4.2 4.0
##  [127] 5.8 4.0 4.4 3.1 4.7 5.1 4.8 4.3 6.3 5.6 3.3 4.4 5.5 5.9 3.4 4.3 5.3 4.6
##  [145] 5.3 4.8 4.8 6.3 3.8 3.4 3.6 5.0 5.8 5.2 3.9 5.6 6.0 3.3 4.9 4.1 6.1 4.0
##  [163] 5.8 5.7 4.1 3.8 5.6 5.3 5.0 4.2 5.7 5.5 3.6 5.0 5.2 5.4 3.8 3.9 4.5 4.3
##  [181] 5.1 6.2 6.4 4.2 5.3 4.8 4.8 5.7 4.0 4.9 2.9 4.9 4.8 4.9 3.5 4.4 6.1 3.7
##  [199] 3.5 4.3 3.2 4.8 3.6 4.9 4.6 2.8 5.0 3.9 3.5 5.1 4.3 5.0 4.3 6.0 4.8 4.9
##  [217] 3.9 5.0 4.2 4.3 4.4 5.2 4.8 3.4 3.7 4.8 4.0 3.8 4.6 5.4 5.2 4.0 5.7 5.3
##  [235] 3.9 4.4 4.5 5.2 5.3 3.0 5.3 4.8 5.1 5.0 5.0 4.1 5.3 5.2 5.5 4.3 3.0 5.1
##  [253] 4.9 4.9 5.3 3.9 4.6 3.6 3.1 3.8 4.9 5.3 4.2 5.6 4.3 4.3 5.8 3.3 4.8 4.3
##  [271] 4.8 4.2 2.9 5.3 4.0 7.0 4.1 4.8 4.5 5.9 3.6 3.8 6.3 3.9 4.4 4.0 4.2 4.3
##  [289] 5.3 3.4 3.3 4.9 3.9 4.2 5.6 5.2 4.8 6.1 4.2 4.8 4.0 3.7 4.6 3.4 5.3 6.3
##  [307] 4.5 4.5 4.2 3.6 4.6 3.4 4.4 4.3 2.6 4.1 6.2 4.5 5.1 5.9 5.8 5.5 4.9 4.6
##  [325] 5.4 4.2 2.7 3.4 4.1 4.8 4.3 3.3 3.5 5.3 3.3 3.7 4.8 4.5 4.6 5.6 4.5 5.2
##  [343] 5.2 2.9 5.4 4.2 3.5 4.3 3.6 4.9 5.0 3.4 3.1 4.2 6.2 5.2 2.6 5.2 4.6 4.1
##  [361] 6.3 4.3 4.7 3.4 4.5 6.0 5.6 4.7 4.9 4.9 5.4 5.4 3.6 5.2 3.1 3.9 3.9 3.4
##  [379] 5.2 5.1 4.1 4.3 4.8 4.5 4.5 3.7 4.4 3.9 3.3 5.9 5.0 5.0 4.4 4.9 4.3 4.6
##  [397] 5.4 5.0 3.3 6.2 4.4 4.7 5.4 4.9 4.2 3.0 4.8 2.7 5.0 5.1 5.0 5.6 4.8 4.1
##  [415] 3.4 4.0 4.9 4.0 4.4 3.1 4.5 4.7 5.2 4.2 5.4 4.4 5.1 5.7 4.4 4.5 3.9 6.3
##  [433] 4.9 4.1 5.7 6.0 4.2 4.0 5.2 4.2 5.4 4.5 3.9 5.5 4.8 4.7 2.5 4.2 2.7 3.8
##  [451] 4.4 2.7 5.0 6.0 5.0 3.6 3.9 4.7 5.0 4.2 5.3 4.5 4.6 6.0 4.0 4.3 3.2 2.8
##  [469] 4.7 5.2 4.6 2.9 3.2 4.7 4.2 5.3 5.1 5.6 4.6 4.8 5.2 2.5 5.6 5.5 3.8 4.0
##  [487] 4.4 4.3 4.2 3.3 1.2 5.4 5.4 3.5 3.5 3.9 4.3 4.1 3.9 4.4 5.6 5.8 5.5 4.7
##  [505] 3.5 5.1 4.9 6.2 3.9 4.0 5.6 4.6 4.5 5.1 5.1 3.2 4.2 2.6 3.3 3.2 4.8 4.2
##  [523] 4.7 3.7 5.5 4.9 3.3 4.8 4.7 4.4 6.1 4.8 5.0 3.5 3.3 5.2 3.3 3.9 4.4 4.5
##  [541] 3.9 3.8 4.2 4.0 3.9 2.9 4.6 5.7 5.4 5.6 2.9 3.3 3.5 6.6 5.1 3.1 3.8 3.7
##  [559] 5.3 4.1 4.5 5.7 4.1 2.7 2.7 4.3 4.0 4.4 4.1 3.1 4.0 4.7 4.2 5.4 4.3 3.5
##  [577] 2.8 4.4 3.5 4.1 2.5 5.2 3.2 5.3 4.7 4.8 3.9 2.8 5.7 4.8 4.7 4.5 3.7 3.6
##  [595] 4.3 3.7 5.5 5.1 4.8 5.5 4.9 4.7 4.1 4.8 5.3 4.5 4.9 4.5 4.6 3.7 4.1 3.7
##  [613] 6.0 4.7 5.0 3.7 4.2 5.7 4.9 4.3 4.8 3.7 4.5 4.0 4.4 5.0 3.6 4.6 3.4 3.7
##  [631] 5.8 3.8 4.7 4.8 3.9 5.7 7.0 5.5 3.4 4.5 5.5 6.7 4.5 3.4 5.2 5.4 4.0 3.9
##  [649] 5.2 4.6 3.0 5.5 3.2 5.3 4.7 4.0 4.6 1.8 4.7 5.1 4.3 5.3 4.2 3.8 5.2 3.3
##  [667] 4.4 4.2 3.7 4.8 3.6 3.9 3.8 4.1 5.5 4.8 2.5 3.5 4.0 5.1 5.3 3.6 4.5 5.3
##  [685] 3.7 5.2 4.2 3.4 4.5 4.6 4.1 4.5 3.6 5.6 3.4 4.3 4.8 5.1 5.7 5.4 4.2 4.3
##  [703] 5.3 4.5 4.0 5.7 3.7 1.7 5.5 3.8 4.4 5.4 3.9 5.8 5.4 3.3 4.8 4.2 4.4 4.1
##  [721] 4.9 4.6 3.7 4.3 4.4 5.7 4.0 3.8 2.9 4.5 3.1 5.1 3.9 4.9 4.9 4.0 4.6 4.6
##  [739] 4.2 5.5 5.7 4.7 4.5 5.6 5.0 5.6 3.6 4.2 1.6 4.8 5.4 5.8 4.8 3.5 5.2 3.5
##  [757] 3.9 4.3 4.7 3.2 4.7 5.0 5.1 4.4 4.6 3.1 2.7 4.9 4.7 4.6 3.5 5.4 4.9 3.7
##  [775] 4.6 3.3 5.0 6.6 4.3 5.0 5.2 5.8 4.3 7.1 6.5 3.8 4.5 3.8 2.7 4.1 6.1 4.4
##  [793] 4.5 4.1 3.9 3.1 3.1 5.4 6.5 5.1 3.0 4.6 4.3 3.6 3.8 4.6 3.3 4.7 4.3 3.8
##  [811] 2.7 5.0 6.4 4.6 5.0 3.1 5.4 5.3 4.7 6.0 6.1 2.8 4.8 3.3 4.2 4.3 6.0 3.9
##  [829] 5.4 4.1 4.8 5.1 5.4 3.9 4.3 5.7 4.2 4.7 4.7 3.5 3.6 3.1 4.6 4.7 5.9 2.6
##  [847] 3.9 5.6 4.2 4.2 4.9 3.4 3.5 3.4 6.0 5.3 4.1 3.0 5.6 4.8 4.6 4.3 4.9 5.0
##  [865] 3.2 3.8 2.8 3.8 3.8 4.4 4.6 4.9 3.9 4.0 4.6 5.4 5.3 5.5 5.6 3.6 4.3 4.2
##  [883] 5.3 3.2 5.3 3.0 4.3 4.2 2.8 4.1 4.2 3.5 4.0 4.8 7.5 3.7 4.3 5.5 3.6 2.5
##  [901] 3.4 4.3 5.1 5.2 4.1 4.9 5.2 4.3 5.2 5.2 5.1 4.3 3.3 4.7 5.5 5.4 4.6 4.1
##  [919] 5.0 5.4 5.5 5.6 4.1 5.3 6.0 5.8 5.9 3.8 3.6 5.0 4.0 4.0 4.9 3.9 4.9 6.4
##  [937] 2.2 5.1 3.9 3.2 4.3 3.3 3.7 4.6 5.0 5.7 4.2 5.0 5.4 4.0 4.9 5.1 5.3 5.6
##  [955] 5.3 3.9 4.7 4.7 5.1 5.0 4.9 4.2 4.8 6.7 4.9 2.9 3.8 3.7 6.1 4.1 4.1 3.8
##  [973] 6.0 3.3 6.0 4.4 4.0 5.6 2.8 4.0 4.3 5.1 3.2 5.4 5.3 3.5 1.6 4.2 6.1 3.7
##  [991] 5.1 5.3 4.6 3.7 5.0 4.5 4.1 5.4 4.5 5.1
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.5 5.4 5.3 5.2 5.1 5.0 4.9 4.9 4.7 4.3
## 
## $jack.boot.se
## [1] 1.013361
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
## [1] 0.2732544
```

What is skew? Skew describes how assymetric a distribution is. A distribution with a positive skew is a distribution that is "slumped over" to the right, with a right tail that is longer than the left tail. Alternatively, a distribution with negative skew has a longer left tail. Here we are just using it for illustration, as a property of a distribution that you may want to estimate using your data.

Lets use 'fitdistr' to fit a gamma distribution to these data. This function is an extremely handy function that takes in your data, the name of the distribution you are fitting, and some starting values (for the estimation optimizer under the hood), and it will return the parameter values (and their standard errors). We will learn in a couple weeks how R is doing this, but for now we will just use it out of the box. (Because we generated the data, we happen to know that the data are gamma distributed. In general we wouldn't know that, and we will see in a second that our assumption about the shape of the data really does make a difference.)


```r
library(MASS)
fit<-fitdistr(original.data,dgamma,list(shape=1,rate=1))
```

```
## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
# fit<-fitdistr(original.data,"gamma")
# The second version would also work.
fit
```

```
##      shape       rate   
##    5.381921   11.057288 
##  ( 2.336084) ( 5.030641)
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
## [1] 0.8253547 0.3867784 0.6935756 0.6556433 0.2848209 1.0172307
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
##    [1]  1.0755166744  0.7455438776  0.5685224228  0.4624679544  0.2385984377
##    [6]  0.0632289226  0.5306446750  0.5466388844  0.6042784951 -0.3013318407
##   [11]  0.6545971326 -0.7574724711  0.0007110177  0.0509734126  0.5084144091
##   [16] -0.8229844940  0.5609302835 -0.0519235428  0.3256581517  0.8316936228
##   [21]  0.6335700306 -0.4258007074  0.4192898794  0.7867672061  0.2102853948
##   [26]  0.1769538703 -0.1738971409 -0.0849356757  0.5899098108  0.2110485008
##   [31]  0.3385519933  0.8102814517  0.7958994755  0.0858864401  0.8699898679
##   [36] -0.4711619632  0.7382783287 -0.6885039267 -0.4012979443  0.1341308506
##   [41]  0.6900202342  0.1873462990 -0.9798909011  0.4669153653  1.8218000507
##   [46] -0.1639068783  0.3886339505 -0.3654868642  0.5093619822  0.3938851630
##   [51]  0.1390789486  0.5304309797  1.1412855318  0.2987956140  0.2751316025
##   [56]  1.0004637355  0.1309251666 -0.0171357779  1.0139986000 -0.5331786159
##   [61]  0.6784411255  0.2088415753  0.3615880056  0.5620619379  0.5695667259
##   [66]  0.3961967504  0.5131038750 -0.6542957945 -0.0001003559 -1.7229797967
##   [71]  0.1350755752  0.4330761486  0.3047377348  0.6742797075  0.7699883867
##   [76]  0.5209832722  0.5819360695  0.7962396199 -0.1446969019  0.7957462913
##   [81]  0.3172443256 -0.1514450496  0.0530101466  0.2842884381  0.2263610306
##   [86]  0.1304605106 -0.1009572605  0.2827265735  2.1036374878 -0.1334034631
##   [91]  0.5303505736  0.1132818160  0.7249121676  0.4102040606  0.2942702332
##   [96] -0.3066474255 -0.3984561122  0.5006343898  0.4007080277 -0.3251079494
##  [101]  0.3364301468  0.0257810835  0.1451621511  0.1294045646 -1.0266379777
##  [106]  1.5653712546  0.4425098034  0.3899768773  0.4061162581  0.0286734182
##  [111] -0.1826538622  0.1534208188  0.5282173724 -0.2399738639  0.1958353527
##  [116]  0.3950391054  0.7885520904  0.2988991826  0.0193962140  0.5834949516
##  [121]  0.2660754974  0.4692416166  1.6333252790 -0.6887825125 -0.0738964061
##  [126]  0.6900255645  0.2646060399  1.1675084057  0.2836448921  0.8763011423
##  [131] -0.3298573905  0.2971386264  0.4987863486 -0.3482131823  1.1375284181
##  [136]  1.0045600759  0.8324628036  0.1082431101  0.2386205372 -0.2486021122
##  [141] -0.6746164350  1.0113838473  0.8566539754  0.6242382170  0.6052673452
##  [146]  0.3812641378  0.5088802972  0.3290460553  1.4496474924  0.4242181200
##  [151]  0.9079927939 -0.5879203049  0.0925008984  0.4965867103  0.3138565484
##  [156]  0.3902290831  0.2348481812 -0.2177297623  0.7177498575 -0.0833324461
##  [161]  1.3192969088  0.7772337596  0.9042496226 -0.1197656086  0.2214232369
##  [166]  0.0280138916  0.0986231444 -0.0252160391  0.4977749922  0.3114726619
##  [171]  0.0909393742  0.1734856600  1.0906541658  0.1519440984  0.2370741611
##  [176] -0.5881297640  0.4738074942 -0.0438701650 -0.1814363599  0.4529112305
##  [181]  0.7419166663  1.1260513090  0.1335066443 -0.3463931314  0.4294140543
##  [186]  1.1307646874  0.0454869666  0.8070469890 -0.0886846922  0.3238991878
##  [191]  0.3547506235 -1.2473839173  0.5974413227  0.3590090720  0.1049901441
##  [196]  0.6220957719 -0.1924612530  0.1943341576 -0.0306234363  0.6124616787
##  [201]  0.5801741836 -0.2781662729  0.3322455862 -0.1140371989  0.0497015153
##  [206]  1.0734698409  0.2011747691 -0.0941304954  0.3930696228  0.4740019274
##  [211] -0.0424412982  0.1098000397  1.0454604506  0.3807241088  0.5122460398
##  [216] -0.2163849461  0.6126095508  0.5254195059  1.8672937670  0.2531382523
##  [221] -0.4339816162  0.0792434030 -0.0927293085  0.0194464065 -0.4251367943
##  [226] -0.1014472472 -1.0366362429 -0.0626801300  0.1303412823 -0.0064112197
##  [231] -0.7202142963 -0.0315419858  0.1786690146  1.0648827248  0.3392518436
##  [236]  0.3208378732 -0.1191258805  0.5804121037  0.7520426937  0.4292198465
##  [241]  1.2772424560  0.3114024861 -0.5896764475  0.5613362517 -0.5785883947
##  [246]  0.2160993472  0.1333981342 -0.6906201349  0.1706438056 -0.2065489565
##  [251] -1.4430482408  0.5418919050  0.5111849963  0.0295459473  0.0610263821
##  [256]  0.3936124260  0.6826282398  0.3047661648 -0.3831243327  0.0158193846
##  [261] -0.1537114004  1.7072343745  1.0742315800  1.2734108167  0.1110115289
##  [266] -0.2926872152 -0.9101059279  1.8592893152  0.0869362497 -0.1069473494
##  [271]  0.4900964255  0.3778064725  0.5936346764  0.4115933444  0.1410508276
##  [276]  0.4735366401  0.0758085126 -0.1480215595  0.3673767162  0.3207429266
##  [281]  0.1022964400 -0.0116933299  1.9935772740  0.2697440643  0.2237235967
##  [286]  0.1844034202  0.6283558265 -0.5159794649  0.4503945808  0.3749306875
##  [291] -0.3752246763  0.1182292272  0.0619715376  0.2863829632  0.5795286905
##  [296]  0.4305739607  0.3850842770  0.3258235257  0.4692680800  0.4264095339
##  [301]  0.0615795037  0.0906842297  0.6828208524  0.6184096754  1.1867904818
##  [306] -0.0012888614 -0.0422077331  0.9004813087  0.5407362780  1.1713095379
##  [311]  0.2352251499  0.6185676889 -0.7347214705  0.6824184659  0.4589260184
##  [316]  0.2242836544  0.3024843235  0.0119498717  0.9315848060  0.8329304034
##  [321]  0.3992815119  0.6774204886  0.0188058732  0.3149239929 -0.0484709173
##  [326]  0.6432706302 -1.6344576255  0.1652625421  0.3923474209 -0.0738964061
##  [331]  0.1472612299  0.3850160024  0.6402507606 -0.1649516093  0.4557745121
##  [336]  0.1081534690  0.2538107638 -0.0814821526  0.3595369463 -0.4470905521
##  [341]  0.1378309368  0.1246800143  1.4839936403  0.1204954840  0.1235913868
##  [346]  0.2114304643 -0.3062171509  0.3469166643 -0.0379513747 -0.0327257885
##  [351] -0.2252087367 -1.0450788316 -0.1209244967 -0.1540876819 -0.9969414672
##  [356] -0.0858899237  0.7591889943  0.1650018841 -0.5945162894  0.6360868228
##  [361] -0.6165678891 -0.4905674549 -0.0376902916  0.3685710734  0.7173663069
##  [366]  0.0888443476  0.5387911883  0.1303389468  0.3735838176  1.0615496579
##  [371]  1.9826258845 -0.0863539105  0.2813142035  0.4110194517  0.8073667274
##  [376]  0.2302720545  0.4027918312  0.2914571363 -1.7640534459  0.1260002698
##  [381]  0.3940400110 -0.6982241758  0.1733714885  0.2440719219 -0.2248631201
##  [386]  0.4698600361  0.2066327910  0.2888858472 -1.5318737518 -0.5293225276
##  [391]  0.0999507666  0.3926182896 -0.0111885531  0.3568199198 -0.2143859552
##  [396]  0.8878515271 -1.3806499799  0.1304923730 -0.4460563279  0.7138659300
##  [401]  0.0412439123  0.0228526122 -0.1431704503  0.2491604772  0.7963050306
##  [406]  0.6177440567 -0.0761298250  0.4614270221  0.6748485349  0.5796524814
##  [411]  0.4649306384  0.4713462670  0.2086823504 -0.2205813390  0.1809774805
##  [416]  0.1619704503 -0.4109887583  1.0957999694 -0.4022938089  0.4222102317
##  [421]  0.1715894506 -0.3392303994  0.1184437213  0.0950431791  0.2492126506
##  [426]  0.2970919719  0.4139151102 -0.1906504393  0.3828326043  1.0606165042
##  [431]  0.3774040733  0.3486687106  0.4379909268  0.4393358897 -0.4406714424
##  [436]  0.6094335948  0.2585979856  1.3117995990  0.1307074252  0.2159781199
##  [441]  0.1759129575 -0.6842416456  0.5482973762  0.0857730452  0.0669463110
##  [446] -0.1861657201  0.2769999051  0.3300541626  0.0646163055 -0.1049702141
##  [451]  0.0030461208  1.6697584047  0.1546355594  0.0295095180 -0.0401400164
##  [456]  0.8191507495  0.2995296935  0.5929769653 -0.2299397094 -0.0328131737
##  [461]  0.3637958493 -0.3462950455  0.1436938288  1.2792180904  0.6566014412
##  [466]  1.1713315310  0.5870171446  0.2660994261  0.3836137382  1.1574391167
##  [471]  0.1091607837  0.1491153120  0.6199958029  0.6261364166  0.4211713342
##  [476] -0.0443272099 -1.1335474273 -0.0115060962 -1.2134144556  0.1131555376
##  [481] -0.2469276612  0.0014194677 -0.1941430511  0.0081422545 -0.1324948533
##  [486]  0.0643919409  0.3716929623 -0.6607615327 -0.1201870801 -0.1668272646
##  [491]  0.4758266041  0.3729723053  1.1797825030  0.2122051777  0.3744017676
##  [496]  0.3276270818  0.2266010648  0.6921002293  0.0294885615  0.0759750867
##  [501]  0.7540454373  0.5398826204  0.2824782465  0.5408141257  1.3524406732
##  [506] -0.0135231354  0.4094122501  0.5734986177  0.2124654117  0.4426890773
##  [511]  0.8183570277 -0.2184676415  0.7518423754  0.0195881626 -0.4047434783
##  [516] -1.1821411353 -0.1326730339 -0.0156519235  0.5556314961  1.0898082719
##  [521]  0.6113986132 -0.3896999909  0.5888939379  0.2231915067  0.2459392600
##  [526]  0.3061729844  0.1975330175 -0.4836314230  0.3328009002 -0.7487852971
##  [531]  0.6169020700  0.4039877107  0.1871433178 -0.4510914428 -0.0507189012
##  [536]  0.4235407930  0.3184995281  0.8391903336 -0.1059708813 -0.2572880019
##  [541]  0.2287614108 -0.5879203049  0.7033204742  0.2792806838 -0.2952204406
##  [546]  1.1646964850  0.3365988629 -1.5219359942  0.1260228371 -0.2512339401
##  [551]  0.2669828478  0.1849380981  0.1579559366 -0.6885039267 -0.1816075098
##  [556] -0.1520471022  0.1563395050  0.0582512642  1.0445462560  0.9075493447
##  [561]  0.1618631250 -0.0876083642  0.7841206596  0.4905119317  0.3423256825
##  [566]  0.2091500506  0.1985878219 -0.6211604321 -0.1491296478  0.0597721190
##  [571]  0.4817112446  0.6179676464  0.3008660033 -0.6171045630  0.5424378880
##  [576] -0.3027306109  0.0855198957  1.0909262843  1.3583913124 -0.1257291417
##  [581]  0.4401270911 -0.1537944438  0.4117142842  0.0942010227 -0.2912489670
##  [586] -0.0582842108 -0.7085206653 -0.3797962607  1.6277833950  0.4640515192
##  [591]  0.9126081222 -0.0517302016 -0.1107742153  0.3199416017  0.1037272215
##  [596]  0.1696502223  0.2729344246  0.4294140543  0.2055835421  0.6020665316
##  [601]  0.6142214612  0.0121200886 -0.4891062874  0.6125033402  0.5058724752
##  [606]  0.4063206075  0.5794148108  0.4804786857 -0.0525661575  0.5080323563
##  [611] -0.2375703651 -0.4604863663 -0.2336195429  0.4907588319 -0.0323630958
##  [616] -0.7821984292  0.1492324220 -1.0071358612  0.7098154057  1.3351416349
##  [621]  0.1191181034  0.3611233013 -0.1755409222 -0.5796736489  0.4812164458
##  [626]  0.4174506567  0.3262774337 -0.1921170371  0.0834846243  1.2145042456
##  [631] -0.1427717140  0.6076919885  0.9125785874 -0.1768615856  0.8859856534
##  [636] -0.1071159458 -0.0263668682  0.5286433812  0.4589260184 -0.0274567032
##  [641]  0.1332754758  0.4012961947  0.5946324983  0.7595914342 -0.0105374331
##  [646]  0.4031472005  0.1364508345  0.2092701231 -0.5644445394  1.3306604617
##  [651] -1.0622944282 -0.1970886985 -0.2452819310  0.3101175237  0.1617618841
##  [656]  0.6592476209  0.4254630161  0.3832033231 -0.6445333318  2.1821947971
##  [661] -0.2781662729 -0.0441372763 -1.2101720898  0.8433932431  0.3420685032
##  [666]  0.3807241088  0.1451855483  1.9418653140  1.8229429152 -0.7740858993
##  [671]  0.2161246637  0.3495051684 -0.1779893910  0.0589784153  0.4168856927
##  [676]  0.6648030103 -0.1059511411  1.1345018709  0.2708496442  1.5378890473
##  [681]  0.1200834825  1.6545343302 -0.4419432127 -0.1419797727  0.7581758660
##  [686]  0.2188632662 -1.1740257765  1.1977783983  0.1426336218  1.2011186873
##  [691] -0.0809708077  0.0051621053  0.9727821717  0.6825269707  0.5496348886
##  [696]  0.4401041413 -0.0901459471  0.4220313482  0.0168069264  0.1663289649
##  [701]  0.2704330901  0.5547655815  0.6502654384 -0.4281866004  1.0136846461
##  [706] -0.1293500535  0.2014535275  0.4650655514  0.2773273696  0.6016876370
##  [711]  0.5573202128 -0.4713056261  0.0960817571  0.0117834106  0.6685738786
##  [716]  0.0594118258  0.7611441274 -0.9003222787  0.9983738347  0.1306521304
##  [721]  0.3098400705  0.0473160861  0.5762252994  0.1953231050  0.3100245334
##  [726]  0.6233271602  1.1386035936  0.3960812155  0.7483082950  0.5386160437
##  [731] -1.5830917172  1.0152143353 -0.2272343479 -0.1181850177  0.3121937453
##  [736]  0.6923444111  0.5972715602 -0.2275843576  0.5132067873  0.3674509639
##  [741]  0.1888252454  0.2816499929  0.1060822282 -0.0624000218 -0.0523258518
##  [746]  0.2367867472  0.2120976703  0.0135173311 -1.4812131004  0.2236875649
##  [751]  0.4089725703 -0.3017431040  0.0247968006 -0.3871395357  0.5971340469
##  [756] -0.0425844716 -0.5739412865 -0.1757653582  0.1576542787  1.4223282266
##  [761]  0.0453767680  0.4884064984  0.1932948425  0.2773396576 -0.4212547022
##  [766]  0.0847662451 -0.1382971143  0.2867626422 -0.0392320572 -0.6958006962
##  [771]  0.4328823035 -0.0216726362  0.3870563563  0.0191456886  0.6656628784
##  [776]  0.2678575754 -0.0261356129  0.1539668343  0.3797922580 -1.0747833739
##  [781]  0.5508603173  0.1630678484  0.3385519933  0.2646619479  0.5440749572
##  [786]  0.7403517698  0.5444350488  0.3902425942  0.6479687497 -1.2079201284
##  [791] -0.1644279529  0.2289551152 -0.0992301116  0.0178883800  0.0951732728
##  [796]  0.5394634850 -1.0561544005  0.0483400149  0.5274958817  0.1359119563
##  [801] -0.1015218733  0.0642772380  0.4879588074  0.4900538046  0.1738030905
##  [806]  0.2890051426 -1.1206192448  0.6632174818 -0.0643206203  0.6241322644
##  [811]  0.4235512510  0.5669180722  1.1913604807  1.3908999822  0.2617433859
##  [816]  0.8420820988 -0.2614939967 -0.7681126044 -0.0111426522 -1.0680177733
##  [821]  0.4596414721  0.4064863637  0.5637624970  0.4018539929 -0.8490997355
##  [826] -0.9806351075  0.0902641803  0.5872863496  0.3070749324  0.2563875914
##  [831] -0.7681126044  0.1821993476  0.1531360858  0.4191735966 -0.0910742857
##  [836]  0.0632082162 -0.7767015925  1.9921258977 -0.1585006606  0.1456831259
##  [841]  0.4831846460 -0.0088114789  0.0657809885  0.1244026329  0.1236171084
##  [846] -0.1452272110 -0.0643106491  1.0898339328  0.1439177052  0.5886028401
##  [851]  0.1529819553 -0.1125768520  0.3401844821 -0.1685664031  0.8503512231
##  [856]  1.3879654812  0.4474679432 -1.0555308253  1.6300496025 -0.1846202318
##  [861]  0.5768073565  0.5719523644 -0.5074032742  1.1640122020  0.2353388689
##  [866]  0.6991470342  0.2725482392  0.6088179415  0.2235821395  0.2121407136
##  [871]  0.3256178111  0.4987863486  0.4122982489  0.1650808329  0.1965442168
##  [876]  0.1331794083  0.6871673795  0.9031221736  0.4420703750  0.4713462670
##  [881] -1.0748836108  0.3177453612  1.3758428642  0.4171981585 -0.4371657318
##  [886]  0.4344162360  0.2473209187  1.6109717635  0.5596808275 -0.0542743009
##  [891]  0.2081911988  0.1135745803  0.5177290386 -0.1932940827 -0.6381186876
##  [896] -0.3430635140 -0.9653653438  0.0397947619  0.4699356656  0.8013044831
##  [901]  0.5963497059  0.4811445806 -0.5690387888  0.3321173991 -0.1073463931
##  [906]  0.0444312048  0.4226751298  0.4675140042  0.6557099677  0.4700159707
##  [911]  0.1450319477  0.7664482257 -0.1804765271  0.1975283122  0.3144492519
##  [916]  0.3021083648  0.1406836556  0.2680809919  0.8235237904  0.6155421949
##  [921]  0.5286433812  0.0555510385 -0.4511350973  0.3030024039  0.2066998866
##  [926]  0.4481170424  0.9083330939  0.5943879636  0.4253638201  0.3012787906
##  [931]  0.2085321988  1.6114106817  0.3940400110  0.4651106215  0.4532929961
##  [936]  0.7795556810  0.4806142733  0.7622259207 -0.2968569760  0.1131134552
##  [941]  0.5138135060  0.1429579639  0.3920580137  0.3137763128  0.7264467870
##  [946]  0.2062187887  0.3245611765 -0.0844537239  0.0326139841  0.0340292131
##  [951]  0.1440666029  0.2341312148 -0.1601915562  0.7182270483  0.2189142941
##  [956] -0.0510853567  0.1344208002 -0.4566420883  0.6319586256 -0.2111821452
##  [961]  0.4671135960  0.3199416017  0.2701459555  0.2186031414  0.2184658571
##  [966] -0.2328162952  0.3508996407  0.2785371429 -0.0862242474  0.0342547720
##  [971]  0.8783327075 -0.5333629787  0.3773544801  0.0809980600  0.2777092331
##  [976]  0.6234078595 -0.3876968556  0.1601752914 -0.1828531538  0.2345384944
##  [981]  1.0826093513 -0.4064685226 -0.2574609463  0.3276067075  0.0753807419
##  [986]  0.4383762131 -1.2292870054 -0.3911649372 -0.1642508318  0.0900208038
##  [991]  0.4316605184  1.2878030364  0.6919728626 -0.2033769255  0.4270219571
##  [996]  0.0568472027  0.9313539614  0.3914883861  0.5482569585  0.7507986792
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

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
fit2
```

```
##       mean          sd    
##   0.48666958   0.19871868 
##  (0.06284036) (0.04443064)
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
## [1] -0.07269935  1.43757757  0.22105843  0.32112841 -1.07841894 -0.26419630
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
## [1] 0.002
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8955099
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
##     original     bias    std. error
## t1*      4.5 0.02262262   0.8872334
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 4 5 6 7 8 
## 2 2 3 1 1 1
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
## [1] 0.0045
```

```r
se.boot
```

```
## [1] 0.9453589
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

