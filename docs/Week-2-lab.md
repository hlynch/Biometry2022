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
## 0 1 4 5 8 
## 1 1 1 4 3
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
## [1] 7e-04
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
## [1] 2.801977
```

```r
UL.boot
```

```
## [1] 6.199423
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.8   6.2
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
##    [1] 3.8 4.4 4.6 4.7 3.7 4.0 3.6 4.9 4.2 5.6 2.9 6.0 4.5 6.4 4.8 4.7 5.9 5.2
##   [19] 5.0 3.8 3.1 5.6 4.8 5.4 5.1 4.6 5.4 5.2 5.5 4.9 4.4 4.3 2.7 5.6 5.0 3.9
##   [37] 3.9 3.4 4.8 5.8 4.5 5.0 4.0 5.4 5.2 5.5 3.3 2.8 4.4 6.1 4.1 4.2 4.0 3.6
##   [55] 3.6 4.4 3.8 4.2 5.1 4.1 4.3 5.5 5.3 3.8 3.8 5.0 2.7 5.9 3.5 4.3 4.5 3.3
##   [73] 5.3 4.9 4.9 4.4 4.4 3.1 4.7 4.5 3.9 4.1 3.7 5.4 3.8 4.7 5.1 4.4 4.9 4.3
##   [91] 5.1 4.5 5.4 4.8 5.7 5.2 3.5 4.2 4.8 4.2 5.6 5.8 5.6 4.1 4.7 3.7 6.1 4.9
##  [109] 4.3 4.2 3.6 4.3 6.2 5.0 4.5 4.4 4.3 5.8 4.5 5.3 4.7 4.3 7.3 3.8 6.0 3.9
##  [127] 4.0 4.7 3.6 4.4 3.1 5.9 6.0 4.8 2.1 5.0 3.7 5.3 5.5 4.4 6.0 5.0 5.6 4.2
##  [145] 4.6 4.9 3.4 4.1 5.3 4.6 5.3 3.7 4.1 4.7 4.6 2.4 6.1 6.7 4.6 5.3 6.2 4.7
##  [163] 4.4 4.8 4.7 4.6 4.0 5.1 4.3 3.9 6.1 4.9 4.4 5.3 5.0 3.8 5.3 4.9 5.1 4.6
##  [181] 4.5 3.2 3.9 5.2 6.6 3.6 3.3 3.8 2.4 2.4 4.2 5.2 4.7 4.2 3.6 3.0 4.6 3.1
##  [199] 5.1 4.4 5.3 4.4 4.4 4.2 3.8 4.8 4.6 4.4 3.5 3.0 4.5 3.6 4.1 4.3 5.2 6.5
##  [217] 3.8 3.4 3.5 3.1 4.1 4.7 3.9 4.3 3.4 5.2 4.7 2.7 4.5 3.7 4.3 3.5 5.2 5.1
##  [235] 5.2 4.0 5.2 3.9 3.8 6.0 5.6 6.1 3.7 3.8 4.0 2.6 5.4 5.5 3.6 5.1 4.2 6.0
##  [253] 5.5 3.5 4.9 3.3 3.8 2.1 4.1 5.8 5.9 4.2 3.0 4.2 2.7 4.8 5.8 4.4 3.4 6.4
##  [271] 6.1 4.5 4.2 5.1 3.8 3.7 3.3 5.1 5.6 5.1 6.3 5.0 3.5 4.4 5.3 5.6 3.7 6.1
##  [289] 4.2 4.8 5.5 3.9 4.3 4.6 5.0 4.5 3.3 4.9 5.8 4.9 5.9 5.1 4.9 3.6 6.3 3.9
##  [307] 5.1 5.2 4.1 5.0 3.5 4.0 4.4 5.3 3.9 4.1 6.7 3.2 3.6 5.4 4.0 6.0 6.7 3.9
##  [325] 5.0 3.6 3.1 4.7 5.9 3.6 3.7 5.1 4.3 3.7 5.0 2.4 3.9 5.2 4.4 4.6 3.8 3.8
##  [343] 3.7 4.6 4.7 4.6 3.6 3.7 1.4 4.6 5.6 3.7 3.7 4.8 4.4 3.1 4.9 5.6 4.4 4.7
##  [361] 5.1 3.3 4.0 4.6 4.9 4.7 5.3 4.9 4.7 3.8 4.2 4.8 3.6 4.7 4.3 5.4 4.2 5.2
##  [379] 6.2 2.2 4.9 4.6 5.1 4.4 3.4 4.3 3.5 3.2 4.2 5.0 5.0 5.7 5.4 2.9 3.3 6.0
##  [397] 3.5 4.4 3.8 3.3 3.5 4.9 4.0 5.0 3.0 4.9 5.8 4.5 2.8 3.7 4.2 3.2 5.7 4.2
##  [415] 6.4 6.2 6.2 6.2 5.3 5.0 4.3 4.1 3.0 3.3 4.5 4.2 3.9 3.8 3.9 4.3 3.9 4.3
##  [433] 4.2 4.6 5.4 3.9 4.0 5.2 5.2 3.8 3.3 4.4 4.8 4.3 2.5 4.2 5.9 4.4 3.4 6.4
##  [451] 4.2 3.8 5.0 5.8 4.1 5.2 3.4 2.8 4.8 5.1 5.1 4.4 4.6 4.8 4.8 4.3 6.1 6.7
##  [469] 4.4 2.9 3.6 5.1 4.1 4.5 4.3 3.7 3.8 5.7 3.6 4.1 6.4 4.3 4.7 4.4 4.7 5.0
##  [487] 3.4 4.6 4.4 3.7 3.2 6.3 3.4 5.1 3.0 3.5 5.3 4.8 3.3 5.5 5.0 3.7 5.7 5.7
##  [505] 3.3 4.8 3.8 4.3 5.6 5.2 3.4 7.3 3.6 5.7 3.5 3.9 2.8 5.1 4.9 2.6 4.2 5.4
##  [523] 4.7 3.5 4.7 4.5 6.4 4.4 4.7 5.7 3.6 5.2 4.6 5.0 3.6 4.5 4.1 5.2 6.3 5.8
##  [541] 3.9 4.5 2.6 3.4 3.4 3.9 5.2 4.3 3.7 4.9 5.0 5.5 4.4 2.2 5.4 4.5 3.2 4.9
##  [559] 6.4 4.0 4.1 4.2 4.5 4.6 5.0 3.6 3.1 5.0 4.6 3.8 3.3 5.5 4.4 3.6 4.1 5.5
##  [577] 4.2 3.6 4.6 5.2 4.4 4.6 2.9 5.6 6.0 5.6 5.7 4.7 4.0 3.8 4.2 4.5 3.9 5.2
##  [595] 4.1 3.5 3.0 4.1 5.0 3.8 3.6 4.1 4.8 4.2 3.2 3.4 5.0 4.6 4.6 3.8 4.2 5.2
##  [613] 4.6 5.0 5.4 3.0 4.4 6.4 4.7 4.2 5.9 4.8 4.5 5.4 3.4 4.6 5.7 5.6 4.2 4.4
##  [631] 4.5 3.0 3.9 4.3 6.1 4.6 5.9 4.8 4.3 4.0 5.9 3.6 5.1 5.2 5.9 4.4 4.1 3.5
##  [649] 6.8 4.8 4.2 3.7 5.6 6.6 4.2 5.9 4.6 5.2 3.6 3.4 5.7 4.6 3.3 4.3 3.9 4.3
##  [667] 3.5 3.9 4.6 4.4 5.5 5.6 5.1 2.6 4.3 4.7 4.7 5.2 5.6 5.6 3.2 3.2 5.9 3.2
##  [685] 3.5 3.6 5.1 3.3 4.1 3.3 4.0 4.8 4.9 4.3 4.2 4.8 5.2 4.1 3.2 4.5 3.4 5.8
##  [703] 5.5 5.2 4.0 3.7 4.2 4.1 4.1 5.4 4.0 3.8 4.8 3.0 2.9 3.6 4.7 4.9 3.2 4.9
##  [721] 5.2 3.8 4.1 6.3 3.0 3.7 5.2 3.2 4.9 3.0 4.4 4.0 5.2 5.3 2.6 5.3 3.2 2.1
##  [739] 4.8 5.5 2.4 5.3 3.5 2.9 5.0 3.2 3.6 3.7 4.3 2.7 4.0 4.1 5.0 5.1 4.2 3.0
##  [757] 5.1 4.1 5.1 4.3 4.6 5.7 5.2 4.3 5.3 4.8 4.1 4.2 4.5 5.3 4.4 4.2 4.8 4.8
##  [775] 4.9 4.4 4.8 3.4 3.6 3.9 3.6 3.1 3.0 5.7 5.6 4.0 4.4 5.2 4.5 3.7 5.3 4.9
##  [793] 5.2 3.4 4.1 4.6 5.9 4.2 3.5 4.8 5.0 4.7 4.3 5.3 4.3 4.9 6.0 5.0 3.5 3.8
##  [811] 4.9 3.8 3.9 5.3 3.4 3.8 4.6 3.6 4.9 4.9 4.6 4.0 3.6 3.7 5.8 3.2 4.4 4.5
##  [829] 5.3 3.0 4.5 2.9 3.1 5.3 4.7 3.0 3.7 4.4 2.6 4.3 4.8 3.8 5.0 5.3 4.7 3.8
##  [847] 4.5 4.4 5.5 3.0 4.4 4.1 5.6 6.0 4.1 6.7 4.5 4.2 4.5 4.0 4.6 5.4 5.1 6.4
##  [865] 3.7 4.4 4.3 5.9 3.4 5.1 4.8 3.8 3.6 5.4 5.1 3.8 5.6 5.4 6.1 3.8 3.2 4.3
##  [883] 5.4 4.2 5.6 2.6 4.7 5.0 4.5 3.2 3.8 4.6 4.7 6.0 4.0 6.5 3.4 5.0 3.9 4.4
##  [901] 4.7 4.8 4.6 5.2 4.5 3.0 4.7 3.1 4.5 4.0 5.0 5.8 5.1 5.8 5.6 5.2 3.2 5.0
##  [919] 4.7 5.5 5.8 4.2 3.3 2.6 5.5 3.6 3.1 5.0 4.6 5.3 4.7 4.1 5.3 5.2 3.4 4.5
##  [937] 5.6 5.4 3.3 3.7 4.5 2.7 3.7 5.9 4.5 5.6 6.0 3.5 4.2 4.2 5.1 6.0 4.4 3.6
##  [955] 3.7 5.0 5.0 4.3 5.9 3.9 5.2 4.6 3.8 5.7 4.6 5.2 4.7 3.9 5.4 5.1 6.6 5.2
##  [973] 3.0 5.5 3.7 5.2 4.8 5.2 5.2 4.6 5.1 3.7 5.9 5.4 4.6 3.9 4.4 4.7 5.0 4.9
##  [991] 4.7 4.8 4.0 4.8 3.4 4.7 4.8 2.8 6.6 4.8
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
##    [1] 5.9 3.5 4.7 6.3 5.2 4.7 5.5 5.5 4.5 5.2 4.4 5.3 5.2 4.0 5.2 4.5 4.9 4.6
##   [19] 4.6 4.4 5.0 4.9 3.9 5.8 5.2 4.8 4.0 5.5 5.4 3.6 4.5 5.5 5.2 4.8 4.2 4.2
##   [37] 4.3 5.6 3.2 6.1 6.4 3.9 5.0 4.7 3.5 4.0 4.5 3.7 5.7 4.3 3.0 4.7 4.7 4.7
##   [55] 3.0 2.2 4.3 3.6 3.6 4.9 3.3 5.0 4.1 6.1 4.8 3.3 2.7 6.3 6.5 4.4 3.6 4.1
##   [73] 5.4 4.8 4.1 4.0 4.2 4.7 3.3 2.6 4.7 5.3 4.4 5.3 5.0 5.8 4.7 3.4 3.7 4.8
##   [91] 3.3 3.7 4.2 3.7 2.7 3.6 4.0 5.4 4.4 5.7 4.8 3.2 3.3 5.2 5.1 4.5 3.2 5.5
##  [109] 4.5 2.9 3.9 5.9 3.5 4.0 5.4 4.9 6.6 5.6 2.8 4.0 4.1 4.6 3.7 3.9 3.2 4.4
##  [127] 5.6 5.6 6.3 4.6 5.2 5.3 4.9 3.7 5.4 5.5 3.7 4.4 4.8 4.5 5.5 4.1 4.7 3.8
##  [145] 4.8 4.3 2.5 5.5 4.9 4.0 4.8 5.0 5.8 3.6 4.5 2.9 3.5 5.4 4.9 5.0 4.5 4.9
##  [163] 4.5 4.9 4.2 3.4 5.4 3.9 5.7 4.9 4.5 3.2 3.2 3.6 4.8 5.4 3.1 4.2 5.2 4.9
##  [181] 4.2 3.3 3.8 4.9 4.3 3.7 6.4 5.0 6.3 5.4 4.9 4.5 4.3 5.1 4.7 4.1 4.0 5.6
##  [199] 5.4 4.4 6.2 4.0 5.7 6.0 3.8 4.8 5.2 4.0 4.8 4.7 3.1 5.1 4.8 5.0 4.5 5.2
##  [217] 4.3 3.9 4.9 4.8 3.1 3.8 4.4 4.1 5.6 6.6 4.7 4.8 6.7 4.5 6.2 4.5 4.6 4.2
##  [235] 6.1 3.7 4.4 3.9 4.3 4.4 2.9 5.1 3.5 2.6 5.2 6.2 5.2 6.1 5.0 4.8 3.6 4.4
##  [253] 4.3 5.1 5.1 3.3 5.5 4.5 3.1 4.9 4.1 4.5 3.9 3.9 2.9 4.9 4.7 5.1 4.9 5.6
##  [271] 4.3 5.2 5.1 4.4 6.2 5.1 5.9 3.0 4.4 5.0 4.0 4.7 5.3 4.4 3.9 3.3 4.7 4.6
##  [289] 5.0 4.3 4.5 4.4 4.4 5.0 5.7 4.0 4.5 4.0 4.6 5.4 4.7 4.0 3.6 4.0 4.7 3.7
##  [307] 4.1 4.5 4.9 5.0 3.2 4.1 5.6 4.8 5.8 5.2 5.5 3.8 2.8 6.0 4.5 4.9 4.1 3.5
##  [325] 4.6 4.9 4.3 5.2 4.8 3.3 3.9 3.7 5.5 4.8 3.2 4.8 4.0 5.7 4.0 3.4 4.5 5.5
##  [343] 6.3 5.4 5.9 4.1 4.6 4.2 5.2 5.0 3.6 3.3 2.8 5.4 6.1 4.4 6.3 3.8 4.3 3.7
##  [361] 4.5 4.4 4.4 4.8 5.6 4.0 4.0 3.5 4.3 3.4 5.3 4.6 3.3 4.2 3.6 4.1 5.8 4.4
##  [379] 4.5 3.2 3.4 4.9 4.5 5.6 4.9 4.5 3.5 3.4 5.5 4.3 5.3 3.8 4.7 4.9 4.3 5.0
##  [397] 4.9 4.6 4.9 5.2 4.7 4.3 3.3 4.9 7.0 3.7 5.1 5.1 4.9 4.5 2.6 3.1 5.1 4.7
##  [415] 4.8 3.9 4.1 4.3 6.0 3.0 3.6 3.4 5.3 4.5 3.4 5.7 3.8 4.8 3.5 4.7 3.8 3.8
##  [433] 2.5 4.1 4.8 4.9 4.0 3.7 4.5 5.2 4.7 5.9 4.4 4.0 5.7 3.9 4.4 4.1 2.8 4.3
##  [451] 5.0 5.2 5.6 3.2 5.0 4.0 6.4 4.7 3.9 3.7 4.7 4.7 4.8 4.2 3.9 3.2 3.0 4.6
##  [469] 6.0 4.2 3.0 4.1 4.8 4.8 3.3 5.9 4.3 4.1 5.3 5.6 3.8 6.1 3.6 5.4 5.7 4.4
##  [487] 5.3 4.8 3.6 4.3 4.0 5.1 4.8 4.1 4.9 3.9 3.9 3.4 4.4 5.4 5.4 5.6 6.2 4.2
##  [505] 3.6 4.3 4.1 4.1 4.6 5.5 4.0 5.2 4.3 4.1 4.1 5.9 3.8 5.4 5.5 4.9 4.3 5.6
##  [523] 3.3 3.1 4.8 2.2 4.1 4.5 3.6 4.7 2.4 3.6 4.0 4.2 3.4 2.9 6.2 5.9 5.2 4.9
##  [541] 4.1 4.7 3.2 5.4 4.6 5.6 5.5 4.4 5.4 4.8 5.5 3.1 5.7 3.4 5.1 5.7 3.5 4.1
##  [559] 5.1 4.1 3.9 3.9 3.9 4.7 3.7 5.3 3.7 4.1 3.6 4.1 4.3 3.8 4.4 4.1 2.3 5.4
##  [577] 2.7 2.3 6.4 4.1 4.0 4.8 5.6 5.6 4.3 3.9 4.7 4.4 5.0 3.3 4.0 2.9 4.9 5.0
##  [595] 4.3 4.3 4.7 4.4 3.2 5.5 5.1 3.1 5.5 4.9 4.0 3.3 5.7 3.3 5.0 5.0 4.9 4.5
##  [613] 4.7 5.2 4.0 4.0 4.9 5.1 3.0 5.6 2.2 5.0 4.8 3.9 4.6 5.0 4.7 4.6 3.9 3.5
##  [631] 4.6 3.4 3.3 4.8 3.6 2.4 4.1 3.8 7.3 3.0 5.9 5.6 5.5 6.1 3.8 4.4 4.7 3.3
##  [649] 4.0 3.7 4.5 3.1 5.3 4.9 5.5 6.3 5.7 5.3 4.4 3.7 5.5 4.6 3.9 4.9 4.9 6.4
##  [667] 3.7 6.2 3.3 3.6 5.1 5.0 5.8 5.0 3.6 3.5 2.6 6.1 5.4 4.8 4.9 4.6 5.8 4.2
##  [685] 4.4 4.9 5.6 4.0 5.6 4.4 4.9 5.5 2.4 4.8 4.2 3.0 4.2 5.1 3.2 4.5 5.9 5.5
##  [703] 4.6 4.9 4.5 3.9 4.3 4.8 4.4 4.3 4.4 4.4 4.3 4.5 5.5 4.3 3.9 6.5 2.7 4.1
##  [721] 5.3 3.2 4.6 7.1 2.5 5.3 4.1 4.0 5.8 4.4 4.7 2.8 3.8 4.6 4.7 4.9 4.9 4.0
##  [739] 4.8 2.5 4.2 4.6 3.6 3.1 4.7 5.8 4.8 3.6 5.6 4.4 4.9 4.4 1.9 3.4 3.8 4.0
##  [757] 5.9 5.8 3.6 4.9 2.8 5.3 3.7 3.5 3.9 4.5 3.9 5.0 4.6 3.6 5.2 4.9 4.6 4.2
##  [775] 2.3 5.5 5.9 5.3 3.9 3.6 3.7 4.1 4.6 4.1 4.2 5.0 4.0 5.3 5.5 3.2 4.2 6.1
##  [793] 4.6 5.5 5.1 3.8 3.9 5.2 4.9 5.6 3.2 5.1 5.1 3.4 3.8 2.8 3.2 4.4 3.3 4.2
##  [811] 3.7 3.5 2.0 4.4 5.3 5.3 6.3 4.5 3.1 4.3 5.5 5.1 4.0 3.6 4.4 5.1 2.8 2.8
##  [829] 5.7 4.9 5.6 4.2 5.8 6.5 4.3 6.2 4.0 5.1 4.1 4.8 3.6 3.7 5.2 5.7 4.4 3.2
##  [847] 4.2 5.3 5.5 4.1 4.3 5.3 4.3 4.0 3.9 4.8 4.4 6.2 6.5 5.7 4.1 5.0 5.9 4.1
##  [865] 3.1 5.9 5.6 4.3 4.8 5.3 4.1 5.8 6.3 4.4 5.3 4.0 4.6 3.6 3.5 4.9 5.0 5.4
##  [883] 3.9 2.9 4.5 4.7 4.7 3.8 4.3 4.2 5.1 5.9 3.2 4.2 3.0 5.3 5.8 3.5 2.5 3.8
##  [901] 5.4 3.7 5.9 6.1 3.4 6.2 4.4 4.2 3.3 3.6 5.3 4.0 3.5 4.1 4.6 4.6 3.7 4.7
##  [919] 4.7 4.2 3.2 4.3 4.2 6.2 3.5 6.0 4.3 4.1 4.4 2.9 5.8 6.5 3.0 5.9 4.4 3.5
##  [937] 4.9 5.4 4.8 5.5 6.0 4.2 4.2 4.5 4.0 4.6 5.2 3.9 5.1 4.5 5.9 5.0 3.2 4.7
##  [955] 3.8 3.3 4.7 5.4 4.4 6.7 2.5 4.3 4.5 3.4 4.0 4.6 3.6 4.1 5.8 4.9 5.3 3.8
##  [973] 4.5 4.7 4.2 3.9 3.5 4.2 3.9 5.8 4.5 5.9 5.6 3.8 4.0 5.8 5.2 5.6 4.7 3.9
##  [991] 5.2 5.8 4.0 5.6 3.6 4.1 4.9 4.4 5.9 5.7
## 
## $func.thetastar
## [1] 0.0124
## 
## $jack.boot.val
##  [1]  0.49368132  0.44017094  0.35478261  0.15796703  0.07219101 -0.07920228
##  [7] -0.18609467 -0.25837838 -0.46442308 -0.41513353
## 
## $jack.boot.se
## [1] 0.9894818
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
##    [1] 3.8 3.7 3.7 4.1 4.6 4.9 2.6 5.5 5.2 4.9 5.9 4.2 5.4 4.2 3.0 3.2 4.8 4.5
##   [19] 3.1 4.8 5.9 5.3 3.7 5.6 5.6 5.0 4.7 4.7 3.7 4.5 3.3 3.6 4.9 4.0 6.2 4.7
##   [37] 4.4 4.5 3.8 5.6 5.7 4.1 4.9 2.5 5.2 4.5 4.1 5.1 3.6 5.4 4.7 4.9 5.0 3.7
##   [55] 4.9 5.6 3.3 2.5 3.9 4.2 5.7 5.0 4.2 5.4 3.0 6.3 4.1 5.2 5.0 5.1 5.2 4.4
##   [73] 5.1 5.7 4.2 2.2 3.7 4.4 3.2 4.1 3.8 3.5 4.4 5.6 5.5 3.9 4.6 3.2 3.9 3.9
##   [91] 2.5 4.8 3.1 2.8 4.0 6.5 3.9 4.8 4.7 4.9 5.0 4.4 4.5 4.5 2.7 3.0 4.3 5.8
##  [109] 2.7 4.9 5.8 5.5 6.3 5.9 3.9 5.1 5.1 4.3 5.5 5.3 4.1 5.3 4.4 4.7 4.7 3.5
##  [127] 4.8 2.7 5.5 4.5 4.5 4.0 4.8 5.6 4.5 4.0 5.8 3.2 4.3 5.0 6.7 4.5 5.1 4.5
##  [145] 4.6 4.8 4.2 3.3 4.7 4.2 5.7 4.1 4.7 3.9 3.6 3.8 5.6 5.6 3.1 4.6 3.8 5.9
##  [163] 4.7 4.0 2.7 4.5 3.7 3.7 5.5 2.7 3.4 4.4 3.3 4.1 4.1 4.6 5.0 5.3 4.4 4.6
##  [181] 4.9 4.1 4.1 2.1 4.7 6.1 4.7 4.6 4.7 6.7 5.0 4.7 4.4 4.6 4.9 4.5 3.3 7.2
##  [199] 5.5 6.0 3.0 4.2 3.4 2.4 4.5 4.6 5.6 5.5 4.5 4.5 4.2 2.6 5.2 3.9 5.1 5.4
##  [217] 2.7 4.4 4.1 4.2 2.5 4.1 4.0 5.0 5.4 3.4 4.2 5.2 3.7 4.6 3.9 4.6 3.5 4.3
##  [235] 4.3 5.3 4.4 5.3 4.6 2.9 4.5 5.4 3.8 5.7 5.5 4.0 4.6 4.1 5.1 5.4 5.6 4.8
##  [253] 4.3 4.8 4.9 3.9 5.2 6.1 3.2 4.6 5.2 3.8 4.9 4.5 4.0 5.2 4.5 3.6 3.0 3.4
##  [271] 4.0 4.2 6.1 6.5 4.5 4.0 4.8 5.8 4.5 3.9 4.3 3.7 4.9 4.0 5.2 5.9 4.1 4.3
##  [289] 4.7 4.8 3.9 4.0 6.0 4.5 4.0 3.4 4.7 3.7 5.3 4.1 4.4 5.9 3.9 4.7 2.7 4.6
##  [307] 5.9 6.6 4.5 4.2 4.4 5.2 4.2 4.8 5.4 3.2 4.1 4.6 5.4 6.7 5.4 4.2 3.9 5.4
##  [325] 5.4 4.8 5.5 4.5 2.2 3.9 5.3 5.6 2.7 4.2 4.2 6.0 4.9 5.8 4.1 4.1 4.3 3.1
##  [343] 3.6 4.7 4.8 4.6 4.2 4.6 5.2 4.4 4.7 4.8 3.5 6.0 5.3 4.2 4.6 4.3 4.4 4.0
##  [361] 5.1 6.3 4.5 5.3 6.3 4.1 4.7 3.7 5.5 4.6 6.2 3.7 4.5 2.7 4.4 2.0 5.7 2.6
##  [379] 5.3 5.0 3.6 4.4 4.0 4.5 3.8 4.8 2.9 3.3 4.8 2.9 3.7 5.0 4.3 4.1 5.1 4.6
##  [397] 5.2 3.6 4.2 3.2 4.2 5.6 3.1 5.1 4.5 5.0 4.5 6.6 5.1 4.7 5.4 3.4 3.4 4.2
##  [415] 4.7 3.8 4.1 3.5 5.6 4.0 5.3 3.6 4.7 5.4 4.8 5.0 5.1 5.6 5.5 4.5 5.3 5.5
##  [433] 4.5 4.3 3.4 5.0 3.2 4.1 3.3 4.4 6.1 4.2 5.0 4.9 3.0 4.9 3.7 3.7 4.6 5.5
##  [451] 4.1 3.0 5.4 7.0 2.4 6.0 3.8 3.7 5.0 4.1 4.7 4.3 4.0 5.8 6.3 3.7 3.3 3.8
##  [469] 4.5 5.4 3.4 3.7 2.5 5.0 3.6 3.2 4.4 3.1 4.5 3.3 4.8 4.0 5.5 3.8 4.5 4.5
##  [487] 3.5 4.1 4.5 4.4 5.2 4.0 5.8 3.4 5.3 5.3 4.7 4.5 4.3 5.3 6.0 4.9 5.2 5.3
##  [505] 4.9 4.1 4.5 5.1 3.0 4.0 5.6 3.7 4.1 4.3 5.6 3.5 5.1 3.2 4.2 5.4 4.4 5.1
##  [523] 4.3 5.3 5.5 4.5 4.8 4.9 6.3 5.2 4.8 3.2 5.5 4.1 4.6 5.6 5.4 5.2 5.0 3.8
##  [541] 6.3 6.2 5.1 5.1 4.6 3.7 4.7 5.2 4.6 3.9 5.7 4.1 5.4 5.0 4.1 4.5 5.1 3.2
##  [559] 4.4 3.7 4.3 3.7 3.9 4.4 4.8 3.7 4.1 5.9 5.3 8.0 5.2 3.9 4.4 4.6 4.0 5.4
##  [577] 3.2 4.1 3.9 4.4 4.7 4.8 5.1 2.8 3.7 3.6 5.6 5.1 5.3 4.8 5.4 5.6 5.4 2.3
##  [595] 3.5 5.1 5.2 3.8 5.9 4.8 4.5 3.8 4.3 4.1 4.6 4.6 5.4 4.9 4.6 3.3 3.7 5.1
##  [613] 4.3 4.6 4.5 5.9 5.3 4.3 5.7 4.1 6.3 4.9 3.1 3.4 5.9 4.5 3.8 5.0 4.6 3.6
##  [631] 5.9 3.5 3.6 2.9 3.9 4.0 5.8 5.2 5.8 4.0 5.2 6.3 6.2 6.6 4.7 4.2 5.4 3.7
##  [649] 4.7 1.8 4.5 2.8 3.5 2.3 4.0 5.5 4.0 4.1 4.2 5.7 3.7 5.3 6.2 4.1 3.8 4.9
##  [667] 5.4 4.2 7.0 3.5 3.8 4.4 5.5 5.0 4.2 4.7 4.3 5.0 3.9 4.4 5.2 5.2 3.8 4.6
##  [685] 3.7 4.0 4.8 3.9 3.5 5.5 3.8 3.8 5.1 4.7 5.5 5.3 4.3 4.8 4.0 4.9 5.3 4.8
##  [703] 5.1 4.4 4.3 5.7 5.0 5.3 6.0 4.5 3.9 2.1 3.7 3.8 4.6 5.5 3.8 3.5 5.2 5.8
##  [721] 5.0 3.2 3.6 4.5 5.3 4.5 5.5 4.3 4.1 2.5 4.3 4.8 4.6 4.8 4.7 4.9 4.4 4.9
##  [739] 4.7 3.7 5.1 3.7 3.6 5.1 4.7 4.7 4.3 5.4 3.4 3.7 4.8 4.1 5.0 4.1 4.4 4.1
##  [757] 5.0 3.6 5.0 5.9 4.1 3.7 4.8 4.5 5.5 4.6 4.3 5.0 3.0 2.9 3.4 4.8 4.1 4.0
##  [775] 5.5 4.9 5.7 5.2 3.0 5.0 4.9 4.8 3.9 4.6 4.7 3.6 4.9 4.3 3.4 3.7 3.4 5.6
##  [793] 3.4 4.2 2.4 5.6 5.7 3.9 4.8 4.9 3.6 4.7 4.3 5.6 6.0 4.4 4.0 6.1 3.0 3.6
##  [811] 4.9 4.8 5.5 3.3 4.0 3.6 3.8 3.8 4.2 3.0 4.6 4.4 3.4 4.5 3.4 4.0 4.0 6.3
##  [829] 5.1 4.1 3.8 4.4 5.0 5.5 3.5 4.1 4.2 4.4 5.0 6.1 5.6 4.2 6.0 6.7 5.1 3.9
##  [847] 3.1 3.5 6.0 3.5 3.1 4.7 3.8 3.9 3.8 4.0 5.3 4.4 5.1 4.1 3.1 4.3 6.4 3.2
##  [865] 5.4 5.2 3.2 4.4 5.0 4.2 3.6 4.7 5.0 2.9 3.4 5.6 3.8 3.5 3.9 2.2 5.1 4.5
##  [883] 5.0 3.9 4.7 5.0 4.9 4.0 5.2 4.4 4.1 4.1 5.7 4.7 4.5 4.0 3.1 4.2 4.3 4.3
##  [901] 5.6 5.4 6.4 5.0 4.6 4.8 3.8 4.3 4.2 5.9 6.4 3.4 4.5 4.8 5.6 3.3 5.3 3.3
##  [919] 5.2 4.8 3.4 4.7 4.0 3.4 4.4 4.0 4.9 6.5 3.9 4.3 3.8 5.5 3.9 4.4 6.2 5.5
##  [937] 4.6 5.6 4.2 6.4 5.1 5.3 2.6 3.8 5.4 6.2 3.7 4.2 4.9 4.1 5.1 5.4 3.5 4.8
##  [955] 3.8 5.2 4.7 5.1 6.2 2.9 5.6 4.4 4.0 4.2 3.9 3.2 3.4 5.2 3.5 3.7 5.6 4.0
##  [973] 5.8 4.5 4.3 5.6 5.4 5.2 4.5 4.8 4.4 4.6 4.8 5.2 4.3 4.4 2.5 4.0 3.9 5.6
##  [991] 4.8 5.2 5.0 4.6 5.5 4.6 5.4 4.2 4.8 2.8
## 
## $func.thetastar
##   72% 
## 5.028 
## 
## $jack.boot.val
##  [1] 5.5 5.5 5.4 5.2 5.2 4.9 4.9 4.8 4.6 4.6
## 
## $jack.boot.se
## [1] 0.9877247
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
## [1] 0.9096572
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
##   1.5352937   2.6045733 
##  (0.6261468) (1.2530774)
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
## [1] -0.26835990  1.50860586 -0.03741104  0.45193945  1.81117127  1.36825373
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
##    [1]  1.155554308  0.849721025  1.340397170  1.109621130  1.219987004
##    [6] -0.496583726  0.097006104  0.345274708  0.447183836  0.921831135
##   [11]  0.280554479  0.541877397  1.304968853  1.540284821 -1.041153856
##   [16]  1.719261247  1.074648444  1.856787727  0.459750782  0.861480622
##   [21]  1.165001121  1.354262829  0.909657220  1.165558710  0.380035730
##   [26]  0.738795776 -0.179639501 -0.160851689  0.525133375  0.216494688
##   [31]  0.918214149  1.404331403  0.698560544  1.059493965  0.253575249
##   [36]  0.739746303  0.457771489  1.701947082  0.615957838  0.574109495
##   [41]  1.652842859  0.146315247 -1.887158475  0.886830670  0.832031447
##   [46]  0.642209542  0.859941274  1.787592976  0.801714757  0.929406783
##   [51]  0.241197085  0.853279916  0.517811588  0.295900831  2.003593809
##   [56]  0.859086456  0.178050596  0.524608384 -0.451635121  0.664017158
##   [61]  0.742338163  1.009991270  1.313590364  0.651738920  0.773896507
##   [66]  0.186163928  0.257073366 -0.058843104  0.901128954  0.091817657
##   [71]  2.081956535  0.408949237 -0.032807817  0.725101995  0.201735003
##   [76]  1.255740116  0.555962555  0.605770165  0.605079813 -0.236541659
##   [81]  1.108734769 -0.588593033  0.179543019  0.973756926  0.642514664
##   [86]  0.544979395  0.417258333  0.858062614  1.720587266  1.028410513
##   [91]  0.483845987  0.529746116  0.367050799  0.642655066  0.873406127
##   [96]  0.542739121  0.954504699  0.587148497 -0.007777598  1.050531615
##  [101]  0.501971043  1.075510322  1.329134327  0.456706274 -0.126114938
##  [106]  0.357631244  0.666660782  1.176079007 -0.117765814  1.170800417
##  [111]  0.784247964  0.453308803  0.729209324  1.138162436  0.373227590
##  [116]  0.757284679 -0.155870579  0.884589207  0.691179085  1.148203950
##  [121]  0.795052406  0.638888189  0.487837086  0.661842917  0.828884622
##  [126]  0.628972698  1.637054622 -0.182018056  0.133036964  0.572839707
##  [131]  0.440742100  0.775880768  0.856390409  1.899827423  1.150354918
##  [136]  0.441013039  0.340924510  0.203727084  0.930037734  0.961948291
##  [141]  1.040124925  0.849920497  1.366153969  0.732042345  0.806071971
##  [146] -0.480566476  0.263001286  0.376665472  0.676628070  0.076840692
##  [151]  0.019572012 -0.023053493  0.255161029  0.623824536  0.498492872
##  [156]  1.309008743  0.574939452  1.911054512  0.395733998  0.154506543
##  [161]  0.600383114  1.026755354  0.292340486  1.337902364  0.611782656
##  [166]  0.592911447  0.346608316  1.066666357  1.451656776  0.662380460
##  [171]  0.993752853  0.356103900  0.061261030  0.540388767  0.939052827
##  [176]  1.174581939  0.351852429  1.887202235  1.070050196  0.674464868
##  [181]  1.322605567  0.296189624  0.312129422  0.772475989 -0.721911438
##  [186]  0.324702421 -0.351614398 -0.118780048  1.162120241  1.443825252
##  [191] -0.616548094  0.742781891  0.986766950 -0.584632907  0.308364790
##  [196]  0.596102650 -0.139780223  0.805246181  0.785705490  0.847754917
##  [201]  0.666172858  0.282452282  1.442395766  0.838401804  0.782650008
##  [206]  2.512313303  0.621506961  1.001343527  1.141800237  0.556422894
##  [211]  1.670761362  0.104205581  1.114510138  0.755115700  1.721434473
##  [216]  1.085457481  0.609969074  0.774622634  0.304074294  0.732570621
##  [221]  0.040581242  0.627751489  1.016372429  0.399044155  0.839164808
##  [226] -0.046862122  0.599316515  1.054857675  1.061373112  0.568933435
##  [231] -0.288563222  1.082764758  0.513173040  0.854939281  0.414526839
##  [236] -0.422872262  1.397427891  0.937308586 -0.380196657  0.761314245
##  [241]  0.495683471  0.709961690  1.177205380  0.774363522  0.765876267
##  [246] -0.058843104  1.512995460 -0.371338728  0.767798029  0.581834742
##  [251]  0.771111879  1.336408825  0.291890782  0.249888972  1.106836700
##  [256]  1.031567382 -0.477686551  0.172609229  1.001258050  2.102088849
##  [261]  1.138392298  1.127450604  1.187266126  0.803332880  0.826471144
##  [266]  0.704045798  0.330983874  0.833306309 -0.071570784  0.182540766
##  [271]  0.321983414  0.138364743  1.013437286  0.653260913  1.585881343
##  [276] -0.174981862  0.141121364  0.534075000  0.242483048  0.119047139
##  [281]  0.074044338  1.790335100  0.785427141  0.332518744  1.672645181
##  [286] -1.555107510  1.322365213  0.677940237  0.767725732  1.091153887
##  [291]  0.185502860  0.891010008  1.450254734 -0.071033430 -0.015787758
##  [296]  0.450831584  1.016845221  1.659458583  0.633246736 -0.508574613
##  [301]  0.584714775  2.431885901  1.307906955  1.901396637 -0.135280911
##  [306]  0.969293134  0.834076627  0.309772884  0.802175367  0.941636415
##  [311]  0.142843650  0.555074670  1.550073987  0.210586841 -0.300674603
##  [316]  0.342624334  1.021681367  0.362847976  1.369036777  1.269662620
##  [321]  0.808645724  1.242783869  0.204807310  0.636702723 -0.252874389
##  [326]  0.853948815  0.972777463  0.099472820 -0.015765020  1.001191196
##  [331]  1.204878152  0.412272094  1.171931279  1.282682242  0.362950758
##  [336]  1.174889988  0.374003203  1.427017717  0.389505097  0.516641283
##  [341]  0.546141331  0.182735078  0.102010820  0.401597438  1.133206157
##  [346]  0.825222078  0.283575476 -0.010639626  1.273074618  0.675089062
##  [351]  0.170125690  1.716500777  1.025258638  0.469924735  1.280932744
##  [356] -0.049380848  0.237854656  1.545434451  0.890767262  1.285498107
##  [361]  0.241231364  0.864313647  1.163364795  0.683062543 -0.006880867
##  [366]  0.506844172  0.385631767  1.947401337  0.699484770  0.749544245
##  [371]  0.496635063 -0.446256814  0.532098718  1.041333281  0.909607689
##  [376]  1.103505091 -0.193110218  0.261641221  0.321522509 -0.832924428
##  [381]  0.991249436  0.164202103  0.132967877  0.327116845  0.212852471
##  [386] -0.068422508  0.630525201  1.139703227  1.578481412  0.465821651
##  [391]  0.639652793  1.587232466  1.145033472  0.530417646  1.813660810
##  [396]  1.182978341 -0.440614964  0.710559391  0.495199436  0.872288462
##  [401]  0.543937131  0.856772970  0.508741712  0.903170905  0.723775410
##  [406]  0.976043440  0.512345140  0.464654729  1.271936495  0.792138729
##  [411]  1.739316858  1.821981159 -0.112600779  0.982565285  1.112843259
##  [416]  1.050487616 -0.567091767  0.657199670  0.183164336  2.035626714
##  [421] -0.830688436  2.420589896  0.274906330  1.019133467  1.148923004
##  [426]  1.094629904  1.191555576  0.940828179  0.641114656  0.450301885
##  [431]  0.278715949  0.551021058  0.504869823 -0.009456268  0.629982978
##  [436]  0.822320357  1.660356869  1.508196045 -0.085380554  0.752016443
##  [441]  0.853208288 -0.701739729 -0.031705373  1.270613871  1.586704897
##  [446]  1.270459370  2.371318491  1.155687612  1.271451571  0.809835037
##  [451]  0.823949538  0.591078742  0.562593144  0.836595868  0.998177804
##  [456]  0.379772413  1.293790902  0.437981596  0.724101361  1.082986248
##  [461]  1.795583024  1.564135986 -0.224208726  0.636500319  0.049778723
##  [466]  0.402998363  0.378977571  1.066466166  0.229999839  0.872361438
##  [471]  0.570870432  0.776795814  0.399488827  0.687392742  1.576418009
##  [476]  0.685138458  0.049087150  0.487618229  1.114746075  0.637632731
##  [481]  1.038672257  0.352080693  0.597355984  1.568335586  0.796911948
##  [486] -0.425034843 -0.092081332  1.329716885  0.715314426  1.500823808
##  [491]  0.555663320  1.229632305  1.626406178 -0.285863693  0.172173715
##  [496]  1.254761147  0.461244109  0.279301619  1.567190615  1.054508518
##  [501]  0.627748971  0.813943822  0.582004180  0.480941750  0.802128703
##  [506]  1.076541035  1.314253402  0.988276980  1.438295897  0.851309547
##  [511]  0.866320199  0.983634347  1.231176721  0.946274635  0.369868013
##  [516]  0.723969258  0.642608564  0.518423105  0.445557325  0.790120941
##  [521]  1.030582853  0.150536658  0.571937188  1.794763150  0.677694463
##  [526]  1.675633664  0.860367494  1.179313635  0.203008911  1.353278391
##  [531] -0.124256914  1.169220964  1.484223464  0.042593647  0.809361431
##  [536]  0.952819079 -0.464492359  0.837907527  0.550644488  0.986314675
##  [541]  1.350536568  1.104474622  1.114359599  0.344294089  0.739147234
##  [546]  0.719927068  0.301901209 -0.075880653  0.400535259  0.500189187
##  [551] -0.153508366 -0.198180872  1.293245163  0.501868357  0.871055752
##  [556]  0.741739770  1.251308299  1.587232466  0.467323069  0.562934531
##  [561]  0.614789009  0.306815462 -0.327189900 -0.045117707  0.114796312
##  [566]  0.742953073  0.756434772 -0.392524330  0.659521055 -0.110029605
##  [571]  0.433130637  0.495091052  1.081104784  0.862694829  1.083909713
##  [576] -0.067695739  0.191668128  0.748086991  1.017411486  0.593804094
##  [581]  0.728557122  0.823363332  0.259467713  0.770080325  1.802022512
##  [586]  0.530790352  0.775452938  1.415200895  0.995708047  0.849755171
##  [591]  0.226332365  1.574207564  0.739788337  1.784939529  1.035758573
##  [596]  1.348994595 -0.054013172 -0.252447711  0.605355477  0.657399539
##  [601]  0.220748645  0.640376386  1.146716191  1.035961962  0.435896631
##  [606]  1.450623397 -0.056728083  1.472478704 -0.462970704  0.473679350
##  [611]  0.159140341  0.803119117 -0.020289995  0.570557164  1.123291391
##  [616]  1.811742295  1.272332142  0.518101265 -0.470559011  0.800459363
##  [621]  0.603992189  0.704776655  0.941909440  0.345828436  0.765383320
##  [626]  0.224254927 -0.559775006  0.134189197 -0.145551068  1.015890316
##  [631]  1.382089498  0.076304065  0.356037701 -0.813042764  1.787592976
##  [636]  0.422878377  0.244026742  0.861194307  0.674836209  1.226457874
##  [641]  0.502623428  0.560641748  0.659584966  0.473679350  1.060072736
##  [646]  1.759588327  0.850489645  0.736085892  0.664017158  0.887729446
##  [651]  1.293342382 -0.547326490  0.888574380  0.796195478  0.466020141
##  [656]  0.976105040  0.244964280  1.257193461  0.320046682  0.387199749
##  [661]  0.444850590  0.806316894  0.808982050  1.022598731  0.594728775
##  [666]  0.414295186  0.669342124  1.289495720  0.920836009  1.087930287
##  [671]  0.581890612  0.804711753  0.745612001  0.456108216  0.473712312
##  [676]  0.293016166 -0.292071370 -0.665140648  0.101288674  1.209458608
##  [681]  0.522626492  0.842206888 -0.080467557  1.309999779  0.071127068
##  [686]  0.924997102 -0.200514519  1.015258683  0.668352594  0.179545309
##  [691]  0.432493186  1.693614095  1.661656221  1.010330980  0.451438366
##  [696]  0.667099369  1.061931478  0.689256541  0.239126683  0.618008845
##  [701]  0.787032702  0.792482805  1.063558587  0.838137543  0.245453393
##  [706]  0.591336174  0.239597453 -0.346528450  1.166123534  0.622216420
##  [711]  0.498222221  0.433685761  0.900078656  1.062573110  0.134144776
##  [716]  0.987617579  0.386259006  0.709538751  0.718813647  0.767960418
##  [721]  0.607782749 -1.368780421  0.544802349  0.541784290  0.847175111
##  [726]  0.075944279  1.401251440  0.991410349  1.034413967  0.948997927
##  [731]  0.909595998  1.196419795  0.256925545  0.138283690 -0.512344230
##  [736]  0.621985708  1.179957732  0.165327126  0.418394940  0.607701125
##  [741]  0.195517771  0.847876070  0.540017721  0.866959362  0.857030402
##  [746]  0.852901330  0.821424005  0.623597515  0.749698879  0.713298794
##  [751]  1.308684341 -0.068212410 -1.284072086  0.354999552  1.003468226
##  [756]  1.383357442 -0.637562407  0.615220574  0.868075991  1.226480617
##  [761]  0.507322684  0.311897848  0.209272427  1.338256586  0.695839287
##  [766]  1.000084973  0.428605914  1.119308871  1.406971793  1.104551442
##  [771]  1.192348666  0.714816887  0.934520073  1.216800907  0.202570178
##  [776]  1.009212419  1.045426805  0.908091957  1.121967729  0.656206926
##  [781]  0.630296453  0.199698568 -0.464809452  0.402726772  0.465684188
##  [786]  0.349710445  1.287616653 -0.484854552  0.943436308  1.531826022
##  [791]  0.101929565  0.081307869  1.161406547 -0.165529621  0.149886349
##  [796]  0.536128847  1.296084798  0.467802760  0.263779033  0.447000910
##  [801]  0.262179951  0.828454472  0.317213565  0.428949512  0.119669988
##  [806]  0.750059967  1.029351496  0.146892491  0.690443035  0.599295451
##  [811]  0.693586165  0.713949785  1.326742087  0.737048319  0.365019999
##  [816] -0.060259388  0.762038917  0.053521063  0.859709208  1.554569423
##  [821]  1.242783869  0.920025555  0.128973913  0.370495741  0.225823577
##  [826]  0.547201911  0.394852383  0.769476733  0.868044621 -0.254099882
##  [831]  0.629028593 -0.502980088  0.480701805  0.154768934  0.295309844
##  [836] -0.717856369  0.413257089  1.237197421  1.050641183  0.818458691
##  [841]  0.367050799  0.844022455  0.985485966  0.540207687  0.259840148
##  [846]  0.822144259  0.188081463  0.498037464  0.650672513  0.301037379
##  [851]  0.946334480  1.851671467  0.860619315  0.610289211  0.216166219
##  [856]  0.909855360  0.806390760  0.112151167  0.899866614  1.511419046
##  [861]  1.233343052  0.348141196  1.128459101  0.853684097  1.181456230
##  [866]  1.375838907  1.505516273  0.340924510  0.958924260  0.278666137
##  [871]  0.072931895  0.353551219  0.387665219  0.544237320  0.099407692
##  [876]  0.547194930  0.568733170  0.784753945  0.845484455 -1.255167612
##  [881]  0.874122825  0.177533375  1.069003553 -0.469282066 -0.087944747
##  [886]  0.568183843  0.619817475  0.891269095  0.054070935  0.536338846
##  [891]  0.841353648  0.387068845  0.641709987  0.993852556  1.995723329
##  [896]  0.969131622 -0.720224765  0.707287147  0.022328721  0.664578401
##  [901]  0.854939281  0.237971704  1.444973937  0.769816379 -0.460081105
##  [906]  0.251215265  0.957663655  0.732328407  0.641123307  0.649770687
##  [911]  0.148545051  1.136768720  0.681146414  1.020048954  0.206933245
##  [916]  0.543937131  0.917100042  1.253371062  0.561152438  0.884909270
##  [921]  0.396301757  0.058690144  2.192018713  1.056364361  0.332086780
##  [926]  0.306793473  1.355341445  1.012896645  0.700772005  0.308984525
##  [931]  0.608661805  0.766101690  0.633609306  1.090134115  0.174027509
##  [936]  0.715696726  2.274019534  1.197631372  0.046744973  0.596310498
##  [941]  0.204165304  0.803332880  1.516813807  0.197580483  1.311470650
##  [946]  1.040124925  1.900508410  0.597580918 -0.036110296  1.524011242
##  [951]  1.289911449  0.052111486  0.209223974  0.250365479  1.138420217
##  [956] -0.465977333  0.967575403  1.028716256  0.614325960  0.306503963
##  [961] -0.477744014  0.146692642  0.813232827  0.945063453  0.128845491
##  [966]  0.318926606  1.277011024  0.496772257  0.128369574 -0.406798778
##  [971]  1.146318568  0.380502935  0.658185304  0.485777436  0.966371500
##  [976]  0.596021721  0.030122077  0.595960557  0.810053192  0.130185495
##  [981]  0.716583999  0.459465477  1.637556280  0.742258569  0.548892374
##  [986]  1.161917565  0.773896507  1.008493405  1.017035954  0.399839764
##  [991]  0.793455359  1.401519139  1.637054622  0.550140421  0.824704690
##  [996]  1.140329477  0.649743573 -0.334770335 -0.387725622  0.503386563
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
##   0.58948199   0.44591974 
##  (0.14101220) (0.09970908)
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
## [1] -0.7722531  0.7905960 -0.4378424  0.6549516  0.7590383  0.2237772
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
## [1] 0.0108
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9356596
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
## t1*      4.5 0.001301301   0.9412972
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 3 5 6 7 8 
## 2 2 1 2 2 1
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
## [1] -0.0219
```

```r
se.boot
```

```
## [1] 0.9447979
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

