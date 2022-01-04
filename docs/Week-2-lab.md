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
## 0 1 2 3 5 6 7 9 
## 1 1 2 1 2 1 1 1
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
## [1] -0.0453
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
## [1] 2.695144
```

```r
UL.boot
```

```
## [1] 6.214256
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
##    [1] 5.1 3.8 4.4 3.1 5.2 4.6 5.3 3.0 5.7 4.6 3.2 4.9 4.4 4.7 4.7 5.8 4.4 5.6
##   [19] 4.7 4.7 4.5 3.7 4.4 4.2 6.4 5.6 4.7 5.0 4.6 4.3 4.9 3.7 5.3 4.4 3.2 3.6
##   [37] 4.6 4.2 4.3 5.8 4.8 4.4 4.7 4.1 3.8 4.3 5.8 4.6 4.5 2.6 4.8 3.6 3.5 3.8
##   [55] 4.4 5.8 3.4 4.4 6.9 4.6 4.8 4.5 4.1 6.1 3.8 3.2 4.3 4.4 5.8 3.9 4.2 4.5
##   [73] 5.3 4.6 5.6 4.5 4.0 3.5 4.3 5.4 5.3 5.5 6.2 4.9 5.2 5.3 5.6 4.2 4.3 5.6
##   [91] 3.6 3.5 2.9 4.5 5.0 4.2 4.0 4.4 6.4 4.1 4.3 4.0 3.3 4.5 5.1 4.7 3.7 4.1
##  [109] 2.6 3.9 2.5 4.8 4.4 2.6 4.0 3.8 3.9 2.2 3.6 5.0 5.7 4.2 5.7 2.9 4.3 3.9
##  [127] 5.4 4.8 5.2 2.1 4.1 4.2 4.5 3.6 4.9 5.2 4.8 5.3 3.6 5.0 7.0 3.7 3.3 3.1
##  [145] 3.7 6.8 4.0 4.7 4.1 5.1 4.6 5.1 4.4 4.3 5.5 3.8 4.2 5.4 3.6 4.9 4.9 2.9
##  [163] 5.2 6.3 3.1 3.9 3.2 4.2 5.8 4.6 4.1 5.0 5.8 3.5 4.7 4.8 4.6 4.6 2.1 4.5
##  [181] 5.2 4.5 3.7 4.5 4.6 5.0 4.5 5.3 5.7 3.6 4.5 5.0 5.6 5.8 4.9 5.6 3.8 3.5
##  [199] 4.0 3.6 4.4 3.3 4.6 3.8 4.6 6.4 6.0 4.7 5.5 4.5 4.0 5.2 3.8 4.7 5.1 3.9
##  [217] 5.0 4.8 6.5 5.9 4.5 4.4 3.7 4.6 4.0 4.8 5.0 5.6 3.9 3.7 4.0 3.0 4.3 4.3
##  [235] 3.4 4.5 6.3 5.5 3.2 5.0 5.2 4.3 2.8 4.6 5.4 4.6 3.3 3.9 3.7 5.8 4.7 4.8
##  [253] 5.6 3.3 4.4 3.8 4.2 5.2 4.3 3.0 4.6 5.3 5.3 4.6 4.6 4.8 3.6 4.1 5.3 4.7
##  [271] 5.4 5.2 7.6 3.7 5.6 5.8 4.6 3.0 4.6 5.3 4.8 4.1 3.7 5.6 4.7 3.4 4.3 3.5
##  [289] 4.1 3.1 4.6 5.0 4.1 4.7 5.0 4.6 4.7 6.7 3.1 4.6 3.6 4.3 3.9 4.9 4.0 4.5
##  [307] 5.6 4.5 4.4 4.6 4.6 3.5 4.3 5.8 4.5 5.7 5.6 4.7 5.0 2.7 3.4 2.5 5.3 3.8
##  [325] 5.1 3.3 5.0 5.6 5.1 3.8 5.1 3.8 4.2 4.2 4.4 4.0 4.1 5.2 5.0 4.1 4.9 6.2
##  [343] 4.4 4.3 5.0 3.8 3.6 3.9 5.5 5.0 5.0 4.9 5.3 4.1 5.7 4.5 4.2 4.4 4.6 3.4
##  [361] 3.6 5.0 4.7 4.0 3.4 3.8 6.0 4.1 4.3 3.1 5.8 3.7 3.2 4.1 3.8 4.3 5.3 5.0
##  [379] 5.1 5.9 5.0 6.6 4.0 3.7 4.6 4.7 4.6 6.1 4.6 4.4 4.3 5.6 4.6 2.7 2.8 3.4
##  [397] 2.8 5.1 5.6 4.7 5.7 6.0 3.5 5.2 4.8 3.7 5.4 4.3 5.1 3.2 3.3 4.3 4.8 3.3
##  [415] 4.6 4.9 5.5 5.4 6.1 4.9 4.4 4.8 3.2 4.9 3.6 2.6 5.7 4.1 4.3 4.4 4.1 3.9
##  [433] 5.0 5.3 4.0 4.0 4.4 3.9 4.6 5.4 5.0 4.2 5.6 4.6 4.2 4.5 4.5 3.8 5.1 5.0
##  [451] 6.1 3.3 3.5 4.7 3.9 3.6 2.8 5.6 4.8 4.3 5.0 4.2 4.8 5.2 4.1 5.4 3.2 6.1
##  [469] 3.0 4.4 5.2 4.0 4.8 3.6 4.5 4.9 3.0 3.1 4.3 5.6 5.7 3.2 3.5 4.0 4.0 5.3
##  [487] 5.3 4.7 3.6 4.2 5.1 4.7 6.0 5.9 4.9 5.4 3.6 6.0 4.3 5.0 4.4 3.7 3.0 4.3
##  [505] 4.8 4.1 7.4 3.0 5.5 5.3 5.8 2.8 5.5 4.2 5.1 5.9 4.6 4.9 5.1 5.7 5.3 4.7
##  [523] 4.4 3.9 4.7 5.2 3.8 4.7 4.0 5.3 3.4 4.7 4.7 5.7 4.9 4.0 5.4 4.4 4.6 4.0
##  [541] 4.0 3.3 4.3 5.3 4.9 5.4 5.7 3.3 5.8 4.2 6.4 4.8 5.3 3.8 4.5 5.0 4.3 4.2
##  [559] 5.4 5.0 4.2 5.5 5.1 5.9 5.4 4.7 4.6 4.1 5.7 4.7 2.8 3.8 5.5 4.9 3.4 4.1
##  [577] 4.4 4.9 3.2 4.3 3.6 4.2 4.4 3.4 4.0 4.3 4.2 4.6 4.6 5.3 5.7 3.7 3.5 4.0
##  [595] 5.7 5.7 2.4 5.0 3.7 3.3 4.1 3.4 4.5 2.8 4.1 3.9 4.8 3.9 5.0 5.0 4.3 7.1
##  [613] 2.6 6.3 3.6 4.1 6.0 5.4 3.8 4.2 4.3 5.4 3.1 5.4 4.2 4.1 5.3 3.9 3.1 3.8
##  [631] 4.0 4.4 6.4 4.5 5.2 4.6 5.3 5.4 4.8 5.0 4.9 4.2 4.7 4.0 4.3 5.1 4.9 4.8
##  [649] 4.6 5.9 5.4 3.9 4.4 4.2 4.3 4.5 3.0 4.2 3.4 3.3 5.3 4.2 5.8 4.0 4.3 4.3
##  [667] 4.5 6.1 5.1 5.5 5.4 4.2 4.1 5.1 6.0 4.4 4.7 3.3 4.6 7.2 5.6 3.3 4.5 4.3
##  [685] 6.4 6.5 5.4 4.6 4.0 5.1 5.2 3.6 4.2 4.9 3.0 3.8 4.5 5.7 5.6 5.7 2.7 4.2
##  [703] 5.1 3.9 5.1 4.6 4.6 7.0 3.3 3.8 3.7 2.9 4.1 5.0 4.5 5.0 6.4 4.5 4.5 5.4
##  [721] 5.1 2.6 5.0 4.6 5.4 4.5 4.1 5.9 4.7 4.2 4.8 4.2 4.6 3.6 5.3 4.5 4.4 4.1
##  [739] 6.2 4.7 5.2 6.6 6.4 5.8 4.8 5.1 5.2 6.2 4.7 4.1 4.8 3.7 4.4 4.3 4.5 5.5
##  [757] 5.1 4.7 4.7 5.8 4.2 4.0 3.5 3.6 3.9 5.6 5.2 4.0 5.9 2.6 4.0 4.7 3.9 4.9
##  [775] 2.8 4.8 1.9 4.5 5.2 3.4 3.5 3.9 2.9 4.1 2.9 4.5 3.8 4.8 3.7 4.8 4.9 5.6
##  [793] 5.1 4.1 5.8 5.1 5.2 4.5 5.2 3.5 3.9 4.0 4.6 5.7 3.5 4.9 4.2 6.1 3.5 4.2
##  [811] 2.1 4.1 4.5 4.4 3.9 4.0 4.2 4.5 3.2 5.8 3.4 4.2 3.6 4.8 4.6 4.3 4.4 3.9
##  [829] 5.6 3.6 5.5 3.8 5.6 4.5 4.6 3.6 4.3 4.2 4.7 3.3 5.4 3.9 4.2 4.0 2.9 4.0
##  [847] 4.3 5.6 4.8 4.7 4.7 5.2 5.1 3.9 4.1 3.1 4.4 4.0 4.5 4.4 3.6 4.5 3.6 4.2
##  [865] 5.5 4.9 5.6 3.3 5.4 3.7 4.0 4.4 3.8 4.6 4.4 4.9 5.2 5.7 3.9 5.4 4.8 4.5
##  [883] 3.2 4.0 4.0 4.1 3.6 6.1 5.3 4.4 3.8 4.2 4.1 3.5 5.4 5.2 3.3 3.8 4.5 2.6
##  [901] 3.2 3.7 5.7 5.2 4.7 4.1 4.9 2.8 5.5 2.9 3.5 4.2 4.2 4.4 5.3 5.0 3.7 5.3
##  [919] 3.8 3.5 3.6 6.0 4.3 5.5 3.3 4.2 4.1 4.9 5.2 5.9 3.9 4.7 3.9 4.5 4.6 4.5
##  [937] 3.7 3.6 4.9 5.4 4.0 6.0 6.2 5.9 5.6 4.9 2.7 4.4 5.0 5.5 4.8 4.7 3.9 4.1
##  [955] 6.2 4.9 4.8 5.8 3.3 6.9 4.9 5.8 3.3 5.4 4.1 3.5 4.6 4.4 4.3 4.1 3.5 4.3
##  [973] 4.6 4.7 4.8 5.1 4.2 4.7 3.6 3.2 5.7 4.8 4.9 3.7 5.3 4.3 5.7 4.4 4.6 3.3
##  [991] 4.9 5.5 3.6 3.9 4.7 4.0 4.9 3.1 3.4 3.6
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
##    [1] 5.5 4.5 3.3 4.1 5.3 5.4 4.8 5.4 5.3 5.1 5.0 5.1 3.5 5.5 4.1 5.3 4.7 4.7
##   [19] 4.6 5.6 6.2 3.6 4.3 4.4 5.5 4.2 4.4 4.8 5.1 5.9 3.7 4.8 4.1 3.9 3.5 4.6
##   [37] 4.2 4.7 4.9 4.5 4.7 4.9 3.8 4.7 4.9 6.4 4.0 4.0 4.8 3.5 2.8 3.6 5.8 4.6
##   [55] 4.3 4.9 4.7 5.1 3.3 4.7 3.5 4.7 4.1 4.2 2.6 4.6 5.3 3.4 4.3 4.3 2.9 5.1
##   [73] 4.8 5.7 5.2 4.2 4.6 6.5 4.7 4.5 4.8 5.8 5.3 5.8 3.8 3.0 4.0 4.7 3.6 3.0
##   [91] 4.8 5.4 4.4 4.4 4.8 3.8 4.4 7.2 4.8 4.4 5.3 3.7 5.3 3.7 5.1 4.0 4.6 4.0
##  [109] 3.9 4.3 5.0 4.0 3.3 5.2 5.3 4.2 5.5 4.2 2.5 3.8 4.4 3.8 4.1 4.9 4.1 3.4
##  [127] 3.2 5.2 4.1 4.2 3.4 6.0 5.8 4.1 4.6 4.9 5.6 5.2 4.5 4.1 3.9 4.7 4.1 5.7
##  [145] 5.3 3.4 4.9 3.9 3.6 6.6 5.5 3.7 5.2 5.6 4.1 3.6 5.0 4.9 4.5 3.2 4.4 4.9
##  [163] 5.4 6.8 4.1 4.7 4.7 4.9 4.2 5.4 3.7 4.0 5.4 5.6 5.9 4.9 6.0 4.2 4.7 4.1
##  [181] 4.9 4.4 5.5 5.5 3.8 4.2 5.3 4.8 3.0 3.7 3.9 4.7 5.3 2.5 5.6 3.4 5.4 6.3
##  [199] 4.3 4.7 5.6 5.4 4.5 4.9 4.1 5.3 5.9 3.6 5.1 4.6 3.1 5.6 5.1 4.6 5.6 4.7
##  [217] 4.1 4.9 5.3 5.2 4.0 4.8 3.3 4.1 4.0 4.3 4.7 4.4 4.8 3.2 3.9 5.1 2.9 2.7
##  [235] 5.4 2.7 4.5 3.4 5.3 6.2 4.5 3.7 5.2 4.4 4.9 5.3 4.9 4.0 3.6 4.6 4.4 5.5
##  [253] 4.2 5.4 4.3 4.2 5.0 4.5 4.6 4.0 4.0 6.1 3.4 5.4 4.6 3.6 3.9 4.9 3.0 4.4
##  [271] 4.3 5.4 3.5 4.5 5.7 2.2 3.8 5.0 5.0 4.9 6.1 3.0 5.8 6.6 3.7 4.8 5.7 4.6
##  [289] 5.7 5.3 4.1 3.5 4.5 5.4 4.0 4.0 3.9 3.9 5.6 3.7 3.8 4.2 5.0 4.3 3.4 5.4
##  [307] 4.7 5.3 4.5 2.5 4.8 5.4 3.0 5.5 4.9 5.1 3.7 3.9 3.6 4.6 7.2 4.8 5.1 4.9
##  [325] 4.4 4.4 5.1 3.6 4.7 4.3 5.4 6.1 3.0 4.3 3.3 3.3 3.4 4.8 3.4 5.3 4.7 4.2
##  [343] 4.0 4.8 5.7 4.2 5.6 5.1 3.9 6.6 2.6 3.6 4.9 6.0 5.5 4.7 3.3 4.1 3.7 4.8
##  [361] 4.1 4.4 5.3 4.1 5.2 3.4 5.0 5.0 6.0 3.9 3.7 4.3 4.6 4.3 6.1 5.5 2.4 3.9
##  [379] 4.1 4.4 4.9 3.8 5.3 5.6 4.2 3.6 4.6 4.8 5.2 5.5 4.0 2.2 3.3 4.9 4.0 5.3
##  [397] 3.7 5.5 4.0 5.3 4.6 5.5 5.2 5.3 4.1 4.2 4.1 4.3 4.2 4.1 5.3 3.2 5.4 5.0
##  [415] 3.8 3.8 4.7 4.9 4.2 3.7 4.3 4.3 3.9 5.8 4.6 3.3 5.4 6.9 3.6 4.9 6.0 4.6
##  [433] 4.8 3.2 6.0 5.8 5.2 5.3 4.0 4.3 5.1 4.9 5.6 3.5 4.6 5.9 4.8 5.5 4.3 3.6
##  [451] 3.4 4.3 4.5 5.3 5.9 4.9 2.9 4.3 3.2 6.3 4.0 4.7 5.2 4.1 5.5 2.9 4.6 5.7
##  [469] 4.8 4.5 6.3 4.8 4.5 5.1 5.2 3.0 5.1 4.7 4.2 6.2 4.3 5.4 5.9 6.1 5.1 4.0
##  [487] 5.6 5.0 4.3 4.9 4.7 4.3 2.7 4.6 4.3 5.1 5.6 3.9 4.6 2.8 4.0 3.8 4.1 4.3
##  [505] 4.3 4.9 5.0 6.2 4.3 4.2 4.6 3.5 4.0 4.9 4.9 4.5 5.9 4.6 3.7 4.0 3.2 5.2
##  [523] 3.4 4.6 3.7 5.8 3.8 2.4 4.0 4.6 4.2 3.5 5.6 5.8 4.9 5.3 3.9 4.7 4.7 5.7
##  [541] 4.1 2.8 3.1 4.7 3.7 3.5 4.4 5.0 3.0 4.2 3.5 5.4 4.6 4.3 5.9 4.1 4.2 4.4
##  [559] 4.4 7.4 5.4 4.6 4.2 4.6 5.5 3.9 4.3 4.1 5.9 3.7 4.4 4.2 5.6 5.4 4.7 3.9
##  [577] 3.7 5.4 4.9 3.3 6.1 3.1 5.5 5.1 4.3 3.2 4.4 5.4 3.9 3.8 3.1 4.7 4.3 6.2
##  [595] 5.5 5.2 5.0 4.0 3.9 5.1 5.1 4.1 4.8 3.6 3.3 5.4 6.5 3.8 5.2 4.7 4.8 5.0
##  [613] 5.2 4.9 4.9 6.6 2.6 3.9 5.3 6.3 5.7 4.3 3.2 7.3 4.6 2.6 5.7 5.0 5.9 4.3
##  [631] 3.2 3.9 4.9 3.9 3.2 4.7 5.5 3.5 4.6 6.2 6.2 4.3 5.2 3.5 5.3 3.9 4.4 5.0
##  [649] 4.6 4.5 3.9 5.4 5.0 4.3 6.6 4.0 4.1 4.8 3.9 4.6 4.3 3.8 5.1 5.4 3.7 2.6
##  [667] 4.2 2.9 6.0 3.8 5.7 3.9 4.6 3.4 3.4 5.6 4.2 4.7 4.5 5.7 3.8 5.3 3.6 5.0
##  [685] 4.3 5.3 4.9 4.7 5.8 4.7 4.7 6.1 3.0 4.9 3.7 3.4 3.3 2.9 5.2 3.4 4.2 3.8
##  [703] 4.7 4.9 3.3 2.7 5.4 4.9 6.1 4.1 6.1 3.5 2.8 4.2 5.1 5.7 5.1 5.5 3.0 5.6
##  [721] 4.8 4.8 4.6 5.1 6.0 5.3 5.1 3.3 4.2 2.6 6.5 4.1 4.4 5.8 3.9 4.4 5.0 4.9
##  [739] 3.7 4.6 4.2 4.2 2.9 3.2 5.2 3.8 3.8 4.6 5.4 4.3 5.0 2.8 3.7 4.4 3.6 3.2
##  [757] 4.1 4.0 4.2 3.4 6.5 4.1 2.8 5.0 3.2 5.5 4.9 3.9 3.8 4.0 4.4 3.8 4.4 2.7
##  [775] 5.2 3.6 3.6 4.6 4.2 5.2 6.0 4.3 3.9 5.8 3.9 4.4 4.0 4.7 4.8 3.1 3.7 3.9
##  [793] 3.8 4.8 5.1 3.6 5.4 3.9 3.4 3.6 4.3 3.8 4.7 4.2 3.3 6.5 4.8 5.1 4.8 3.5
##  [811] 4.5 4.6 5.1 3.9 5.3 5.2 5.4 3.9 4.9 5.6 5.4 4.6 3.7 5.0 5.6 5.7 5.1 4.4
##  [829] 4.5 3.1 4.4 5.1 5.3 3.7 4.2 5.5 4.5 4.3 4.5 4.5 3.1 3.1 6.0 4.6 6.3 5.2
##  [847] 5.2 5.6 5.2 5.4 5.5 4.7 2.8 3.7 3.5 4.3 4.8 4.8 4.5 6.2 4.8 6.1 6.5 4.8
##  [865] 4.8 4.8 4.6 5.4 5.8 4.5 2.9 5.0 5.5 5.5 4.6 4.0 3.0 2.8 4.6 3.4 4.9 4.0
##  [883] 5.0 4.3 4.7 4.0 3.4 4.3 5.0 4.8 5.6 4.7 5.7 4.1 5.0 5.2 5.1 5.4 5.3 4.6
##  [901] 4.9 4.7 5.3 4.6 4.9 4.5 5.5 4.4 4.5 4.9 4.2 3.0 5.2 4.1 3.3 3.3 4.3 4.0
##  [919] 6.3 2.8 3.5 5.5 4.3 3.8 4.0 3.8 4.7 5.6 4.7 4.9 5.0 4.8 4.8 3.3 4.6 3.8
##  [937] 5.4 4.1 5.2 6.1 5.4 5.1 5.3 5.8 4.2 6.1 3.7 4.6 5.6 4.7 4.2 4.5 2.9 4.3
##  [955] 5.2 4.3 6.1 5.0 5.0 4.9 4.1 2.0 5.4 5.5 5.5 3.0 4.6 7.5 3.9 5.0 5.9 4.7
##  [973] 5.2 4.1 5.1 5.9 4.6 3.5 4.7 3.8 3.2 3.7 4.0 5.5 5.4 3.4 3.8 4.5 4.8 4.2
##  [991] 5.8 5.1 5.8 4.7 4.8 6.2 6.0 3.7 4.0 3.5
## 
## $func.thetastar
## [1] 0.0609
## 
## $jack.boot.val
##  [1]  0.54971910  0.45158501  0.37065527  0.23204420  0.16790831 -0.01461538
##  [7] -0.04750733 -0.24464286 -0.29770115 -0.44209040
## 
## $jack.boot.se
## [1] 0.9578036
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
##    [1] 4.1 3.7 5.1 4.1 3.5 5.2 3.3 5.0 2.9 5.4 5.5 4.5 5.6 4.5 6.2 4.2 4.9 3.3
##   [19] 3.4 5.2 5.9 3.8 5.2 4.0 3.3 3.7 4.1 5.1 5.3 3.1 2.8 2.8 4.0 3.8 4.3 2.8
##   [37] 3.0 4.8 4.2 3.5 4.9 2.5 4.4 2.2 4.2 5.1 4.7 5.7 4.8 4.9 4.1 4.1 4.2 4.7
##   [55] 3.7 5.5 4.5 4.8 3.0 4.9 4.9 4.4 3.3 4.5 5.2 4.4 4.7 3.1 4.2 5.0 3.8 3.7
##   [73] 5.1 4.0 2.6 5.3 4.4 5.7 4.5 4.6 3.6 3.7 4.5 5.6 5.3 4.1 3.2 5.6 4.7 3.8
##   [91] 4.3 4.5 4.1 5.1 5.0 3.9 5.3 3.4 5.1 3.1 5.8 4.3 5.6 6.1 5.5 4.2 4.4 3.6
##  [109] 5.2 4.6 4.7 3.9 6.3 5.8 5.2 4.9 3.7 4.7 5.9 4.9 3.5 6.6 3.7 5.0 4.2 5.4
##  [127] 4.7 4.0 4.2 4.8 4.9 3.7 4.1 4.5 6.0 3.9 3.6 5.6 5.5 5.4 4.4 4.5 2.0 3.7
##  [145] 3.3 4.6 4.6 3.8 4.6 4.0 4.1 5.8 5.3 3.4 4.4 3.3 2.5 4.8 5.1 5.5 5.4 4.6
##  [163] 2.4 5.0 4.3 5.3 3.6 3.6 4.0 4.0 5.1 4.1 5.0 4.7 4.9 6.4 4.1 4.9 5.1 4.8
##  [181] 4.9 5.6 2.1 5.9 4.4 5.3 3.6 5.3 4.4 3.8 3.9 5.0 3.7 3.1 5.0 4.9 3.5 4.7
##  [199] 5.7 4.6 5.1 4.5 2.6 3.8 4.8 5.2 3.9 4.4 3.5 6.3 5.1 3.2 4.3 5.5 4.5 5.5
##  [217] 4.2 4.5 4.4 4.5 5.6 4.4 4.4 4.7 6.4 3.5 5.0 3.1 4.4 5.3 4.4 5.4 4.8 5.0
##  [235] 4.3 5.1 2.5 4.7 6.2 4.7 3.9 4.5 5.1 6.6 4.3 4.2 4.6 4.2 5.2 5.2 6.1 3.7
##  [253] 4.4 5.4 3.8 3.4 4.9 4.0 7.1 3.9 3.6 4.3 4.7 4.1 3.7 3.0 4.5 4.6 4.9 5.0
##  [271] 4.9 3.6 5.7 5.0 5.0 6.2 6.5 5.4 3.6 4.3 2.9 5.2 5.5 2.1 4.7 4.4 4.1 3.9
##  [289] 5.6 3.8 3.1 4.6 3.3 4.7 5.6 4.8 4.2 3.8 5.5 3.2 3.3 3.0 5.7 3.1 6.0 3.0
##  [307] 5.9 4.2 4.4 5.0 4.3 5.5 1.9 4.2 4.1 3.8 7.0 4.5 5.5 5.0 4.6 4.8 4.8 5.5
##  [325] 4.2 5.7 4.6 5.5 5.2 3.7 3.5 4.8 3.1 5.7 4.5 4.3 3.7 4.4 6.2 4.0 5.3 4.5
##  [343] 3.8 5.0 4.1 4.3 4.6 4.1 4.2 5.1 3.1 3.8 4.4 4.8 6.4 4.2 3.5 3.2 4.3 4.8
##  [361] 5.3 5.5 4.7 5.5 3.7 3.3 4.2 3.8 4.4 4.8 4.1 5.2 5.8 3.8 3.8 6.2 6.6 5.9
##  [379] 3.4 3.1 4.2 4.7 4.3 4.4 4.9 5.3 4.9 4.1 2.5 4.0 3.7 4.2 3.7 4.5 3.9 4.4
##  [397] 4.4 5.8 3.4 4.1 5.6 3.9 4.7 5.3 4.9 3.1 3.9 3.2 5.2 4.7 4.6 2.9 4.0 4.7
##  [415] 5.0 3.6 3.8 4.1 5.4 3.9 5.8 5.9 5.7 4.9 4.0 5.0 4.7 5.8 4.2 5.5 5.8 3.8
##  [433] 4.1 4.1 4.9 3.2 4.8 5.1 4.3 4.5 4.7 4.1 5.4 3.1 5.3 4.4 4.6 5.1 6.1 4.0
##  [451] 5.3 4.1 4.3 4.0 3.2 2.9 3.9 4.3 4.8 4.8 4.6 6.4 4.9 4.3 2.6 3.5 3.9 4.8
##  [469] 5.4 4.4 4.6 6.6 4.7 4.9 3.9 3.9 5.3 4.8 3.5 5.8 4.7 3.9 5.7 4.5 4.7 4.4
##  [487] 3.2 5.1 4.2 4.3 4.3 6.4 5.4 5.9 3.2 5.3 5.5 5.6 3.1 4.3 4.1 4.6 3.4 4.8
##  [505] 6.0 4.3 5.7 5.5 3.0 3.2 4.2 2.8 4.9 6.2 2.4 4.7 3.8 2.8 3.6 2.4 3.4 2.7
##  [523] 4.1 4.4 5.0 4.8 4.8 4.3 4.9 5.0 4.3 6.7 4.9 4.6 4.0 3.7 4.3 5.2 5.6 4.6
##  [541] 5.3 4.2 4.0 6.3 6.3 5.1 5.7 4.0 5.1 5.3 4.6 3.4 4.4 5.8 7.6 4.5 3.5 3.1
##  [559] 4.6 3.4 5.0 5.3 4.5 2.8 7.2 2.9 4.5 4.9 6.0 5.0 5.6 4.8 4.3 3.5 3.8 3.4
##  [577] 5.2 4.6 6.9 5.9 4.7 3.8 4.5 4.9 4.8 4.6 6.9 4.1 5.0 4.0 5.7 3.1 3.2 3.2
##  [595] 4.9 4.6 4.6 6.0 3.7 4.7 3.0 4.9 4.7 5.9 5.1 4.1 4.8 6.4 4.3 4.5 5.8 4.6
##  [613] 4.7 4.6 5.2 4.9 4.6 5.0 3.6 5.3 3.6 4.7 5.5 6.3 3.5 5.8 5.8 4.5 6.3 4.1
##  [631] 5.1 3.5 3.6 4.1 5.3 4.3 3.6 5.2 4.6 5.8 4.6 2.2 4.0 5.1 4.7 4.9 5.7 4.8
##  [649] 4.3 5.0 2.9 5.9 3.3 5.2 4.4 4.8 4.2 4.7 4.0 6.1 6.0 5.6 4.2 4.0 5.5 5.7
##  [667] 5.3 4.0 4.0 5.1 3.6 4.6 5.3 6.0 5.4 4.6 5.6 4.9 4.2 4.1 7.1 2.9 3.9 5.2
##  [685] 4.1 5.9 5.0 3.8 5.5 5.7 2.9 4.2 3.4 3.2 4.2 6.2 4.4 2.8 3.2 4.8 4.9 4.3
##  [703] 6.4 4.9 4.1 6.0 4.5 4.2 4.1 6.4 3.6 3.1 2.4 4.0 5.6 5.1 4.0 5.2 4.1 4.2
##  [721] 4.9 3.4 4.5 5.5 4.8 4.9 4.7 3.3 5.8 5.0 5.0 3.4 4.4 5.6 5.7 5.7 5.4 2.6
##  [739] 3.8 4.3 4.7 6.0 3.8 5.4 3.8 5.8 4.9 2.9 3.6 5.3 4.8 3.9 5.2 5.7 4.9 6.1
##  [757] 6.2 5.1 3.6 3.7 4.6 4.0 4.3 5.4 4.7 3.4 5.3 3.7 4.6 3.0 5.1 4.3 4.9 4.5
##  [775] 5.7 4.1 3.9 4.2 5.4 2.8 5.6 4.2 5.7 4.4 3.9 5.4 3.0 6.4 3.9 4.4 6.0 4.7
##  [793] 5.7 4.8 2.4 4.7 3.7 6.8 5.0 6.3 5.8 4.8 4.4 4.0 4.6 4.8 4.6 3.1 5.8 5.3
##  [811] 5.1 4.1 5.0 4.9 4.9 3.4 4.6 4.4 4.4 4.4 5.3 4.1 4.3 4.3 4.7 3.6 2.3 5.5
##  [829] 3.1 6.3 4.0 6.0 4.2 5.5 4.7 5.4 3.6 4.0 5.5 5.2 5.2 3.1 3.7 3.4 4.8 4.9
##  [847] 4.2 4.5 4.4 4.9 3.9 2.9 4.3 4.9 5.7 4.0 4.2 4.2 5.6 5.8 4.9 3.9 4.1 4.4
##  [865] 6.1 3.8 3.1 3.8 4.8 3.0 4.2 4.4 3.7 3.5 5.1 3.8 4.5 4.1 5.0 4.7 5.3 5.6
##  [883] 4.2 6.0 4.2 3.4 4.2 3.0 4.3 5.3 4.4 5.6 5.5 3.7 2.3 3.8 4.4 5.6 3.6 4.8
##  [901] 5.1 4.2 5.6 4.5 2.8 2.8 5.8 4.3 5.4 3.8 3.6 4.6 4.8 4.8 5.1 3.1 4.6 3.8
##  [919] 3.8 6.7 4.8 3.3 4.1 3.7 4.5 3.9 4.1 2.5 3.8 4.4 3.1 3.4 5.4 4.9 3.4 4.0
##  [937] 4.8 3.6 4.9 3.6 3.3 4.1 4.5 4.4 6.2 4.1 3.8 4.4 5.1 2.0 3.7 4.2 4.6 3.4
##  [955] 5.3 4.9 4.3 3.8 4.2 4.2 5.4 3.2 4.6 5.4 5.0 3.9 4.3 6.0 4.9 6.5 5.3 6.1
##  [973] 5.8 4.9 5.4 3.5 4.3 5.3 5.7 3.0 4.1 4.9 4.8 5.9 4.4 4.6 3.9 4.1 6.1 5.4
##  [991] 5.0 5.4 2.9 5.7 4.3 5.5 5.1 3.6 3.8 5.3
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.620 5.500 5.300 5.300 5.100 5.100 4.800 4.800 4.600 4.592
## 
## $jack.boot.se
## [1] 1.034965
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
## [1] 0.9758791
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
##   3.175994   5.588225 
##  (1.352403) (2.577917)
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
## [1] -1.2392648  0.5247130  0.5634076  0.9736075  0.7476959  1.2004939
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
##    [1] -0.3125013870  1.5022983613  0.4859987543  0.5179652992  1.0895589971
##    [6] -0.4528715032  0.2165001476  1.4086030077  0.3058321318  0.6227520216
##   [11]  2.0395213158  0.3462493695  0.9665440049  1.2750956183  0.4494025714
##   [16]  0.8940795964  0.8885549192  0.7236850280  1.0071582027  0.5519401423
##   [21]  0.5194237866  0.8296816511  0.4437062738  0.0080303399  0.7247788083
##   [26]  0.8898805838 -0.0334803205  0.8448444336  2.2211712722  0.2988645563
##   [31]  0.7790267574  0.6703224650  1.1739465630  0.1751289567  0.5824246937
##   [36] -0.2782563983 -0.6148344908 -0.8874177868  0.4666729758  0.3761605926
##   [41]  1.4814829490 -0.3771151512  1.1398108342  0.0333595333  0.8730136654
##   [46] -0.0577498976  1.2800050624  0.6661771145  0.6896917553  1.0655510944
##   [51]  0.8633951354  0.8253071408  0.4893881444  0.8772160542  0.7017327347
##   [56]  1.0078234217 -0.0275647866  1.0571896019 -0.3961221584  0.8993439687
##   [61] -0.1653225996  0.2215787476  1.0896732928  1.2885887551  0.4613457141
##   [66]  0.3798480601  0.7167015348  1.1833621700  0.0461212097  0.9010205739
##   [71]  1.1922495897  0.1546834820  1.3938993982  0.7408123346  0.0081828384
##   [76]  0.5835483793  1.2702334601  0.4815424604  0.0156974569  0.8520276452
##   [81]  1.5011984455  0.7400731927 -0.2915322401 -0.2246692316  1.4481247656
##   [86]  1.0437907266  1.3199467586  0.5961104023  0.6193875710  0.0007299674
##   [91]  0.2759003786  1.0797383254  0.6115573004 -0.3010413485  1.1843497965
##   [96]  1.2832824542  0.9954082646  0.9202699619  1.4223049326  1.5330337893
##  [101]  1.4123057404  0.5245540188  0.4969976013  0.5876887692  0.0786989516
##  [106]  0.7158607937  0.1410113030  0.4106415055 -0.7629942322 -0.0786337065
##  [111]  1.2907657195  0.3220940174  1.1550720565  1.1631959819  1.7201306028
##  [116]  0.5641505022  0.4449434515  0.3284560077  0.4406409440  0.7852375632
##  [121] -0.4348598617  1.1569177468  0.2866020485  0.9615608143  1.6951495216
##  [126] -0.0535084897  0.6010011849  0.4124452960  0.9159711128  0.2069633788
##  [131] -0.2599333196  1.0631433347 -0.1296829293  0.2165578421  0.0055682460
##  [136]  1.8026703467  0.5804404912  1.2509018445  0.9159673903  0.3302361566
##  [141]  0.7922252993  1.5723371094  0.8414433126  1.3015254450 -0.3907240332
##  [146]  0.4242194877  1.4372143465 -0.8291676225  0.6376794936  0.3544358361
##  [151]  0.6871464303  1.0578546520  0.5733836638  0.5555636382  0.1741089820
##  [156]  1.3946387827  1.5807865139  0.0327623870  0.3646985199  1.0947086662
##  [161]  0.4039131168  0.7774278742  0.4680288050  0.6266473110  0.8332753964
##  [166] -0.0542535017  0.6901515406  0.3707787172  1.1318222845  1.0293097642
##  [171]  0.1173430887  0.2433886101  0.7277596830  0.7129089056 -0.5157039776
##  [176]  0.5064017890  1.0363112822 -0.1080818896  1.1859403445  0.5812359468
##  [181]  0.8342937668  0.2855198559  0.1606350659  0.0249401388  0.2156336503
##  [186] -0.4011379094  1.0268915332  1.2548266016  0.4979027652  1.0910698900
##  [191]  0.4245311584  1.2917230332  1.0332918323  1.0066639877  1.1428418458
##  [196]  0.8758663621  1.5733073127  0.8715069231  0.6559494727  1.3155781239
##  [201]  0.7715496761  1.0338267796  1.8921545779 -0.2648737378  0.8419528955
##  [206]  0.6509576715  0.3603271531  1.0013694203  0.3414784629  0.4991373971
##  [211]  0.9001316130  0.1731466210  1.3130517825 -0.2524689488  0.1909990143
##  [216]  0.9107880918  1.2083332118  0.9936317567  0.8385415888  0.0873423892
##  [221]  0.8281508386  0.9927201130  0.9349676270  0.1468252186  0.8276462097
##  [226]  0.7619595513  0.8863290944  1.0079508405  0.8669092105  0.6685490786
##  [231]  0.3117373884  0.8704234473  1.0531569397  0.4283571662  0.9126892513
##  [236]  0.8092573870  1.1450242942  1.0387277593  0.4788375580 -0.2473395832
##  [241]  1.3753970851  0.6376274894  0.3849109718  0.3702517071  1.1877390243
##  [246]  1.3400047225  0.7908136196  1.0666656635  0.9523500401  0.9211429729
##  [251]  0.9986221701  1.1400148683  1.6760928869  0.8723678810  0.7395472569
##  [256] -0.0373955977  0.5981966395  0.8429714528  0.4813250157  0.8262421150
##  [261]  1.9861907639  1.4222744066  0.0486921179  0.8745807411  0.9958910520
##  [266]  0.6311159147  1.1623244090  1.6839338908  0.8947337963  0.9623983141
##  [271]  0.4307054748  2.2932484796 -0.1307258263  0.6610090604  0.6794724591
##  [276]  0.0517731788  0.2586223177 -0.1695601174  0.6755588692  1.0602152956
##  [281]  1.4573908903  1.3004352344  1.4350841115  1.1892107269  0.8443093897
##  [286]  0.7041147724  1.4844181486  0.7102661554  0.0317671807  1.3383913096
##  [291]  0.8957872491  0.2997887716  0.6524378470  0.2928654052  1.4486838754
##  [296]  0.5519401423  0.6660145924  0.7250314583  0.4914849426 -0.1392018744
##  [301]  0.7837873053 -0.3579419082  0.5792167650  0.8483638480  0.8093431923
##  [306]  0.1476723588  1.3081601792  0.4324853293  0.2288603377  0.2156584277
##  [311]  0.1084500332  0.4401472929  1.1812674598  1.3249507185  0.8142746220
##  [316]  1.3496102156  0.9194111488  0.9619778426  0.6227289115  1.1676388690
##  [321] -0.3292255585  0.5184118103  0.1357955712  0.6443156626 -0.1472650115
##  [326] -0.6304956495  0.8251608472  0.5572442643  1.2477808572 -0.7415788117
##  [331]  1.0589549739 -0.2071720043  1.2318702703  0.9121703239  0.7512383596
##  [336]  0.7131617352  0.6735790554  0.7903749573 -0.1626473728  1.1064097399
##  [341]  0.5239965373  0.0155790806  0.7393858264  1.7003082934  0.0057476284
##  [346]  1.7521928811  1.0585064862  0.3595053783 -0.2570826339 -1.4417833989
##  [351] -0.4066338840  0.4319569556  1.2860566688  0.3263023018  1.1739465630
##  [356] -0.3998797650  0.2673613062 -0.3425057093  0.4757018668  1.1041442130
##  [361]  0.1355792769  0.7148299918  0.9836997192  0.7911063337  0.2518973850
##  [366]  0.8453129460  0.9632735547  0.6518679453  0.4209673945  0.1049273602
##  [371]  0.4740599083  0.1483640888  0.7804593027  0.9708714527  0.6150713034
##  [376]  1.3613294645 -0.2239315366  0.7547415929 -0.0819274552  0.7621217033
##  [381]  0.7877032042  0.6316342263  0.8779298355 -0.9429011098  0.6693185837
##  [386]  0.4689626884  0.1710782597  0.2823003827  0.4011127591  0.8441608815
##  [391]  1.1741556643  0.8313708481  1.0816506663  0.2942168006  0.3227530629
##  [396]  0.2486457732  0.3569092809  0.2018727588  0.9609292774  0.5277770490
##  [401]  1.0140416409  1.5376823846  0.9109719610  2.2027563763  0.7599636624
##  [406]  0.3592844470  0.7041225811 -0.0964975859  0.7159909742  0.9491672182
##  [411]  1.6286791385  0.8953796658 -1.0272389408  0.7255274261  0.3629406406
##  [416]  0.9220967272  0.4750851481  1.1916540870  0.2650569745  1.2409762012
##  [421]  0.3885234118  1.9275267974  0.8955658542  0.0488785969  0.9867072060
##  [426]  0.4089511112 -0.3528045250  0.8482017181  1.1068889792  0.9195101572
##  [431]  1.0097751327  0.4278637036  0.7978692645  1.6836228662  0.6703224650
##  [436]  0.4244067233  1.0677929997 -0.0316511651  0.4082655229  0.7408542981
##  [441]  1.4737834743  0.1773605406  0.9339334297  0.5383816673  0.8000709972
##  [446]  0.8957806470  0.0456201899  0.8316287194  0.9602994309  0.6346368658
##  [451]  0.8887944566 -0.9090950691  0.7414484574  0.6743121916  1.0001608459
##  [456]  1.1788226990  0.8350948172  0.6938535222  1.5368486078  1.0342732889
##  [461]  1.4495301493  0.1595219907  1.0882326047  0.7522238013  1.0384253987
##  [466]  0.4047692886  0.9465078920  0.8334460729 -0.0276066985  0.1343324102
##  [471]  1.3271790022  0.2465572998 -0.0942849933  1.2816022931  0.4471997026
##  [476]  1.0897541253  0.0174940361  0.3482007073  0.4309488844  1.0017036711
##  [481]  1.0814879013  0.4337437509  0.6982005051  1.0641891646 -0.2443199262
##  [486]  0.3933318315 -0.0856053015  1.9658056833 -0.1473138589  0.5478202245
##  [491]  0.9040522565  0.8544783868  0.5073264663 -0.1782357458  1.1634108905
##  [496]  0.2350796722  1.7667915647  1.0112015095  0.6700363688  1.9714063527
##  [501]  0.1339689218  1.0823673239  0.4531933205  1.4047331555  0.2999031855
##  [506]  0.2490255349  0.5846633034  0.4102401348  1.1014745303  1.1137652337
##  [511]  0.4115772507  0.0085611849  0.9730670393 -0.2310052935  1.0207821363
##  [516]  0.2173972705  0.7636914345  0.7387620743  0.5790797457  0.8054890515
##  [521]  0.4663100106  0.6519083513 -0.1840226861  0.9642035593  0.4854475980
##  [526]  1.1533856690  1.4640936292  0.8172493543  0.4836883020  0.6544906858
##  [531]  0.4282534228 -0.2225329531  0.3885264304  0.2492712310  1.7728772278
##  [536]  0.4498370138  0.4654198165  1.3649602454  0.7903128573  0.5141739734
##  [541]  1.0055676243  0.5747299876  1.3048783971 -0.2516404241  0.8948802225
##  [546]  0.7837873053  0.2814859224  0.6523268458 -0.0053948545 -0.0060545318
##  [551]  0.7308719000  0.3643912559  0.1292070791  1.1649253783  1.0949967767
##  [556]  0.6571443191  0.2065629074  0.2849036935  0.8921715744  1.3175573553
##  [561]  0.5961581847  0.2162932768  0.4800241805 -0.7746697022  1.2691425844
##  [566] -0.0053930494  0.7180370863  1.8463821505  0.9966317072 -0.3190830675
##  [571]  0.2803961561  0.7011918301 -1.0266998113  0.5356309103  0.6253917627
##  [576]  0.1500569096  1.2335806209  0.3926465847  0.3453007605  1.0952414453
##  [581]  0.8007353534 -1.0422237123  1.6536358231  1.3292058998  0.6272423234
##  [586]  1.1713199262  0.4314140576  1.4938145996  0.2217661137  0.5655832409
##  [591]  0.6988623321  0.9802286243  0.3962696282  0.2604464564  1.0636108953
##  [596]  0.5248746523  0.2490852316  0.6795641909 -0.1174095412  0.5417316110
##  [601]  0.6083152668  1.1705199022  0.3430040084  0.3065774782  1.1230338097
##  [606]  0.7605335730  0.7359336051  0.3982978650  1.7996856692  1.9394589174
##  [611]  0.4394391075  1.2166135269  0.5572452189  1.1742193469  1.6117292511
##  [616]  1.0057278076 -0.1292014447 -0.3948308018  1.2607238963  0.4205510498
##  [621]  0.4080199976  0.3393909455  1.3273853896  0.7076260924  0.5875927820
##  [626]  0.7642706624  0.3857261895  1.5993725070  0.4008163070  0.6935402522
##  [631]  0.6935402522 -0.5058132253  0.5453096866  1.1975103929  0.0789654260
##  [636] -0.5021658292  1.8227559168  1.8161210112  1.0878539216  0.5314173535
##  [641]  0.1619499010  1.9594635996  0.1055852224 -0.3998797650  0.3254191226
##  [646]  0.8810882217 -0.5490778886 -0.2352257736  1.7185321827  0.0655065063
##  [651]  0.2284812603 -0.0023694372 -0.4206710428  0.7321357351  0.6667321805
##  [656]  1.3084008467 -0.1044895409 -0.2763642422  0.0327352782  0.7038370536
##  [661]  0.7973248782  1.0444041331  0.6979588611  0.8760520262  1.2571234277
##  [666]  0.6937707627 -0.3689633653  1.0914269635  0.9546324227 -0.2844482190
##  [671]  1.0702537975  0.6581473431  0.4268570489  1.0545908891  0.7608703544
##  [676]  1.9812552509 -0.0227048909  0.7216741043  0.7074036366  0.6348144298
##  [681]  0.9530815829  0.8557015805  0.0345246663 -0.2372094792  1.0620185650
##  [686] -0.3356348702  0.8378422750  1.5877417612  1.2304686066  0.4666219564
##  [691]  0.2962566225  0.1077494925  0.2047218832  1.0193996738  0.8312292916
##  [696]  0.6811463525  0.3175603889  1.3553059306  0.3416331385  1.1684528188
##  [701]  1.0247622209  0.7682671492  1.1613387967 -0.0254380687  1.1786756074
##  [706]  0.3401373976  0.4819291093  1.6316209493  0.3015649129  1.0460595592
##  [711]  0.9503679941  0.9758791096  0.2515232558  0.5667080972  0.2779123171
##  [716]  0.2168812350  0.4462404411  0.8167410481 -0.1983666994  0.7357578771
##  [721]  1.2010770494  0.5338885589  0.5610137429  0.8323843054  0.5073734906
##  [726]  0.7037572242  1.4001398567  1.1207717561  1.7583637495  0.4378765098
##  [731]  0.6945894650  0.6250023251 -0.2414756386  0.4972945398  0.6852886104
##  [736]  1.0931495245  1.3256796663  0.2283332750  0.8715032708  0.9027881485
##  [741]  0.2998720808  0.6421486895  0.6177186213  0.2575032354  0.6124788755
##  [746]  0.1752146306 -0.8380596742  1.7615237007  0.9431409230 -0.3873315851
##  [751]  0.0440924904  0.9019518051  1.2289867343 -0.7899582813  0.7183375221
##  [756]  0.6249769237 -0.1153917506  0.2971002753  0.7983727423 -0.1191121146
##  [761]  1.1215845738  1.3636140257  0.8312610396  0.3254191226  0.5174464609
##  [766] -0.1830452911  0.5161929257  1.2415703418  0.6431324916  0.5986367443
##  [771]  1.4494686611  0.3889937691 -0.4190271646  0.1777376983  0.4260474522
##  [776]  0.3889937691  0.5874680770  0.2515176720  0.7896492266  0.5469414664
##  [781]  0.7874321768  0.9903895720  1.5876557108  0.8066601653  0.5654645163
##  [786]  1.2387694411  0.4676595310 -0.1957482261 -0.6765297586  0.5183286385
##  [791]  0.4955207207  0.7686496712  0.8144852596  1.6787539540  1.8904478064
##  [796]  0.6056654122  0.4963712447  0.6705871891  0.7827297129  0.7250707414
##  [801]  0.1492569827 -0.0511454183  0.4274758454  0.6709481795 -0.1053925746
##  [806]  0.8620839065  0.1288965548  0.8285679930  0.2277433480  1.0979513558
##  [811]  0.9779525245  0.7515977324  1.5093388945  1.1816496494  0.8320665291
##  [816]  0.9436760836  0.7480139649 -0.1843974135  1.2616650436  0.4243476403
##  [821] -0.6823491866  0.5707348302 -0.3340089493  1.5737760750  1.3212661347
##  [826]  0.4380568229  0.8560080379  0.3933346003  0.0630534808  0.9660855835
##  [831]  0.4237196737  0.1284165655  0.7908136196  0.2437054174 -0.0817312435
##  [836]  0.7742317439  0.3926807920  1.3227922479  1.2974407005  0.8956483462
##  [841]  0.0291380852  0.6107946680  1.2996313113 -0.5525710949  0.7607398890
##  [846]  0.8010682609  1.0862759459  0.7738895440  0.2466708938  0.4885046930
##  [851]  0.5822133451  1.2498076854  0.5845369019  1.8181014729  1.0552988615
##  [856]  0.9266031710  0.6332729089  0.7506701024  1.3503261007  0.5528291504
##  [861]  0.2342593600  0.8527604567  0.3753175164  0.6937707627  0.0029351120
##  [866]  0.5935499764  0.6383253016  0.1248730364  0.0118687497  0.7331982291
##  [871]  0.6595680129  1.4065210355  0.5793832157  0.3548049466  0.6820235115
##  [876]  0.7010592051  0.4102122794  0.4882849538  0.9389425934  0.5912826486
##  [881]  0.2111179612  0.3779931667  0.9276207297  1.2888748181  0.8650031222
##  [886]  1.2323724553  0.9180961655  0.6204475621  0.4483736535 -0.0613199799
##  [891]  1.0749864471  0.0008689132  0.7896057711  0.0148684257  0.4127811840
##  [896]  0.6927181005 -0.2293998217  0.8283532014  0.4462101367 -0.3186918479
##  [901]  0.7395472569  1.3736965136  1.1190810121  0.6769071451  0.6169779572
##  [906] -0.2720962188 -0.2178672158  0.8898460920  0.0976881599  0.6038279146
##  [911] -0.0288576816  1.7016731418 -0.0728205143  0.6244484715 -0.1508370868
##  [916] -0.1001119428  0.2576120004  0.8516277455  1.6337278839  1.0118668919
##  [921]  0.5346550679  0.1595219907 -0.2915322401  0.1590631892  0.9203650563
##  [926]  0.7328276366  0.5190023274  0.9986387833  0.4063075312 -0.0663308468
##  [931]  0.8443093897  0.1383443718  1.1516695716  0.5826242379  0.5770669659
##  [936]  0.2698464058  0.2315566793  0.3114799011  0.7949069763  0.6400389994
##  [941]  0.7070087983 -0.3464119311  0.5808117839  0.6506501992  1.0737925112
##  [946]  0.9685381265  0.4500900435  1.4810169373  0.5846045305  1.2726635221
##  [951]  0.4743599068  0.1624256888  0.3821131969  1.2836892515  0.4593819523
##  [956] -0.1950002968  1.1661632851 -0.1712788017  0.8005147680  0.2459367109
##  [961]  0.4324853293  0.6522375713 -0.5180127398  0.3772104297  0.7751443882
##  [966]  0.1270236732  0.8934860004  0.2560771141 -0.1093063498  1.1707761667
##  [971]  0.8161577965  0.9922448416  1.4926488171  0.9126892513  0.0278833254
##  [976]  0.9423533112  0.1674447007  0.0523183464  0.1634275772  0.2156661047
##  [981]  1.8101508629  1.8314182406  0.3284560077  1.0930576434  0.7428977034
##  [986] -0.0068415170  0.4293033492  0.3115885215  1.1046771688 -0.0679370983
##  [991]  0.7587479489  2.3154036758  0.5276218441  0.1157418335  0.5972697639
##  [996]  1.1697349623  0.1588679758  0.7589819947  0.5302488819  1.2671612623
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
##   0.56833934   0.33121734 
##  (0.10474012) (0.07406022)
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
## [1]  0.6340012  0.2009763  0.2523314  0.3767114  0.2711430 -0.1910739
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
## [1] 0.0147
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9326219
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
## t1*      4.5 0.02902903   0.9405037
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 3 4 5 7 9 
## 1 1 1 2 2 2 1
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
## [1] -0.0336
```

```r
se.boot
```

```
## [1] 0.8985312
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

