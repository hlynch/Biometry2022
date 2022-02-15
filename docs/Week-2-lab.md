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
## 0 1 3 5 6 8 9 
## 1 1 2 1 1 2 2
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
## [1] -0.0123
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
## [1] 2.700388
```

```r
UL.boot
```

```
## [1] 6.275012
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.3
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
##    [1] 4.4 4.4 4.2 5.2 4.8 3.6 3.8 2.5 5.1 4.3 3.8 4.2 4.2 4.2 5.0 6.4 3.8 5.6
##   [19] 4.3 3.9 4.3 4.4 3.6 3.9 7.1 4.6 4.3 5.2 3.9 2.4 4.5 5.0 3.8 3.3 4.3 4.0
##   [37] 5.5 3.4 5.1 3.5 5.4 3.7 3.8 2.3 5.2 5.1 3.6 5.4 3.8 4.1 4.0 5.6 4.4 4.5
##   [55] 2.5 4.2 3.2 4.4 5.1 3.9 5.5 3.3 5.1 3.6 5.1 4.9 3.3 3.8 4.8 3.5 4.2 4.1
##   [73] 4.7 5.1 3.9 3.6 4.4 5.3 4.2 4.0 4.3 4.9 6.2 4.2 4.3 4.1 5.3 4.4 5.7 6.0
##   [91] 4.6 5.6 5.5 4.9 3.0 6.0 4.5 5.0 4.4 4.6 4.3 5.6 5.7 3.6 3.0 5.8 5.0 3.2
##  [109] 3.0 3.5 5.0 4.9 5.8 4.1 4.5 5.6 5.5 5.3 5.8 1.1 3.9 4.2 3.1 4.0 4.9 5.5
##  [127] 4.0 4.6 5.5 4.3 5.0 5.3 5.2 4.8 5.8 5.2 4.8 5.8 5.7 4.9 4.8 5.7 6.0 4.8
##  [145] 2.4 5.0 4.3 5.5 5.3 3.8 3.5 5.0 7.0 3.9 4.5 5.1 4.9 4.4 3.9 5.4 4.1 4.5
##  [163] 3.6 3.8 3.2 5.6 6.4 5.2 5.5 5.0 3.7 4.3 5.4 4.7 4.3 3.3 4.1 5.5 3.9 6.2
##  [181] 3.3 7.0 6.2 3.9 5.5 5.4 4.4 4.3 3.8 1.6 4.1 4.2 4.9 3.4 4.7 5.2 4.3 5.1
##  [199] 3.9 4.4 3.2 4.7 4.2 4.0 2.8 3.6 4.8 5.3 4.1 5.7 4.3 5.0 4.4 3.5 5.4 5.0
##  [217] 3.9 6.1 4.8 3.5 4.3 5.5 4.1 5.5 4.3 5.9 5.0 5.7 2.8 4.3 4.8 2.7 5.8 3.3
##  [235] 3.4 2.9 6.8 4.3 4.1 4.9 5.4 4.5 4.8 5.8 5.8 4.2 5.6 6.3 5.1 3.9 5.2 4.5
##  [253] 5.5 3.9 4.7 3.9 3.8 4.1 3.4 5.7 5.5 4.4 5.5 3.4 2.8 4.2 6.2 3.4 5.3 4.8
##  [271] 5.1 5.0 2.3 5.3 3.1 6.5 4.7 3.5 4.3 1.9 3.1 3.7 4.6 5.4 3.6 5.2 3.8 4.0
##  [289] 4.3 3.9 4.1 4.4 6.0 4.9 3.7 4.5 4.9 4.2 6.0 3.5 4.0 5.5 4.7 4.0 6.7 4.8
##  [307] 3.6 5.3 5.4 5.0 5.7 5.1 3.5 3.4 5.9 5.0 5.4 4.1 4.5 3.6 4.9 2.7 4.9 5.3
##  [325] 5.5 3.4 5.0 4.1 5.1 6.6 4.1 4.2 4.8 3.6 3.5 4.6 4.2 3.2 5.6 4.2 4.8 5.3
##  [343] 3.5 4.8 4.4 3.9 4.9 4.3 6.5 6.1 2.5 5.5 3.3 4.3 2.6 4.0 5.7 5.1 4.2 4.0
##  [361] 4.9 4.0 4.9 5.9 4.7 4.4 6.2 4.6 2.4 5.8 5.2 4.0 3.2 3.5 5.0 3.4 6.0 5.2
##  [379] 4.5 4.0 4.2 4.9 5.3 3.7 4.6 2.6 5.1 3.5 5.0 3.5 3.6 4.7 4.3 4.5 3.8 4.7
##  [397] 3.5 5.3 3.5 4.7 4.2 4.5 5.4 5.1 3.8 4.0 3.4 5.4 3.8 4.3 4.5 3.1 4.9 6.0
##  [415] 4.0 5.0 4.4 3.6 4.4 4.5 4.1 5.1 5.8 5.2 4.3 5.8 4.6 4.6 4.0 4.4 4.4 5.0
##  [433] 4.9 4.3 3.9 5.6 4.5 4.9 4.3 5.5 5.3 2.7 3.6 4.1 4.1 5.0 4.7 6.1 3.9 4.4
##  [451] 3.9 3.5 3.3 3.8 4.7 4.1 3.8 3.1 4.2 4.6 4.8 3.5 3.3 3.5 6.3 6.6 3.1 4.4
##  [469] 4.8 4.8 3.8 2.9 4.7 6.7 4.5 5.1 3.6 5.3 4.0 5.7 5.1 5.2 5.3 4.0 4.4 6.1
##  [487] 5.1 2.7 4.4 4.4 3.5 3.0 4.7 3.5 5.8 3.8 4.2 4.0 4.4 5.3 4.2 3.3 4.7 4.3
##  [505] 3.9 5.3 5.8 6.0 3.5 3.6 3.8 3.9 3.8 4.1 5.8 3.6 3.8 5.7 4.1 3.2 5.5 5.3
##  [523] 6.3 3.7 4.0 3.4 3.6 6.1 5.5 6.2 4.2 5.0 4.1 4.8 4.5 5.6 4.2 3.2 4.9 4.3
##  [541] 7.0 3.9 2.6 3.9 3.6 5.2 4.7 4.4 4.7 4.8 4.2 5.3 4.7 5.7 3.9 5.1 3.8 4.7
##  [559] 5.0 2.6 4.9 3.7 4.1 5.0 3.7 5.1 4.7 3.6 5.2 5.4 4.4 3.7 4.8 5.9 5.1 3.5
##  [577] 4.0 4.3 5.1 2.8 3.5 4.8 4.6 6.0 3.5 4.5 3.0 3.2 4.5 4.3 3.8 4.2 5.2 4.6
##  [595] 3.3 4.4 5.4 4.7 4.8 5.7 3.4 4.1 2.9 6.1 4.6 3.8 5.0 4.2 3.4 5.1 5.1 4.0
##  [613] 5.1 4.5 3.5 5.0 5.1 4.7 4.1 4.4 6.0 5.8 4.7 3.8 4.8 4.4 5.1 2.9 4.5 6.0
##  [631] 4.1 5.1 4.0 4.2 5.8 3.3 4.9 5.6 4.7 2.6 6.5 5.0 4.1 3.3 3.9 5.3 4.3 4.1
##  [649] 4.4 5.9 5.5 5.3 4.5 3.3 4.9 4.7 5.0 4.8 5.3 4.8 2.1 5.1 3.8 4.8 4.8 5.5
##  [667] 5.1 5.2 5.3 5.6 4.0 5.2 5.6 5.4 4.8 5.1 3.3 5.0 4.6 4.0 5.3 4.3 4.6 5.7
##  [685] 3.6 3.8 5.2 3.5 3.1 3.6 5.1 4.1 5.1 6.1 2.3 4.8 5.0 5.0 3.7 5.2 4.3 3.7
##  [703] 5.4 5.3 4.1 4.4 3.9 5.2 4.4 5.0 4.7 4.2 5.7 4.2 3.7 4.6 4.5 4.8 5.0 5.1
##  [721] 4.8 4.5 3.9 4.2 3.6 4.7 5.0 5.8 4.5 4.5 3.5 5.2 4.9 3.9 5.4 5.1 4.4 4.5
##  [739] 5.3 6.5 4.7 5.6 6.4 3.6 5.9 3.1 2.5 4.8 4.3 5.8 3.2 3.9 6.2 5.7 5.2 6.1
##  [757] 6.1 5.0 5.2 3.4 5.1 3.4 7.2 2.8 5.2 4.4 5.9 4.3 3.9 4.0 5.7 5.3 4.0 4.5
##  [775] 3.9 5.8 5.0 4.9 4.7 6.3 3.7 3.5 3.6 3.2 4.8 5.7 4.2 4.8 5.4 5.5 6.0 4.3
##  [793] 5.1 5.7 5.4 4.0 6.3 3.6 5.0 2.5 3.7 4.0 2.3 6.3 4.4 4.9 5.2 4.9 4.0 5.0
##  [811] 3.0 3.7 3.9 3.4 5.6 3.8 4.8 4.6 5.6 4.7 4.6 6.3 3.9 5.8 4.2 4.6 6.0 4.6
##  [829] 4.0 4.8 4.6 4.3 3.9 5.7 4.7 3.6 4.2 6.2 4.1 6.1 5.5 3.5 4.8 4.1 6.2 3.7
##  [847] 4.4 5.2 4.3 4.2 4.6 3.5 4.8 3.1 6.8 5.4 4.7 4.8 5.1 3.8 3.5 4.1 3.4 3.1
##  [865] 4.6 4.3 4.2 3.9 2.3 4.6 3.1 4.1 3.5 4.5 5.0 3.7 5.2 5.8 3.8 5.2 4.1 5.8
##  [883] 4.2 6.2 4.5 5.2 4.7 3.3 4.6 3.3 5.2 4.1 5.0 4.8 4.4 4.9 5.4 4.1 5.4 3.9
##  [901] 3.5 2.6 6.7 4.2 4.4 5.2 4.3 6.9 5.2 4.7 3.8 4.7 4.5 4.5 4.6 4.3 5.3 3.3
##  [919] 4.9 4.2 4.2 5.9 5.8 3.9 4.1 4.4 4.2 4.2 5.2 2.9 4.9 5.6 3.5 4.9 5.5 4.0
##  [937] 3.6 2.8 2.8 4.1 5.6 5.4 4.6 4.4 3.8 5.8 3.1 5.5 5.2 5.9 5.8 5.0 3.6 5.6
##  [955] 5.1 3.8 4.2 5.6 4.8 5.5 4.2 3.7 4.1 5.3 3.8 5.4 3.7 4.4 3.0 3.1 2.7 3.1
##  [973] 6.9 5.9 5.4 4.6 4.7 3.3 6.9 4.3 3.9 3.8 5.3 5.3 4.4 3.2 3.5 5.2 4.6 4.5
##  [991] 5.9 5.9 4.1 3.8 3.8 4.8 1.9 4.4 6.2 4.3
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
##   2.7   6.3
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
##    [1] 2.5 4.4 3.3 3.9 5.9 4.1 3.8 3.7 3.8 3.6 3.9 5.7 2.7 2.5 6.9 4.9 5.3 4.0
##   [19] 4.7 5.9 4.4 4.6 3.7 5.0 3.7 3.8 4.6 5.7 4.4 5.7 5.2 4.9 2.9 5.1 5.1 4.0
##   [37] 4.2 4.5 4.9 5.7 5.0 2.8 4.9 5.9 3.6 3.3 5.1 5.0 5.1 3.2 3.8 4.2 5.6 4.7
##   [55] 3.8 4.5 6.5 4.8 2.3 5.0 4.4 5.2 4.4 5.6 5.9 6.7 6.2 3.1 4.2 5.3 4.5 4.3
##   [73] 4.3 6.3 4.8 4.1 5.4 3.2 4.4 3.0 4.0 4.9 3.6 4.9 4.1 4.6 4.5 3.9 3.9 3.7
##   [91] 3.7 4.6 2.5 3.8 5.0 3.2 5.1 3.5 2.3 3.8 5.2 3.7 4.2 2.0 5.8 5.8 4.0 4.3
##  [109] 4.0 3.8 3.5 5.1 4.6 3.8 3.9 5.5 3.1 4.2 4.3 3.3 4.6 2.7 4.3 4.1 4.1 5.1
##  [127] 4.6 6.2 4.8 4.3 4.9 4.4 4.8 3.8 3.7 3.2 4.5 4.2 5.5 3.4 4.0 4.8 5.5 5.6
##  [145] 4.0 5.5 5.9 5.0 4.1 3.4 4.6 5.7 2.7 3.7 6.2 4.4 3.8 3.3 4.2 4.0 5.0 5.4
##  [163] 3.4 4.8 5.7 3.7 4.2 2.6 6.2 3.4 4.6 4.5 3.6 5.3 5.9 4.1 4.6 2.7 4.8 4.1
##  [181] 4.4 4.6 4.6 6.0 3.1 6.4 3.2 3.6 4.4 4.5 6.6 5.0 5.6 4.6 2.9 3.5 4.5 4.6
##  [199] 5.0 4.6 4.0 2.7 3.5 4.6 5.2 3.3 4.3 5.1 3.8 4.4 5.4 3.4 6.1 5.5 3.9 2.8
##  [217] 5.6 5.4 3.7 3.7 5.3 5.1 2.4 4.8 5.4 4.0 3.1 3.6 4.8 4.6 5.2 6.2 5.3 5.4
##  [235] 3.9 4.1 5.1 4.9 3.0 4.6 6.6 3.4 5.0 3.3 4.8 4.7 5.6 5.7 4.2 4.4 5.1 4.1
##  [253] 3.9 4.5 3.4 4.3 3.6 4.3 5.0 5.1 3.2 5.1 5.7 5.0 4.7 4.7 4.6 4.6 4.5 4.8
##  [271] 3.7 4.6 3.5 4.5 4.3 3.2 6.6 5.4 3.8 4.2 4.4 4.8 4.8 3.9 3.3 4.0 3.8 3.6
##  [289] 4.8 2.4 3.9 6.3 4.2 3.0 5.3 4.4 4.3 5.2 4.6 5.1 4.4 3.4 3.5 6.7 4.2 6.8
##  [307] 3.3 5.4 6.0 2.6 4.3 4.2 3.7 3.8 5.0 6.3 4.6 5.9 3.6 5.4 3.2 4.6 3.9 5.5
##  [325] 4.8 6.1 3.3 3.7 4.9 3.1 3.7 4.4 3.3 5.4 3.8 5.5 5.0 4.2 4.1 5.5 5.0 5.6
##  [343] 4.6 3.9 3.4 6.0 2.9 2.5 4.4 6.1 5.1 5.0 4.5 5.3 3.4 5.2 3.6 5.4 3.5 4.9
##  [361] 5.7 3.9 4.9 3.2 5.4 3.8 3.4 3.7 3.9 2.4 5.1 4.1 5.2 4.3 4.5 3.8 3.5 4.3
##  [379] 4.0 5.2 4.4 5.4 3.0 5.5 5.4 4.8 5.4 4.6 3.7 5.2 4.0 4.8 4.0 4.6 4.7 4.0
##  [397] 4.7 4.0 5.0 3.7 6.1 2.6 4.3 5.6 4.9 2.5 4.3 5.3 5.5 4.9 5.4 4.7 4.7 5.3
##  [415] 4.4 4.2 4.9 3.4 5.0 5.0 3.7 5.7 4.5 4.6 5.6 3.3 4.2 4.1 5.5 4.4 4.2 4.7
##  [433] 4.0 4.0 4.0 4.6 2.9 4.9 4.5 4.3 5.7 4.8 4.1 5.4 5.6 5.1 5.6 5.2 5.3 5.0
##  [451] 4.6 5.5 6.1 4.5 4.6 4.1 4.8 5.0 1.8 5.3 3.5 3.9 3.9 4.2 4.6 3.8 3.4 3.8
##  [469] 4.9 5.0 4.2 6.8 3.9 4.9 4.3 5.3 5.3 3.7 3.1 2.5 6.2 4.3 5.3 5.0 4.4 6.9
##  [487] 4.9 4.5 4.5 4.2 4.2 4.9 5.3 4.1 4.4 5.1 4.6 4.3 7.2 5.4 3.9 3.4 4.6 4.3
##  [505] 4.0 3.4 4.3 4.4 4.9 5.6 5.7 7.0 4.0 4.9 4.2 3.1 4.5 3.7 2.5 5.1 5.8 4.2
##  [523] 5.0 3.5 3.7 4.6 4.3 3.4 4.5 3.2 5.5 5.1 6.5 5.8 6.0 4.5 6.0 5.2 4.9 5.3
##  [541] 2.1 5.7 4.7 5.2 4.2 2.9 5.6 4.4 4.4 3.3 4.8 5.0 3.4 3.1 3.4 4.5 4.2 3.8
##  [559] 4.1 4.4 3.0 6.1 3.9 3.6 4.8 3.7 5.0 4.3 4.3 4.9 4.4 6.2 2.9 3.8 5.6 1.9
##  [577] 3.2 5.9 3.6 6.6 5.2 3.5 4.4 4.5 5.2 3.5 2.2 4.4 2.8 4.7 3.9 4.8 3.9 4.9
##  [595] 4.1 3.4 6.4 4.8 4.4 5.0 4.5 4.2 5.6 3.3 5.5 5.0 5.2 4.3 4.2 6.1 5.2 5.4
##  [613] 4.8 4.5 6.3 4.9 4.6 4.5 3.9 4.9 4.2 5.2 6.5 4.8 4.5 4.4 5.9 4.9 4.1 3.6
##  [631] 3.7 6.2 4.3 3.9 5.0 4.5 4.7 3.9 5.1 4.5 5.2 5.7 5.5 5.1 5.6 5.0 3.4 4.0
##  [649] 5.6 5.4 3.0 6.0 3.9 4.5 6.7 4.5 4.3 5.1 3.9 5.9 6.2 3.6 4.5 5.1 5.4 5.2
##  [667] 3.6 3.4 5.0 4.9 3.2 4.9 5.4 3.8 3.5 5.2 5.7 4.9 5.6 4.9 3.8 3.2 3.6 4.8
##  [685] 4.0 4.7 3.6 5.7 3.7 4.6 4.6 5.6 4.7 5.3 3.7 3.9 4.5 3.9 5.2 4.0 4.7 5.6
##  [703] 5.5 5.2 4.6 5.5 3.0 5.4 4.0 5.0 5.9 6.1 3.5 3.5 3.7 3.8 4.8 4.1 5.5 4.8
##  [721] 3.8 2.9 5.6 4.4 4.3 5.2 4.1 5.0 4.8 4.5 3.4 5.3 3.7 4.6 3.8 3.5 2.9 3.5
##  [739] 3.8 4.4 6.0 3.9 4.9 5.4 3.5 4.8 6.6 3.2 4.8 2.4 6.0 4.3 6.4 3.8 4.6 4.3
##  [757] 6.5 5.7 5.2 3.8 3.8 4.8 4.1 3.8 6.0 4.1 5.3 5.3 4.3 4.8 4.5 4.6 4.6 4.5
##  [775] 4.5 5.3 4.3 6.5 6.5 5.7 5.4 2.7 3.6 5.3 4.9 4.6 4.7 5.8 4.2 4.9 5.0 5.2
##  [793] 4.5 5.1 5.1 3.7 3.0 6.0 4.8 4.6 5.8 3.8 4.0 2.6 6.2 3.4 3.8 3.6 4.5 4.7
##  [811] 6.4 3.5 4.7 6.6 3.6 4.3 3.9 3.3 5.1 3.8 5.3 4.5 3.1 5.4 5.8 3.1 4.7 5.1
##  [829] 4.5 5.0 5.3 4.7 5.0 3.7 6.3 5.9 5.7 5.7 4.7 4.5 5.9 4.5 5.4 3.7 5.0 4.0
##  [847] 3.7 3.9 2.8 3.8 4.8 3.6 5.4 5.1 4.1 4.0 4.9 4.4 5.9 4.1 4.5 2.9 4.0 5.3
##  [865] 3.0 5.8 5.0 3.9 4.9 3.8 4.1 3.6 3.8 2.8 4.4 5.1 5.0 4.0 3.7 4.4 5.1 4.3
##  [883] 5.4 3.7 2.6 4.9 4.1 3.9 3.8 3.7 5.2 4.1 4.1 5.9 6.1 4.8 4.1 4.1 4.1 3.4
##  [901] 4.6 4.6 3.6 5.4 4.7 4.5 4.1 4.6 5.7 3.8 3.9 6.6 3.0 6.2 5.9 5.0 3.9 4.1
##  [919] 3.1 5.2 4.9 3.4 4.2 4.9 5.0 3.8 5.7 5.0 4.2 4.4 5.0 4.6 4.7 5.4 5.4 4.4
##  [937] 3.4 4.6 6.8 5.3 3.9 5.1 6.0 4.7 4.9 2.2 5.9 4.9 4.4 5.2 4.7 3.3 3.6 4.7
##  [955] 3.5 5.1 3.7 5.3 3.8 5.3 4.4 2.8 3.9 5.4 4.5 3.0 5.0 4.7 4.7 3.7 4.6 4.5
##  [973] 4.7 4.8 4.6 4.8 3.8 3.9 3.5 3.8 4.9 2.7 4.2 4.0 5.1 4.7 4.6 4.7 4.4 5.5
##  [991] 3.6 6.7 5.5 4.5 4.8 4.3 4.7 4.7 4.7 5.8
## 
## $func.thetastar
## [1] 0.0175
## 
## $jack.boot.val
##  [1]  0.51942029  0.40117302  0.38355438  0.24435262  0.01127596 -0.04970760
##  [7] -0.10918635 -0.22409639 -0.37584270 -0.53958944
## 
## $jack.boot.se
## [1] 1.007519
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
##    [1] 5.3 5.0 3.9 5.4 5.9 3.5 3.6 5.4 4.1 3.1 3.9 4.1 5.1 3.1 2.8 5.1 4.5 6.0
##   [19] 4.6 4.1 4.7 3.4 3.6 3.7 4.2 3.8 3.6 3.7 5.0 3.1 3.4 4.2 4.9 5.4 3.3 5.3
##   [37] 3.9 4.3 3.2 5.0 2.9 4.6 5.1 5.2 5.2 5.6 5.3 5.8 3.8 4.1 4.4 5.4 5.3 4.0
##   [55] 6.1 5.5 6.1 4.3 5.1 4.4 2.4 5.1 3.9 5.4 5.5 6.0 3.7 3.5 5.7 3.7 4.8 5.1
##   [73] 5.3 5.8 4.1 4.6 5.6 4.7 5.0 5.0 3.4 5.2 3.8 4.3 3.6 3.4 4.3 3.3 5.0 3.2
##   [91] 4.7 4.9 6.4 5.3 5.9 4.1 4.1 4.1 4.5 6.5 4.5 7.0 5.6 3.3 4.2 5.4 5.7 4.3
##  [109] 4.8 3.4 3.0 4.4 3.4 3.9 5.0 5.0 4.4 4.0 5.2 4.7 4.1 4.7 4.6 4.1 4.5 3.4
##  [127] 2.7 3.1 3.8 6.3 4.0 5.8 6.4 2.9 4.8 3.1 3.6 3.6 6.3 4.3 5.3 4.8 3.9 6.3
##  [145] 4.9 6.3 3.3 3.2 4.0 4.6 3.1 5.7 4.5 4.5 6.0 3.0 3.4 4.9 3.3 5.4 4.1 4.4
##  [163] 4.6 3.6 6.2 3.1 5.7 3.2 5.0 4.6 4.7 3.9 6.1 4.7 3.8 4.5 2.8 4.0 5.1 5.2
##  [181] 5.0 4.7 5.6 3.1 4.0 4.7 3.8 3.1 4.5 4.1 4.4 5.2 5.5 2.8 4.1 2.8 5.2 4.4
##  [199] 3.8 3.2 4.2 3.5 5.7 4.7 6.2 4.6 4.4 5.1 4.3 4.6 5.8 3.7 4.7 4.4 5.3 4.2
##  [217] 3.1 3.1 2.7 3.8 4.9 4.0 4.1 4.1 4.5 4.7 4.2 6.0 4.6 4.0 3.2 4.1 5.2 4.8
##  [235] 3.4 4.8 4.0 3.9 4.5 4.7 4.1 4.7 2.4 4.5 5.1 4.5 2.9 4.1 3.1 3.7 4.5 5.5
##  [253] 4.3 4.3 4.2 5.7 4.1 3.9 4.5 5.5 4.3 4.0 5.4 5.5 5.6 5.2 3.9 3.8 3.7 4.5
##  [271] 5.8 4.1 3.8 3.1 4.7 4.6 5.5 4.3 5.5 3.9 4.2 4.0 5.2 3.5 3.7 4.8 4.9 6.1
##  [289] 5.2 4.1 5.5 5.6 4.2 4.9 4.8 3.4 5.1 4.8 4.2 3.7 5.0 3.8 3.5 4.7 5.4 5.3
##  [307] 3.6 2.6 5.1 4.6 3.5 4.9 3.5 6.2 3.5 4.0 4.0 4.1 4.9 5.4 4.7 4.5 4.1 5.0
##  [325] 3.9 3.5 3.3 3.1 4.0 3.9 4.3 5.6 3.7 5.3 4.4 6.0 4.7 3.6 5.1 5.0 5.4 4.5
##  [343] 5.2 4.7 5.4 3.3 5.6 3.2 4.6 4.9 5.2 3.6 5.0 4.6 3.0 6.2 3.4 4.2 4.9 5.0
##  [361] 5.0 4.4 3.2 4.7 5.4 4.3 2.9 5.5 5.8 6.1 4.4 2.3 5.5 3.8 5.2 5.7 6.7 4.6
##  [379] 4.2 4.6 2.8 4.2 5.2 5.9 5.3 4.0 3.6 4.7 3.6 4.2 4.8 5.2 4.9 5.2 5.0 4.1
##  [397] 4.9 2.7 5.2 4.7 2.6 5.3 4.3 4.5 4.1 5.6 4.8 3.7 3.3 4.9 5.2 3.6 4.2 5.5
##  [415] 5.2 5.4 6.0 5.0 5.5 4.0 4.3 5.7 3.8 5.4 4.0 3.3 4.3 4.9 4.3 4.1 3.9 5.2
##  [433] 4.9 3.8 5.6 4.1 4.4 6.3 3.7 5.7 4.2 5.2 3.0 5.5 3.4 3.8 4.5 6.2 3.4 5.1
##  [451] 4.0 5.7 4.9 3.0 4.1 5.2 5.2 4.1 2.7 3.0 5.4 5.4 5.3 5.3 4.7 5.2 3.9 4.4
##  [469] 5.3 4.7 4.1 4.9 2.5 3.8 3.2 3.9 3.7 5.1 5.0 4.1 3.2 3.4 5.6 3.9 4.4 3.5
##  [487] 3.0 4.4 4.8 3.9 4.2 3.6 4.5 5.0 5.6 3.8 4.4 3.0 3.9 5.2 3.5 4.5 4.2 4.4
##  [505] 3.6 3.9 4.4 4.5 4.6 4.5 3.3 3.3 4.8 4.9 5.0 4.7 5.2 6.0 5.0 5.4 3.8 5.3
##  [523] 4.4 3.0 4.1 5.0 5.4 5.0 5.7 4.5 4.3 4.3 4.7 4.4 4.3 3.9 4.8 3.9 6.1 5.1
##  [541] 3.6 5.1 3.7 5.6 4.1 2.6 3.9 4.3 3.3 5.2 5.3 4.1 5.8 3.3 5.6 3.9 3.8 4.6
##  [559] 4.7 3.2 4.3 6.2 4.5 3.6 5.0 5.3 5.4 4.3 4.5 4.7 5.4 5.2 2.7 6.3 3.6 3.4
##  [577] 4.6 4.5 3.6 4.9 5.0 4.0 3.7 4.5 4.7 5.7 5.0 4.2 5.1 4.7 5.3 5.1 5.2 4.4
##  [595] 4.2 3.2 4.4 4.6 3.2 6.6 2.8 3.5 3.6 3.7 4.3 5.4 4.3 3.6 5.0 4.7 5.2 6.5
##  [613] 6.0 4.6 6.7 3.8 5.3 4.5 4.7 2.4 6.1 4.3 3.5 3.8 5.4 4.7 3.6 4.8 5.5 5.0
##  [631] 4.3 5.0 4.0 4.6 4.6 3.6 4.3 3.7 5.2 3.4 4.1 3.4 4.4 2.6 6.3 5.3 5.4 4.5
##  [649] 5.0 3.5 2.9 3.0 4.6 4.8 4.9 4.5 4.4 4.2 3.3 5.3 4.7 4.2 4.5 4.0 4.7 3.1
##  [667] 4.3 5.9 3.5 5.1 4.9 6.5 4.8 6.3 4.9 4.5 5.5 4.5 4.3 3.6 4.0 4.5 3.2 3.7
##  [685] 4.8 3.7 4.8 4.3 4.0 4.7 5.7 6.3 5.1 6.1 4.0 3.4 5.5 5.0 4.2 4.9 4.1 4.4
##  [703] 4.7 3.3 5.6 4.8 3.5 4.7 4.4 4.4 3.2 4.2 2.7 4.2 4.4 5.0 3.9 4.5 4.5 5.3
##  [721] 4.6 3.9 4.5 5.1 5.0 4.5 4.5 4.3 4.9 4.6 4.5 5.1 5.5 3.5 4.7 2.5 2.3 5.2
##  [739] 3.2 4.7 4.1 5.7 3.0 3.9 4.3 3.4 4.0 3.9 4.2 4.9 4.7 5.1 2.4 3.3 5.2 5.2
##  [757] 3.3 3.9 4.6 4.4 3.9 3.3 4.1 4.1 4.2 4.8 4.6 3.7 5.3 4.7 4.9 4.7 4.4 3.9
##  [775] 3.7 4.3 3.3 4.5 4.2 4.4 4.4 3.6 4.5 3.2 4.3 5.3 4.6 5.2 3.7 4.9 4.4 4.7
##  [793] 3.2 4.3 5.7 5.0 4.4 3.6 5.6 4.3 6.5 4.2 4.3 4.9 4.3 5.4 4.7 4.1 5.7 4.3
##  [811] 4.3 3.7 3.6 4.0 4.2 4.2 5.4 5.4 5.1 3.9 4.6 6.6 4.9 2.4 4.8 5.5 4.7 4.2
##  [829] 5.6 3.2 4.3 3.7 4.4 5.8 4.2 2.9 3.6 3.2 5.5 4.3 2.8 5.9 4.8 4.5 3.7 4.0
##  [847] 3.6 2.6 7.2 6.2 5.5 3.8 3.4 4.5 4.1 5.8 3.7 4.4 4.5 2.6 4.3 4.5 2.4 5.3
##  [865] 3.3 3.7 5.0 4.3 6.3 4.1 6.6 5.2 5.4 2.6 4.2 4.5 4.6 3.3 4.4 2.8 5.4 5.5
##  [883] 4.2 6.8 5.0 5.0 5.0 4.1 4.7 5.0 3.8 4.0 5.5 4.7 5.3 4.8 5.3 5.4 4.4 3.4
##  [901] 4.5 3.7 4.8 3.8 5.0 3.5 4.6 3.1 4.7 4.3 4.5 5.2 4.6 5.3 4.8 3.8 5.1 3.9
##  [919] 2.9 3.8 4.2 3.1 4.1 3.5 5.7 4.7 3.2 4.7 4.5 5.2 5.0 5.4 6.3 3.9 4.0 4.5
##  [937] 4.0 3.9 5.8 3.0 5.0 4.6 4.6 3.8 5.8 4.7 4.6 4.2 5.3 5.1 4.4 2.8 4.8 6.6
##  [955] 4.9 4.7 4.6 3.8 4.0 5.7 4.0 3.9 4.2 4.2 3.3 4.3 4.0 4.3 5.2 4.4 4.5 4.1
##  [973] 3.0 4.7 5.4 2.8 4.1 4.6 4.6 3.9 2.9 4.8 4.7 4.7 5.1 4.9 2.8 3.6 4.0 5.6
##  [991] 3.2 6.3 4.9 4.2 3.4 3.7 4.2 4.5 4.7 5.2
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.4 5.3 5.2 5.2 5.0 5.0 4.8 4.7 4.6 4.5
## 
## $jack.boot.se
## [1] 0.880284
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
## [1] 0.2874263
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
##     shape       rate  
##   4.705856   8.351612 
##  (2.034316) (3.810099)
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
## [1]  0.2321867 -0.1590004 -0.3804103  0.9262135  1.0288573  0.2817626
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
##    [1]  0.9225901900 -0.0413584335  0.3990216747  0.2130111323  0.6228024422
##    [6] -0.0348851520 -0.0081719902  0.8845603297  0.6812180464 -0.4627692875
##   [11] -0.1845079056  0.0504360688  0.6118080095 -0.4305278156  0.0222393653
##   [16] -0.2680469056 -0.0709991863  0.6381890692  0.6081992921  0.4534679594
##   [21] -1.1514126097 -0.5038343433  0.4590444171  0.4242119795  0.3919691339
##   [26]  0.6062384652  0.8458473552  0.0335274281 -0.2222871770  0.6752419778
##   [31]  0.2320972519  0.2394678839  0.6495081469 -0.6068549955  0.1209204616
##   [36]  0.4124963849  0.6359873201 -0.3526258258  0.5798160249  0.0063359918
##   [41]  0.0713558055 -0.1661209335  0.5251544305  1.3007472401  0.2607106677
##   [46]  0.5114072422  0.7599377921  0.6910585666 -0.7220295508  0.1684764151
##   [51]  0.2890383840  0.6964629361  0.5273277533 -0.4688315739  0.7615637515
##   [56]  0.3410027908 -0.2210587854 -0.7798664572 -0.8282720656  0.3506479320
##   [61]  0.2329398636 -1.7668104542  0.0378610757 -0.1878880258  0.6460180141
##   [66] -0.2015665820 -0.1558113727 -0.3609023325  0.3133615080  0.0015854200
##   [71] -0.1039658464  0.1698068588  0.9871600339  0.2564216418  0.0418900196
##   [76]  0.4312127212 -0.2125181174 -0.0179068671 -0.0552921563 -0.2057230571
##   [81] -0.8535304777  0.9675582036  0.4458294658 -0.5730131914  0.2089344552
##   [86] -0.4005621482  0.1986766382 -0.5601791089  0.2497613177  0.3008032676
##   [91] -0.4962051581 -0.3295389191  0.1524717797 -0.0404723790  0.6910585666
##   [96] -0.2379460945  0.5458674140  1.1508363330 -0.0556217317 -0.1667576548
##  [101]  0.8932021696  0.5823496068  0.5966014812 -0.1225225112 -0.6096653256
##  [106] -0.1738133229  0.6698076447 -0.1971164814  0.7686197986 -0.4461408610
##  [111]  0.8563754929 -0.9641573660 -0.6179708877  0.0850436234 -0.7346376228
##  [116]  0.0496741273  0.0918698357 -0.3767713350 -0.5029875867  0.6298316794
##  [121]  1.4251019138  0.2759152723  0.3107127040 -0.0582774523  0.6986630783
##  [126]  0.1326506423  0.2600114618 -0.0641790886  0.2590433870 -0.1701975374
##  [131]  0.0175903510  0.4812992506  0.2075166630 -0.4127601256  0.1111642636
##  [136] -1.2710839637  0.2998449097  0.1216982958  0.2953691984  0.7998768542
##  [141]  0.6404191012 -0.8718493236  0.1253172620  0.1683612133 -0.2059604822
##  [146]  0.5526725537  0.0030206472  0.4880722700  0.6017289506  0.5790720408
##  [151] -0.1620555427  0.7164859138  0.4178341628  0.2611284521 -0.4592620321
##  [156]  0.2960453943  0.0468451742  1.2635879134  0.0301063470  0.5273168130
##  [161]  0.6971981256  0.9274446361  0.6208153109  1.0045024946 -0.2714575869
##  [166]  0.2706204830  0.1063417136  0.7614381906 -0.7251573680  0.0010091095
##  [171] -0.2188387304  0.1131524501 -0.3553345493  0.2851113196  0.1482767254
##  [176]  0.3591936912 -0.6791907138  0.3114600501 -0.0493362861  0.8166177116
##  [181] -0.2785800107 -0.4163587154  0.3754513181  0.5011790230  0.4554822239
##  [186]  0.4220072061  0.9021806229 -0.6098312352 -0.1222158048  1.6525098863
##  [191]  1.0562079437 -0.5605933829 -0.8153120116  0.9464947803 -0.1068380682
##  [196] -1.1094531848  0.0001005305 -0.2396884946  0.6966559065  0.2885330461
##  [201]  0.0515942176  0.7290101433  0.5541123496  0.2119262380  1.3347010871
##  [206] -0.0294520657  0.3978471865  0.3743948480  0.5842565126 -0.3076867296
##  [211]  0.0267293180  0.3572424254 -0.0812666175  0.0034191588  0.2268669543
##  [216]  0.3701209591  0.3173455705  0.2814257176 -0.5007143002 -0.0618654987
##  [221] -0.5455591042 -0.4891282404  0.5268949933 -0.1895449124  0.4619605365
##  [226]  0.1477220969  0.0821599843  1.0364982587 -0.1350172030  0.2847428888
##  [231] -0.9274522753  0.1671678826  0.7647331826 -0.2370722846 -0.9416246990
##  [236] -0.1674453764  0.8040398963  0.1694411775 -0.2710719329  0.5858755375
##  [241] -0.5670862293  0.0015903507 -0.3498611922 -0.4882035949  0.9105820873
##  [246]  0.3221229771 -0.6184621408  0.3675607325  0.3178877275 -0.7835454073
##  [251] -0.0540215318  0.4948913053  0.7708318225 -0.8147588772 -0.6122441976
##  [256]  1.8517048515  1.1403265414  0.0752840486  1.0340974883  1.0456164332
##  [261] -0.4802475572 -0.2895876387 -0.2935583299 -0.0010013040  0.4952649794
##  [266]  0.3476370366  0.0882706713  0.6657888843 -0.1224118326  0.2274946057
##  [271]  0.6390659156  0.6505982631  0.9393702717 -0.3362529868  0.3234903983
##  [276]  1.0876536062  0.6902798417 -0.7258528670 -0.2857164836 -0.0131317409
##  [281] -0.1005975922  0.6771803447  0.5401420785 -0.0928102508  0.2750997603
##  [286] -0.2951314504  0.2351582418  0.0507861009 -0.7374879274  0.5915266044
##  [291]  0.3922191781  0.5329011618  0.2481515689 -0.0707926055  0.6382298718
##  [296]  0.7023976030  0.0643727432  0.3000395902 -0.0076597629 -0.0384694814
##  [301]  0.4856558645  0.2511185074  0.9570749876  0.6612395806  0.4444796640
##  [306]  0.4458294658  0.4449225293 -0.0112314064 -0.1722272358  0.0229881675
##  [311] -0.4822531612  0.3683114243  1.0285730496  0.5875273977 -0.3654876948
##  [316]  0.0238919419 -1.4404960014  0.1905129104  0.6250840970  0.3013260674
##  [321] -0.3692512152  0.6258948291  0.6799044421  0.1141444870 -0.0909866219
##  [326]  0.5655340009  0.5289556289 -0.1929180978  0.2085087784 -0.4461408610
##  [331] -0.9384270881  0.0939597604  0.3448238969  1.3654360807  0.7797197127
##  [336]  0.1094900910  0.7845571160 -0.1739465485  0.5725980130  0.8725036942
##  [341]  0.5157649491  0.0549396028  0.2460402384  0.6709583503 -0.3652370057
##  [346]  0.5715337509 -0.0691011562  0.6789655815  0.0363651673 -0.5145345173
##  [351] -0.5735029309 -0.1857984523  0.3064847788  1.3219336651  0.4257634355
##  [356]  0.1012132352 -1.5391756419 -0.0206080419  0.1651793956  0.6883680330
##  [361]  0.2814907273 -0.7954688710  0.3566805280  0.5081493245  0.6991592875
##  [366]  0.2829726416  0.4681728570  0.0869212007 -0.6100949612 -0.2865395481
##  [371]  0.3787218559  0.7760536147  0.5915797479 -0.0707447339 -0.3115866930
##  [376]  0.2285104636  0.5114220510 -0.4352555593  0.0527290735  0.1973879881
##  [381] -0.6346370196  0.2597163503 -0.1360407498  0.8474751068 -0.0461583669
##  [386] -0.5844947184  0.2535763166  0.3188582161  0.0881776765 -0.6046494425
##  [391]  0.0616971940 -0.2785002572 -0.4348141375  0.5722978100  0.7521624846
##  [396]  0.8975923486  0.3512268882 -0.2017077459  0.5401420785  0.0929727853
##  [401]  1.0189662508  0.5646270766  0.3074337378  0.2137223990 -0.1953072052
##  [406]  0.6595597085  0.0213715848  0.0455755374  0.4536842486  0.0342218339
##  [411]  0.5547088888 -0.2219376549  0.2989918010 -0.2142263059  0.1808899712
##  [416]  0.1827971790  0.8556151264 -0.3869706579  0.3970562062  0.9449067884
##  [421]  0.3954866396 -0.0028743679  0.5800717885  0.3522466746  0.2346352450
##  [426] -0.3825822269 -0.7359112011  0.1450341871 -1.2153978398  0.6638075332
##  [431] -0.0322218393  0.1518622123  0.6674692165  0.3832984312 -0.0774638671
##  [436]  0.6013239240  0.3438191447  0.3097449637  0.7184840588  0.9387896623
##  [441] -1.1942838853 -1.5372677581 -0.0702792737  0.1442863806 -0.7116045712
##  [446]  0.1964873817 -1.1109524303 -0.2189191886  0.4401633197  0.0886309532
##  [451] -0.5275522736 -0.4853043975  0.5090538343  0.6961888880  1.1239527722
##  [456] -0.4631491611  0.0080602240  0.2858617123  0.4016582772  0.6327129393
##  [461]  0.4302705527 -0.2277642256  0.8008562623 -0.5049809475  0.7092841692
##  [466]  0.2857938494  0.2223709931  0.0729135719  0.6345929685 -0.1631378584
##  [471]  0.8388496286 -1.2540270698  0.6606090922  0.2600907595 -0.4136573430
##  [476]  0.1021687440 -0.8473946440  1.4113700198 -0.2613360568  0.0479946980
##  [481]  0.0410792499 -0.8302800260  0.2236068422 -0.8782237962  0.4155887596
##  [486] -0.0492679675  0.4696691290 -0.1430950902 -0.7506320302  0.1837760897
##  [491] -0.2944436045  0.5540844827  0.4666919727  0.4512620834  0.1560202144
##  [496]  0.1473795442  0.9778866905  0.0781186658 -0.0010690625  0.7946460173
##  [501]  0.4545536186 -0.9370957783 -0.3144712268  0.4740811342  0.1049595517
##  [506]  0.3102066185 -0.4872647200  0.0414169664  0.1397563475 -0.2606468804
##  [511]  1.1839883369  0.2289455870  1.3793350053  0.7715143843  0.5321362292
##  [516]  0.4573739796 -0.4917345443 -0.8718493236  0.5143407185  0.3313632372
##  [521]  0.2222026808 -0.5969551328  0.2453430350  0.4356270671 -0.2109843981
##  [526]  0.0554823953  0.2910278789  0.8035792390  0.6257087648  0.3173162188
##  [531]  0.6071870143 -0.1506826326  0.7796653385  0.8678324937  0.0750523103
##  [536]  0.6302455240  0.5007268695  0.3765738831  0.4377931596  0.4319617374
##  [541] -0.2283203335  0.6555188180  0.3324690647  0.6154443370 -1.2513506619
##  [546]  0.4674869264  0.5098498218  0.5533312897 -0.0974052140  0.3970199094
##  [551] -0.0187647090  0.3396106414  0.5737436474  0.0008943267  0.8542345361
##  [556]  0.1192481910  0.7822370615 -0.7214057835  0.5499541359  0.6746805768
##  [561] -0.4871827123 -0.2356598477  0.1163282511  0.6385393173  0.3917635371
##  [566] -0.2418811100 -0.0641790886  0.5485067221  0.1146777125  0.7189287057
##  [571] -0.3830944660  0.2302550081 -0.1332066778  0.2521493390  0.2198868946
##  [576]  0.7471754142  0.1136808681  0.0130062068  0.1974149784  0.9345169311
##  [581]  0.4966247669  0.3456962290 -0.8161106772  0.5395675864  0.6991592875
##  [586]  0.1068089522  0.4631477739  0.1237226532  1.2075140544 -1.5390304125
##  [591]  0.3988590105 -0.2891051716  0.2743041900 -0.4992616748  0.2351514202
##  [596] -0.3860524067 -0.4064586776  1.2753494150  0.4003258090 -0.2316698232
##  [601]  0.3111979314 -0.4237031209 -0.8869026158  0.8266982144  0.4468272799
##  [606]  0.4426144486  0.6138916552 -0.2542184636 -0.0141067627  0.4639250029
##  [611]  0.2825052502 -0.2610281283  0.2080626910  1.3669548887  0.3976652153
##  [616]  0.0291365249  0.2812569942  0.5720465973 -0.3921484191 -0.0833750253
##  [621]  0.9996959560  0.1772905635 -0.5782866518 -0.2204870230  0.2077483197
##  [626] -0.6404786891  0.7528160869 -0.9075318493  0.2720166880  0.5884996357
##  [631]  0.1026256197  0.0463581891 -0.1171853639  0.7109667546 -1.5541255807
##  [636]  0.5012795167  0.1988808974  0.3321773620 -0.9527414788 -0.1471571240
##  [641] -0.4049258604  0.0710149509 -0.6980732659  0.2977119778 -0.6828059477
##  [646]  0.0940141864  0.1988856298 -0.6864655473  1.3723303999 -0.3200800932
##  [651]  0.2221796852  0.6884406305  1.1351306557  0.3703499584 -0.1566271093
##  [656]  0.4661253018 -0.5280548870  0.2777668773  0.8351457359 -0.1532026642
##  [661]  0.6266049861 -0.0285355848  0.4953153802  0.3208360777 -0.2328161223
##  [666] -0.3361168956  0.7090832421  0.3238953195 -1.1135317970  0.2836258195
##  [671]  0.8413894907  0.0985919365 -0.3744115365  0.2680124095  0.1005184973
##  [676]  0.6156032725  0.1270221086  0.3266699401 -0.0908785219  0.8641026663
##  [681] -0.0114470388 -0.4714271349 -0.1124850191  0.6630894610  0.4675596978
##  [686]  0.6557919677 -0.1141438765  0.0777530647  0.4034266747  0.4266077410
##  [691]  0.5529884807 -1.1190519220  0.9731760454 -0.9402773316  0.1244447816
##  [696] -0.1372455662 -0.4297944905  0.4596910778  0.3616424360  0.0365636438
##  [701]  0.4502533031 -0.7902928092 -0.4803680799 -0.1564052433 -1.7892844159
##  [706]  0.3948499973  0.8057336797 -0.3076867296 -0.0881729762 -0.1633622593
##  [711]  0.1447776390 -0.2405693594 -0.2253078418  0.3154614258  0.1673537969
##  [716]  0.2105294059 -0.0334540816  0.5680312727 -0.4315668289  0.6415119628
##  [721]  0.5636514001 -0.0711063306  0.0114666320  0.1209179505  0.3474656466
##  [726]  0.0601886615  0.0577517609 -0.0914484318  0.9968590788  1.1454850381
##  [731]  0.9568790006 -0.3599725951 -0.6192385148 -0.2617662842  0.8765766046
##  [736]  0.1638537192 -0.0729688437  0.3707082533  0.2531765182 -0.3843087319
##  [741] -0.9477243521  0.5375029521 -0.6033616585  0.1511900162 -1.2573316298
##  [746] -0.2293410068  0.2270996437 -0.4866926508  0.2258850954  0.6012329434
##  [751]  0.4297677084  0.0365636438  0.3073876550  0.3960902794 -0.3218371957
##  [756]  0.1513245852 -0.5373172969  0.5179912022  0.7770991688 -0.0211746840
##  [761]  0.0082461235  0.0392961999 -0.0098541937  0.5242664554  0.0294189179
##  [766]  0.4590898049 -0.5723784279  0.9493353668  0.4484904178  0.1960842990
##  [771]  0.2935581875 -0.1564052433 -0.0875791482 -0.5187080189 -0.3874575870
##  [776]  0.1514620360  0.0022127229 -0.2119110565 -0.1754681455 -0.8739389130
##  [781]  0.2400387682  0.6164623226  0.3579166506 -0.3476754644  0.4870210557
##  [786]  0.0297041734 -0.2900925023  0.0726476780  1.1810350251  0.4701273269
##  [791]  0.0496834790  0.4214579079  0.1289863337 -0.3912417134  0.2893124142
##  [796]  0.8711849368 -0.1424841001  0.2765709655  0.6872705157 -1.5801574504
##  [801]  0.6947199300  0.1685657018  0.8367525232  1.3123612622  0.8333509668
##  [806]  0.1520014157 -0.1397974507  0.4227282327  0.2516924899  0.2729414954
##  [811] -0.3518332409  0.1556928429 -0.0303435988  0.4282327660  0.7411962222
##  [816]  0.2481509997 -0.5063300451  0.0866183945  0.6683550398  0.0356539876
##  [821] -0.7216598467 -0.7411570957  0.6390659156  1.2034680271  0.6591411622
##  [826] -0.3535223865  0.2129843855  0.0515942176 -0.3020965721 -0.6329834394
##  [831]  0.9445769868  0.5312311940  0.5196035754  1.7073917411 -0.3841160891
##  [836]  0.0828158300  0.3029457584  0.3001445685 -0.3953234945 -0.0662451868
##  [841]  0.6881480364  0.2483228456  0.5242664554  0.1821076645  0.0213245569
##  [846] -0.1636565626  0.3658405358 -0.4841901671  0.1448034538 -0.9736024318
##  [851] -0.8993115986 -0.0624767084  0.7822935374  1.2902792827  0.8185865640
##  [856] -0.4912727815  0.3662306186 -0.3388976300 -0.4078086716  0.1988141299
##  [861]  0.2680124095  0.5820102881 -0.2166681158  0.6425177148  0.0886801651
##  [866]  0.0970429444 -0.0622831162 -0.0054980181  0.3239314176  1.3903177327
##  [871]  0.1727501929  0.5954782599  0.5825390752 -0.1976949555 -0.2489429497
##  [876]  0.3434398753 -0.0554137798 -0.2884148719  0.8176692388 -0.6559174285
##  [881]  0.2705690687 -0.5899779816  0.1853695471  0.7965183766  0.4581786603
##  [886]  0.1771296979 -0.2636952351  0.2885721696 -0.1889104298 -0.1937473337
##  [891] -0.1786095584  0.1502812838  0.1160558423 -0.5256429369  0.5343251289
##  [896]  0.9243796759 -0.9414366037 -0.7935519914 -0.1620555427  0.3527802004
##  [901]  0.5858963486  0.2577795140  0.5643195573  0.3874355895 -0.0582774523
##  [906] -0.3119003160  0.3864045160  0.0113424843  0.1710056378  0.6292061302
##  [911]  0.3711768518  0.5904748773 -0.4576566316 -0.2772299993  0.3573331844
##  [916] -0.9079063441 -0.3057269429  0.8562921447  0.5402988094 -0.0255374858
##  [921]  0.0186460411  0.8384263600  0.0324393311 -0.0180964698  0.7679150268
##  [926]  0.5208014763 -0.0233976878  0.0601357257  0.1800228009  0.6036444015
##  [931] -0.5937840183  0.6038299564  0.5642877285  0.8511397943  1.1078368517
##  [936]  0.4718216069  0.5580877305 -2.0584760062  0.4864291259  0.7844490877
##  [941]  0.5911758382  1.1950386688  0.0055795777 -0.4163587154  0.5262253427
##  [946]  0.2426648098 -0.0935843515  0.6228455652 -0.4714915503  0.5154733411
##  [951]  0.3569124351  0.1063417136 -0.4694571760 -0.4070425447  0.3575961741
##  [956]  0.0920949061 -0.7129405430  0.6726388817 -0.7173707395  0.1837678257
##  [961] -0.3613210993  0.2296033329  0.4684537523 -1.1109524303 -0.0633852123
##  [966]  0.4797572779  0.1156403016  0.6207728256  0.7822844623  0.5585040899
##  [971] -0.0444487216  0.8767013624  0.3375225253 -0.0456505473 -0.1436975285
##  [976] -0.0658291505  0.3221907382  0.0492262409  0.3740910992 -0.0384752473
##  [981]  0.2067653004 -0.4783665180  0.4234965267  1.0337662601 -0.5049809475
##  [986]  0.0829337537  0.5752237154 -0.0721984321  0.0332075672 -0.9374390013
##  [991] -0.4054635390  0.2871221705 -0.9250065420  0.4167598271 -0.3742735428
##  [996]  0.1793484247  0.8021971557  0.9304282503  0.0670684061 -0.0455132246
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

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
fit2
```

```
##       mean          sd    
##   0.56346609   0.24296797 
##  (0.07683322) (0.05432623)
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
## [1] -0.4317706 -0.2819086  0.3026320  1.1422548  0.1160337 -0.1642994
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
## [1] -0.0377
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9050015
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
## t1*      4.5 -0.02022022   0.9224388
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 3 4 5 6 9 
## 2 1 2 1 2 1 1
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
## [1] -0.0309
```

```r
se.boot
```

```
## [1] 0.9502727
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

