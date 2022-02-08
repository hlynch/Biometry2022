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
## 0 1 2 4 5 6 7 8 9 
## 1 1 1 1 1 2 1 1 1
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
## [1] 0.0016
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
## [1] 2.650204
```

```r
UL.boot
```

```
## [1] 6.352996
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.6000 6.3025
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
##    [1] 4.5 3.8 4.7 4.5 5.6 4.4 4.8 3.9 4.9 4.1 4.1 4.6 6.1 3.1 5.3 5.0 3.8 5.0
##   [19] 5.0 5.7 5.6 4.3 3.5 3.8 3.8 3.8 5.4 5.2 3.9 3.8 5.6 4.3 4.3 3.0 5.4 4.5
##   [37] 5.8 4.0 3.7 4.5 3.9 3.7 4.8 4.3 5.0 3.3 3.7 3.6 3.9 5.4 6.3 4.6 2.9 4.4
##   [55] 5.2 4.1 3.6 3.4 5.0 4.1 5.2 4.9 3.8 5.0 5.5 5.3 3.2 3.6 4.3 5.7 4.1 2.7
##   [73] 4.0 3.8 6.0 5.6 3.5 4.1 3.4 4.4 5.2 4.9 4.7 3.8 3.4 4.5 5.7 3.8 4.7 2.6
##   [91] 3.2 5.4 3.2 3.6 5.9 5.9 4.8 4.2 3.8 5.4 6.1 3.0 3.9 4.5 5.2 3.1 6.8 5.1
##  [109] 4.6 6.3 4.3 4.2 4.7 4.4 4.7 2.5 3.9 4.8 5.2 4.0 4.6 5.2 5.8 2.9 5.0 3.6
##  [127] 4.8 4.2 4.5 4.6 5.6 5.1 3.3 4.5 3.2 2.7 4.2 3.2 5.4 5.4 6.3 3.0 4.6 3.6
##  [145] 6.3 4.6 4.3 4.6 5.6 4.1 5.5 4.4 4.1 3.7 4.5 4.7 3.2 3.4 4.4 5.8 3.5 3.9
##  [163] 5.5 4.6 4.5 4.5 4.0 5.1 3.9 5.0 4.1 4.3 3.9 3.8 5.2 3.9 4.6 4.1 4.4 6.3
##  [181] 4.6 3.1 5.0 6.4 4.2 4.4 2.9 4.5 4.2 4.7 5.3 5.0 5.1 2.9 4.7 3.2 4.4 4.8
##  [199] 4.9 4.7 4.7 4.5 5.7 4.5 4.3 3.9 3.9 2.7 5.1 3.9 3.2 4.3 2.5 3.6 4.6 4.9
##  [217] 4.2 4.5 3.1 4.8 5.7 6.3 3.9 4.3 4.4 3.8 5.3 3.5 4.3 5.3 5.9 3.3 3.9 3.8
##  [235] 4.1 3.7 4.5 3.4 4.3 5.3 4.4 3.6 5.5 5.0 5.0 3.9 5.4 4.5 3.6 4.3 3.3 5.0
##  [253] 4.8 4.6 5.8 4.6 5.9 5.3 4.4 4.3 2.9 5.2 4.2 5.4 3.6 4.1 4.1 5.0 5.4 4.1
##  [271] 3.0 4.8 5.4 4.4 5.2 3.5 4.6 4.9 3.5 4.3 5.4 4.5 3.4 3.7 2.8 3.9 5.5 4.8
##  [289] 5.1 4.0 4.2 3.2 5.6 3.8 3.7 5.1 5.5 6.2 5.7 2.4 5.6 4.2 4.4 4.2 5.1 4.2
##  [307] 5.0 4.1 3.3 5.2 2.9 5.3 4.7 2.4 4.6 5.9 4.7 4.2 5.1 4.6 3.8 4.5 4.9 5.2
##  [325] 5.1 4.2 3.6 3.1 5.4 4.2 4.6 4.5 4.6 5.6 4.2 5.2 3.3 4.2 4.4 3.3 3.4 3.2
##  [343] 4.9 4.4 4.2 4.2 3.4 4.4 2.7 6.3 4.9 5.0 3.3 4.7 4.0 6.2 5.7 6.1 4.7 3.8
##  [361] 4.4 3.6 4.9 3.2 4.7 4.7 5.1 5.7 2.5 4.6 4.3 3.8 3.5 4.1 4.5 4.8 3.1 2.2
##  [379] 3.9 4.4 4.4 3.4 5.1 3.6 5.3 4.8 4.3 5.1 3.9 5.3 5.6 4.8 4.8 4.8 4.3 4.7
##  [397] 5.3 4.8 3.9 3.6 5.5 2.8 4.5 3.8 5.6 6.4 4.1 6.3 4.7 5.5 3.3 3.7 5.0 3.8
##  [415] 5.6 4.7 4.1 3.2 5.4 4.3 3.9 4.0 3.1 4.3 2.7 3.2 4.2 4.2 3.1 5.1 4.4 4.8
##  [433] 5.1 4.8 5.5 3.6 5.0 5.5 5.7 4.3 5.3 4.8 3.4 2.4 3.5 5.3 4.1 4.2 4.0 6.2
##  [451] 2.9 4.7 3.9 5.9 5.4 3.8 3.9 3.2 5.0 3.5 4.6 5.4 4.6 4.3 5.2 6.6 6.3 3.4
##  [469] 5.6 5.5 4.4 5.3 3.7 3.6 3.9 6.0 6.4 2.2 4.9 5.4 4.8 4.0 2.5 4.9 4.4 6.5
##  [487] 4.8 3.6 4.1 4.1 4.4 6.1 5.3 2.7 4.6 6.1 5.1 4.0 4.4 3.9 4.5 5.9 5.4 4.8
##  [505] 3.0 4.8 4.0 3.6 5.2 3.1 6.7 2.4 5.7 4.7 4.3 5.3 4.5 4.9 4.6 4.2 4.0 4.1
##  [523] 3.9 5.7 3.9 3.7 4.8 4.1 3.5 4.6 2.8 5.3 4.9 5.4 4.3 4.4 4.9 4.6 5.0 5.6
##  [541] 3.1 5.9 5.4 4.9 5.1 3.4 4.4 5.4 5.0 4.7 3.6 2.2 2.3 4.0 4.3 3.5 6.1 4.9
##  [559] 4.0 4.8 3.8 3.2 5.3 4.8 4.9 4.4 4.2 4.0 5.1 3.0 5.9 4.8 3.8 1.8 5.0 5.2
##  [577] 5.4 4.8 3.5 4.4 3.3 3.1 4.0 4.4 4.6 4.0 5.5 2.8 2.7 4.8 4.1 3.8 4.3 3.4
##  [595] 5.3 5.5 4.5 4.7 5.2 2.5 4.9 5.9 5.9 4.1 3.9 4.9 4.9 4.4 6.7 5.6 4.9 5.1
##  [613] 3.3 5.1 4.2 4.7 3.6 3.4 4.7 5.3 5.0 4.7 3.4 5.0 4.6 6.0 5.1 3.2 5.4 4.6
##  [631] 4.2 5.5 2.8 4.5 4.8 3.7 5.4 5.2 4.0 5.9 5.5 5.7 5.7 5.4 4.1 5.7 5.5 4.9
##  [649] 5.5 4.0 4.6 4.1 5.2 4.5 3.8 4.0 2.5 4.0 4.7 5.3 3.6 6.4 5.0 3.7 4.6 4.2
##  [667] 5.1 3.9 4.1 5.7 3.9 3.2 3.6 4.1 5.8 4.4 5.1 6.4 3.6 3.6 5.8 4.0 3.2 4.8
##  [685] 4.2 4.1 4.6 4.9 3.1 5.5 3.7 5.9 4.5 3.6 5.4 4.0 3.4 5.5 5.6 4.0 5.3 5.1
##  [703] 3.5 5.1 5.3 3.9 4.7 4.5 4.5 3.4 6.0 3.4 4.1 5.9 5.0 5.1 4.3 3.6 3.9 5.5
##  [721] 5.0 4.2 5.0 4.6 3.7 6.3 3.9 4.0 6.4 4.7 3.5 4.8 5.4 3.5 3.9 5.5 6.2 5.6
##  [739] 5.0 4.5 3.3 6.1 1.7 4.3 3.7 6.2 5.0 5.8 5.4 5.7 5.6 4.2 5.3 4.4 4.5 3.6
##  [757] 5.5 3.4 5.3 4.1 3.8 4.6 4.2 5.8 4.9 5.6 4.0 6.0 3.4 4.3 4.3 4.4 3.9 3.7
##  [775] 5.2 4.2 4.6 3.2 4.8 3.1 4.3 4.8 4.8 2.9 3.1 3.7 3.2 4.8 4.2 3.0 4.4 5.4
##  [793] 4.3 3.6 3.8 4.6 3.8 4.0 4.2 4.5 3.7 4.8 5.0 4.8 3.5 4.9 4.3 5.2 6.0 5.1
##  [811] 4.7 5.8 6.6 4.6 3.9 3.6 5.8 4.5 5.6 5.9 6.2 3.4 5.4 4.5 4.7 3.1 4.9 4.3
##  [829] 4.3 6.0 4.7 3.4 4.1 4.8 3.8 4.3 5.6 4.1 6.2 6.2 5.5 4.7 5.6 3.9 5.6 3.1
##  [847] 4.4 3.8 3.2 3.7 2.8 3.5 3.3 5.2 4.4 4.3 4.9 3.4 5.5 3.3 3.5 3.7 4.6 4.5
##  [865] 4.9 3.7 4.0 5.1 5.1 4.0 4.7 4.9 4.9 6.1 5.7 6.3 4.1 2.8 5.1 4.7 4.8 2.7
##  [883] 5.3 3.7 4.3 3.7 4.9 4.7 4.7 4.0 5.2 4.8 5.0 5.7 3.6 4.1 3.5 3.7 3.4 4.6
##  [901] 4.7 5.1 3.4 4.0 4.4 3.9 3.2 4.3 5.3 4.6 4.3 4.3 5.6 2.9 4.3 5.8 3.6 6.1
##  [919] 4.1 4.4 4.8 2.2 3.4 5.4 3.7 3.1 4.6 2.4 3.3 3.8 4.5 3.4 5.6 6.1 2.4 2.9
##  [937] 6.0 3.8 5.0 3.8 4.4 4.8 3.7 6.8 3.4 4.0 3.5 4.9 3.7 4.5 3.6 3.2 4.3 5.2
##  [955] 3.9 4.1 4.9 3.7 4.4 4.7 3.9 3.4 4.3 6.8 3.5 4.4 5.7 5.1 4.6 4.5 4.2 4.8
##  [973] 2.7 4.2 3.8 4.0 6.3 3.9 3.8 2.7 4.3 3.9 5.9 3.3 5.3 4.5 4.6 4.9 4.5 4.9
##  [991] 4.3 4.5 4.5 5.4 5.4 4.8 4.6 3.0 4.0 3.4
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
##    [1] 5.1 4.2 4.3 5.7 4.0 6.1 4.4 4.7 4.6 4.4 5.1 5.1 5.8 4.4 4.8 7.1 4.1 3.7
##   [19] 3.6 3.8 3.4 5.0 4.2 4.2 2.9 4.4 4.1 4.1 5.5 3.7 3.2 5.9 4.6 5.6 5.0 5.4
##   [37] 4.7 4.5 3.7 4.3 5.8 4.6 4.5 4.6 7.0 2.7 3.8 5.5 4.8 3.7 5.3 4.5 5.6 4.6
##   [55] 5.4 4.6 3.8 3.5 4.6 4.7 4.2 5.3 5.0 5.6 4.4 3.7 4.9 2.5 3.5 4.2 4.4 4.9
##   [73] 1.9 4.9 4.6 3.9 3.0 5.2 3.5 4.1 6.0 4.8 4.4 5.9 4.5 4.3 3.6 6.1 4.4 5.1
##   [91] 3.3 5.8 3.6 3.3 4.7 4.9 5.8 4.7 4.8 6.2 4.1 2.8 5.5 3.4 3.1 4.6 3.2 5.4
##  [109] 6.3 3.3 5.2 5.0 4.3 5.5 4.9 5.0 3.0 4.2 3.6 3.3 2.4 3.7 2.4 5.4 4.9 4.2
##  [127] 4.3 3.5 4.1 2.5 6.7 5.3 4.6 5.0 4.9 3.4 4.7 3.9 3.3 3.9 4.5 5.7 5.2 5.6
##  [145] 3.9 3.8 4.4 5.1 3.9 6.1 4.6 4.5 3.0 2.3 3.8 3.5 3.9 3.9 5.8 5.7 3.6 5.3
##  [163] 4.1 3.6 4.5 3.0 5.4 4.7 4.9 4.8 4.3 4.1 5.5 4.8 4.1 3.1 4.5 5.2 3.2 3.9
##  [181] 5.7 4.1 4.9 4.8 5.6 5.0 3.2 4.6 5.3 5.1 4.2 3.3 2.3 4.1 3.0 3.7 6.0 4.4
##  [199] 4.8 4.4 4.4 4.7 3.6 3.0 3.2 4.7 4.1 4.2 5.7 4.2 6.1 4.9 4.0 3.2 3.7 3.9
##  [217] 3.1 4.6 4.1 3.8 5.1 3.0 3.1 3.6 3.5 4.0 3.1 4.5 6.8 4.4 5.9 5.1 4.1 3.9
##  [235] 3.5 4.0 6.2 3.2 4.7 5.3 4.6 4.8 4.0 5.8 5.8 3.0 3.9 4.9 3.8 5.7 3.8 5.0
##  [253] 3.4 5.3 4.0 3.9 4.9 3.9 3.3 4.6 4.9 3.2 5.1 4.8 3.6 4.1 3.9 3.7 3.8 3.8
##  [271] 2.8 5.4 3.9 4.4 3.7 3.3 5.8 3.2 4.1 4.1 4.9 5.9 3.8 2.3 2.6 6.4 4.6 4.8
##  [289] 4.6 4.3 4.1 4.0 5.9 5.1 5.5 3.2 4.0 2.8 3.5 4.1 3.9 3.5 5.4 4.5 4.3 4.3
##  [307] 5.7 4.9 4.0 4.5 2.6 6.3 3.5 4.5 3.9 6.0 5.3 3.7 2.7 5.2 4.2 5.7 3.5 5.1
##  [325] 3.6 5.2 4.7 4.9 5.8 5.2 3.1 3.2 5.0 3.9 5.6 5.2 3.8 3.6 3.0 3.8 4.7 5.2
##  [343] 3.6 3.4 3.7 4.9 4.3 6.1 4.9 4.2 2.5 4.6 5.2 3.8 2.8 5.5 5.2 5.4 4.8 4.5
##  [361] 4.9 2.8 4.2 4.5 4.2 5.0 6.5 6.1 4.4 5.1 4.7 4.3 3.8 4.4 3.2 5.8 4.8 4.9
##  [379] 4.7 5.6 3.1 5.5 3.3 3.5 3.6 4.7 3.9 5.2 5.2 6.2 4.5 3.8 4.4 6.9 6.4 4.1
##  [397] 4.6 4.7 4.2 4.7 4.4 4.3 4.7 2.8 5.0 3.7 4.9 4.5 4.1 4.9 3.3 4.3 6.1 5.5
##  [415] 5.5 4.5 3.1 5.1 2.9 4.8 3.4 4.0 3.2 4.7 5.4 5.4 4.5 4.4 3.4 6.5 4.0 5.3
##  [433] 4.3 4.7 5.2 4.1 5.3 4.4 3.9 3.2 4.1 5.6 4.0 4.8 5.7 4.8 4.3 3.4 4.0 4.5
##  [451] 4.5 4.0 4.2 4.3 4.8 4.9 5.1 3.7 3.7 2.5 5.0 3.8 3.6 5.2 5.9 2.6 4.2 6.3
##  [469] 5.1 4.4 5.2 4.9 4.7 4.0 3.0 4.1 5.5 5.0 2.8 5.8 4.8 5.7 4.5 4.7 4.7 4.2
##  [487] 2.9 3.2 4.4 5.7 4.2 3.9 4.2 3.1 3.7 2.8 4.7 3.2 4.2 4.9 3.6 4.7 4.8 3.3
##  [505] 2.7 4.0 4.6 6.1 4.6 3.4 5.0 4.7 4.7 2.7 2.8 4.5 3.4 5.6 4.0 3.4 4.4 4.9
##  [523] 4.1 3.6 4.5 6.3 6.0 6.4 3.4 3.9 4.3 3.9 3.6 3.8 4.9 3.8 4.2 4.4 5.7 3.2
##  [541] 4.9 4.4 5.2 5.8 3.9 3.6 4.6 4.1 4.6 4.2 5.8 5.7 3.9 3.0 4.0 3.3 4.7 2.5
##  [559] 5.4 3.3 4.1 3.5 3.4 5.5 5.3 4.7 4.2 4.7 3.8 3.4 5.5 4.3 6.5 3.8 4.0 5.4
##  [577] 4.8 5.5 4.2 2.8 4.3 5.9 3.1 4.2 3.1 4.9 4.8 5.0 3.6 4.7 4.5 5.3 3.7 1.9
##  [595] 6.1 3.1 5.2 3.4 3.4 3.7 5.1 4.0 5.5 4.5 4.6 3.6 5.3 5.1 4.7 5.0 4.9 4.1
##  [613] 3.5 5.1 6.4 5.7 3.2 4.0 2.8 5.2 5.2 4.4 3.8 4.3 4.8 4.8 3.0 5.1 4.0 5.0
##  [631] 4.5 5.2 4.8 2.3 4.8 5.3 6.5 3.8 3.9 2.6 5.3 4.8 4.3 2.7 4.6 5.7 3.3 4.3
##  [649] 6.2 4.6 5.4 5.3 3.7 5.1 5.4 3.9 3.9 4.5 3.9 4.3 5.3 5.0 5.9 4.8 3.9 2.8
##  [667] 6.2 4.5 2.5 5.0 3.1 4.4 5.2 4.3 4.4 3.2 4.5 3.5 4.6 4.9 5.1 3.0 4.4 6.5
##  [685] 5.2 6.1 4.9 4.9 4.6 5.4 6.1 3.8 4.8 4.2 4.0 5.2 4.4 4.6 4.7 3.2 4.9 6.3
##  [703] 5.2 4.1 3.1 3.6 4.8 5.9 5.5 5.1 5.2 4.1 4.2 4.9 3.6 3.4 4.2 5.8 5.1 5.1
##  [721] 5.2 2.7 4.7 5.4 5.4 4.6 6.1 4.6 5.1 2.7 4.8 5.7 3.8 3.3 4.8 6.6 5.0 4.7
##  [739] 4.7 4.1 5.1 5.9 4.4 3.4 4.5 4.4 4.4 4.5 4.1 3.5 3.1 4.9 3.6 2.8 5.8 4.3
##  [757] 5.9 3.1 6.2 6.3 4.8 4.5 4.6 2.9 4.9 3.4 4.0 4.2 3.0 4.3 4.4 4.8 2.5 4.4
##  [775] 5.6 4.9 3.1 4.5 5.7 4.9 4.8 4.4 4.8 5.2 5.1 4.2 3.6 5.6 4.1 4.8 4.0 5.8
##  [793] 4.0 4.4 4.7 5.4 4.3 5.0 4.4 4.2 5.7 5.2 4.6 5.0 4.2 4.2 3.5 6.1 5.5 4.0
##  [811] 4.9 4.6 5.2 3.6 4.8 4.2 5.5 5.1 4.8 3.3 4.3 4.9 4.4 4.6 4.2 3.4 5.7 5.6
##  [829] 5.4 4.5 5.2 4.1 6.6 6.2 3.7 3.8 4.2 3.4 3.4 4.7 3.0 4.3 4.0 4.9 4.7 4.1
##  [847] 6.1 4.5 4.1 4.4 4.1 4.1 4.5 3.9 4.6 4.3 5.1 5.8 4.4 3.8 3.7 4.7 3.8 4.6
##  [865] 4.3 4.0 4.4 4.7 4.6 4.1 6.1 3.0 4.8 3.8 3.3 5.1 5.3 4.1 4.5 4.7 4.9 4.2
##  [883] 5.1 3.8 4.3 4.7 6.0 4.8 5.3 4.2 5.5 5.7 5.5 4.7 5.9 3.4 5.0 3.9 3.5 3.5
##  [901] 4.9 3.9 3.2 7.4 6.9 4.2 5.1 6.5 4.9 3.2 4.5 2.2 4.8 5.5 5.2 5.0 6.0 3.1
##  [919] 6.2 4.2 5.2 4.5 3.4 4.3 3.8 5.4 3.3 6.7 4.9 4.0 4.3 4.6 3.8 5.1 4.8 5.5
##  [937] 3.0 4.6 3.8 4.7 4.3 5.0 3.6 4.9 6.5 3.7 4.0 3.5 5.7 3.9 4.3 5.5 3.5 4.5
##  [955] 4.3 4.0 4.1 4.8 4.5 3.0 5.1 5.2 4.5 3.9 5.1 4.2 3.5 5.6 6.5 3.9 6.5 3.3
##  [973] 3.8 4.6 4.3 3.6 4.3 6.4 3.2 5.8 2.8 3.9 5.3 4.6 3.7 6.7 4.6 5.2 4.7 4.4
##  [991] 4.3 3.8 6.5 2.2 3.2 5.1 5.2 5.3 3.0 2.4
## 
## $func.thetastar
## [1] -0.0373
## 
## $jack.boot.val
##  [1]  0.5297214  0.3776119  0.2964286  0.1489297  0.1066852 -0.1231231
##  [7] -0.1992021 -0.3117978 -0.4736842 -0.6042017
## 
## $jack.boot.se
## [1] 1.073204
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
##    [1] 4.7 4.0 4.2 6.2 5.9 5.5 4.6 3.2 2.9 5.8 4.5 4.5 4.2 4.3 4.1 6.3 4.6 5.9
##   [19] 5.1 4.1 3.4 1.7 4.9 3.0 5.2 3.0 5.2 4.2 4.7 6.0 5.7 4.5 5.3 3.3 4.1 5.3
##   [37] 4.9 6.1 5.5 5.1 5.7 5.4 5.0 4.2 3.6 5.0 4.1 4.5 3.8 4.6 4.7 4.2 4.9 6.0
##   [55] 4.0 5.6 5.2 1.8 5.2 3.6 5.3 2.3 3.1 3.5 2.9 3.9 3.5 4.4 4.9 4.8 3.9 3.8
##   [73] 4.2 4.9 3.8 6.4 4.3 5.1 5.4 4.7 4.7 3.7 5.5 4.3 4.7 3.6 4.0 3.7 4.6 4.3
##   [91] 4.9 5.5 5.5 2.9 3.2 3.4 4.5 7.2 2.3 5.2 3.8 4.2 5.6 3.7 5.3 4.9 3.8 5.4
##  [109] 4.4 4.4 3.8 3.9 3.1 4.7 2.5 5.3 4.8 3.5 3.1 4.4 5.3 5.6 4.8 4.4 5.1 4.7
##  [127] 3.0 4.0 4.7 4.1 6.5 3.5 5.3 4.8 5.0 5.6 4.9 4.8 4.2 2.2 3.3 4.8 3.0 4.4
##  [145] 4.3 5.0 5.0 5.2 3.7 3.8 4.2 2.8 4.2 4.8 4.1 5.1 5.5 5.3 4.5 3.8 5.3 5.5
##  [163] 4.6 5.3 4.1 5.5 4.5 4.3 4.6 5.2 4.7 3.9 6.8 2.3 3.6 4.7 5.5 3.2 6.7 4.8
##  [181] 5.5 5.0 3.1 5.0 4.4 4.9 5.3 6.0 5.1 5.4 4.8 6.3 2.7 4.5 5.0 6.3 3.4 6.9
##  [199] 5.2 5.8 5.3 6.0 4.6 3.9 5.5 3.8 3.5 4.6 2.9 4.5 3.9 6.9 6.1 4.8 4.2 5.6
##  [217] 5.7 3.7 4.4 4.5 4.3 4.3 4.5 3.1 4.1 4.4 2.7 4.4 5.9 4.8 4.5 3.2 7.3 5.5
##  [235] 4.5 4.3 5.6 3.6 4.9 3.8 5.0 3.8 3.9 4.1 3.1 4.4 4.6 5.3 4.5 5.5 4.7 4.1
##  [253] 4.3 4.1 5.1 5.4 4.2 5.2 4.9 5.9 4.5 5.0 5.1 3.7 3.8 6.3 3.7 6.1 4.4 4.9
##  [271] 4.2 4.5 4.5 2.3 3.0 5.0 4.3 3.2 3.1 5.3 4.7 4.1 4.0 3.4 5.5 3.9 5.3 4.4
##  [289] 5.1 5.7 2.8 2.8 4.6 4.4 5.0 5.1 6.6 4.7 3.1 5.0 5.7 4.0 3.1 4.5 4.7 5.3
##  [307] 4.0 4.0 5.6 3.8 3.0 4.9 5.3 5.0 5.8 4.2 4.4 4.4 7.0 3.9 5.1 4.2 4.2 5.0
##  [325] 3.9 4.3 6.0 3.5 5.1 4.0 2.8 4.6 4.3 4.4 5.2 4.3 5.0 4.8 5.1 4.3 4.3 4.7
##  [343] 4.7 3.3 5.5 5.9 3.3 4.5 5.2 4.6 2.8 3.7 4.7 3.0 4.2 2.8 2.1 4.0 4.4 5.2
##  [361] 3.7 4.3 5.3 5.6 5.0 4.9 4.8 3.6 4.0 4.7 4.2 6.5 3.8 3.5 4.3 6.2 2.3 3.5
##  [379] 4.6 3.6 4.1 4.7 3.4 6.3 3.4 5.1 2.8 3.5 5.3 4.3 3.9 5.8 3.5 5.2 5.7 4.1
##  [397] 4.6 3.8 5.6 4.4 4.8 5.1 5.8 6.4 5.4 5.2 5.5 4.6 3.9 4.9 6.1 4.2 4.9 4.6
##  [415] 3.6 3.8 4.9 6.3 4.0 4.2 5.7 4.5 5.0 4.5 3.9 3.7 4.7 4.6 4.4 2.8 4.4 7.0
##  [433] 5.2 4.9 3.4 5.8 4.8 4.8 5.6 4.2 5.2 4.0 3.3 4.7 4.6 3.8 4.4 4.6 3.9 4.2
##  [451] 3.2 4.1 4.1 4.1 4.6 5.3 3.0 4.1 3.4 4.2 4.8 3.6 4.8 4.7 5.5 5.3 5.4 3.1
##  [469] 4.6 4.3 4.7 5.8 3.7 5.3 3.9 5.9 4.8 3.5 5.1 5.3 3.4 4.9 5.0 4.8 5.2 5.9
##  [487] 5.7 3.3 3.6 3.3 4.6 2.8 5.3 5.4 5.6 3.9 4.9 4.1 4.0 4.4 5.2 4.2 4.7 5.7
##  [505] 5.3 6.5 4.9 5.7 4.2 4.8 4.2 4.6 4.7 3.0 5.0 3.7 4.0 4.4 4.8 4.2 3.8 3.9
##  [523] 5.1 5.4 5.6 4.7 2.7 2.9 5.1 3.2 2.9 3.0 4.6 5.0 4.0 5.2 3.5 5.5 4.4 4.6
##  [541] 4.5 5.1 5.9 3.6 4.0 5.1 5.1 4.9 5.7 6.0 5.5 5.0 2.8 4.7 4.3 3.4 6.1 3.8
##  [559] 5.4 4.8 6.2 5.1 4.3 4.9 5.4 3.5 4.4 4.4 4.8 2.1 5.4 5.0 5.8 3.6 4.0 4.9
##  [577] 6.3 5.0 4.5 5.1 4.7 5.6 3.7 5.2 3.3 4.6 6.2 3.7 2.4 3.6 4.0 5.0 3.9 4.0
##  [595] 5.5 4.6 4.6 4.3 4.8 4.7 3.5 4.7 4.8 4.4 4.0 4.2 4.5 5.0 4.4 5.5 3.4 4.3
##  [613] 6.0 4.0 4.5 4.5 2.7 2.4 3.9 5.1 6.8 3.8 5.2 5.1 5.0 5.9 3.7 4.8 4.3 5.3
##  [631] 4.3 4.8 3.3 3.7 5.6 5.7 4.3 4.7 5.9 3.8 3.6 4.7 4.2 5.2 4.7 2.4 4.9 3.2
##  [649] 5.8 3.8 5.6 4.1 6.2 4.1 5.3 4.1 5.0 4.9 3.4 2.7 4.8 5.1 5.1 2.3 3.3 4.9
##  [667] 4.2 4.3 3.2 4.5 4.7 4.0 4.6 3.3 5.4 4.4 4.1 2.9 5.2 5.3 5.7 3.7 3.8 5.4
##  [685] 4.8 4.5 4.0 2.9 4.9 3.6 4.0 3.0 5.5 6.4 5.2 5.0 3.8 5.3 3.3 3.7 3.9 5.6
##  [703] 6.0 4.6 4.3 3.7 2.8 4.4 3.8 4.4 4.9 4.2 4.8 5.5 5.0 3.9 5.3 6.4 4.5 3.7
##  [721] 3.4 5.1 3.9 4.2 3.5 4.2 4.9 3.9 4.4 4.6 4.5 3.9 5.5 4.2 3.9 6.2 5.7 4.5
##  [739] 4.0 4.4 4.7 5.3 4.9 5.1 3.1 5.3 4.2 4.8 3.2 5.0 3.0 4.2 5.0 4.8 4.0 4.0
##  [757] 5.1 4.3 4.2 5.0 5.5 3.8 3.5 3.7 5.1 6.4 5.1 3.5 5.6 5.5 3.8 4.3 4.1 3.1
##  [775] 4.8 4.8 5.0 6.1 4.1 4.6 3.6 5.0 4.2 4.5 5.1 4.7 4.5 4.7 4.7 6.7 4.1 4.2
##  [793] 5.2 5.5 4.0 4.5 3.8 4.3 4.8 5.5 4.3 4.3 6.2 6.2 4.4 5.2 5.0 4.2 6.0 4.6
##  [811] 3.6 2.8 6.9 4.8 3.9 5.1 6.3 5.5 6.2 5.3 5.3 2.4 6.0 4.9 6.1 4.3 5.4 4.7
##  [829] 4.6 5.3 4.4 3.5 4.5 4.1 4.9 4.9 4.4 4.2 4.7 4.8 5.1 5.2 5.9 4.3 4.6 4.4
##  [847] 5.9 4.6 5.6 6.6 3.7 4.3 4.5 4.9 4.8 5.1 4.9 3.7 3.3 4.4 5.0 4.7 4.1 3.8
##  [865] 3.0 3.4 5.8 3.3 5.0 4.6 4.2 3.7 4.2 5.4 4.4 5.1 4.7 3.3 2.9 4.4 4.8 4.4
##  [883] 4.3 4.4 4.1 3.9 5.4 3.4 5.6 6.3 6.0 1.9 3.7 5.1 3.2 4.8 3.5 3.6 6.6 5.9
##  [901] 5.4 6.3 5.9 3.9 2.7 4.4 3.6 4.6 5.1 4.5 3.9 6.4 6.1 6.1 5.4 3.7 5.1 5.7
##  [919] 4.8 5.4 5.3 4.5 4.9 4.1 3.6 4.1 4.6 3.1 4.1 5.2 3.6 4.3 3.4 4.4 6.5 4.3
##  [937] 5.5 4.2 3.2 4.4 5.6 4.9 5.6 5.1 3.5 3.2 4.7 5.4 4.2 2.9 2.7 5.7 4.3 5.1
##  [955] 3.8 4.3 5.4 3.8 4.4 4.1 5.0 5.0 4.4 4.3 4.4 5.0 4.9 5.8 3.2 5.6 4.6 4.2
##  [973] 3.9 5.2 4.9 4.8 4.1 3.6 3.1 5.0 4.3 3.9 4.9 4.8 4.5 5.3 3.8 4.4 6.9 3.8
##  [991] 2.9 4.2 6.7 3.7 4.4 3.5 4.1 5.4 3.2 5.0
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.500 5.400 5.300 5.300 5.200 5.100 4.916 4.800 4.600 4.500
## 
## $jack.boot.se
## [1] 0.9763193
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
## [1] 0.6897887
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
##   3.159318   6.402645 
##  (1.344982) (2.954175)
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
## [1]  0.9296082  0.3433544  0.4722085  0.5377488  0.9075917 -0.5271777
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
##    [1] -0.0030944790 -0.4028283399  0.4123781709 -1.1228282643  0.6508702381
##    [6]  0.6845392876 -0.4665713417 -0.4814222601  0.9000079258  0.0820199801
##   [11]  0.4017316483  0.5239638125  0.1251245665  1.3391611157  0.6639045443
##   [16]  2.0156865325 -0.8446678133  0.3614731426  0.5638369423  0.5955907431
##   [21] -0.8424527744  0.8842436510 -1.2089669278  0.1934766269  0.7053533891
##   [26]  0.7926079928 -0.4514011606  1.3442261699  0.9064680463  0.8328222724
##   [31] -0.1274526530 -0.3666123665 -0.8477291023  0.2953256305  0.4283806519
##   [36]  0.1227371812 -0.0191354584  1.6404467978  0.6041994467 -0.1955131690
##   [41]  0.6201333372 -0.0667931092 -1.1249881474  1.4144838254  0.7550599506
##   [46]  0.3737822659 -0.3306619199  0.9983641720  0.4262391848  0.2861666117
##   [51]  1.2069633039 -0.0119489952 -0.5434383037  0.0692750060 -0.2093275273
##   [56]  0.4744613926 -1.2678778894  1.1014131029 -0.2243027141 -0.7265280803
##   [61]  0.1398686793 -0.0210732780  1.0780086165  1.7534955142  1.2744000167
##   [66]  0.4279002142  0.1078279202 -0.0570453488  0.0010304551  0.1387598411
##   [71]  0.9156435648  0.5726832788  0.5563466141  0.4017411662  2.1543967832
##   [76]  0.8131804128  0.9386296750  0.6215484827  0.9405932995 -0.2449441781
##   [81]  0.8765118596  0.1225792635  0.9732149386  0.8735331605 -0.3209353863
##   [86]  1.1245818936  0.8512448664  0.2625102409 -0.2204352795  0.0061066303
##   [91] -0.1506659306  0.4056365022  1.1688524223  0.5862548515  0.5374861145
##   [96]  0.4032856623  0.7790692610  0.1207600605 -0.0806859399  1.1746286350
##  [101]  0.9225523589 -0.5990245138 -0.4936851842 -0.9901826442 -0.5833630523
##  [106] -0.7079708249  0.5197325524  1.5268841544  0.9822907257  1.2298622236
##  [111]  0.8589670804  0.0304244742 -1.0445212172  0.5657327111  0.5293423338
##  [116]  1.4326001842 -0.5841079666  0.6600647884  1.0342132015 -0.0980077443
##  [121]  0.6053638848 -0.0132372408 -0.3003127218  0.6050707323  0.0565707752
##  [126]  0.6099402233 -0.7281097674  0.5452226037 -0.0858881570  0.0112119471
##  [131]  1.3864692468  0.5395978540  0.8400137699  0.6836472274  0.9065271206
##  [136] -1.1357282429  0.0270280725 -0.4291959270  1.0453423895  0.0206278548
##  [141] -0.1355062849  1.2052592930 -0.7767035266  0.4435446959  0.3194689771
##  [146] -0.1923614107  0.4360142082 -0.1798893226  0.0742544153 -0.1027401257
##  [151]  1.4766740257  0.8851785478  1.0128496295  0.4226551543  0.7972108538
##  [156]  1.8766279582  0.6811746552  0.0860476498  0.9599138359 -0.0730861822
##  [161]  1.3941645906  0.5739424385  0.6576559996  1.5003264766 -0.5967567109
##  [166] -0.1950014502  0.3284217819 -0.8854097221 -0.2750282835  0.7421659543
##  [171]  0.3696673451  0.4589100598 -1.4078838251  0.8787565685  0.9774438005
##  [176]  0.6299844761  0.0750753465  0.7610292527 -0.5109253259 -0.1715246815
##  [181]  0.6947179428  0.4974640489 -1.1531385705  0.2058355361  0.3706742723
##  [186]  1.0302992601 -0.0919492047 -0.7234400057  0.0564663225  0.1300089094
##  [191]  0.7904128012  0.4363711707 -0.6024629904  0.0048947884  0.7603909440
##  [196]  1.0063786211 -0.2634807109  0.4268654887  0.8263727973  1.4497443993
##  [201] -0.1418436936  0.7486214230  1.0449089386 -0.5947180463  0.5054759823
##  [206]  0.9101271428  0.6515890438  1.5759123691  0.6969968282 -0.2604517389
##  [211] -0.3369833326  0.8240059874  1.0047230991  1.1630637492  0.5653675695
##  [216]  0.9157710259  1.0578887285  1.2244983100  0.0842051049  0.0943188336
##  [221]  0.7309975092 -0.5063643349  0.7744249337 -1.9580977547  0.8087175508
##  [226]  1.2884276638 -0.2425336298  0.9291048325  0.5975978325  1.0144161014
##  [231]  0.7546227946  0.6031363836  0.8532837487  1.9328568526  1.5504893237
##  [236]  0.5967091968  0.5678545098 -0.7102610880 -0.0625984179  0.6108274011
##  [241] -0.4142929039  0.5664989181  1.0865716254  0.6023262461  0.5893653276
##  [246] -0.4769612030  0.6913187501  1.0387516201  0.9763893773  0.5750912071
##  [251] -0.1782331059  0.4843776005 -0.0207586791  0.5257489084  0.0916616188
##  [256]  0.3291009805  0.9516935273  0.3247640977  1.0706301554  0.4997017846
##  [261]  0.4185869023  0.9179695782  0.6102465383  0.3106877845  0.9865209391
##  [266]  0.5191727177 -0.3372833421  1.6044284668  0.9803801793 -1.3885180443
##  [271]  0.5887840401  1.7835484402 -0.3999767648 -0.3167489049 -0.5653944862
##  [276]  0.0644499038  0.9193966727  0.6341123773  0.2972620406  0.5362810168
##  [281]  0.6015626463  0.4495239515  0.4356828331  0.9327049842 -1.2979522452
##  [286]  0.6105800115 -0.3627087161  0.8700418127  0.2422784137 -0.7252922367
##  [291]  0.0041210642  0.8472688681  0.6726518764 -0.2514198616 -0.2305910298
##  [296]  0.9403221041 -0.0613168239 -0.5295699728  0.0001084411  0.7042303823
##  [301]  0.0876019556  0.4816424357 -0.2473217433  0.7859869881 -0.0771879642
##  [306] -1.3162304995  1.5890083321  0.8374781840  0.6237420043  0.3978808073
##  [311]  0.3093502721  0.0914004350  0.6692502121 -0.0041210735  1.0305078380
##  [316]  0.9568432454  0.6490842504  0.6490763115  0.4279071754  0.4459328414
##  [321]  0.9897345634 -0.6284444550  0.1471886724  0.7782399665  0.8784354500
##  [326] -0.2900500656  0.7965213021  0.5039767996 -0.0756879467  1.5508210101
##  [331]  1.2309256919  1.2537730808 -0.1798654272  0.5321966535  1.2673159967
##  [336]  0.1518834329  1.4021136045  1.0911119942  1.4431205315  0.5055970149
##  [341]  0.8523589186  1.3780565012 -0.6933416411 -0.0002077491  0.9742510589
##  [346]  0.9842007856  0.3437961422  1.0949820495  0.5441697721  0.5396226144
##  [351]  0.9494333341  0.5697754497  0.8489438075 -0.3625836086  0.5603179852
##  [356]  0.6272399002  0.7053459165  0.7239217379 -0.6115901722 -0.2328047061
##  [361]  1.1344239435  0.6047095925 -0.5655326123 -0.0122952820 -0.0086833046
##  [366]  0.3411344749 -0.5653944862  0.1048061587  1.1268303949 -1.0850581271
##  [371]  0.7759711123  0.7182049057  0.0391385349  0.1957738291  0.5540966225
##  [376]  0.4880097966  0.1369607487  1.1497329709  0.4489916117  0.2967529513
##  [381]  1.0290060600  1.0829310949  0.3118285689 -0.4120412079  1.1303592574
##  [386]  0.2708847855 -0.8171793596 -0.0234887400  0.4923435545  0.7045024257
##  [391] -0.0223093461  1.1219385011  1.0110843180  0.0701276684  1.4647871647
##  [396]  0.1052123537 -0.1460367470  0.7707322393  1.1129006795 -0.4470147640
##  [401] -0.9457754381 -0.5226875934  0.4389094407  0.6201333372  0.9192953726
##  [406] -0.1679443563  0.5712316400 -1.6851960179  0.9464439595  0.7477089229
##  [411]  0.4620962946  0.0518568451 -0.3299437369 -0.0079088405 -0.0839119934
##  [416]  1.0441614843  0.9672894745  0.1732768080  0.2348836141  0.4446338655
##  [421]  0.0809356405  0.8340180911  0.7143328399  0.7228770815  1.0071198106
##  [426]  0.8868003776  0.8699553314  2.6219615466  0.3770265206  0.4533473909
##  [431]  0.9927185927  0.7812419530  0.1991211753  1.0719263796  0.8876951192
##  [436]  0.1711925979  1.5386716057  0.7287995815  0.7637422624  1.0115070388
##  [441] -0.1541844270  0.4727775243  0.4768161354  0.4593737497 -0.1505508788
##  [446]  0.8310258704  0.9598305804  0.3869239179  0.4047639983  0.5882346655
##  [451]  0.6897886953  0.4441260103 -0.5626522677  0.9848098368 -0.3661742266
##  [456] -0.8986322554 -0.3229051300  0.7326115557  1.1658052877  0.5339748081
##  [461]  0.6561435251  0.1375692549  0.0400518279  0.7367763208 -0.1135843380
##  [466]  0.2480095646  0.2223743079 -0.8701284953  0.6299844761  1.4037040694
##  [471]  1.2259413836  0.7223507171  0.4521095443  1.5974532972  0.0773511618
##  [476] -0.2141481250  0.3731705288  0.3259588456  0.1312830155  0.7050213672
##  [481]  0.4246244823  0.8112868137  0.1642556086 -0.0238566520  0.4877393458
##  [486]  0.1572073687  0.2167286911 -0.0646627577  0.5663203579  0.0737811266
##  [491]  0.6412674618  1.4469178454  0.4593737497  1.2669470647  0.3975852863
##  [496]  2.2994816109  0.6068623003  0.5715532940 -0.6922855650 -1.2432112891
##  [501]  1.2294469144  1.2784767282  0.3401762605  0.6946544107  0.6897886953
##  [506]  2.3813675452 -0.7898000028  0.6803103202  1.0995513395  0.4478531751
##  [511]  1.0733642500  0.8796261564  0.8906151343 -1.1856604502  0.8785265075
##  [516]  0.2268806527 -1.4244807379  0.9554721168  1.4657551465  0.7792105933
##  [521] -0.4190300403  1.0488613102  0.9665607798  0.5344250963  1.7238161370
##  [526]  0.6189519740  1.1207015670  1.4505155343  0.3906392321 -0.2277057147
##  [531]  0.7193091062  0.6844126621  1.4764355573  2.0988878653  0.9893995337
##  [536]  0.5954116999  0.3907103378 -0.2676675737 -0.6884068733 -0.3821479656
##  [541]  0.3125789051 -0.0264905216  0.6483878962 -0.2094714268  0.9713216323
##  [546]  0.8739524797 -0.8969644248  0.1519231935  0.8462236205 -0.1882315907
##  [551]  0.4329086157  0.0315287301 -0.6446013097  0.1539102179  0.9679958780
##  [556]  0.2440644578  0.4533113723  0.6248774005  0.4264814491  1.8420453474
##  [561]  0.1082840947  1.6883266001  0.6009083508  0.5267651996  0.9438001890
##  [566] -0.0556866393  0.5517695123  0.8912640810  0.0505080749  0.5769407693
##  [571]  0.1911015923  0.9523296164  0.0468599650  1.0366862656  0.5087704125
##  [576]  0.7011191159  0.6422142110 -1.2730110454  0.5286285452  0.6425733088
##  [581]  0.9009453855 -0.7226548765  1.2259123214  0.1149018677  0.8435395696
##  [586] -0.2170766490  0.3742157355  0.5885347979 -0.5825699970 -0.2967644579
##  [591]  1.2636560912  1.5518768164 -0.3828730154  0.1642556086  0.5020986276
##  [596]  0.0554369701  1.2466550396  0.8779416472  0.1594639631  0.2229720786
##  [601]  0.1472857348  0.2002629749  0.9393985070  0.9105397195  0.9179205194
##  [606]  1.0140755052  0.3081172214  0.7623360505  0.9434168282  0.1145195700
##  [611]  1.0332177526  0.6027624912  0.5191821617 -1.0147391774 -0.7488477761
##  [616]  0.9110693979  0.4943916298 -0.4370622903  0.8152608959  0.1005758404
##  [621]  0.9137910960 -0.1348681601  0.2836061562  0.5366107150  0.8148774039
##  [626]  0.1875338309  1.2444600065  0.4476763782  0.9346391705  0.2475782743
##  [631]  0.8538074070  1.5626437627  1.0848239328  0.5953889961  1.0642818860
##  [636] -0.1461656848  0.8607733273  1.2456642171  1.1157928652 -0.6797990760
##  [641]  1.1767405779  0.1278563796  1.2243617747  0.1227288145  0.0691328652
##  [646]  2.0339500621  0.2587986623  0.5714516251  0.4041526789 -0.0532507933
##  [651]  0.8952804416  0.9190351908  0.8730074570  0.3459594768  0.8343958736
##  [656] -1.0107113758  1.0278048645 -0.3626710322  0.7496796634  1.0481360210
##  [661]  0.1304946426 -0.4124433415 -0.7959119390  1.3847350122  1.2589243037
##  [666]  1.0923055440 -0.5815082886  0.3506847662  0.4270930628 -0.2029544228
##  [671]  0.1534992627  1.3139529071  1.3574823354  0.1474027808 -0.5107885534
##  [676]  0.8728104074  0.6082926566  0.4674473974  1.0505862072  0.3617269737
##  [681]  0.5147849477 -0.2318164125  0.5353532492  1.2084711532  0.6958204695
##  [686]  1.4678943226 -0.0317551080  0.4474068034  0.8650475078  0.9318859953
##  [691]  0.9250517113  1.5308319799  0.8328670687  1.0731723074  0.0889865735
##  [696] -0.2447326764  0.2159475391 -0.2934533868 -1.2665980999  1.0805339909
##  [701]  0.7928851559  1.2458636147  0.4769272960 -0.3000616729  0.0517546282
##  [706]  0.7431428357 -0.2752429831  1.0885408274  0.1892262462 -0.2978771575
##  [711]  1.2537730808  1.0641218287  0.8864377197  0.3975799309  0.2122025713
##  [716]  0.0095433097  1.6775420354  1.1800655193  0.7351466195 -0.4217397427
##  [721]  0.0635550950  0.8529510072 -0.0695074846  1.0053672875  0.7520747693
##  [726]  0.9401091310 -0.9803939173  0.6416277035  0.8367421202  0.9848098368
##  [731] -0.0657893449  0.0459476499  0.5661378622  0.5167893478  2.2613540465
##  [736]  0.2605980214  0.4646567457  1.9191157699  1.0208441565 -0.0035252646
##  [741]  0.3550745634  0.6949665975  0.2420751219 -2.3785178970 -0.1994944818
##  [746]  0.4425076012  1.1840423200  0.0664448567  1.4144838254  0.3389067602
##  [751]  1.3219742912 -0.0376286908 -0.4912252766  0.1870212210 -0.3453335962
##  [756]  0.9669960466 -0.6243054475  0.7076964401 -0.6460873287  0.3525057486
##  [761] -0.1471859191  0.2074066250 -0.8649249181 -0.0122671360  0.4475670393
##  [766]  0.4389540858  1.2758579155  1.0191678947  0.0157923496  0.1755344277
##  [771] -0.2038317090 -1.7546638149  0.8863195234  1.1219029465  0.9409090691
##  [776]  0.8158655229  0.6241186525  0.8346097280  0.3450102586  0.0200249215
##  [781]  1.0278048645  1.1245045530  0.5106417128  0.1520222805  0.1665853590
##  [786]  0.3071832726  0.9405870416  1.0988462485  0.4421481527  0.4918032030
##  [791] -0.1388807269 -0.5108512732  1.3466882464  1.1329334062  1.3207287309
##  [796]  0.4123781709  1.4297718023 -0.5477316920  0.7663021346 -0.0530205821
##  [801]  0.4380469132  0.6227019273 -0.1643520220  0.7265641732  1.1290308164
##  [806]  0.5797871955 -0.0911123532  0.0442564707  0.5607800012 -0.7546966505
##  [811]  0.4066341212  0.6153059099  1.0912064794  0.1771167569  0.7103835565
##  [816]  0.5533451350  1.1455487176 -0.0399447298  1.2843315180  0.7722511492
##  [821]  0.1547991100  0.8127931017  0.2051979108  1.4000118176  1.2400377364
##  [826]  0.8028451799 -0.9449672349  0.8928555364 -0.1417829860  1.2196291319
##  [831]  0.7866118609 -0.2914981426  0.9140943760 -1.0084406562  0.2971714818
##  [836]  1.4737607442  0.4096368183  0.0947250837 -0.2994569538  1.0746539212
##  [841] -0.4097346672  0.1125334289  0.7693382079  0.1710346708  0.6559610088
##  [846]  1.4630078266  0.3525270696  0.8529259173  0.9905913280  0.4505956984
##  [851] -1.3847989108  0.0815085150 -0.4651941534  0.2062547035  0.0862922977
##  [856]  0.9928563811  0.4384946664 -0.0007128448 -0.3477862061  0.7389053938
##  [861]  0.8050388221  0.0372368978 -0.1085058693  0.5020986276  0.6761387893
##  [866]  0.6239662072  0.8899194119  0.6011193740 -0.1303703337 -0.3294867165
##  [871] -0.2952436687  1.8763502625  0.8470117443  0.8640589385  0.5908664785
##  [876]  0.8059424092  0.9575417956  0.4788078877  0.4443258437  1.2851682897
##  [881]  0.1780024945  0.8002519569  0.6842463433  0.2213348029  0.4118614123
##  [886]  1.2259123214  1.4182738311  0.0748069657  1.0753604635 -1.0211350334
##  [891]  0.1197929643 -0.6375261215  0.2089616042  0.6036040417  0.9818272881
##  [896]  0.4846498828 -1.3117761866  2.2346343271  0.2246346935 -0.1009998982
##  [901]  1.0046788839 -0.0120704754  0.2831282009  1.1486333024  0.6005144379
##  [906]  0.0611700023  0.9551079057  0.4435446959  1.4154077864  0.7984557878
##  [911] -0.1030122558  0.8461413315  1.2208937108 -0.7409962227  0.9252545396
##  [916]  0.3644562437  0.0183329779  1.8287021792 -0.0611723646 -0.2525633997
##  [921]  0.1038123536  0.4121788563  0.9009453855 -0.4002125823  0.8227382400
##  [926]  0.3409472242  0.1209575942 -0.3041737342 -0.1322469966  0.4359295627
##  [931]  0.8107532346 -0.8136308620  1.4484136825  1.0137502733  0.5375912091
##  [936]  1.3619082784  0.7654293630  1.2494987808  0.0016549052  1.1906142393
##  [941]  0.3864847355  0.8956817071  0.3643368811  0.3942796233 -0.1477425503
##  [946]  0.3520852315  0.8944296737  0.1097904075  0.5200488826  1.1548866200
##  [951]  0.9846457376  0.5385492147  0.9162685217  0.6119414731 -0.1970267110
##  [956] -1.3564238704  0.3392938772  0.8823909239 -0.1311879448  0.0436814113
##  [961] -0.6075954950  0.3328456019  0.6537417352  0.5025910789  0.8664299119
##  [966] -0.1561935349  0.5329632521 -0.0115096310 -0.0315273255  1.7191152731
##  [971] -0.2851880894 -0.3320725619  1.7488217085  0.2767989174  0.5057192282
##  [976] -0.4822943349  1.6730396545  0.2681485665  0.1090603592  0.1255625094
##  [981] -1.1127937887  0.3533457080  0.8389254175 -0.4075271930 -0.3675131811
##  [986]  0.3818360868  0.1027687895  0.2315128733 -0.2434668727  0.3560445900
##  [991]  0.8442760084  1.4831764961  0.4657537154  0.1952109389  0.5181847167
##  [996]  1.4455315472  0.4496043667  0.1480220312  1.3138346007  0.1193226290
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
##   0.49343969   0.26370000 
##  (0.08338926) (0.05896162)
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
## [1]  0.2215545 -1.2060853  0.2387656 -0.2067027  0.3428475  0.1604340
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
## [1] 0.0131
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9257621
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
## t1*      4.5 0.03743744   0.8571752
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 4 6 7 
## 3 1 1 2 3
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
## [1] -0.0213
```

```r
se.boot
```

```
## [1] 0.8659019
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

