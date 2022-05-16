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
## 0 2 3 4 6 7 8 9 
## 1 2 1 1 2 1 1 1
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
## [1] -0.0083
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
## [1] 2.736933
```

```r
UL.boot
```

```
## [1] 6.246467
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.2
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
##    [1] 5.6 4.1 5.4 4.7 3.5 4.4 4.2 3.4 4.3 3.4 4.0 4.8 5.0 5.5 3.7 5.4 3.7 3.9
##   [19] 4.5 4.5 4.1 4.7 5.3 4.1 4.9 4.5 4.2 4.8 2.3 5.3 3.5 5.3 2.8 6.7 4.4 4.7
##   [37] 4.1 5.7 3.8 4.8 4.4 5.2 4.5 2.4 4.6 4.7 3.9 3.4 4.8 5.2 3.8 3.5 4.0 3.8
##   [55] 4.5 6.6 4.7 3.5 4.5 3.6 4.7 4.6 2.3 4.2 3.7 4.2 2.9 3.5 3.2 5.5 5.4 2.5
##   [73] 5.7 4.9 4.5 3.8 4.4 5.6 5.1 3.5 6.0 4.9 5.0 2.7 7.3 2.4 5.4 4.5 2.9 5.0
##   [91] 5.3 5.8 5.0 4.9 4.2 3.8 5.4 4.9 4.9 5.1 3.4 4.3 5.2 5.0 4.1 5.1 4.1 4.7
##  [109] 4.6 4.2 4.2 6.1 3.1 5.2 3.6 3.2 3.8 4.6 4.5 3.6 5.7 3.5 3.5 5.3 4.4 4.1
##  [127] 3.1 3.6 3.8 3.4 5.1 3.3 4.3 4.4 2.7 5.5 6.2 4.7 5.5 6.1 4.2 4.9 5.9 4.5
##  [145] 3.4 5.4 3.8 4.6 3.2 3.3 4.6 5.0 5.6 5.3 6.4 4.0 4.6 4.4 3.2 4.0 4.2 4.9
##  [163] 5.2 4.4 3.4 3.1 5.0 4.5 3.4 4.0 6.1 5.6 4.6 5.1 4.4 4.9 6.0 5.0 4.3 4.5
##  [181] 3.8 3.9 2.5 4.7 4.9 5.0 3.5 5.2 2.7 3.6 4.1 5.0 3.4 3.4 4.6 5.8 3.3 5.1
##  [199] 4.9 4.7 5.2 4.5 3.3 4.7 2.8 5.1 4.4 5.0 5.2 2.4 4.9 5.1 3.7 3.1 5.0 4.3
##  [217] 3.6 5.9 3.7 4.2 4.9 6.5 4.1 3.5 4.6 5.1 3.1 1.6 3.9 2.9 3.5 4.2 5.5 5.2
##  [235] 4.1 3.8 3.7 3.7 4.2 4.3 3.6 5.4 4.3 4.7 4.8 5.1 5.3 4.3 3.6 2.9 4.6 5.2
##  [253] 3.7 4.1 5.2 4.4 4.9 5.4 4.2 4.4 3.1 4.7 3.6 4.4 4.8 3.7 3.8 4.0 4.0 3.2
##  [271] 5.3 4.2 5.8 4.4 2.9 5.8 4.2 5.6 5.3 6.4 4.3 5.7 3.3 4.1 5.2 5.5 3.8 4.3
##  [289] 5.4 4.4 3.9 4.1 3.9 5.3 5.3 5.7 5.4 4.3 5.4 4.3 3.2 4.0 2.9 3.7 4.5 5.2
##  [307] 4.5 4.7 3.3 4.1 5.6 4.9 4.9 2.7 3.9 4.1 5.3 6.1 4.8 5.0 5.2 3.1 3.9 3.8
##  [325] 5.7 5.0 5.1 4.6 4.3 5.2 5.2 4.8 2.9 4.4 4.9 5.4 6.4 4.0 4.9 7.2 3.6 4.8
##  [343] 3.6 3.1 4.8 6.6 4.6 3.5 5.5 4.7 4.9 4.7 5.5 4.5 3.8 4.4 3.4 5.1 5.9 6.0
##  [361] 5.2 4.5 5.1 3.6 4.5 5.4 4.9 4.3 3.0 2.3 4.2 5.3 3.2 5.5 4.1 5.3 3.9 4.5
##  [379] 4.4 3.9 6.0 4.7 4.7 6.7 4.6 2.9 4.5 3.6 3.1 3.9 4.6 5.2 3.9 4.3 4.9 4.3
##  [397] 2.7 5.8 5.1 5.7 6.1 5.7 5.2 5.9 3.3 5.8 6.0 3.2 4.7 3.0 2.7 5.3 4.8 4.0
##  [415] 3.1 3.7 5.0 5.0 4.5 4.0 4.2 4.3 4.2 3.9 6.3 5.9 4.6 5.7 4.7 4.6 3.1 5.3
##  [433] 4.5 3.6 5.5 5.0 3.8 4.3 4.5 5.1 4.7 4.5 4.1 5.0 4.4 3.7 4.1 3.7 3.0 4.2
##  [451] 4.6 4.0 3.5 3.0 5.1 4.0 4.9 4.5 4.0 5.8 4.4 3.4 4.8 5.7 4.7 3.9 4.1 3.8
##  [469] 1.9 3.2 4.0 4.2 5.7 4.4 3.5 4.6 3.2 4.5 5.3 5.2 3.9 6.0 4.8 4.8 4.1 5.0
##  [487] 2.7 4.5 4.4 3.6 5.4 3.4 5.8 3.9 4.0 6.5 4.6 4.5 4.7 4.6 3.0 4.0 5.2 4.0
##  [505] 4.8 5.2 4.5 4.3 5.2 4.4 5.7 3.8 5.8 4.1 4.8 3.4 4.3 5.7 4.1 3.9 6.1 5.7
##  [523] 3.9 3.9 4.8 3.7 4.6 4.8 5.2 4.3 4.9 5.8 4.0 4.9 4.1 4.0 3.9 6.7 6.1 3.5
##  [541] 3.1 3.3 4.2 3.1 4.1 6.6 3.9 4.2 5.6 4.0 4.9 4.9 3.9 4.7 3.6 4.7 4.8 3.2
##  [559] 5.2 5.0 3.6 4.7 5.0 3.3 3.9 4.9 4.1 4.2 3.1 5.0 3.3 3.1 3.5 5.5 6.0 4.9
##  [577] 4.8 5.9 3.6 5.1 3.7 5.2 5.1 4.4 3.8 4.7 5.6 4.0 3.0 3.9 3.9 3.8 3.6 4.7
##  [595] 5.1 5.3 5.4 4.1 4.2 4.8 4.5 3.1 5.3 5.0 5.6 4.9 3.9 4.6 2.7 3.6 5.4 4.2
##  [613] 4.1 4.6 3.5 5.5 4.1 3.3 4.8 5.8 3.1 3.0 5.0 4.8 6.7 4.5 5.4 4.1 5.3 5.4
##  [631] 4.0 6.5 4.0 4.7 3.2 4.7 4.5 4.6 5.0 5.3 3.4 4.7 4.5 4.3 5.1 5.1 4.1 4.3
##  [649] 2.6 2.2 6.1 4.7 4.5 5.7 4.1 5.0 6.1 4.7 5.3 3.4 4.1 4.2 4.4 3.2 5.6 3.3
##  [667] 3.7 5.0 6.1 6.0 3.9 4.8 4.2 5.3 4.5 4.1 4.8 3.8 5.4 3.7 4.8 4.7 4.3 5.6
##  [685] 5.9 6.5 4.4 5.2 3.8 4.7 4.3 4.1 5.7 4.4 4.7 6.2 4.7 4.4 3.3 4.9 4.2 6.0
##  [703] 4.8 4.7 3.7 4.2 4.6 4.3 5.2 1.9 4.7 4.4 4.3 4.1 2.7 3.7 7.1 6.8 5.8 4.4
##  [721] 4.9 3.3 3.8 3.0 3.7 5.9 4.2 5.2 3.3 2.7 3.8 3.8 4.9 5.6 3.7 3.4 3.2 3.4
##  [739] 4.2 5.2 4.2 4.1 4.0 4.4 3.9 5.0 4.1 5.3 4.5 5.5 4.9 5.4 5.3 4.8 4.5 2.3
##  [757] 4.1 4.4 4.0 4.0 4.8 5.1 4.8 3.0 4.1 3.9 2.8 4.9 5.1 4.4 3.4 4.0 4.4 5.2
##  [775] 3.6 5.2 4.7 2.8 6.4 2.6 4.0 5.0 4.9 6.0 5.1 3.1 4.7 5.5 4.6 3.7 5.5 6.2
##  [793] 4.9 4.6 2.9 5.6 4.9 2.9 4.1 3.8 4.6 3.6 4.2 4.9 3.7 4.3 3.7 3.6 3.5 2.2
##  [811] 3.5 5.6 5.0 5.9 4.6 4.9 3.9 4.3 4.0 4.4 5.1 4.5 3.3 4.4 3.9 4.1 4.9 3.1
##  [829] 4.2 4.5 6.3 6.1 3.3 5.0 4.4 3.9 4.3 6.0 5.3 4.4 3.4 4.6 3.9 5.4 3.9 4.2
##  [847] 5.5 5.5 3.0 5.1 5.1 4.5 3.1 4.1 5.4 4.8 3.8 4.4 5.2 5.0 5.2 3.9 5.3 3.5
##  [865] 3.0 3.0 5.0 5.4 3.3 4.4 4.3 5.1 3.5 4.4 4.1 4.7 4.5 4.0 4.4 4.5 4.2 4.8
##  [883] 5.7 5.2 3.5 4.5 6.0 2.6 4.9 4.8 5.3 5.7 4.0 4.2 4.6 4.7 3.7 4.9 4.7 5.4
##  [901] 5.1 4.4 4.8 4.3 2.8 5.9 3.4 3.6 4.5 4.7 4.1 4.2 5.0 4.9 3.3 5.5 4.9 5.2
##  [919] 4.9 5.2 3.7 3.6 4.9 3.3 4.4 4.4 4.3 3.5 5.6 4.3 2.5 6.4 4.6 4.5 4.7 3.1
##  [937] 4.3 3.2 4.9 5.0 5.2 5.7 5.0 3.3 4.6 3.3 4.5 5.0 5.0 5.3 4.6 5.5 5.0 5.1
##  [955] 3.4 3.6 4.9 5.2 5.3 4.2 3.6 3.3 4.2 6.3 4.0 5.0 3.5 4.1 3.9 5.3 3.6 2.2
##  [973] 4.1 4.9 5.9 6.4 3.9 5.7 3.1 4.8 4.2 4.4 3.1 5.6 5.8 4.3 4.1 0.9 5.8 4.0
##  [991] 3.9 4.4 3.4 4.5 5.3 5.9 3.7 4.1 4.1 4.2
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
##   2.7   6.2
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
##    [1] 5.6 4.2 4.3 5.5 4.3 5.2 4.2 3.9 5.1 3.5 3.7 4.5 3.7 5.6 4.6 3.6 5.7 6.2
##   [19] 5.9 2.7 4.3 5.2 3.9 2.8 3.7 5.0 4.7 5.0 4.5 3.7 3.7 5.9 4.3 5.3 4.3 3.1
##   [37] 5.0 3.8 4.1 3.1 5.8 4.4 3.8 4.6 4.9 3.1 5.0 5.3 5.1 3.3 5.5 4.7 3.8 6.8
##   [55] 5.1 4.4 6.9 4.8 4.1 4.5 5.3 4.8 5.9 3.5 5.3 5.8 4.5 5.2 5.4 2.6 3.9 3.9
##   [73] 2.5 3.4 4.2 4.0 5.7 5.1 3.7 3.1 4.0 5.1 4.1 5.3 2.9 3.8 4.3 3.0 6.0 4.5
##   [91] 5.1 2.9 2.5 6.5 4.2 5.3 4.4 4.9 4.4 2.7 3.9 5.4 4.3 5.6 3.9 3.5 5.1 4.8
##  [109] 4.7 4.2 6.0 4.6 3.4 5.7 5.8 5.1 5.7 4.8 5.0 5.0 5.4 3.0 4.4 5.8 4.7 4.9
##  [127] 4.3 4.8 4.1 3.5 5.7 3.5 5.2 2.9 5.5 3.6 5.3 3.9 3.6 4.5 2.5 6.6 4.1 5.7
##  [145] 5.2 4.9 4.2 4.2 5.9 3.5 3.7 4.5 3.2 4.5 4.1 2.5 4.4 4.0 4.9 5.0 4.0 3.1
##  [163] 2.8 4.1 6.0 4.0 4.1 5.9 4.5 7.6 7.3 3.7 4.5 4.5 6.7 4.5 4.9 4.0 3.5 4.5
##  [181] 5.9 3.8 4.1 4.4 4.4 5.4 4.2 6.1 4.6 4.6 4.3 5.1 3.5 2.5 3.5 4.1 5.9 5.9
##  [199] 3.9 4.0 4.6 5.2 5.4 4.9 5.5 3.8 4.0 5.2 4.2 3.6 3.3 3.7 5.5 4.1 4.7 4.4
##  [217] 3.6 4.7 4.9 5.7 4.5 4.2 4.5 5.3 4.1 4.5 4.6 4.1 4.2 3.4 4.8 4.3 5.2 5.4
##  [235] 3.4 5.8 4.7 5.7 3.6 4.4 3.3 4.2 4.9 5.3 4.9 4.9 5.2 4.3 4.9 5.1 6.8 4.7
##  [253] 4.2 4.3 4.5 4.9 4.5 4.4 4.6 5.5 4.4 2.7 4.4 4.7 3.8 4.6 2.2 5.5 4.3 3.1
##  [271] 5.7 6.0 3.4 4.5 3.0 5.5 7.2 5.7 5.4 4.7 6.2 5.9 4.2 5.9 4.0 4.0 4.9 5.4
##  [289] 4.7 7.2 4.1 4.0 5.6 5.1 5.6 4.3 5.1 3.3 5.5 4.4 4.0 4.8 3.9 5.0 4.9 4.8
##  [307] 3.8 4.6 3.4 4.9 4.8 2.5 5.3 4.1 4.6 3.9 5.0 4.6 4.9 5.3 5.2 4.6 4.9 4.4
##  [325] 3.8 5.3 3.9 4.6 2.4 4.9 3.9 3.9 4.6 3.9 5.1 4.6 4.4 4.2 4.3 3.3 4.2 4.1
##  [343] 4.7 5.4 5.5 5.4 4.8 3.7 4.3 3.6 2.7 4.6 4.8 4.7 3.0 4.4 4.2 2.8 4.4 4.5
##  [361] 5.4 4.9 3.7 5.5 3.1 5.2 5.1 1.4 5.3 4.0 3.9 5.5 6.1 4.6 4.2 6.7 4.1 5.5
##  [379] 5.4 4.4 4.2 5.6 3.8 2.5 4.9 3.2 4.3 5.2 4.3 5.1 2.2 4.2 5.2 3.2 5.3 5.2
##  [397] 3.8 5.0 5.8 4.1 5.2 5.2 2.8 4.6 3.7 4.8 5.4 5.0 3.9 3.0 5.6 4.5 3.3 5.2
##  [415] 5.7 3.3 5.1 4.9 4.1 4.4 4.3 4.3 5.5 4.6 4.7 3.3 4.4 3.5 4.3 2.5 3.6 3.7
##  [433] 4.9 3.9 5.4 3.9 4.0 4.3 4.2 4.5 5.4 3.8 4.1 4.5 4.4 4.6 4.0 2.7 4.6 4.4
##  [451] 4.3 4.9 5.3 4.4 5.6 3.5 4.7 4.5 4.6 3.7 5.2 3.9 6.1 5.2 3.1 3.7 4.4 4.4
##  [469] 4.9 5.0 3.3 3.4 4.9 5.2 3.4 5.7 5.0 3.5 3.9 5.3 3.8 3.7 6.1 4.1 5.3 4.7
##  [487] 5.2 6.3 5.1 4.8 3.1 4.1 5.6 5.2 3.8 4.0 4.7 2.8 4.6 3.8 3.4 6.0 3.0 3.1
##  [505] 5.2 4.3 6.2 3.2 5.6 4.7 2.8 3.6 4.8 4.2 4.3 5.2 5.0 3.9 7.3 6.4 4.0 4.9
##  [523] 3.6 3.4 4.2 6.2 5.4 4.1 5.1 4.7 5.5 3.8 5.3 3.1 4.3 3.9 4.6 2.7 4.5 3.7
##  [541] 4.1 5.3 5.5 4.8 2.9 5.8 3.6 3.6 4.8 6.5 5.2 4.1 4.9 4.6 2.8 3.5 5.3 3.4
##  [559] 3.5 4.5 4.7 5.5 4.9 6.4 3.9 3.6 4.5 4.2 5.0 4.6 5.8 4.5 4.3 4.4 4.5 3.9
##  [577] 4.5 4.4 6.6 4.2 2.3 4.0 3.0 6.9 3.7 4.0 6.1 3.5 4.2 5.8 7.0 2.9 4.4 4.2
##  [595] 5.0 3.5 5.8 3.4 3.7 5.0 3.7 4.5 4.1 4.8 5.9 3.6 4.1 5.4 4.0 6.1 3.6 4.6
##  [613] 4.9 5.3 4.3 5.5 4.7 5.2 3.3 2.2 4.1 3.3 4.2 3.3 5.2 5.3 2.4 4.5 4.6 4.8
##  [631] 5.4 3.8 5.9 3.8 5.5 5.7 3.5 4.7 4.5 4.1 4.5 4.9 3.9 2.9 5.8 6.1 4.6 5.1
##  [649] 4.7 3.9 5.6 3.4 6.5 5.7 4.1 4.4 6.0 4.0 4.6 4.9 5.6 4.3 4.7 4.1 2.1 3.4
##  [667] 4.2 3.8 5.1 4.1 2.2 4.8 4.5 4.9 5.1 4.7 5.7 3.9 3.4 3.5 4.0 3.8 4.8 3.9
##  [685] 2.6 5.1 3.7 4.3 6.8 3.8 3.5 5.7 4.8 2.7 3.8 5.8 4.3 4.6 3.7 4.6 4.8 5.5
##  [703] 5.3 5.3 3.6 4.6 4.1 2.7 4.5 4.7 5.1 3.6 2.2 4.3 4.9 4.1 5.3 5.6 4.2 4.9
##  [721] 4.6 4.4 5.9 5.7 4.8 3.6 6.4 5.8 3.5 4.6 5.3 3.6 4.7 5.3 4.3 5.5 3.4 3.6
##  [739] 4.9 5.8 4.0 4.1 3.1 4.2 3.4 4.2 5.4 5.6 3.8 4.0 3.4 3.5 5.1 5.7 3.2 5.3
##  [757] 5.7 3.3 5.0 4.1 4.8 5.6 3.7 4.8 4.5 4.0 4.5 4.8 5.3 4.1 4.1 5.4 4.3 5.3
##  [775] 4.1 4.5 4.6 3.2 4.5 3.4 4.7 4.4 4.4 3.0 2.7 3.6 5.8 4.4 4.1 5.3 4.3 5.3
##  [793] 4.7 5.8 4.5 5.4 4.3 4.8 3.5 5.7 5.0 5.1 5.5 4.1 5.0 6.2 4.6 4.8 5.4 4.7
##  [811] 3.6 3.8 4.9 4.7 2.8 3.9 2.9 4.9 4.4 3.6 4.1 5.1 4.4 3.8 2.2 5.1 5.8 4.1
##  [829] 3.0 5.2 5.4 5.0 5.4 6.0 2.1 4.9 4.0 5.6 3.6 4.7 3.6 3.3 5.8 3.1 4.3 4.7
##  [847] 3.6 4.8 4.0 4.5 4.9 5.2 5.7 3.7 4.2 5.6 5.3 3.8 4.7 3.4 4.8 4.6 3.7 4.9
##  [865] 3.8 2.9 3.7 3.8 5.8 6.3 4.4 3.1 4.9 4.3 5.3 5.1 4.1 3.7 5.7 5.3 5.6 5.0
##  [883] 5.0 4.8 5.3 3.4 4.7 4.3 3.6 4.1 4.0 5.3 4.7 4.0 3.5 4.9 4.0 3.9 5.8 4.9
##  [901] 5.6 6.2 2.1 4.2 3.8 4.4 4.3 3.8 3.1 4.6 5.1 3.3 5.6 3.9 4.6 3.7 3.4 4.4
##  [919] 3.9 3.6 5.8 4.8 5.2 4.0 3.9 4.2 1.7 4.2 5.3 4.8 4.1 2.7 3.4 5.0 5.0 3.1
##  [937] 3.8 4.5 5.6 3.6 3.9 4.4 3.3 2.9 4.5 3.9 3.9 4.1 3.1 4.1 5.9 4.4 3.1 5.3
##  [955] 3.5 4.9 4.2 5.3 3.8 4.5 4.3 4.4 3.2 4.0 4.7 4.2 2.9 5.1 4.6 6.1 4.7 3.6
##  [973] 3.7 3.2 4.9 2.7 5.6 4.8 4.8 5.5 3.3 5.3 2.4 2.6 3.7 4.4 5.8 5.0 4.4 3.9
##  [991] 4.7 4.6 3.5 4.9 2.1 5.3 5.6 6.2 2.8 4.5
## 
## $func.thetastar
## [1] -0.0263
## 
## $jack.boot.val
##  [1]  0.497118156  0.404705882  0.236931818  0.108077994  0.008401084
##  [6] -0.137941176 -0.293655589 -0.338920455 -0.354155496 -0.581502890
## 
## $jack.boot.se
## [1] 1.013879
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
##    [1] 4.8 4.4 4.8 4.9 4.9 3.5 2.9 6.1 4.5 3.3 3.7 6.6 4.3 2.2 3.6 4.7 6.1 5.0
##   [19] 3.7 4.9 4.3 5.4 3.7 4.5 4.8 3.6 4.5 4.2 5.2 3.5 5.7 4.2 5.1 4.4 3.8 5.1
##   [37] 3.7 4.0 4.2 3.2 4.4 4.8 4.0 5.1 6.5 5.4 6.3 4.1 5.5 4.6 4.8 5.7 6.1 5.2
##   [55] 3.9 2.8 4.7 4.1 4.0 3.9 3.3 3.3 3.0 3.7 5.7 4.2 4.0 5.5 6.0 4.2 4.8 5.7
##   [73] 5.2 5.8 5.2 2.9 5.1 4.4 5.5 5.7 4.0 4.5 5.0 3.9 4.1 4.6 4.1 5.6 4.8 6.1
##   [91] 3.1 3.9 4.5 3.8 4.3 5.2 3.3 3.0 3.6 4.1 2.2 5.7 5.2 4.5 5.1 5.2 5.1 3.3
##  [109] 5.6 4.3 4.3 4.0 4.4 4.9 4.3 6.8 4.0 4.3 4.5 4.0 6.5 4.7 2.5 1.3 4.9 5.8
##  [127] 4.2 3.7 5.7 4.1 4.9 4.4 4.3 5.5 3.3 6.0 4.3 5.2 5.6 3.4 3.4 3.9 4.1 3.3
##  [145] 4.2 3.7 4.1 5.0 4.1 5.1 3.9 4.4 4.0 4.7 3.8 4.9 2.7 5.8 3.4 3.7 2.1 5.3
##  [163] 4.0 5.3 6.5 4.5 5.5 3.5 4.3 3.8 3.6 6.3 5.5 6.8 3.5 3.7 3.7 4.5 4.4 4.7
##  [181] 5.5 4.0 5.2 4.6 5.0 4.4 5.8 5.2 3.9 4.7 4.7 5.4 4.3 4.5 6.1 4.1 4.5 3.7
##  [199] 5.8 4.0 5.6 4.9 6.2 5.3 4.8 3.9 3.6 3.9 4.8 4.5 5.2 3.0 4.8 6.1 6.3 4.6
##  [217] 4.0 5.3 6.2 3.4 4.3 3.5 3.5 5.0 4.1 4.1 5.2 4.8 4.1 5.8 5.3 3.8 4.4 3.1
##  [235] 4.7 4.5 6.4 4.8 4.0 2.6 6.3 3.6 3.6 5.4 4.2 2.6 3.7 5.1 4.0 4.1 4.8 3.7
##  [253] 5.0 5.6 3.1 5.1 5.6 4.4 4.3 4.9 5.7 5.7 3.6 4.9 4.3 4.6 5.1 3.3 5.0 4.5
##  [271] 5.3 3.0 4.5 4.2 4.4 4.5 4.3 2.7 4.6 4.8 5.9 6.1 3.6 5.1 2.8 3.8 6.2 4.2
##  [289] 4.5 4.8 4.7 3.3 3.7 4.4 2.2 5.8 4.9 4.6 5.1 4.9 5.8 5.4 4.4 4.2 2.5 4.7
##  [307] 3.8 3.3 4.1 3.5 4.1 4.2 4.5 4.3 4.8 4.4 5.6 3.2 4.8 5.5 4.5 2.9 5.2 5.7
##  [325] 5.1 5.3 4.5 4.7 3.7 5.9 4.2 5.7 4.9 4.0 3.4 4.9 4.6 4.0 4.6 4.5 4.3 5.9
##  [343] 3.5 2.9 4.9 5.0 6.1 4.0 3.0 2.7 4.1 5.3 4.3 5.7 5.1 5.4 5.1 6.0 4.0 5.0
##  [361] 3.4 6.5 4.1 4.9 5.1 4.3 5.7 4.7 5.6 3.9 4.1 4.4 4.5 5.6 2.4 5.1 5.2 4.1
##  [379] 5.0 4.1 5.8 4.5 5.1 4.9 4.6 5.3 4.0 4.7 4.6 5.7 3.6 3.3 5.9 2.6 5.4 5.9
##  [397] 4.8 3.9 5.7 5.6 5.7 3.9 4.7 4.6 3.1 3.3 3.4 4.7 4.4 4.5 4.0 3.3 5.0 5.5
##  [415] 5.4 3.7 4.1 3.5 4.2 2.9 4.9 5.6 4.5 7.1 3.6 2.8 5.9 5.2 4.7 5.4 4.4 4.3
##  [433] 4.6 4.2 4.5 3.4 5.1 4.1 5.6 5.6 5.9 5.5 3.2 3.5 4.4 3.7 4.9 5.7 6.1 6.7
##  [451] 4.0 3.8 3.7 7.0 4.7 4.6 4.0 4.7 4.7 6.2 4.4 3.2 5.5 4.1 3.4 3.8 4.7 5.1
##  [469] 4.7 4.0 3.2 5.3 5.2 6.2 3.8 3.2 5.1 4.9 5.1 3.0 2.8 4.0 3.8 4.3 5.8 4.6
##  [487] 4.3 3.6 5.7 4.7 3.3 5.7 4.6 4.6 6.4 4.8 5.1 3.7 4.6 4.0 4.5 6.0 3.8 4.3
##  [505] 4.8 3.6 3.4 5.0 5.0 3.6 4.2 3.4 2.9 3.6 4.9 4.0 4.0 2.8 4.5 4.3 3.2 5.1
##  [523] 5.3 4.0 3.6 3.9 6.3 3.4 5.9 4.4 4.8 5.0 5.3 5.8 3.3 4.4 3.1 3.8 4.7 3.6
##  [541] 3.1 5.5 4.8 4.5 4.3 5.2 4.3 4.2 5.3 4.4 3.6 4.8 3.0 3.9 3.3 4.4 5.4 6.0
##  [559] 6.3 4.1 5.7 5.1 5.2 5.9 3.9 4.1 4.0 5.2 5.2 5.0 3.5 4.3 4.4 3.0 3.9 3.5
##  [577] 4.6 4.5 4.4 4.5 4.5 2.7 5.5 4.4 4.9 5.8 6.0 4.6 4.7 2.9 5.3 4.6 4.5 3.5
##  [595] 3.1 5.2 5.7 5.2 5.2 5.7 4.4 3.7 4.8 4.6 5.2 4.6 3.5 5.3 5.2 6.6 4.9 4.7
##  [613] 2.9 3.1 6.4 4.7 3.7 4.8 6.3 4.6 4.8 4.6 3.2 3.4 2.0 4.7 4.3 5.0 4.5 4.7
##  [631] 3.6 5.3 3.4 3.5 5.0 5.8 3.1 3.9 4.4 5.0 3.9 4.3 5.3 2.8 4.4 4.1 5.2 6.1
##  [649] 3.0 4.3 4.6 3.4 3.8 6.3 5.7 2.8 5.0 5.4 4.6 6.3 4.6 3.5 3.8 5.2 5.2 4.2
##  [667] 3.9 2.7 4.4 5.0 4.2 4.3 3.7 4.3 4.2 4.7 4.5 5.1 5.9 5.9 4.5 3.6 7.0 3.4
##  [685] 4.5 3.8 4.4 4.7 4.5 5.3 4.6 4.3 4.9 4.6 3.8 5.7 5.2 3.8 3.9 4.3 3.9 6.1
##  [703] 4.7 4.2 4.3 3.9 4.9 6.3 5.9 4.6 4.7 3.9 4.8 3.9 4.7 4.9 4.4 4.1 2.9 6.2
##  [721] 3.8 3.2 5.0 3.1 5.4 4.2 3.7 5.4 4.9 4.3 4.0 4.8 5.0 4.5 6.0 5.1 4.8 4.2
##  [739] 5.3 5.2 2.2 4.7 4.0 2.2 6.3 4.8 5.6 6.2 5.3 7.6 6.3 3.0 5.5 4.7 3.7 6.4
##  [757] 5.2 4.3 3.9 3.5 5.1 3.8 4.9 3.2 3.7 5.2 4.3 3.6 3.4 4.3 5.0 2.6 4.2 4.2
##  [775] 2.7 3.6 5.0 5.0 4.2 4.9 4.4 3.4 4.1 5.2 2.6 5.8 3.7 5.5 4.8 4.4 3.3 4.1
##  [793] 6.3 4.8 5.1 5.7 5.1 5.8 5.6 2.9 4.4 3.8 4.1 4.3 4.4 5.4 4.2 3.6 4.8 5.0
##  [811] 4.4 6.7 3.9 4.0 4.1 4.9 4.4 4.7 2.7 4.3 2.9 3.7 6.5 5.9 4.3 3.6 3.7 4.8
##  [829] 5.7 4.8 4.1 3.1 5.2 3.9 5.1 4.5 3.7 5.4 4.6 4.5 4.1 2.9 4.7 5.2 6.4 3.7
##  [847] 4.7 6.4 4.8 6.0 4.7 4.6 4.8 4.9 5.0 3.6 5.1 5.0 3.0 4.6 4.6 3.7 4.2 3.8
##  [865] 4.9 4.9 4.2 3.1 4.7 3.6 3.6 3.6 4.2 3.7 5.4 3.9 6.1 4.3 5.7 4.7 5.1 4.4
##  [883] 5.2 3.5 2.8 5.0 7.0 4.7 4.1 5.9 4.0 4.7 4.9 3.9 4.1 4.3 2.4 3.1 4.3 4.6
##  [901] 5.7 6.0 3.6 3.0 3.3 5.9 5.2 5.0 3.7 5.3 3.8 5.7 3.5 5.2 3.9 5.3 4.9 4.4
##  [919] 5.7 4.4 2.8 5.4 4.8 5.6 4.0 5.0 4.6 2.8 5.0 4.6 3.8 4.3 3.6 3.0 3.0 5.1
##  [937] 6.1 2.8 5.3 3.6 4.5 3.8 4.6 3.9 3.8 3.4 3.8 3.9 3.1 3.3 3.7 4.5 6.0 4.0
##  [955] 3.7 3.8 4.7 4.2 4.1 5.4 5.2 5.9 5.8 5.7 5.0 4.6 5.6 3.4 5.0 4.4 6.6 4.0
##  [973] 2.2 3.8 4.8 4.0 4.8 5.1 4.8 4.2 5.6 3.7 5.2 4.1 3.9 3.2 4.6 2.4 4.6 4.5
##  [991] 4.2 5.6 4.2 5.1 3.2 3.4 6.1 3.4 2.7 4.3
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.600 5.500 5.224 5.200 5.200 5.000 5.000 4.800 4.600 4.400
## 
## $jack.boot.se
## [1] 1.070255
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
## [1] 0.07720175
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
##      shape       rate   
##   13.982698   23.082931 
##  ( 6.180098) (10.387308)
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
## [1] -0.4364187  0.5675118  0.5101486 -0.8060255 -0.5457150 -0.3414744
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
##    [1]  0.3685073493  0.3560187150  0.3017886884  0.3075910073 -0.5533357345
##    [6] -0.3918649594 -0.1584406321  0.0712335799 -0.3474873997  0.0430053842
##   [11] -0.5959068276 -0.2894536844  0.4391723974 -0.3794691366  0.2267664880
##   [16] -0.2290758343 -0.5709704426  0.1206140130  0.3119772871  0.3919625269
##   [21]  0.2767577683  0.0923257732  0.1519900463 -1.3128138434  0.1978262368
##   [26]  0.2697496047  0.2164825029 -0.4496433889 -0.0397217964 -0.8825686306
##   [31] -0.1136509681 -0.5453668284 -0.3785406353  0.1456212348 -0.0332727858
##   [36]  0.2341057044  0.2375598349 -0.0199607711  0.1520473094 -0.2518646159
##   [41]  0.0931777495  0.5240738745  0.1121796447  0.0619117128 -0.3855732000
##   [46]  0.6331034512 -0.4378571460  0.8546568685 -0.3982691862 -0.3424152718
##   [51]  0.1324179154  0.1210174952  0.1484651210 -0.0152901447 -0.0508325824
##   [56]  0.0423397687 -0.5512800130 -0.0478532080 -0.3239939627  0.2656590687
##   [61]  0.7111345423 -0.3448356111  0.4792952371  0.4125770199 -0.1585798552
##   [66] -0.5528031771 -0.1004266266  0.1741049616 -0.1332956201  0.0191884014
##   [71] -0.5288091931 -0.1310332490  0.9780696531  0.1456654776 -0.3579911453
##   [76] -0.3748240810  0.4104162269 -0.2912276963  0.3863009786  0.5317870835
##   [81] -0.1833088415  1.0370871886  0.0430053842  0.1717458252  0.1387726190
##   [86] -0.1372929300 -0.0102554369 -0.0015346462 -0.3016908820  0.2998421389
##   [91] -0.1931279951  0.1350756728 -0.6873643915 -1.1601751182 -0.0993614370
##   [96] -0.2816161738 -0.0822295260  0.1731185196 -0.6779303969  0.1361784327
##  [101] -0.6441442558  0.5873035673  0.2561599364 -0.4366219206  0.1555081226
##  [106]  0.2487158228 -0.0076488073 -0.2894748914  0.1621645543 -0.7946170050
##  [111] -0.4044938364 -0.8063412921 -0.6249293264 -0.3321658462 -0.3153974007
##  [116]  1.1216538967 -0.1483281789 -0.2571325796  0.0324395004  0.2133090290
##  [121] -0.3165565320  0.5015323778  0.0173029833 -0.1914272092  0.2728313639
##  [126] -0.6490822984 -0.0414025524 -0.0493773092  0.1510079954 -1.3067781082
##  [131]  0.1955583036 -0.7808381326  0.7211819739  0.2386618686  0.8487935432
##  [136]  0.3139407259 -0.0096326288  0.2362535955  0.1498562009 -0.2615651771
##  [141]  0.1006400859  0.4444270852  0.0010632860 -0.2298801731 -0.7155269159
##  [146] -0.1125521161 -0.5453668284 -0.6489837133  0.4589557218  0.8729997903
##  [151] -0.0704199603  0.3287251179  0.3946603695 -0.7989204909 -0.5221010439
##  [156] -0.1005526269  0.1250912500 -0.7847773197 -0.7588695163  0.5053482359
##  [161] -0.0182134839  0.2161263669 -0.1518019246  0.0351418856 -0.0344375023
##  [166]  0.8885545654  0.3657374952 -0.0720734310  0.4083370231  0.1102148028
##  [171] -0.4099086733 -0.0849273146 -0.6644390808 -0.1501886193 -0.3207989719
##  [176] -0.2285309404 -0.0955517772  1.0384938065 -0.1765690581 -0.3826889670
##  [181]  0.1552297947  0.1192274491 -0.1024360009  0.5240738745  0.1308280864
##  [186]  0.1815470393 -0.5250942846  0.4458253825 -0.9493980835  0.2030082385
##  [191] -0.1576084493  0.3339151082 -0.0926497316 -0.4734109188 -0.2193182941
##  [196] -0.1248472781  0.1951741250  0.0632999202 -0.0372167185 -0.1950729653
##  [201] -0.2305286034  0.4652730749  0.2159335611 -0.3076108370  0.1950904298
##  [206] -0.5439214900  0.6056737883  0.2592640629 -0.0206787180  0.4841623056
##  [211]  0.3452226454 -0.3058607029 -0.0840709203 -0.1440249631  0.3398350247
##  [216]  0.0482807745 -1.4005977986  0.2957360157 -0.3726214980 -0.2676776067
##  [221]  0.2602712555 -0.0162062831  0.5948432580 -0.4557483294 -0.5973147248
##  [226]  0.1697660084 -0.2448966008 -0.3154526384 -0.7354332960  0.3680121587
##  [231]  0.5105282691 -0.7252514789  0.1634661699  0.3262656808  0.9350295621
##  [236] -0.8847899210  0.4866903536  0.3273641429 -0.0326110345  0.0434625938
##  [241] -0.7764166660 -0.3113180121  0.3771154853 -0.6298306052  0.2805419157
##  [246] -0.2820668454  0.0975161134 -0.1940828512  0.5640087569  1.1080885650
##  [251]  0.0149100802  0.1480685767  0.1220660435 -0.3655713374  0.6599170456
##  [256]  0.5017860820 -0.1646693575  0.8348317522 -0.6062845751  0.3018532763
##  [261] -0.7305237795  0.3230845391 -0.9484464349 -0.1670490479 -0.1685917442
##  [266] -0.0569916571 -0.2620208177  0.6043986099  0.3528161102  0.2653691628
##  [271] -0.6514218337  0.3674417019 -0.5907144308 -0.9484454719  0.2711072984
##  [276]  0.9702796961 -0.1649390924  0.5354513691  0.0223885988  0.0711474061
##  [281] -0.0290057640 -0.1588846606 -0.5243521811  0.3561270819  0.1504403859
##  [286]  0.1700592131  0.4604803895 -0.3160745424 -0.3024531837 -0.1358128059
##  [291]  0.2458357318  0.3391568195  0.1988422903  0.0969344171 -0.5025251751
##  [296] -0.0572246848 -0.1990164339  0.1043826926 -0.2886116325  0.0210126640
##  [301]  1.0835041346  0.1300895163  0.8802485243  0.2266900177 -0.1240898202
##  [306]  0.0563399979  0.5359571662 -0.6147166760 -0.3971593359  0.4599144413
##  [311] -0.4974593634  0.3657374598 -0.1512624252  1.4284375066 -0.0459438077
##  [316] -0.1530975666  0.4587886547  0.1497662008 -0.2000983453 -0.8008435424
##  [321]  0.8578667210 -0.1618569205 -0.2055040098  0.2826801757 -0.0299992494
##  [326] -0.4780413939 -0.2524344287 -0.2045591316 -0.7743343871 -0.1830932376
##  [331]  0.1589196658 -0.4492871009  0.1549338793  0.0112455722  0.5519406604
##  [336]  0.0773612557 -0.6353276337 -0.2314298099 -0.2968703486 -0.1155088804
##  [341]  1.1746744017 -0.8901710872  0.2240744050 -0.2655453511  0.6534485752
##  [346] -0.4916797702  0.1258402027 -0.1127756136 -0.1189273260  0.7031962574
##  [351]  0.2642334882 -0.2503644992 -0.2473450200  0.4236973102  0.3070764957
##  [356] -0.3520473426  0.2115744581  0.3799039880 -0.5662858349  0.0002114752
##  [361]  0.4533835821  0.7853772894  0.3751033030 -0.3077040184 -0.4279028685
##  [366]  0.8438222789 -0.2447340012 -0.3136598313  0.3851612763 -0.2687994643
##  [371] -0.1978035636  0.4552736490 -0.2777834671 -0.0213202191 -0.2867185115
##  [376]  0.5997099488 -0.3539082789  0.0739387403  0.5368673855 -0.3161292799
##  [381] -0.4496433889  0.8767431362  0.9470561286 -0.1039804507 -0.2143717771
##  [386]  0.2891306875 -0.5256897888 -0.2308274487  0.6570722984 -0.4034838838
##  [391] -0.3088322669  0.2018824375  0.3999286021  0.6017399053  0.3282875058
##  [396]  0.2265015388  0.1871770076 -0.3977561320 -0.1439249865 -0.4262368058
##  [401] -0.3059877210  0.4936476047 -0.3576852255  0.8489926771  0.8005322046
##  [406] -0.5843348930  0.8641970569 -0.5352764827 -0.0447086988  0.1934191689
##  [411] -0.0129999795 -0.5502768666  0.7002388577 -1.1366611873  1.0108647181
##  [416]  0.0912778966 -0.3085016407 -0.0229876845 -0.2255624875 -0.8742302261
##  [421]  0.5691657849  1.3245050867 -0.3124135535  0.4029997841 -0.3711030742
##  [426]  0.5528755511  0.1974938882  0.2379373784  0.3917252391  0.1930589730
##  [431]  0.8274755485 -0.2455968603 -0.2273827478  0.2458377897  0.9314955678
##  [436] -0.1595107760  0.3903147450 -0.2088113534  0.0787662938 -0.3914789171
##  [441] -0.7682956585 -0.5476536023  1.1436934813  0.7235388874  0.4921365870
##  [446]  0.8791992442  0.1740530884  0.0828571095 -0.4332060734  1.0207138228
##  [451]  0.0076124156  0.6704615460 -0.2056370663  0.6193849351 -0.2011259655
##  [456]  0.7506383602 -0.7250819990  0.2059683230 -0.0870041797  0.0467564184
##  [461] -0.2811424658 -0.0625624540 -0.6863492127  0.3386774839  0.1995087922
##  [466]  0.3250757317  0.4104162269  0.4859182895 -0.1575022689  0.9076731792
##  [471] -0.3326359990  0.0805096628 -0.1573810881  0.3877946725 -0.2564734901
##  [476]  0.0573987122  0.2575105263 -0.1541761984 -0.1094020222 -0.0373391491
##  [481]  0.1725404578 -0.1930958539 -0.1990284964 -0.0737714583 -0.1911781361
##  [486]  0.6284241401  0.4042986334  0.3543844073 -0.5577361571  0.0390597881
##  [491] -0.7437270743 -0.6745951671  0.4742328394  0.3575310907 -0.3016908820
##  [496] -0.3484958279  0.2750726823  0.3927563827 -0.8302875661  0.6416529133
##  [501]  0.2436084231 -0.0743453406 -0.3782762620  0.4250204384  0.4018829711
##  [506] -0.3551717910  0.2933789839 -0.4722925405 -0.2384613636  0.0588604612
##  [511] -0.1187105521 -0.0160023414 -0.7938059233 -0.0752440474 -0.4115967809
##  [516] -0.1273097204  0.8586504847 -0.3470852410 -0.9744907945 -0.5099458311
##  [521] -0.5125361430 -0.3727352704  0.0706031851 -0.3617354976 -0.3382226775
##  [526]  1.2735721218 -0.3360431103  0.0730709742 -0.3221111408  0.1137356124
##  [531]  0.4647072790 -0.0580602952  0.3278080640 -0.0284469747  0.7864160943
##  [536]  0.0023713267  0.2348890479 -0.1131906715  0.6362138701 -0.3336678507
##  [541] -0.0118882321  0.5984985655 -0.7893843546  0.3370093061  0.0526298607
##  [546] -0.5292810343  0.2971620089 -0.3919555316 -0.0711819397  0.2131432809
##  [551] -0.0411938835  0.3284533314  0.0660130226  0.1274327551  0.5599808514
##  [556]  0.1003398703 -0.6487109045 -0.1249956890  0.3171759905 -0.2892451318
##  [561]  0.3278080640  0.2094185163 -0.5694940219  0.5319589819  0.2907797734
##  [566]  0.3282149770  0.0547955046  0.1555051270  0.4312680251  0.0361477479
##  [571]  0.3516429134  0.2117906492  0.3520602674 -0.0601040793 -0.0514127085
##  [576]  0.1294812677  0.2342214177 -0.4409078296  0.2591998082 -0.4898716034
##  [581] -0.2022025025 -0.6254974010  0.0066278672  0.0241451747 -0.0426774734
##  [586]  0.1155740036  0.2372871408  0.5247465093 -0.0376650785  0.1037096757
##  [591] -0.7282936796  0.0598071312  0.0930414682  0.3966894136  0.3352081474
##  [596] -0.4754718435 -0.5900260274 -0.5562163532  0.2452740808 -0.7537423955
##  [601] -0.5175167416  1.2407211030 -0.0534171214 -0.3678021989  0.5172294305
##  [606] -0.1054505014 -0.6055321956  0.3117458091  0.4973670272  0.0928170235
##  [611]  0.5289139507  0.4105510043 -0.2428626724  0.0227695820 -0.1640503781
##  [616]  0.2394022079 -0.1362544024  0.3051722534 -0.1312642446  0.5463455273
##  [621]  0.5928657961 -0.5318143388 -0.1134116385 -0.4779453826 -0.3886246476
##  [626] -0.0147357811  0.3892098277  0.8883426574 -0.4584038675 -0.5997884466
##  [631] -0.1784987284  0.0028639903 -0.1771559284  0.1936199413 -0.3289983438
##  [636]  0.5083886183 -0.1612889276  0.5553434423 -0.3081961895  0.2211334096
##  [641]  0.3640808312  0.0020064365 -0.2510875391 -0.3914402642 -0.5999149973
##  [646] -0.6399870715 -0.0020351987 -0.0704438437 -0.8510071697  0.0486487507
##  [651]  0.2799134902 -0.2415783423  0.5429204882 -0.2059877045  0.1116395740
##  [656] -0.4199501726 -0.1218590173  0.0122498686 -0.2455968603 -0.4283321713
##  [661]  0.9954626767 -0.3237543996  0.4929469696 -0.3553672751 -0.4609875446
##  [666]  0.1642000437 -0.5212405642  0.0687089339  0.0970637452  0.5024991034
##  [671]  0.3001792407  0.2990424058  0.2577529990 -0.1434081086 -0.1685742855
##  [676]  0.0476303733 -0.0018969384  0.4623821054  0.1328832237  0.0657940768
##  [681] -0.1660646404 -0.6539617316 -0.6023279102  0.1397798404  0.1706404366
##  [686] -0.5132814850  0.6372707013  0.0331359187  0.3038263288 -0.1537231174
##  [691]  0.1022229245 -0.0055896147 -0.0982816348 -0.1806688278  0.2946501067
##  [696]  0.6649984795  0.2120989395  0.7162758421 -0.2240725841 -0.1572010826
##  [701]  0.2120692171 -0.7195784205 -0.0943602361 -0.1802657499  0.3575184201
##  [706] -0.1272930835  0.3980581202  0.9552190513  0.6758897063 -0.9674422992
##  [711]  0.3576241400 -0.2635892900 -0.3258339737  0.0575049795  0.4442596922
##  [716] -0.4308762910 -0.2037644600 -0.2282749780 -0.0887564238  0.3051455899
##  [721]  0.1100811309  0.2366174901 -0.0536884333  0.0646726274 -0.2529762300
##  [726]  0.3703689063  0.1621493887 -0.8456135460  0.6277467871  0.7826585255
##  [731]  0.6955196444 -0.0626699999  0.1311081122 -0.6427190549  0.0467564184
##  [736] -0.4820847261 -0.0854091637 -0.2316953659  0.0194548541  0.4018829711
##  [741]  0.1173108229  0.4108988341 -0.4495856548 -0.9654186459  0.9387096573
##  [746]  0.2709287634 -0.4970379965  1.4006358344  0.6813270849  1.3379808849
##  [751]  0.2317400484  0.0334819976 -0.1707159721  0.5319848829  1.1400834927
##  [756] -0.0042743907  0.1694900568 -0.1997653835 -0.1868796537  0.2013550937
##  [761] -0.4689532833 -0.9159599614 -0.7161964930  0.0778817274 -0.1670632628
##  [766] -0.3756704510  0.3645114125  0.8400035731  0.6391006505 -0.8718277129
##  [771]  0.4058138593  0.2626855303 -0.1214185645 -0.4432339086 -0.5048007505
##  [776] -0.6747340703  0.2554610658 -0.0314467597 -0.0130168115 -0.6531300962
##  [781]  0.2176174172  0.3115937969  0.0758356520 -0.1268196118 -0.6468349949
##  [786] -0.3748240810  0.7460545069 -0.1557963332 -0.1050653426 -0.3996979250
##  [791]  0.2716380585 -0.3268221094  0.1776462905 -1.1892460782  0.4853657081
##  [796]  0.7271251603 -0.5111073981  0.0116175719  0.2096650992  0.0756293010
##  [801] -0.0127723370  0.0121060169  0.0588898246 -0.1070313023 -0.9196579981
##  [806] -0.8195533226 -0.0372665694  1.1794992598 -0.0195787083 -0.1164135200
##  [811]  0.4690525526  0.0043849832 -0.1488135232  0.8083417128  0.6089880475
##  [816] -0.0356014564 -0.5875283587  0.0849901073  0.3715529134 -0.0014188904
##  [821]  0.4391401282 -0.2232020441  0.0784589552 -0.8426879774  0.2721257681
##  [826]  0.6314243579  0.0519117258  0.3524585528  0.5303941187 -0.4664069899
##  [831]  0.3393447191  1.0517567169  1.1780672742 -0.3273578622  0.4848187645
##  [836]  0.8870617661  0.3776370177  0.1840408330 -0.4267281965  0.1136967680
##  [841] -0.2991601932  0.2167307068 -0.0924967318  0.0600044832 -0.3804039197
##  [846]  0.7670017526  0.1932699398  0.2255922357 -0.0178661000  1.0395739116
##  [851]  0.8065326925 -0.0715794403  0.5117970541  0.0666156801  0.7001308646
##  [856] -0.1671046698  0.5844966033  0.5710483782  0.0593781061  0.0203334220
##  [861] -0.2359947040  0.5712239180  0.4264232578  0.9500634016  0.6532826210
##  [866]  0.8981928464 -0.1216922051  0.0457445209  0.3256831778  0.0733750504
##  [871] -0.3462727489  0.1479022679 -0.3520672029  0.1691662348  0.1468859350
##  [876]  0.3991193568  0.2865286391 -0.8683570010  0.4014143910  0.0682758882
##  [881]  0.1342062506  0.2022467853  0.4112489681  0.2448638007  0.0389391972
##  [886] -0.4829238522  0.3684469875  0.7637558769 -0.0695692183  0.3835148317
##  [891]  0.3250584346  0.3090346413 -0.1245362579 -0.1945218252 -1.1780862820
##  [896] -1.0785332673  0.4018955333 -0.1711454689 -0.6442034236 -0.2097755804
##  [901] -0.3689396893  0.9955258244  0.5624378626  0.1469165514 -0.6618673041
##  [906] -0.1999517204  0.1128553841  0.3647839961  0.4841623056 -0.0909185088
##  [911]  0.2387542121  0.1985996369  0.8401532587  0.2413363540 -0.0599208853
##  [916]  0.1525092207 -0.3056417681  0.1653445729 -0.1806633122 -0.3344461244
##  [921]  0.5843928480  0.9100645533 -0.0999019841 -0.2307685953 -0.4624089940
##  [926] -0.3269193529 -0.1033576465 -0.3126441709 -0.0209268563  0.5538908409
##  [931]  0.1066834950 -0.2687944845 -0.5698051404 -0.7451885832  0.0667940249
##  [936]  0.2127072555 -0.2219618336 -0.1048022526 -0.3973217890  0.1175758529
##  [941]  0.0736822665 -1.1084441348  0.7207279063 -0.1110031615  0.2102648213
##  [946] -0.2413423381  0.1632064833  0.5325540711 -0.0661315561  0.4184890785
##  [951]  0.0843276159  0.9234113790  0.5470004883  0.1394123606  0.0830448918
##  [956]  0.6995341951  1.1258023969 -0.5898110630  0.4197623009 -0.3838986800
##  [961] -0.1759216920 -0.1867107894  0.8249109368 -0.2837101496 -0.4191267783
##  [966]  0.0170049562  0.3752881488  0.3383031506  0.2611850496 -0.1596106726
##  [971] -0.3692548652 -0.1034948763  0.3882994863 -0.0629954831 -0.8361199778
##  [976] -0.7278918120  0.5633318606  0.8250572780 -0.2341073502  0.7471729021
##  [981] -0.4056258649  0.1411642805 -0.2629433249 -0.0563795755  0.0047111349
##  [986] -0.0191992136 -0.0487111465  0.3799112600 -0.4313416303 -0.4220318796
##  [991] -0.1940113072  0.0628552825 -1.0605404030  0.2189944560  0.3252498749
##  [996] -0.2908752386 -0.7047120497  0.4403531638 -0.0900443472 -0.3455974156
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
##   0.60574506   0.15835437 
##  (0.05007605) (0.03540240)
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
## [1]  0.32184972 -1.81478475  0.49737338 -0.32612818  0.01133344  0.97318310
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
## [1] -0.0052
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8817655
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
## t1*      4.5 -0.00970971   0.9317468
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 3 5 7 8 9 
## 2 2 1 3 1 1
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
## [1] 0.0229
```

```r
se.boot
```

```
## [1] 0.952908
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

