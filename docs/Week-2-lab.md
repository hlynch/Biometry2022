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
## 0 1 4 7 8 9 
## 2 2 2 2 1 1
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
## [1] 0.0112
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
## [1] 2.783294
```

```r
UL.boot
```

```
## [1] 6.239106
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.8975 6.2025
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
##    [1] 3.3 4.3 4.2 5.5 4.7 4.5 4.6 6.0 3.9 5.0 4.5 3.4 5.1 3.9 4.3 2.5 4.0 4.8
##   [19] 5.1 4.9 4.7 6.2 4.3 4.2 4.1 1.8 6.2 3.4 4.7 6.3 4.0 3.1 3.5 5.8 3.7 6.2
##   [37] 3.5 3.8 3.5 6.7 5.9 3.4 4.3 5.2 5.3 4.1 3.2 4.1 4.5 3.6 4.4 6.2 4.7 4.3
##   [55] 3.6 4.3 3.4 6.1 4.1 3.8 4.1 4.0 5.4 4.6 5.1 6.0 3.8 4.5 4.5 2.4 3.9 3.7
##   [73] 3.9 4.9 3.7 6.9 3.7 4.1 4.0 4.4 4.1 4.3 4.3 4.6 5.2 4.4 5.6 4.2 4.4 4.6
##   [91] 4.2 6.2 3.6 5.4 4.1 3.5 5.0 5.7 5.0 3.1 3.1 3.8 4.9 4.7 3.8 4.5 4.6 6.1
##  [109] 4.8 4.4 3.7 5.9 3.3 5.2 5.7 3.6 6.6 5.0 5.2 5.0 4.7 6.3 3.6 6.0 5.5 3.4
##  [127] 3.3 3.9 5.8 5.0 5.4 6.5 5.8 3.7 3.8 4.9 5.0 5.6 4.0 5.8 4.2 6.2 5.6 3.8
##  [145] 5.3 2.6 5.7 3.6 5.6 5.5 3.3 6.0 4.1 5.1 5.0 4.8 5.2 3.1 4.7 4.6 5.2 2.8
##  [163] 4.6 2.8 4.1 4.6 5.0 5.8 2.9 4.2 4.0 3.3 5.6 4.7 3.1 3.3 5.3 4.1 4.9 4.7
##  [181] 3.5 3.8 5.0 4.7 4.0 4.6 4.7 4.5 3.8 6.0 3.7 5.2 4.9 5.0 5.5 4.2 3.5 4.5
##  [199] 4.7 4.7 4.3 2.8 5.4 6.2 3.5 5.0 4.5 3.6 4.0 3.3 6.1 4.2 4.8 4.0 2.6 3.0
##  [217] 5.6 4.0 4.5 3.8 2.0 5.1 3.9 3.5 4.9 3.5 3.9 5.3 3.5 4.6 5.1 5.6 4.3 5.0
##  [235] 3.4 5.1 3.6 3.7 3.9 5.0 3.8 5.8 5.6 3.1 4.3 4.7 6.0 5.9 4.3 4.1 4.1 5.6
##  [253] 4.9 6.1 3.9 3.9 3.4 3.4 4.8 4.7 5.5 4.6 4.9 3.8 4.6 5.4 5.5 4.0 5.3 5.3
##  [271] 5.0 2.9 5.1 3.0 4.8 5.4 4.6 6.0 4.9 3.7 5.1 4.1 4.7 4.6 5.0 4.4 5.6 3.7
##  [289] 3.8 4.7 4.9 5.7 4.7 6.1 5.2 5.2 3.9 5.4 4.0 5.3 4.2 4.8 5.5 5.1 4.8 5.3
##  [307] 3.6 4.8 3.3 6.3 4.1 3.4 5.6 4.6 5.6 4.3 6.5 4.4 4.4 4.8 5.2 3.3 5.1 2.6
##  [325] 6.5 4.3 5.5 4.5 4.7 5.5 4.4 5.3 5.3 3.8 4.6 4.5 3.6 4.2 3.1 4.6 5.2 5.6
##  [343] 4.6 3.7 5.4 4.2 6.1 6.2 3.2 2.6 3.4 3.3 6.2 3.9 4.8 4.3 4.4 2.7 4.1 2.5
##  [361] 1.9 4.8 3.5 5.3 5.4 5.4 4.2 4.8 5.6 4.5 4.0 4.3 5.3 3.3 5.0 5.6 4.9 5.5
##  [379] 3.5 3.1 4.9 4.0 3.9 5.5 4.4 4.0 5.7 4.7 3.9 5.4 4.2 2.9 4.3 3.8 5.0 4.8
##  [397] 3.4 4.2 4.4 4.9 5.6 3.6 4.4 4.5 4.2 4.6 3.9 3.7 5.4 4.8 6.0 5.4 5.5 3.8
##  [415] 5.1 3.6 4.1 4.9 4.0 5.0 4.0 5.6 4.6 5.1 5.3 3.5 4.3 5.1 6.8 5.3 4.9 4.9
##  [433] 6.3 4.7 4.2 3.1 4.9 3.6 4.3 4.9 3.5 3.9 4.1 4.1 4.3 3.9 4.0 3.8 3.0 6.6
##  [451] 4.8 6.8 4.7 4.4 3.5 4.3 6.0 5.0 4.9 4.4 4.6 4.9 4.2 4.4 2.5 4.3 4.0 3.8
##  [469] 5.6 4.9 5.5 5.5 5.2 5.0 2.7 4.5 4.4 4.9 5.5 3.7 5.3 3.9 6.4 5.3 4.7 4.5
##  [487] 4.2 4.6 2.4 5.0 3.6 5.3 5.6 3.3 4.9 6.1 6.5 4.1 4.8 4.4 4.1 4.2 4.7 5.2
##  [505] 3.7 3.8 5.1 3.9 4.1 4.4 4.9 5.4 4.7 6.2 2.9 3.2 5.1 5.7 4.7 4.2 5.4 4.1
##  [523] 4.7 3.8 6.1 6.1 5.5 3.2 6.4 2.3 4.0 4.9 4.3 2.4 4.3 4.2 4.6 4.6 3.3 5.2
##  [541] 3.7 6.8 5.1 5.7 2.6 6.0 4.3 4.4 5.7 3.7 3.2 4.8 4.8 3.0 2.1 3.6 4.2 4.7
##  [559] 3.9 3.7 3.6 4.5 3.5 5.5 3.5 4.5 4.6 6.6 5.4 4.8 4.7 5.1 5.0 6.2 5.2 4.6
##  [577] 4.1 4.1 5.1 5.2 2.4 3.2 4.4 5.4 3.7 4.6 5.0 3.8 5.2 5.1 3.6 4.0 5.5 4.7
##  [595] 5.2 4.4 4.7 4.8 6.3 4.9 3.8 3.7 6.0 5.7 4.8 4.1 4.4 5.1 4.4 3.2 5.1 3.5
##  [613] 4.5 5.4 5.5 3.8 4.4 4.5 3.5 3.3 5.6 3.8 4.3 5.4 5.1 5.3 3.8 5.1 5.1 3.5
##  [631] 5.3 4.5 1.9 5.2 4.4 4.5 3.7 3.5 5.2 5.0 5.9 3.0 4.9 4.0 4.5 5.8 5.3 4.6
##  [649] 3.0 3.2 6.1 5.0 4.2 5.7 4.7 4.0 5.2 4.2 5.2 4.0 3.4 2.9 4.8 3.1 4.9 5.5
##  [667] 5.1 4.8 4.3 4.7 5.5 4.1 3.3 4.6 3.9 4.4 4.3 3.7 3.5 3.7 4.1 3.9 3.9 5.7
##  [685] 4.2 3.9 5.4 4.1 4.4 4.3 5.6 4.3 5.8 4.5 3.5 2.4 3.4 3.3 3.8 6.2 5.0 3.7
##  [703] 5.2 5.3 4.7 5.1 2.8 5.2 4.9 2.8 4.3 5.8 3.6 3.7 4.6 5.3 4.7 3.8 4.1 6.0
##  [721] 4.6 4.8 3.4 3.9 3.1 3.7 3.2 6.1 3.0 1.9 5.3 4.2 5.2 2.9 5.3 4.7 4.3 3.1
##  [739] 4.5 4.0 5.9 4.1 4.8 6.2 4.6 4.7 4.2 4.1 4.9 2.8 5.5 3.1 3.7 4.6 4.4 5.6
##  [757] 4.9 4.6 3.6 3.8 3.5 5.0 4.5 6.2 6.6 4.8 4.5 3.9 5.0 5.4 4.0 4.3 5.4 4.9
##  [775] 5.1 5.2 4.5 1.7 3.1 3.9 4.3 3.7 4.6 6.0 4.6 4.4 5.4 5.3 4.1 4.0 5.7 5.7
##  [793] 5.8 4.2 3.4 5.3 3.9 4.6 2.9 4.2 4.9 5.1 4.1 2.9 6.3 3.2 3.4 3.0 4.6 6.5
##  [811] 5.7 2.2 5.6 4.4 5.0 4.4 5.9 5.4 3.5 4.8 4.3 4.7 4.7 3.3 5.3 5.4 4.3 3.4
##  [829] 3.8 3.6 4.3 3.2 4.9 4.4 5.6 4.1 5.9 3.1 4.3 4.1 5.0 4.3 5.4 4.0 5.4 3.4
##  [847] 4.8 5.4 4.9 5.0 6.1 3.8 4.5 4.4 6.1 4.8 4.4 5.8 3.6 4.7 5.5 4.4 3.2 4.2
##  [865] 4.9 5.3 4.8 3.5 4.7 3.7 4.0 5.4 4.2 4.8 4.1 4.9 4.5 4.9 4.4 4.4 5.0 3.2
##  [883] 4.8 5.4 6.1 5.5 2.3 3.7 2.2 5.2 4.0 3.5 4.9 3.6 3.9 5.6 3.0 3.9 5.4 4.9
##  [901] 5.4 2.8 5.8 5.3 4.1 4.7 5.2 4.4 6.8 4.5 2.9 4.1 5.2 5.1 3.6 3.5 5.5 5.1
##  [919] 4.8 4.5 3.9 4.5 4.8 4.7 4.1 4.8 4.6 3.6 6.2 5.3 3.1 3.9 5.2 4.2 4.7 4.3
##  [937] 6.6 5.4 4.2 5.2 3.6 3.3 4.5 4.3 5.5 4.0 5.1 6.1 4.9 4.3 6.5 5.0 4.2 4.3
##  [955] 4.0 3.6 3.7 4.6 4.3 3.9 4.8 4.9 4.5 3.9 4.3 3.7 5.1 2.3 4.7 3.6 3.7 5.3
##  [973] 4.5 5.0 5.1 4.0 5.7 3.2 5.8 3.5 2.3 4.7 4.3 6.2 2.6 4.9 3.9 3.7 4.3 5.3
##  [991] 4.6 5.5 6.1 4.0 6.2 4.7 4.5 3.5 3.1 2.8
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
## 2.6000 6.2025
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
##    [1] 4.2 5.2 3.0 5.0 2.6 5.8 4.3 3.9 4.0 4.7 3.1 4.5 5.5 3.8 3.8 3.2 4.7 4.7
##   [19] 2.8 4.6 4.9 4.8 5.2 4.3 2.1 6.3 5.1 3.0 4.3 5.6 5.4 4.8 4.9 4.1 3.5 5.7
##   [37] 5.7 3.2 4.9 3.9 3.6 3.7 3.4 4.3 5.4 5.5 3.3 4.5 4.4 5.7 3.6 5.1 6.4 5.1
##   [55] 4.4 5.5 3.9 3.5 3.9 6.4 4.9 4.9 4.2 3.6 3.9 6.4 4.5 3.9 4.4 4.3 4.7 4.9
##   [73] 5.4 4.7 3.4 4.1 3.1 4.2 4.7 3.3 3.0 4.4 5.0 3.9 5.3 4.9 3.8 5.4 4.2 3.9
##   [91] 4.6 5.3 4.2 3.4 4.6 5.5 4.2 5.7 4.3 3.9 3.2 5.5 4.4 5.4 5.1 3.9 5.1 4.6
##  [109] 5.3 5.7 5.1 3.9 4.6 2.5 4.2 4.5 3.9 5.7 3.7 4.8 5.1 4.7 4.6 2.6 3.0 4.1
##  [127] 3.5 5.0 4.8 5.7 5.5 5.3 4.1 4.3 4.0 2.7 4.4 4.4 5.4 5.8 5.3 2.7 5.1 4.3
##  [145] 5.9 4.3 6.0 6.3 2.5 5.3 4.1 3.1 4.0 3.4 4.1 4.6 5.2 5.4 5.8 5.3 3.4 4.3
##  [163] 2.6 4.9 4.6 3.5 4.9 4.2 4.1 4.7 5.0 4.5 4.0 5.7 5.0 6.1 3.4 3.0 3.5 4.2
##  [181] 4.5 5.7 5.2 3.9 5.1 5.5 5.2 3.8 4.1 4.3 4.4 4.0 5.7 5.9 4.6 3.4 4.9 4.2
##  [199] 4.6 5.0 3.8 4.3 6.5 5.3 3.2 6.8 5.5 3.9 3.1 5.2 5.4 4.7 4.8 4.3 5.6 3.8
##  [217] 4.4 5.7 4.4 3.6 5.2 4.8 4.6 3.4 4.6 3.8 4.8 4.8 6.0 5.1 6.1 5.0 5.5 3.6
##  [235] 6.2 5.2 4.1 2.7 5.6 5.3 5.7 3.9 4.4 5.8 3.5 4.9 2.9 5.6 5.6 4.4 3.6 4.4
##  [253] 5.3 3.2 4.4 3.7 5.0 5.5 5.8 5.3 4.7 3.6 3.4 5.1 4.5 6.1 4.9 3.2 3.2 5.1
##  [271] 4.6 4.1 4.8 4.2 3.9 3.8 5.5 5.1 4.5 4.5 3.2 4.8 4.0 4.3 3.4 2.4 4.4 3.9
##  [289] 4.3 3.6 4.8 5.3 2.8 4.4 4.4 4.5 4.5 4.4 4.8 5.7 4.4 4.1 3.2 3.6 2.5 5.2
##  [307] 5.8 4.4 5.5 4.5 5.0 5.3 5.5 4.9 5.4 3.4 4.5 3.2 3.8 5.5 4.8 5.2 5.4 5.7
##  [325] 5.0 3.3 3.7 4.8 4.1 4.5 4.7 4.2 3.3 6.1 5.0 3.7 6.6 2.8 4.4 4.7 4.2 5.1
##  [343] 5.0 4.7 6.2 3.6 5.3 5.4 4.3 4.1 6.0 4.8 3.4 5.3 5.2 4.2 3.9 4.8 5.7 5.2
##  [361] 3.7 5.0 5.5 2.8 3.6 5.2 4.9 4.6 5.7 2.7 4.4 3.0 6.1 5.5 5.7 5.2 5.1 4.2
##  [379] 4.9 4.5 4.1 5.4 7.0 3.2 4.6 4.7 3.3 5.4 4.0 5.4 4.0 5.3 4.4 4.4 5.4 3.6
##  [397] 4.5 5.2 4.0 4.7 4.2 4.6 3.9 5.0 6.8 4.0 4.4 3.2 4.0 4.6 4.7 5.1 3.8 4.9
##  [415] 3.2 5.7 3.2 3.7 5.1 4.4 4.9 6.4 3.8 4.0 4.6 2.9 3.5 7.1 4.9 4.6 5.1 5.3
##  [433] 5.3 4.6 4.4 5.2 4.6 3.7 4.0 4.6 3.6 3.5 5.2 5.5 5.8 4.9 3.1 4.9 3.9 4.3
##  [451] 4.4 2.7 5.5 4.1 3.7 2.6 5.5 4.0 5.2 5.4 4.4 4.5 1.7 6.3 4.4 4.8 3.3 3.6
##  [469] 4.7 5.7 3.5 5.1 4.2 5.6 5.1 4.2 5.5 5.8 5.4 3.3 5.6 4.6 5.6 4.3 4.7 4.6
##  [487] 3.2 3.2 3.7 3.3 4.6 4.3 3.5 4.3 3.2 4.1 3.6 5.4 5.2 4.2 4.9 3.7 5.7 3.8
##  [505] 3.5 4.4 4.3 5.3 5.0 5.4 4.8 3.1 4.5 4.7 2.7 4.3 4.6 4.5 4.8 4.4 3.0 4.4
##  [523] 5.0 5.2 4.2 3.6 4.0 4.6 5.4 5.0 5.6 4.9 4.7 3.9 5.0 4.5 4.2 2.9 6.6 4.0
##  [541] 3.0 4.5 3.6 6.1 4.7 3.5 5.0 4.4 4.9 3.9 3.8 2.3 3.7 4.9 4.9 4.6 4.5 4.2
##  [559] 4.1 4.4 5.4 5.4 3.6 3.6 3.2 3.6 6.4 3.4 4.2 6.4 3.5 3.9 4.3 5.1 5.2 4.3
##  [577] 5.0 3.7 5.3 5.5 3.2 5.1 4.0 3.1 4.4 4.1 3.1 5.5 5.6 4.6 5.1 5.5 3.6 6.1
##  [595] 3.1 3.6 5.7 4.8 4.3 4.3 4.6 4.8 2.8 4.2 3.9 5.2 3.7 3.5 5.4 2.9 4.7 3.5
##  [613] 3.8 5.0 5.3 5.4 4.9 5.9 4.5 4.2 6.9 4.0 2.5 4.8 2.9 4.0 4.6 4.3 4.9 4.5
##  [631] 3.2 4.6 5.0 4.8 4.2 4.3 3.5 5.7 3.3 3.3 4.6 2.7 4.9 4.5 3.7 3.6 4.6 5.1
##  [649] 5.1 4.1 3.4 4.6 5.5 3.5 2.7 4.6 5.1 6.0 3.1 4.6 5.0 6.0 4.2 5.8 4.7 5.3
##  [667] 3.8 6.8 6.4 4.8 3.9 2.8 4.2 4.7 3.0 4.8 5.1 5.5 3.8 3.5 4.1 4.7 5.4 3.8
##  [685] 4.8 2.9 4.8 4.7 4.7 5.1 4.2 3.1 5.3 4.2 5.0 4.2 4.8 4.6 5.8 2.5 4.1 5.4
##  [703] 6.2 4.0 4.1 6.1 4.4 5.2 3.3 4.8 5.2 4.1 2.6 5.2 4.1 4.0 3.9 2.4 4.6 4.2
##  [721] 4.9 5.2 5.0 3.9 4.7 5.7 5.4 4.7 5.5 4.5 6.4 6.8 5.2 2.8 6.2 4.5 4.5 3.9
##  [739] 5.3 4.6 5.1 2.5 4.3 5.1 5.5 3.5 5.4 3.9 4.6 5.8 3.8 4.3 6.2 6.1 4.7 3.0
##  [757] 3.9 3.9 4.7 3.8 4.9 5.1 4.8 2.5 4.2 5.9 4.8 4.9 5.4 4.8 3.5 5.5 3.6 4.6
##  [775] 5.2 4.9 5.0 5.6 4.2 3.6 5.5 5.0 4.5 4.3 5.6 4.0 3.6 3.3 4.0 5.7 4.6 4.8
##  [793] 4.4 3.9 4.9 5.3 5.6 4.3 4.7 3.5 5.2 5.9 4.1 4.8 4.9 5.0 5.2 4.7 4.1 3.5
##  [811] 3.3 2.8 4.7 4.8 5.2 2.6 4.5 5.4 4.0 4.4 5.6 5.3 6.2 3.6 5.4 4.2 4.2 4.0
##  [829] 4.8 5.3 5.3 4.8 4.7 4.4 3.8 2.3 4.5 4.2 5.5 5.3 6.2 3.6 4.0 4.8 4.5 5.7
##  [847] 4.4 6.1 3.7 4.2 5.6 5.6 4.7 3.6 3.2 5.6 3.4 4.6 5.1 5.0 2.5 4.8 2.5 5.2
##  [865] 4.2 5.1 5.2 4.6 4.5 6.1 4.5 5.2 4.7 4.2 4.5 5.9 5.2 3.6 4.1 4.1 5.4 4.4
##  [883] 6.3 2.7 5.8 3.6 3.9 5.1 4.9 3.7 4.5 4.7 3.6 5.1 2.8 3.9 4.7 4.3 3.7 5.8
##  [901] 4.1 5.0 4.5 3.7 3.7 3.4 3.8 4.4 5.0 3.5 4.6 5.5 5.2 2.6 5.4 4.0 5.4 3.6
##  [919] 3.3 2.8 5.1 4.2 3.9 5.6 3.7 4.3 5.9 4.6 4.3 5.9 3.0 4.6 3.5 5.4 5.3 5.0
##  [937] 3.9 4.1 4.8 5.1 3.9 5.4 4.3 4.4 5.1 5.0 4.9 3.7 4.3 3.5 4.3 5.9 4.1 4.2
##  [955] 2.8 4.4 5.1 3.8 5.5 3.6 3.7 6.8 4.8 4.9 5.3 4.1 4.4 4.4 6.1 4.4 4.3 5.1
##  [973] 4.9 5.2 4.7 5.5 4.6 4.7 3.7 4.5 5.5 4.4 4.9 6.1 4.1 4.6 6.2 3.4 5.3 5.6
##  [991] 4.3 4.6 5.3 3.1 6.6 4.2 4.6 4.5 4.5 4.6
## 
## $func.thetastar
## [1] 0.022
## 
## $jack.boot.val
##  [1]  0.509090909  0.381656805  0.273780488  0.174238227  0.060115607
##  [6]  0.006969697 -0.141017964 -0.241736695 -0.370588235 -0.505917160
## 
## $jack.boot.se
## [1] 0.9407161
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
##    [1] 5.2 3.6 4.1 3.8 4.0 2.4 3.3 5.1 4.4 4.4 5.3 6.5 5.6 3.8 4.0 4.2 5.0 3.4
##   [19] 5.6 4.8 5.7 3.5 4.5 4.8 4.5 4.4 5.0 4.2 4.0 5.0 3.2 5.1 5.0 6.1 3.8 4.6
##   [37] 4.0 4.4 3.7 4.4 4.0 3.5 3.5 5.1 4.4 5.4 5.3 3.9 4.9 5.3 4.0 4.5 3.4 5.4
##   [55] 4.3 5.8 5.3 4.2 5.9 4.9 3.5 3.3 5.0 4.7 3.1 4.9 3.4 4.1 5.9 4.5 5.6 2.9
##   [73] 4.9 3.9 5.3 4.5 6.4 3.4 5.1 5.3 4.9 5.4 3.5 4.2 4.9 4.9 6.0 3.4 3.3 3.9
##   [91] 4.0 5.1 4.9 4.2 5.2 3.5 3.8 6.3 4.7 4.3 4.7 4.2 4.2 3.5 3.6 4.3 3.6 4.9
##  [109] 5.0 5.2 4.2 4.6 6.0 4.7 4.3 3.4 6.6 3.7 4.1 4.1 4.2 5.0 5.4 5.6 4.7 2.7
##  [127] 5.1 3.3 3.7 3.1 2.8 5.0 3.8 5.6 3.9 4.5 2.5 4.7 4.6 4.8 6.0 5.2 4.3 4.5
##  [145] 3.3 4.1 2.8 4.4 4.8 4.3 5.1 3.9 3.4 3.4 2.8 5.0 2.0 4.6 6.1 5.0 3.9 4.8
##  [163] 2.9 3.1 3.5 5.4 5.1 5.5 4.6 4.7 4.9 4.2 5.4 4.4 4.5 2.9 3.8 3.3 4.4 4.2
##  [181] 5.8 3.8 3.8 3.6 4.0 3.5 4.0 4.6 3.6 4.2 3.7 7.6 5.4 4.4 4.1 5.1 4.2 3.2
##  [199] 3.9 4.1 4.7 4.6 4.0 5.0 6.0 4.8 5.0 4.8 4.1 5.0 4.9 5.0 4.9 6.0 1.7 2.8
##  [217] 3.3 3.8 3.5 5.6 5.7 4.2 3.3 4.5 2.4 2.4 4.7 5.1 4.0 3.8 3.8 5.9 4.6 4.2
##  [235] 3.7 3.2 2.2 3.9 5.3 4.1 4.7 4.1 3.6 6.5 4.8 5.9 2.9 3.9 4.4 3.9 5.6 6.1
##  [253] 5.9 4.9 4.4 4.9 4.4 5.2 3.9 2.9 3.5 4.7 5.2 4.2 4.2 3.5 5.9 2.9 3.1 5.1
##  [271] 3.7 5.0 3.9 3.7 4.9 4.2 5.0 5.9 2.7 3.4 3.5 5.4 4.1 5.5 5.3 4.2 4.6 5.3
##  [289] 4.4 2.6 4.3 5.7 4.0 4.9 3.5 5.4 3.9 5.3 4.8 4.2 6.1 5.2 4.8 4.5 5.5 4.0
##  [307] 4.3 5.0 5.3 3.7 3.2 2.7 4.9 4.6 4.4 3.6 5.7 3.4 2.3 4.2 4.2 4.6 4.6 3.5
##  [325] 5.3 4.7 5.4 4.4 5.5 4.8 3.3 4.2 3.7 4.5 5.8 3.9 6.0 3.6 5.0 5.6 7.7 5.7
##  [343] 3.7 4.0 5.0 4.2 4.1 2.6 3.0 3.1 5.7 3.0 5.4 3.4 4.1 4.3 6.3 4.4 4.3 3.9
##  [361] 5.4 3.4 6.4 5.5 4.8 3.7 3.6 3.8 4.3 4.0 5.9 3.1 6.2 4.5 4.6 5.0 3.8 5.3
##  [379] 4.5 5.3 4.7 6.4 3.5 3.9 3.7 4.8 2.7 3.4 5.2 4.3 4.7 5.4 2.6 5.8 5.8 5.5
##  [397] 4.7 5.2 2.4 4.6 3.4 2.9 4.6 3.8 4.6 4.9 3.4 4.0 4.6 5.1 4.9 4.7 4.6 4.7
##  [415] 4.7 4.3 3.6 6.2 4.5 5.2 3.2 6.2 4.9 4.1 3.1 4.6 3.7 4.9 2.7 4.9 5.5 4.3
##  [433] 5.7 5.4 4.2 3.9 4.9 6.1 4.7 6.0 3.8 3.4 5.5 4.2 5.0 4.0 4.2 3.4 2.3 4.1
##  [451] 3.6 3.4 5.2 5.1 6.2 3.3 5.4 4.5 4.5 5.3 3.7 4.9 5.4 3.4 4.2 4.2 5.3 4.9
##  [469] 6.0 5.1 4.8 4.3 5.4 5.2 5.2 4.8 5.1 3.8 3.9 3.2 4.7 3.8 4.1 2.9 6.9 6.2
##  [487] 4.6 4.4 3.3 4.6 5.0 4.0 3.7 3.3 4.6 4.5 5.4 3.4 4.8 3.5 4.6 3.2 4.1 3.9
##  [505] 3.7 5.7 5.2 4.2 4.9 3.4 3.0 4.1 6.1 4.3 4.6 4.3 3.1 3.5 2.5 5.3 5.5 4.1
##  [523] 4.3 4.7 3.8 3.1 5.7 6.1 3.4 3.9 3.4 4.5 6.4 4.3 3.8 4.3 3.5 5.1 3.6 4.2
##  [541] 5.4 3.8 3.8 4.4 4.4 3.4 4.5 5.0 5.1 5.3 3.9 4.0 4.1 3.8 4.7 4.4 4.4 3.3
##  [559] 5.2 4.5 4.6 4.1 5.5 3.4 3.7 5.5 5.3 4.7 4.7 3.4 3.9 3.5 3.6 3.1 4.0 4.1
##  [577] 6.0 4.8 4.8 4.0 3.2 5.9 4.2 2.8 3.1 4.9 4.7 5.3 4.8 4.0 5.1 5.0 5.1 4.5
##  [595] 4.6 4.3 5.6 6.6 2.9 4.4 3.5 4.7 4.3 4.8 5.1 4.7 4.5 4.3 5.3 3.7 6.3 3.7
##  [613] 5.7 4.7 7.1 5.0 5.0 6.9 5.3 4.3 6.0 3.7 5.8 5.5 3.3 6.2 4.8 4.9 4.8 5.3
##  [631] 3.4 2.6 4.9 4.5 4.7 4.0 5.2 4.6 4.9 6.1 4.8 4.0 4.7 3.8 5.3 4.7 4.5 4.5
##  [649] 5.4 4.8 2.3 3.7 4.7 5.2 4.9 4.6 4.3 5.6 4.6 4.0 4.0 4.7 3.7 4.8 5.0 5.7
##  [667] 4.2 5.5 4.3 3.2 4.3 5.1 3.1 5.0 4.0 4.7 4.5 4.6 4.7 4.8 4.6 3.9 4.7 5.2
##  [685] 5.0 4.4 4.4 3.7 5.9 3.0 4.5 4.2 2.9 3.8 3.9 4.4 4.8 5.8 4.5 5.1 3.4 4.2
##  [703] 5.1 2.9 6.0 3.1 4.3 4.0 4.2 4.3 4.5 5.7 4.3 4.3 4.4 4.1 4.9 3.8 5.5 4.6
##  [721] 5.1 3.7 5.1 4.3 3.8 3.4 4.3 4.7 6.2 5.6 4.8 3.3 5.2 5.0 4.3 4.1 4.0 5.1
##  [739] 2.6 6.0 3.9 4.7 4.6 4.8 3.5 4.4 3.3 5.2 3.1 4.1 4.8 6.1 5.1 4.2 4.2 3.6
##  [757] 4.7 4.6 4.6 4.2 4.2 4.0 6.0 6.2 5.1 5.3 3.4 5.1 5.3 4.1 4.4 3.2 5.0 3.1
##  [775] 4.0 5.0 5.2 4.5 5.5 4.6 5.4 4.3 2.4 3.0 4.1 3.6 4.8 3.2 5.6 3.5 4.4 5.1
##  [793] 3.5 3.5 3.3 5.4 3.0 4.1 5.1 5.8 4.3 4.0 4.0 5.2 3.8 4.0 6.1 5.1 2.9 5.9
##  [811] 5.2 3.4 5.1 5.1 4.7 3.6 5.1 5.2 4.4 4.5 4.0 3.2 3.3 3.7 5.3 4.7 3.5 6.7
##  [829] 4.1 3.8 4.0 5.2 2.6 4.4 5.0 4.4 4.8 4.2 3.8 2.6 4.2 4.1 4.6 5.7 3.8 6.3
##  [847] 5.0 3.6 4.8 5.3 4.1 5.2 5.1 3.6 3.7 4.9 5.7 3.5 5.4 2.9 4.4 4.1 4.5 4.8
##  [865] 5.0 4.2 4.4 4.1 5.4 4.2 4.6 5.4 2.7 6.0 4.4 4.0 6.0 4.0 5.3 5.1 4.7 5.7
##  [883] 5.0 4.4 5.6 4.4 5.5 4.8 5.0 2.5 5.2 3.8 4.1 4.0 5.1 5.1 3.1 5.1 6.0 4.5
##  [901] 3.0 4.4 4.5 4.3 5.5 3.2 4.0 4.7 4.1 3.6 3.3 4.6 3.4 4.5 4.6 4.6 5.3 3.9
##  [919] 5.8 4.9 5.4 4.7 4.8 4.8 3.6 4.1 3.8 6.1 3.6 5.1 5.3 1.9 2.8 2.8 4.2 5.1
##  [937] 4.1 5.2 5.6 2.3 4.0 4.0 4.4 5.4 6.1 6.3 4.6 4.2 4.9 2.2 4.8 4.2 4.8 4.6
##  [955] 5.9 6.0 3.3 4.7 4.2 4.2 4.7 3.9 4.1 2.9 4.5 4.4 6.1 2.6 5.4 4.5 3.4 4.5
##  [973] 3.3 4.9 3.9 3.3 5.3 5.5 6.3 3.9 3.9 5.1 5.3 4.9 3.7 3.3 4.6 3.9 3.1 4.8
##  [991] 4.7 3.7 3.1 4.3 4.5 6.2 3.8 4.3 4.4 4.7
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.4 5.3 5.3 5.2 5.1 5.0 4.8 4.6 4.6 4.5
## 
## $jack.boot.se
## [1] 0.946784
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
## [1] -0.3483155
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
##   5.173150   7.821600 
##  (2.242880) (3.561246)
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
## [1]  0.07979759  0.76311364  0.88676952 -0.08484322  0.93273284 -0.47234428
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
##    [1]  0.4703850883 -0.3939626920 -0.0031869243  0.2435522191 -0.5667151686
##    [6] -0.1645579889 -0.5274505428  0.3435831312 -0.0254997578 -0.6436541921
##   [11] -0.7654441579 -0.1880258459 -0.6587053683  0.3886544454  0.0028354268
##   [16] -0.7354079298 -0.2541788782  0.6736019805 -0.1872993683 -0.6142681911
##   [21] -0.8345641533 -0.2006219925  0.0603315792 -0.5472527453 -1.5996613843
##   [26] -0.6638159685 -0.5837185126 -0.9330658176 -0.1205401657 -0.2121323813
##   [31]  0.0675667014 -0.3694886911 -0.1600505538 -0.4938188783 -1.3564987476
##   [36]  0.2955902070 -1.3351613912 -0.7632195528 -1.3468433299 -0.8012320789
##   [41] -0.7611672056 -0.7771899526 -0.5215837100 -1.2540991335 -0.3410070433
##   [46] -0.3483841377 -0.1831920708 -0.5389926586 -0.4537224957  0.0460117326
##   [51] -0.3108585234 -0.7265336739 -0.7550562721 -0.5592109389 -0.4942722094
##   [56] -0.4935853408 -0.7994313207 -0.5255784905 -1.4119144973  0.0896861440
##   [61] -0.6156661733 -0.6325360156 -0.9251518900 -0.4541228507 -1.0143521223
##   [66] -0.6698356332 -0.5150234101 -0.1441805866 -1.1683927493 -0.2645230344
##   [71]  0.3403152323 -1.1668943805  0.0914656719 -0.3599543583 -0.0953088280
##   [76] -0.3382556731  0.2232280633 -1.3607490788 -0.3034648108 -0.4898925331
##   [81] -1.7219031246 -1.6301842851 -0.5067502619 -1.5970534518 -0.3782380908
##   [86]  0.0844697056 -0.5543928058 -0.1331464841 -0.9123498619 -0.4940932474
##   [91]  0.1609731087 -0.0484934162 -0.0697927424 -0.5229666381 -0.5779572054
##   [96] -1.6108733025 -0.9014802160 -0.0320619253 -0.4163989801 -0.1087872861
##  [101] -1.0021475107 -0.8549355559  0.5160237117 -0.9016530869 -0.9440572124
##  [106] -0.8662095940 -1.1721720389  0.0504351229 -0.5745635491 -0.7507395836
##  [111] -0.4575992968  0.4123953446 -0.0275463295 -0.4466664405  0.0059134098
##  [116] -1.5760364168 -0.6724466275  0.2643917017  0.8476617561  0.1372402853
##  [121]  0.2813181670  0.2063489863  0.0893425707 -0.8550929874 -0.2940472456
##  [126] -0.6140941617 -0.7325993006  0.3106254234 -0.2002812405 -0.9805894598
##  [131]  0.3189747416 -0.3245299443 -0.5047701470 -0.6033713277 -1.1613620512
##  [136]  0.5510241990 -0.8788206531  0.0206123326 -0.8035336180 -0.2741603915
##  [141] -0.4564552223 -0.4216522879 -0.0231702754 -0.1740003434  0.0288407225
##  [146] -0.9396432006 -0.3987654222  0.0226713396  0.0079253796 -0.5522788089
##  [151] -0.0127941389  0.0644036269 -0.5257334148 -1.1218942209 -0.0474436193
##  [156] -0.6090949661  0.0586243430 -0.2706383739 -0.5374628666 -0.7245973338
##  [161] -0.3410157474  0.0298424418 -0.9952722579 -1.0449178510  0.9773925010
##  [166] -1.1167768453  0.1258971375 -0.7762422793 -0.1633165270 -0.5013570620
##  [171] -0.8417709931  0.3214553919  0.6531424859 -1.2928876682 -0.7841142488
##  [176] -0.6163397813 -0.9645906587  0.0145165385 -0.6095169446 -0.8416605443
##  [181]  0.5126904639 -0.7480499707 -0.0354049850  0.0307899843  0.4779320284
##  [186] -0.6335337879 -0.8059736601 -0.4627305999 -0.0493261849 -0.7052845848
##  [191]  0.3493139266 -0.1379383904 -0.7785894148 -0.2678414170 -0.8519750411
##  [196] -0.3378299525 -0.3790831512 -0.6051283691 -1.2788174368  0.0891336073
##  [201] -0.3794905144 -0.0696511890 -0.1935560438 -0.1721555480 -0.5283099764
##  [206]  0.0621327590  0.3416122296 -0.0880498218 -0.2106008553 -0.5572752002
##  [211]  0.2562004576  0.0617915817  0.0567666354 -0.3871303347 -0.9645906587
##  [216] -0.8964371393 -1.2439537757  0.1780873863 -0.6507924790 -0.1655365651
##  [221] -0.7295771773 -0.4538899993 -0.3545990768 -0.4400106247 -0.3361103027
##  [226] -0.2834673698 -1.1322633938 -0.0610901936  0.9378375657  0.1959681932
##  [231] -0.0374098581 -1.2106335992 -0.0583564028 -1.0522883294 -0.8302672581
##  [236] -0.3798942749 -0.6316830053  0.3114167001  0.1043242341 -0.2863243911
##  [241] -0.2281027645 -0.7275453508  0.2501867451 -0.1778710408  0.4850571790
##  [246]  0.0731338538 -0.3610730468 -1.0818923473 -0.3464115858  0.3961126663
##  [251]  0.0056032588  0.3300602719  0.0285324846 -0.9435501361 -0.4574967465
##  [256] -0.8035336180 -1.3418714609  0.1731120715 -0.3662209234 -0.4796931176
##  [261] -1.0820826429 -0.3878591877 -0.8695324704 -0.8302493864 -0.6683205739
##  [266] -0.5762398045  0.0692086715 -0.8188701744 -0.7526792078 -0.8884530440
##  [271] -0.8188075921  0.1703344609 -0.4013884796  0.5848469387 -0.0436069742
##  [276] -0.3377297020 -1.2348950468  0.0850994056 -0.6489831599 -0.8171244174
##  [281]  0.3878928181 -0.8534224602 -0.3190695959 -1.1693420950 -0.7959566647
##  [286] -0.1458526846 -0.1558933604 -0.0610901936 -0.2112962877 -1.2952036105
##  [291]  0.3799531752 -0.8008769489 -0.8226522318 -0.8645663600 -0.0062887369
##  [296] -0.0061490906 -0.1230396881  0.0353988720 -1.6921303093 -0.0354049850
##  [301] -1.3674288528 -0.5651526005 -0.9056069408 -0.6690583368 -0.3649344717
##  [306] -1.3108556389 -0.5649232483 -0.1178108171  0.1460690591 -0.2310015445
##  [311] -1.1139175590  0.0769389633 -1.1644961993  0.1804235990 -0.8722876243
##  [316] -1.3169924641 -0.6350134096 -0.9427149804 -0.3993352190 -0.3335820666
##  [321]  0.2912647995 -0.5965435588  0.6365637124 -1.5453280773 -0.5339666857
##  [326]  0.6474836844  0.4471904855 -0.9165412653  0.2922071967 -0.8245704814
##  [331] -0.4566605728 -0.6535490028  0.3647984011 -0.3152971502 -0.2499801775
##  [336] -0.2248183756 -0.3874679172  0.5058695997 -0.3539973214 -0.2082974364
##  [341] -0.7660672362 -1.1460431062 -0.5712037588 -1.0824343673 -1.1114967898
##  [346] -0.9837181678 -0.0858602498 -0.5999225864 -1.0197627865 -0.8439504322
##  [351] -0.7067885338  0.0259183144 -0.1184397057  0.0868852275  0.4310880710
##  [356] -0.9661684589 -0.6916941544  1.0820148768 -0.0014595249 -0.1147756635
##  [361] -1.2469245259  0.4351607082 -0.3739620128 -1.2252349551 -0.5469429182
##  [366] -0.8871397535 -0.2744460020 -0.8332315427 -0.6894016484 -1.0410057282
##  [371] -0.3365109847 -0.2018627167 -0.3418004822 -0.4345769001 -0.7362422275
##  [376] -0.1538788733 -0.3271392435 -0.3312928158 -0.3533989798 -0.4480439469
##  [381]  0.4939758696  0.7677629070 -1.0560680512 -1.4094169643 -0.4798076015
##  [386] -0.3455603148  0.4257377753 -1.0442643494  0.9703799381 -0.7595403301
##  [391] -0.1491575999 -0.2724383633  0.2248964601  0.0584566666 -0.3458982176
##  [396]  0.3660081815 -0.6259293832 -0.7249957530 -0.0443512386 -0.1577250241
##  [401] -0.4235834439 -0.4687381698 -0.2966905694  0.0574910284 -1.2392080626
##  [406] -0.6701178559 -0.4029221841 -1.0392240371  0.2141416151  0.3307658988
##  [411] -0.1941862816 -0.3525254240 -0.2492051732 -0.1340673159 -1.6197225616
##  [416] -0.3682024701 -0.4280240620 -0.4045851997 -0.3357811583 -0.0416784374
##  [421] -0.1300566548  1.1538566860  0.5132199664 -0.6270798449 -0.7084471571
##  [426] -0.3074284044 -0.1449644188 -0.6997251291  0.3061529612 -1.0672356421
##  [431]  0.4403064869 -0.6365121866  0.1282967216  0.0999592654 -0.2868225221
##  [436] -0.8774680864 -1.2872869977 -0.6352855144  0.8109119402 -0.5543711925
##  [441] -0.0043347169 -0.7024431933 -1.1943519389 -0.4961602500 -0.2797128248
##  [446] -0.0188365617  0.0819523720 -0.2753561931 -0.2661002532 -0.7189087126
##  [451] -0.5485819261  0.1985032537 -0.1633239038 -1.1771042329 -1.6495026938
##  [456] -0.8892290279 -0.0968340675 -1.1244213629 -0.5608412268 -0.3187578207
##  [461] -1.1464731244 -0.0122151077 -0.8537076690 -0.6852638346 -0.6973307759
##  [466]  0.0053443426  0.1279131935  0.5988233479 -0.6832507595 -0.6632755656
##  [471] -0.2730578450 -0.1907786460 -1.3103778806  0.1121998502  0.1041282362
##  [476] -0.6262684471 -0.3885319491 -0.2911271161 -0.6392459743 -0.3231735827
##  [481] -0.5335759476 -0.3110997694 -1.8643247963 -1.0598786903  0.0295570347
##  [486] -0.2379759056 -0.6761442734 -0.6675550797 -0.8055832778 -0.1522447555
##  [491]  0.6020698609 -0.5135172058 -0.4715869104 -0.1244280412 -0.7403236534
##  [496] -1.2324735349 -0.1537615901 -0.4093365826  0.2327224621 -0.2527401059
##  [501] -0.7480499707  0.2011784494 -0.8384000711 -0.0026462811 -0.0441397480
##  [506] -0.4509816416  0.1446301850 -0.3466403184 -0.0002739009 -0.4990528069
##  [511]  0.4963266905 -1.1074198445 -1.1133519965 -1.2711977951  0.0906759226
##  [516]  0.0714414858 -0.0998911332  0.5965483729 -0.4523556730 -0.4695418641
##  [521] -0.5784367450 -0.7855174067 -0.9728823453 -0.9662887229 -0.3413217954
##  [526] -0.2687949972 -0.0405721737  1.0626336355 -0.1361615056 -0.9017838128
##  [531] -0.6683624189 -0.8110674129 -0.7174105807 -0.6085307003  0.0739179925
##  [536] -2.2586347813  0.3540983866  0.4069493215 -0.7194917697  0.5623206511
##  [541] -0.9224238047 -0.4481735428 -0.1549889831 -1.0765262477 -0.6836938299
##  [546] -0.3585612281 -0.3447977353 -0.9843988496 -1.1208991838 -0.3537433134
##  [551] -0.5254657021  1.0056759656 -0.8688930322 -0.2407166870  0.3468597035
##  [556] -0.2627436473 -0.7064180757 -0.6533465503 -0.9763686348 -0.4158566159
##  [561]  0.0080736389 -1.2142238862  0.3694249282 -0.5138082353  0.0598761117
##  [566] -0.3662507814 -1.1121753792 -0.0663641168  0.6501120246 -0.7221891480
##  [571] -0.1839985306 -1.0820826429 -0.4461270853 -0.7056380418 -0.6298816840
##  [576] -0.2573874935 -1.0943106426 -0.0455592107  0.1176426593 -0.5137788339
##  [581] -0.8439092661 -0.1798155309 -0.2609129211 -0.6301567334 -0.7855174067
##  [586]  0.1350478387 -0.6921747130  0.6205331944  0.2880414636  0.0345777849
##  [591] -1.3152205177 -0.3443288023 -0.5115301003 -1.1423883954 -0.3547378877
##  [596] -0.9565102526 -0.3476656576 -0.0786907155 -0.2391683840 -0.9336740622
##  [601]  0.8952230115 -0.1413626508 -0.4548395752  0.1456734724 -0.3297991542
##  [606] -0.9143359928 -0.3756116517 -0.9703823842 -0.8421427402  0.3326529356
##  [611] -0.3954073184 -0.4272841832 -0.5444408957 -0.1545706911 -0.8181904507
##  [616] -1.1717406431 -0.4899490105  0.6633223590 -0.6555058514  0.1446739050
##  [621] -0.3789861554 -1.2013428205 -0.0623514596 -0.0999433149 -0.1231306335
##  [626] -0.2572213809 -0.0277256993 -0.8403845284 -0.8323885022 -0.2181168763
##  [631] -0.3730717092 -1.2469245259 -0.3426078233 -1.0763608810 -0.2448294953
##  [636] -0.6168672991 -0.1938693990 -0.4387568673 -0.1690831337 -0.1105934714
##  [641] -0.0183949076 -1.1886406706 -0.8270962922 -0.4389844743 -0.0995075934
##  [646] -0.4956141409 -1.0565541328 -1.4232961232  0.0540171292 -0.5323566102
##  [651] -1.3982095874 -0.9143359928 -0.6962555514 -0.0397711605 -0.2371009100
##  [656]  0.0930299759 -0.5052494895 -0.6446591891 -0.3662209234  0.1320772813
##  [661] -0.9728120851 -0.5150955475 -0.4386046366  0.1090231224 -0.8198493902
##  [666] -0.2603771596 -0.7706189848 -0.6643878147  0.6120416902 -0.0722146133
##  [671] -0.4614251749 -0.0698178158 -0.6752451757 -0.5398941066 -1.3018425480
##  [676] -0.3638472868 -0.7620289606 -0.2572213809 -0.8369212044 -0.6704309182
##  [681] -0.9070144449 -0.8529480370 -0.6848730439 -0.6993600633 -0.5013226379
##  [686] -0.7254006123  0.2406530918  0.1466391474 -0.3274943746 -0.3217556378
##  [691] -0.0351819739 -0.9251028346  0.0949269036 -0.1195251607 -0.8972886815
##  [696] -0.1587976433  1.2130285999 -0.7056113655 -0.0994845439 -0.4264124233
##  [701] -0.6925424929 -0.1878262063 -0.6493341280 -0.1746844794  0.2813371284
##  [706] -0.4565648204 -0.1367492727 -0.1552146880  0.1911816394  0.4539165936
##  [711]  0.2904307102 -0.7211686423  0.3303277151 -0.3082460774 -0.2874419167
##  [716]  0.4127611366  0.3275282009 -0.7411261864  0.0300778854 -0.8527605636
##  [721] -0.1787213590 -0.7035377136  0.3027923083 -0.2884126195 -1.8958469204
##  [726] -1.4715655290 -0.7613202160  0.0746174257 -0.8131216241  0.4837284008
##  [731] -0.8564118741 -0.1718921336 -0.4113819936 -0.6861899022 -1.0858397113
##  [736] -0.3611922355 -0.4587503258 -0.1276201705 -0.4563474090 -0.9687383099
##  [741] -0.7285759184 -0.4733356990  0.1755758871 -0.6931949756 -0.7452243103
##  [746] -0.5941830338 -1.0027865579 -0.4024498349  0.5045453455  0.5128091901
##  [751] -0.2257348978 -0.8736737834 -0.2748524619 -1.1275688642 -0.4572407603
##  [756] -0.2150880294  0.0296603067  0.1430568484 -0.0002228188 -0.9603323156
##  [761] -0.9869396601  0.2315526716 -0.3759912998 -1.1922446952 -0.7736872116
##  [766] -0.7989650287 -1.0870992986 -0.5037677188  0.2265811530 -0.0813987150
##  [771] -0.4134590204 -1.0555672557 -0.4422985519  0.8642127559 -0.2281027645
##  [776] -0.5812073000  0.5988633093  0.6570843369 -0.9435899617  0.1607859798
##  [781]  0.3391603091 -0.5822606858 -0.2269723542 -0.4203235991 -0.9275819267
##  [786] -0.3053453563 -1.6011766502 -0.6418229758  0.1034323281 -0.2918626255
##  [791] -0.2119363171 -1.0217546011 -0.8489596799  0.1769183610 -0.0996839528
##  [796] -1.0555354156  0.1257623575 -0.8180780390 -0.6052723221 -0.4542569965
##  [801] -0.4143689706 -0.3472838165 -0.0776239315 -1.0381560616  0.1582385257
##  [806] -0.3732730757 -0.3189369090 -0.1149298137  0.1691777629  0.0566470355
##  [811] -0.4760547334 -0.2497172122 -0.1852808497  0.2670193604 -0.0672420336
##  [816] -0.0960993929 -0.3201455474 -0.3835839271 -1.1520784689 -0.2399902101
##  [821] -1.1053593627  0.3833529822 -0.0203101652 -0.4382891493  0.3937753643
##  [826]  0.9658115295 -0.6667245459  0.4557054457 -0.4715420073 -0.3015329592
##  [831] -0.5188183737 -0.1055707308 -0.7719038986 -0.4112199054 -0.7856947393
##  [836] -0.3055676276  0.1984780438 -0.1103896294 -0.6426975821 -0.4734361221
##  [841] -0.4212595723 -0.7959770968  0.3859324961  0.1358482769 -0.3458502019
##  [846]  0.6597798206 -0.7355579972 -0.9675558293  0.5823970640 -0.2263379962
##  [851]  0.6694647103 -0.6493484764 -1.3672241808 -0.4928286846 -0.0068730013
##  [856]  0.3904129548 -0.3300671522 -0.7236517241 -0.5553852475 -0.0252694593
##  [861] -1.7825805730 -0.7445672386 -0.5567121221  0.0181988862 -0.1244687475
##  [866] -0.6001155500 -1.0593523929 -0.1611191454 -0.7818685570  0.0689807478
##  [871]  0.3973143308 -1.3432066041 -0.4496184921 -0.0075312841  0.2997510246
##  [876] -0.8707729555 -0.0416392765 -0.7860665308 -0.6219890565 -0.9315529296
##  [881] -1.1238067221 -0.3803086440 -0.2383985920  0.2102188848  0.5428458632
##  [886]  0.2678047643 -1.0525741494 -0.8705666553  0.7856677980  0.3468597035
##  [891] -0.6154872437 -0.3678348529 -0.2457770365  0.7079494422 -0.6270213838
##  [896] -0.2956945224 -0.4187421807 -0.0223245373 -0.2318677575 -0.2843034113
##  [901]  0.5260717824 -0.4769972286 -0.5482348641  0.4849949479 -1.3574227928
##  [906]  0.4638537895  0.5786402390  0.4440539292 -2.0102209966 -0.3878788925
##  [911] -0.4805065506 -0.5871254946 -0.5981825009 -0.6074139762 -1.1522279871
##  [916] -0.1258681382 -0.9137336923 -0.1790041060 -0.6310069814 -0.1493423975
##  [921] -0.3108898856 -0.5884128975 -1.2612852499 -0.6926130397 -0.5453312837
##  [926] -0.3266280087 -0.1991525656 -1.0082145771 -1.0430708711 -0.7365546740
##  [931] -0.8758732668 -0.5753263945 -1.0622747308 -1.0422268267  0.0418394939
##  [936] -0.2670237731 -0.0315922505 -1.2046434548 -0.7735599988 -0.4406672013
##  [941] -0.0443611928  0.4471751161 -0.0261581958 -0.3851604095 -0.4658731209
##  [946] -0.7773420145 -1.4788445286  0.0509736708  0.0668348463 -0.7196995376
##  [951]  0.1818734415 -0.4481477289  1.6671872130 -1.0631978665 -0.6622817699
##  [956] -0.3842890070 -0.6836916738 -0.5214052436 -1.0131018419 -0.1937713023
##  [961] -1.0574749429  0.3039723274  0.1511869041 -0.2312814114 -2.2978699643
##  [966] -0.0266193625  1.4722671291 -1.0240093615 -0.0101123531 -1.0180802491
##  [971] -0.0827371575 -0.5328925291  0.2094554066 -0.9267690361 -0.0572623779
##  [976] -0.8673922836 -0.3570240211 -0.6436446503 -0.3367864659 -0.8662590052
##  [981] -1.7624383111 -0.0312471011 -0.2572213809 -0.3666922513  1.1542819725
##  [986]  0.2351148175  0.4195896096  0.0703456357 -0.4061128055 -0.2557303518
##  [991] -0.3608942727 -0.4311785962 -0.9191039967 -0.5649249819 -0.1829647066
##  [996] -0.4350014515 -0.4446248260 -0.7051329176 -0.7775199188 -1.1854858827
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
##   0.66138610   0.26019966 
##  (0.08228236) (0.05817818)
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
## [1] -0.4113939  0.2500961 -1.1855401 -0.2535807  0.2526233  0.7339533
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
## [1] -0.0062
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9491483
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
##     original       bias    std. error
## t1*      4.5 -0.004204204   0.9146998
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 3 4 5 6 7 
## 3 1 2 1 1 1 1
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
## [1] -0.0084
```

```r
se.boot
```

```
## [1] 0.9172081
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

