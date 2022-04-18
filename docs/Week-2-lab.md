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
## 0 2 3 4 5 8 
## 1 2 1 1 2 3
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
## [1] 0.0217
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
## [1] 2.756492
```

```r
UL.boot
```

```
## [1] 6.286908
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##   2.5%  97.5% 
## 2.9000 6.2025
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
##    [1] 5.3 3.6 4.1 5.1 4.8 5.7 3.4 3.3 6.4 3.5 6.1 4.2 3.6 3.1 4.5 4.6 4.9 4.8
##   [19] 3.9 3.9 5.0 6.4 4.2 5.8 3.2 5.5 4.6 5.9 4.1 4.6 5.9 3.3 5.4 6.3 3.9 6.7
##   [37] 5.1 4.2 2.4 3.2 3.5 4.0 4.9 4.8 2.9 4.4 5.8 4.4 3.3 5.4 5.4 6.5 4.6 3.9
##   [55] 4.0 5.0 4.5 4.0 4.6 4.7 5.4 5.6 4.8 4.7 2.7 4.8 3.6 5.8 4.8 3.7 4.3 4.2
##   [73] 3.8 4.5 3.2 6.1 5.1 4.0 5.7 3.3 4.6 4.4 3.7 3.5 5.3 4.5 5.4 4.4 2.7 4.3
##   [91] 5.0 5.5 4.3 3.6 5.4 5.0 4.7 3.4 5.2 3.3 4.9 4.7 5.6 4.0 5.5 3.7 3.4 3.7
##  [109] 4.5 3.6 5.0 4.3 3.7 4.6 5.1 5.4 5.2 2.9 3.0 4.7 4.0 3.8 5.0 5.1 4.1 5.6
##  [127] 3.9 3.3 5.1 3.7 4.4 3.7 5.9 5.3 3.4 2.8 5.3 4.0 4.2 4.8 2.6 3.3 3.5 3.9
##  [145] 4.8 5.7 5.6 4.8 4.8 4.7 4.7 6.0 4.6 5.5 5.3 4.4 3.6 4.6 4.7 4.4 5.1 3.7
##  [163] 3.8 3.2 3.9 5.5 6.1 4.3 4.4 5.6 3.2 4.7 4.3 5.2 4.6 5.5 6.1 3.6 5.1 3.8
##  [181] 5.0 4.0 4.0 2.9 4.9 5.1 6.8 5.5 4.5 4.2 3.1 4.6 3.7 4.1 3.8 5.6 4.7 2.8
##  [199] 3.8 2.1 3.5 5.1 3.6 4.4 3.6 5.3 3.0 4.2 4.5 2.4 4.8 5.7 4.1 5.0 4.0 4.5
##  [217] 3.5 5.6 4.7 4.1 4.1 4.3 2.9 4.9 5.4 5.5 5.2 4.7 4.9 4.2 3.9 4.3 3.6 4.4
##  [235] 4.0 5.0 5.1 4.9 5.1 3.7 5.0 2.3 2.3 5.4 4.9 4.0 3.7 5.4 4.3 5.1 4.2 3.7
##  [253] 4.1 6.0 3.2 5.0 4.4 5.0 3.2 3.3 3.7 3.7 3.6 6.6 5.8 4.7 4.6 3.7 2.4 4.4
##  [271] 4.5 4.6 4.2 4.4 5.6 6.2 5.1 6.2 5.2 4.4 3.8 4.5 4.5 4.1 4.5 4.9 5.8 4.4
##  [289] 4.4 5.5 4.5 4.0 5.4 4.1 5.0 5.2 3.1 3.3 4.0 4.5 2.9 3.7 3.3 4.5 4.2 4.4
##  [307] 4.5 3.8 6.1 4.2 4.6 5.8 4.8 3.7 4.8 4.0 3.3 3.9 3.6 3.5 4.5 3.8 2.8 5.1
##  [325] 4.9 5.5 4.1 5.6 4.3 4.2 5.1 5.2 3.5 4.6 5.1 1.3 3.1 5.4 5.5 5.1 4.9 5.9
##  [343] 4.0 3.8 4.3 5.6 4.9 4.6 4.4 5.5 6.0 5.0 4.6 4.6 3.4 5.8 4.1 5.6 4.6 5.3
##  [361] 3.8 3.9 2.6 4.4 3.4 4.4 3.6 3.7 4.8 5.3 4.9 5.5 5.5 4.9 5.1 6.1 3.2 4.7
##  [379] 4.6 4.7 6.3 5.1 3.6 4.1 4.9 5.3 3.7 5.3 3.7 4.9 5.1 5.2 4.1 5.6 4.5 5.0
##  [397] 5.0 5.6 2.7 5.0 4.5 3.8 5.2 4.3 5.0 5.8 5.0 4.3 4.7 4.0 6.0 5.2 5.0 4.8
##  [415] 3.0 4.7 6.5 4.7 5.4 4.8 4.7 5.0 4.1 3.6 4.0 4.7 2.6 6.3 4.6 4.6 4.8 4.6
##  [433] 3.2 4.6 3.6 5.5 4.4 5.0 5.7 4.3 3.8 3.2 4.1 6.0 3.3 4.7 4.8 4.8 2.9 3.3
##  [451] 4.5 4.1 3.1 4.6 3.9 3.4 5.3 3.6 4.3 3.6 6.2 4.9 5.1 3.7 5.1 3.6 5.6 5.2
##  [469] 4.8 5.7 4.8 5.6 5.5 4.1 4.7 5.1 4.4 4.4 5.3 4.2 3.4 4.9 5.1 3.6 4.4 5.1
##  [487] 3.5 5.3 4.0 4.5 4.5 4.0 4.8 5.2 5.5 4.0 3.0 4.5 5.4 2.8 4.6 5.5 6.0 4.4
##  [505] 4.3 5.2 4.6 4.5 4.7 5.0 3.6 3.8 6.3 4.2 4.3 4.6 4.1 3.5 4.8 4.1 4.3 5.7
##  [523] 4.1 5.6 5.4 3.7 4.4 4.8 3.9 6.5 5.3 3.8 4.6 5.4 4.0 5.2 4.3 3.7 5.0 5.0
##  [541] 3.9 6.0 4.7 3.5 3.7 6.6 3.7 3.7 4.6 5.0 4.8 3.6 5.6 6.1 4.3 4.6 4.8 6.0
##  [559] 5.5 2.4 4.3 5.3 3.8 2.5 5.3 3.7 3.3 5.3 3.2 6.0 5.4 6.3 3.9 5.2 5.4 4.0
##  [577] 4.4 4.1 4.4 2.5 4.8 4.0 4.6 5.2 3.4 5.2 6.0 4.7 5.0 3.7 3.3 6.0 4.4 4.7
##  [595] 5.0 4.4 3.8 4.4 5.2 5.8 3.7 5.5 2.2 4.7 3.4 5.2 3.5 4.3 4.7 3.4 4.2 5.8
##  [613] 4.0 4.2 5.5 4.6 3.4 4.7 3.8 4.6 4.1 4.7 3.7 6.6 3.5 4.8 5.3 5.3 3.6 4.9
##  [631] 2.9 3.4 2.9 3.9 5.1 5.5 3.6 5.8 4.3 4.9 2.8 5.4 3.1 3.6 5.1 4.5 3.9 3.7
##  [649] 5.3 4.9 4.6 5.2 4.6 4.8 5.1 4.0 5.6 4.9 5.3 3.7 5.2 3.3 3.3 6.1 5.2 6.0
##  [667] 6.6 5.5 3.7 5.0 4.0 2.2 5.4 3.9 4.9 5.2 4.7 3.9 4.7 5.1 3.4 3.6 4.3 4.7
##  [685] 5.5 3.9 6.0 4.2 3.1 3.6 2.5 3.2 5.7 4.9 5.1 4.1 4.9 4.3 3.4 4.0 5.7 4.7
##  [703] 5.0 4.0 4.6 4.7 2.8 5.6 4.3 5.2 2.7 4.7 4.5 4.3 5.5 4.9 5.6 3.7 5.3 3.7
##  [721] 6.1 4.5 3.7 5.1 4.6 4.6 4.9 4.0 5.7 5.9 3.7 7.7 5.4 6.4 3.5 4.0 2.3 3.8
##  [739] 4.4 3.2 3.6 4.6 4.4 4.0 5.2 5.9 4.3 3.3 6.1 3.0 4.8 3.6 5.0 3.3 5.3 4.5
##  [757] 4.7 1.8 4.6 4.0 5.5 5.3 6.1 4.6 2.8 4.4 4.7 4.3 3.0 4.4 4.3 3.5 4.6 4.0
##  [775] 5.3 4.3 5.0 4.4 1.9 5.0 4.9 5.0 2.8 4.9 5.6 4.6 5.3 4.5 4.2 5.4 4.1 4.3
##  [793] 4.8 2.6 3.0 4.9 3.8 5.3 4.6 2.8 5.7 4.7 5.2 5.3 4.4 5.9 3.8 5.3 6.6 3.9
##  [811] 3.0 3.1 4.5 4.5 4.4 4.1 4.2 4.0 5.3 5.6 3.7 6.5 5.7 4.3 3.1 4.1 3.3 4.7
##  [829] 4.1 3.7 5.3 3.3 3.8 4.2 5.8 3.1 4.4 5.0 4.6 4.8 3.5 4.3 5.7 3.6 5.5 4.5
##  [847] 4.7 5.0 5.6 5.9 3.5 3.6 3.8 4.6 2.9 2.9 4.5 4.3 4.8 3.4 3.4 4.2 2.7 3.3
##  [865] 5.0 4.9 5.7 5.3 4.4 2.5 5.0 3.8 3.6 4.7 3.7 5.1 4.9 6.0 5.1 3.7 4.7 5.1
##  [883] 4.2 5.0 3.4 2.7 2.8 5.3 4.6 4.1 3.3 5.7 5.2 6.4 4.2 5.1 4.3 4.3 4.9 3.7
##  [901] 3.4 5.0 4.8 3.8 5.6 4.6 3.3 4.0 3.8 4.5 5.1 4.0 4.5 4.5 3.3 3.5 3.4 3.3
##  [919] 3.8 6.0 3.7 4.1 4.7 3.8 4.9 3.9 3.4 4.4 4.2 4.8 4.6 6.2 4.0 3.4 5.3 4.6
##  [937] 3.4 3.6 3.6 3.7 4.3 5.7 3.3 4.5 4.5 4.8 5.2 4.5 4.3 5.4 3.8 4.7 4.5 5.6
##  [955] 3.1 5.6 5.3 5.1 5.2 3.5 4.3 5.3 4.5 4.2 5.3 4.3 3.5 3.3 4.1 3.6 5.3 5.6
##  [973] 5.0 5.2 5.5 5.1 5.4 4.0 6.0 5.0 3.3 5.1 3.8 2.6 4.7 3.5 5.5 3.5 4.7 3.9
##  [991] 4.8 3.3 4.8 4.4 6.2 4.9 4.3 5.4 5.5 2.8
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
##    [1] 4.7 2.4 4.2 5.4 4.6 5.0 4.2 4.8 3.1 4.4 5.7 4.9 4.2 4.2 4.2 4.6 4.3 3.1
##   [19] 4.6 4.8 3.0 6.3 5.4 5.2 5.3 6.2 5.4 6.2 5.4 5.0 4.1 3.4 2.6 4.0 4.3 5.1
##   [37] 4.5 5.6 5.9 4.0 3.2 2.7 3.5 5.1 4.0 3.8 4.3 4.4 4.1 4.9 3.9 4.8 5.0 6.0
##   [55] 5.2 5.5 5.0 3.4 5.2 4.5 4.3 6.5 4.7 5.3 4.8 3.2 5.5 4.3 4.5 5.5 5.9 4.2
##   [73] 3.5 3.6 5.5 4.4 3.3 5.3 3.2 5.7 5.0 4.0 6.0 5.9 2.3 3.8 4.0 5.0 4.5 4.8
##   [91] 5.0 5.9 4.3 5.2 4.7 5.7 4.8 3.9 4.7 4.0 5.6 5.9 3.8 6.0 4.8 3.0 4.8 4.7
##  [109] 4.0 3.3 5.6 6.7 4.6 3.3 2.6 4.7 5.2 4.9 3.7 4.5 4.4 5.0 5.3 3.9 4.3 4.0
##  [127] 5.6 4.2 5.9 4.4 4.5 5.4 4.7 3.4 5.5 3.6 3.5 5.0 4.0 3.8 3.3 4.1 5.0 3.6
##  [145] 4.0 4.6 3.5 4.2 3.9 4.5 3.5 3.2 3.8 4.8 5.0 4.6 4.0 5.3 4.9 3.5 5.2 4.6
##  [163] 3.7 4.7 5.8 5.3 3.5 4.4 3.5 5.6 5.2 4.8 3.2 3.8 6.3 4.9 4.8 3.2 3.5 4.1
##  [181] 4.3 4.1 3.8 3.7 5.1 5.7 2.8 4.9 4.5 3.5 5.5 3.8 4.6 4.4 3.9 3.7 5.1 4.0
##  [199] 6.3 5.0 4.4 4.9 4.1 3.7 4.3 6.6 5.4 5.6 4.1 5.5 4.1 4.4 4.9 3.3 4.1 4.1
##  [217] 4.4 5.1 5.2 2.7 4.2 3.9 3.6 4.6 4.4 4.3 4.2 5.1 5.9 5.0 5.9 4.2 3.8 5.0
##  [235] 5.7 4.3 4.7 5.2 6.1 6.2 4.2 3.4 4.6 5.0 2.3 5.1 5.4 5.3 5.2 4.8 4.2 5.6
##  [253] 4.4 5.2 5.4 4.2 6.5 6.1 3.1 4.0 3.7 2.7 3.5 6.2 4.1 4.1 3.0 6.2 4.6 4.2
##  [271] 5.2 5.6 4.3 5.1 4.6 4.3 6.3 5.5 6.1 4.9 5.6 5.0 4.3 4.4 4.5 2.8 3.9 3.9
##  [289] 4.9 4.3 4.4 4.3 4.8 4.2 4.6 5.0 4.6 5.5 4.0 5.4 4.9 7.0 4.9 4.5 3.1 4.7
##  [307] 5.5 4.3 3.2 4.7 5.6 5.3 4.6 5.9 3.2 4.4 4.0 4.2 5.1 3.2 4.3 4.0 4.3 3.5
##  [325] 6.2 6.1 4.9 3.5 5.8 3.2 1.4 5.7 3.6 4.6 3.7 5.2 4.7 6.4 5.2 4.0 3.4 5.6
##  [343] 5.2 4.5 4.9 3.5 3.9 3.7 4.0 5.8 3.6 5.7 6.2 4.7 5.2 4.1 3.4 5.0 4.7 3.4
##  [361] 3.4 3.7 6.0 5.5 4.0 3.9 4.3 4.8 4.8 4.4 3.4 3.9 4.0 3.6 5.3 4.2 3.4 5.4
##  [379] 3.1 3.4 5.0 3.3 2.9 5.5 3.2 3.0 3.7 5.2 5.4 6.3 5.5 4.6 5.3 5.3 3.9 4.4
##  [397] 4.9 3.9 5.4 5.1 3.8 4.5 4.9 5.5 5.0 3.7 5.1 4.5 5.1 5.4 2.8 4.2 4.1 5.3
##  [415] 4.2 4.0 3.5 3.6 5.0 4.0 5.3 3.5 5.6 4.8 3.4 5.2 4.8 4.8 4.5 3.1 4.6 3.9
##  [433] 4.2 4.5 4.3 4.8 5.7 3.6 4.8 5.0 5.1 5.0 4.1 3.0 5.2 5.9 3.3 4.4 5.3 3.5
##  [451] 4.1 5.4 3.7 6.1 4.6 3.8 5.0 4.2 4.9 5.3 5.2 5.0 4.3 5.1 5.2 5.0 4.3 4.4
##  [469] 4.2 3.2 4.9 3.7 5.2 4.6 4.3 4.1 4.2 4.5 3.0 4.4 3.9 3.8 3.5 6.0 4.6 4.2
##  [487] 6.4 4.6 5.6 4.9 5.2 4.6 5.5 3.3 4.7 3.9 5.2 4.5 3.3 5.3 4.7 5.6 4.8 5.7
##  [505] 5.6 4.6 4.3 4.7 3.7 5.9 6.2 3.6 3.5 4.5 6.1 5.5 4.8 5.7 4.3 2.7 4.2 3.9
##  [523] 4.6 4.4 4.1 4.3 5.2 4.3 5.5 4.6 3.7 4.7 3.9 3.5 5.6 5.3 3.4 2.8 5.0 4.2
##  [541] 2.5 3.8 2.9 5.2 5.7 5.1 6.2 3.5 4.1 2.2 3.9 4.3 4.9 5.5 3.3 4.5 4.9 4.2
##  [559] 4.0 4.5 5.3 4.5 5.1 4.2 4.8 5.8 5.7 4.1 4.3 4.3 3.4 4.4 3.8 6.1 2.7 3.4
##  [577] 5.7 3.1 4.5 3.0 4.5 4.7 3.9 6.2 5.8 4.8 3.9 4.8 4.2 4.8 5.4 3.8 4.8 2.4
##  [595] 3.2 3.5 5.7 4.5 3.6 5.2 4.7 4.1 4.6 3.9 4.7 3.8 6.5 5.3 4.9 4.6 6.3 4.6
##  [613] 4.3 5.7 5.2 3.7 3.6 4.2 3.2 4.7 5.4 5.1 3.2 3.6 3.4 6.3 6.0 4.7 3.9 5.6
##  [631] 5.6 4.5 3.7 4.6 3.9 3.6 4.9 3.9 3.1 4.4 3.4 4.6 4.3 3.7 5.5 5.4 4.4 2.7
##  [649] 4.4 4.8 2.8 5.0 5.0 4.2 4.1 4.1 3.9 5.9 4.9 4.3 5.1 4.0 5.5 5.8 3.9 3.4
##  [667] 3.9 5.1 3.8 4.6 5.0 5.0 4.1 3.2 5.1 4.4 5.3 4.0 4.5 4.5 5.4 5.8 3.5 5.3
##  [685] 5.8 3.4 4.8 5.0 4.0 5.7 4.8 4.6 4.0 3.8 3.7 5.2 3.0 5.6 4.1 5.0 4.0 4.0
##  [703] 3.1 5.0 3.5 4.2 4.8 6.4 2.6 4.4 6.5 5.3 3.0 4.2 4.7 4.2 3.8 5.1 4.3 2.9
##  [721] 4.3 3.0 5.7 4.5 3.1 4.1 3.9 6.2 4.9 4.2 3.9 7.4 4.2 5.4 3.1 4.3 4.0 4.6
##  [739] 2.6 4.8 4.4 3.2 3.9 4.7 4.6 3.3 4.4 5.2 5.1 4.4 5.2 5.1 4.0 3.5 4.6 4.0
##  [757] 5.6 5.4 5.6 3.5 3.8 5.7 6.0 4.5 5.4 4.2 3.4 4.3 4.6 2.6 3.8 3.4 4.4 4.7
##  [775] 4.0 5.7 3.1 5.9 4.7 4.0 4.7 4.3 3.6 6.1 5.2 1.7 5.4 4.2 4.3 3.7 4.5 3.6
##  [793] 6.1 4.9 3.3 4.0 5.2 3.9 3.6 6.4 3.9 5.0 5.5 2.7 4.5 4.2 3.0 4.6 5.7 5.2
##  [811] 4.8 4.5 5.7 5.4 5.5 5.2 3.8 3.5 4.1 5.7 4.7 4.2 7.1 3.5 4.4 3.9 4.8 4.4
##  [829] 4.9 3.0 3.6 4.7 3.2 5.7 2.6 4.2 4.5 4.2 4.2 4.6 3.9 4.2 4.9 5.5 7.3 3.9
##  [847] 5.1 5.2 4.3 3.3 4.9 3.6 3.6 5.2 4.7 3.7 4.9 5.9 4.8 4.7 3.8 3.5 5.2 4.3
##  [865] 4.4 3.3 4.8 5.0 2.9 4.5 5.6 3.4 4.1 5.9 3.6 3.4 3.6 5.6 4.8 4.1 5.3 4.2
##  [883] 4.3 6.3 5.1 2.9 4.4 5.6 4.3 3.7 5.2 3.8 4.1 3.7 5.1 6.3 4.1 4.5 3.4 3.1
##  [901] 5.1 4.8 4.4 5.5 4.4 4.2 3.9 4.7 4.5 5.1 4.2 4.3 3.1 3.6 4.7 5.6 5.4 6.0
##  [919] 2.7 5.0 4.9 3.9 5.9 5.1 4.3 4.3 3.7 5.3 5.5 4.5 3.4 3.9 4.3 5.3 4.3 4.2
##  [937] 4.7 3.6 3.4 5.4 5.3 5.2 4.6 4.2 4.2 6.2 6.6 4.5 5.7 4.0 4.8 5.1 3.5 3.9
##  [955] 4.6 5.2 3.0 3.8 4.0 4.2 4.5 3.9 4.5 4.1 3.1 5.2 6.4 6.2 4.5 5.4 4.2 2.8
##  [973] 5.0 4.3 4.0 5.0 6.0 6.1 5.9 3.0 3.4 4.9 3.1 4.1 4.4 3.1 6.5 3.0 2.5 5.1
##  [991] 3.2 5.3 4.3 4.4 3.3 3.9 3.6 5.3 3.8 4.8
## 
## $func.thetastar
## [1] 0.0084
## 
## $jack.boot.val
##  [1]  0.49361111  0.40230548  0.29106145  0.16222222  0.06197605 -0.02147059
##  [7] -0.16366279 -0.26892655 -0.44695122 -0.44231884
## 
## $jack.boot.se
## [1] 0.9557223
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
##    [1] 3.3 4.1 3.4 4.1 5.2 3.8 3.7 5.3 5.4 3.8 4.0 6.0 6.1 4.0 4.2 3.7 3.1 5.5
##   [19] 5.9 4.7 5.0 3.7 4.3 5.2 3.7 5.2 5.6 3.9 4.5 4.9 5.3 5.9 5.2 4.0 3.5 3.8
##   [37] 5.3 4.0 5.3 5.0 5.0 4.2 4.6 4.4 4.9 5.3 3.9 3.6 4.9 4.9 3.7 3.5 4.3 4.5
##   [55] 5.4 5.6 5.6 4.0 5.4 3.5 5.8 4.2 5.5 3.6 5.4 4.6 5.2 4.3 3.7 4.8 3.7 4.9
##   [73] 3.3 3.8 3.7 3.4 4.8 5.5 4.9 3.5 5.2 4.8 5.2 5.2 5.8 5.8 4.6 5.3 4.9 4.6
##   [91] 5.1 4.2 4.5 4.5 2.9 4.2 2.7 5.5 2.9 4.2 5.0 4.3 4.8 4.0 5.5 3.8 4.8 5.9
##  [109] 3.5 4.7 2.8 3.0 4.7 4.7 4.8 4.6 5.2 3.9 4.5 5.8 3.7 5.5 3.7 5.7 3.2 4.1
##  [127] 3.2 3.0 4.5 4.7 4.5 5.1 5.1 6.0 4.3 4.9 3.9 6.1 4.0 4.2 2.4 4.6 3.9 5.4
##  [145] 5.9 6.1 3.0 4.8 5.3 4.5 5.4 4.4 4.8 5.2 4.2 3.6 4.1 4.9 5.2 5.6 4.8 3.6
##  [163] 5.0 5.4 5.6 2.7 4.8 3.1 3.5 3.8 4.4 3.7 4.2 3.7 5.1 3.9 4.0 2.6 5.5 6.3
##  [181] 5.1 2.7 3.2 3.1 5.1 5.0 3.6 5.1 3.6 5.2 5.5 5.1 4.3 4.3 4.3 4.5 3.5 4.1
##  [199] 4.9 4.2 6.7 4.5 5.5 4.8 3.1 5.0 4.4 4.7 4.7 3.7 5.3 3.4 4.1 4.3 4.9 5.1
##  [217] 5.4 4.3 4.5 4.0 6.6 4.1 4.5 4.8 5.3 4.0 5.4 4.0 4.2 4.3 5.1 5.7 4.9 3.6
##  [235] 4.5 4.4 4.2 3.4 5.6 4.2 3.6 4.4 5.8 3.8 5.9 4.8 4.5 5.1 6.5 5.5 4.5 6.2
##  [253] 3.6 3.9 5.5 4.6 4.7 3.4 3.4 3.5 7.0 3.8 4.7 6.1 3.0 4.2 5.1 4.4 4.2 6.7
##  [271] 4.9 4.4 4.7 5.0 5.5 4.6 3.5 6.1 3.7 3.6 3.9 4.2 5.1 5.7 4.3 4.8 5.2 4.4
##  [289] 4.5 6.0 5.7 3.0 4.1 7.1 4.6 3.4 2.9 2.9 3.9 4.9 3.3 3.5 3.8 4.3 6.9 3.0
##  [307] 5.5 5.2 3.8 4.6 5.3 5.3 3.8 3.5 3.4 4.1 5.3 3.3 4.7 4.1 5.7 4.6 5.2 4.6
##  [325] 4.0 6.5 6.0 4.5 4.6 6.1 4.8 5.0 4.7 5.4 3.8 3.4 5.4 5.7 4.8 4.5 3.3 4.7
##  [343] 4.7 4.3 4.1 5.7 4.8 4.2 4.9 5.7 4.3 5.0 4.4 4.3 5.3 4.8 5.1 3.7 3.8 4.2
##  [361] 3.5 4.5 6.4 3.1 4.5 3.5 2.5 3.2 5.5 5.6 4.4 4.5 3.1 4.7 4.9 4.4 5.6 2.9
##  [379] 4.3 4.8 3.9 5.5 4.0 4.2 3.9 4.3 3.4 4.4 3.8 4.2 5.5 4.2 4.3 4.4 3.9 4.6
##  [397] 5.3 5.2 5.6 5.1 4.6 4.5 4.7 4.4 3.1 3.2 4.9 4.9 5.9 4.9 3.1 3.8 3.2 5.4
##  [415] 5.1 4.3 5.6 4.6 5.5 3.9 4.9 4.8 3.6 4.8 3.9 3.9 3.7 4.8 3.7 3.8 4.4 4.0
##  [433] 4.0 5.3 3.6 4.2 5.2 6.0 5.7 3.8 4.2 4.7 4.9 3.0 4.7 5.3 3.8 2.8 3.5 4.2
##  [451] 4.1 5.3 3.9 3.3 6.3 4.4 5.0 5.6 4.0 5.4 3.3 4.0 4.3 4.5 3.9 4.6 4.5 5.2
##  [469] 5.2 3.7 5.0 5.1 5.2 4.0 6.2 5.3 2.8 5.0 5.5 3.7 4.9 4.1 5.8 5.7 3.7 4.5
##  [487] 2.9 4.4 4.3 4.5 4.4 4.3 3.3 4.3 5.0 4.0 3.3 4.8 3.5 3.9 5.4 4.2 5.6 5.3
##  [505] 4.3 5.4 5.8 3.6 4.8 4.8 4.4 4.4 4.5 4.4 4.4 4.6 5.0 3.8 4.9 5.4 2.2 5.2
##  [523] 3.8 4.4 2.5 6.4 6.1 4.7 4.4 4.3 4.4 4.7 5.0 4.1 3.6 5.3 4.0 4.1 4.6 4.7
##  [541] 4.8 4.1 3.8 4.8 2.1 3.9 4.6 4.8 3.9 4.8 4.5 4.7 4.9 4.4 5.0 2.6 3.7 4.5
##  [559] 5.9 5.3 5.4 5.0 6.0 3.8 5.4 5.4 4.5 5.2 4.9 4.4 3.5 4.7 4.7 4.6 4.8 4.0
##  [577] 3.6 5.1 4.7 5.1 3.9 4.4 3.5 4.7 4.7 3.2 4.3 6.1 4.4 2.9 4.4 4.2 5.8 4.8
##  [595] 4.5 5.0 4.1 2.1 5.6 4.6 3.5 3.4 4.8 4.4 5.5 3.5 3.9 5.9 4.5 4.0 3.5 3.9
##  [613] 3.0 4.1 4.0 6.6 4.0 3.9 4.5 3.0 4.8 4.9 3.3 3.9 6.1 4.1 3.5 4.7 5.4 4.9
##  [631] 4.0 4.4 5.3 4.4 4.3 4.7 3.9 4.9 4.7 6.2 4.9 4.2 5.8 4.7 4.1 5.0 5.5 4.0
##  [649] 3.2 5.7 2.7 5.1 4.6 3.1 5.0 5.3 4.8 6.4 5.0 3.9 4.1 5.8 4.1 3.8 3.4 4.6
##  [667] 4.4 4.8 5.3 3.6 4.0 4.2 4.4 5.0 5.3 5.4 4.9 5.2 4.9 4.4 4.2 3.9 4.6 5.4
##  [685] 3.9 3.5 4.9 4.1 5.3 4.4 5.8 4.9 3.9 4.4 5.7 3.4 4.7 5.1 4.9 4.9 4.4 4.1
##  [703] 5.3 2.1 4.2 4.3 6.3 5.2 4.3 4.9 3.7 4.5 5.5 4.0 4.0 3.7 3.9 4.4 4.8 3.6
##  [721] 4.2 4.2 3.7 4.9 5.0 4.2 5.0 4.7 5.6 4.2 4.6 5.0 4.2 4.2 6.7 4.8 4.8 2.4
##  [739] 4.8 2.0 4.6 5.5 4.6 5.2 3.5 4.4 5.1 5.5 4.0 3.2 7.0 6.1 4.2 3.7 5.0 4.3
##  [757] 4.0 5.0 4.4 3.6 4.7 5.6 4.8 4.1 2.3 6.2 5.4 4.2 4.1 4.3 3.0 6.2 2.9 3.7
##  [775] 3.6 3.9 3.8 3.6 4.4 5.4 4.8 4.7 3.0 5.5 5.1 2.8 3.8 5.0 5.4 4.9 4.6 3.0
##  [793] 5.1 4.3 4.3 3.8 3.1 5.7 4.2 4.3 5.3 5.8 4.9 3.8 2.2 2.5 3.5 5.7 3.7 3.6
##  [811] 4.9 5.9 5.2 4.3 4.4 7.2 6.1 4.6 3.1 5.1 4.2 3.3 5.2 6.3 4.4 4.7 4.1 5.9
##  [829] 4.3 5.1 3.8 5.4 2.5 4.8 4.9 3.8 5.5 4.5 3.4 5.4 3.1 5.5 5.1 4.7 5.2 6.1
##  [847] 5.4 5.1 2.5 4.5 4.2 5.6 4.2 3.9 5.0 3.3 3.3 5.9 5.0 5.3 4.6 4.6 5.8 2.7
##  [865] 4.1 3.3 4.7 4.3 3.9 6.3 3.3 3.7 3.0 4.2 4.2 3.6 4.8 4.4 3.5 4.3 3.9 3.5
##  [883] 3.5 5.1 4.4 4.5 3.2 5.8 4.0 3.6 2.8 5.3 5.3 3.6 2.8 3.0 3.4 4.4 5.0 2.4
##  [901] 3.7 5.3 3.0 5.7 4.6 4.1 5.2 3.3 3.8 3.7 3.9 4.7 3.6 3.3 4.5 5.0 3.2 2.9
##  [919] 3.7 4.2 5.8 5.2 4.0 6.1 3.5 4.7 3.7 5.1 5.1 4.3 4.7 3.2 4.2 4.7 4.5 4.6
##  [937] 3.0 5.2 5.8 4.5 4.2 3.2 4.2 3.2 4.0 4.9 3.9 5.2 3.8 5.5 4.2 3.9 5.0 2.8
##  [955] 5.0 5.6 5.6 5.0 4.6 4.2 4.6 5.1 4.4 4.0 5.6 5.7 5.2 3.4 4.3 4.3 4.0 3.2
##  [973] 3.5 2.8 3.0 4.3 4.7 6.8 5.2 4.6 5.4 4.6 3.7 3.7 5.8 6.0 3.5 2.8 4.5 4.8
##  [991] 4.4 4.8 3.1 5.9 4.2 4.7 4.9 5.0 5.1 3.5
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.4 5.3 5.2 5.1 5.0 4.9 4.7 4.5 4.4
## 
## $jack.boot.se
## [1] 1.064894
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
## [1] 0.4842611
```

What is skew? Skew describes how assymetric a distribution is. A distribution with a positive skew is a distribution that is "slumped over" to the right, with a right tail that is longer than the left tail. Alternatively, a distribution with negative skew has a longer left tail. Here we are just using it for illustration, as a property of a distribution that you may want to estimate using your data.

Lets use 'fitdistr' to fit a gamma distribution to these data. This function is an extremely handy function that takes in your data, the name of the distribution you are fitting, and some starting values (for the estimation optimizer under the hood), and it will return the parameter values (and their standard errors). We will learn in a couple weeks how R is doing this, but for now we will just use it out of the box. (Because we generated the data, we happen to know that the data are gamma distributed. In general we wouldn't know that, and we will see in a second that our assumption about the shape of the data really does make a difference.)


```r
library(MASS)
fit<-fitdistr(original.data,dgamma,list(shape=1,rate=1))
```

```
## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
# fit<-fitdistr(original.data,"gamma")
# The second version would also work.
fit
```

```
##      shape       rate   
##   2.0573278   6.5458184 
##  (0.8559693) (3.0821813)
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
## [1]  0.3706102  0.4505149  0.5026684 -0.3090574  1.5447532  0.7951919
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
##    [1]  0.324359310  0.546575236 -0.031496920  1.405641737  0.748306931
##    [6]  0.595182238 -0.611778232  0.170243314 -0.159786467 -0.053369951
##   [11]  0.513817603 -0.128584470  0.202304273  0.215104724  1.516218681
##   [16] -0.172657133  1.066859453 -0.121102834  0.885873233  0.751979118
##   [21]  0.932507331  0.568677663  0.733395911  0.186482478  0.784237239
##   [26]  1.336885760  0.874847246  0.570394761  0.361657314  0.412558347
##   [31] -0.261581225  1.007198129  1.007190647  0.457191845  0.108231643
##   [36]  0.233446294  0.368987174 -0.123616333 -0.454563072  0.693407030
##   [41]  0.516313408  0.055660849  0.181560510  0.932692465  0.768872937
##   [46]  1.069278103 -0.279077054 -0.451921069  0.016341046  0.675229185
##   [51]  0.923145994 -0.860002616 -0.148638012  0.620719575  0.311958753
##   [56]  0.411867506 -0.346703068  0.320334981  1.894871911  0.270758996
##   [61]  1.189073024 -0.002488273  0.610754277 -0.112768623  0.629242592
##   [66]  0.543005849  1.196357589  0.502945936 -0.306615679  0.601111980
##   [71]  0.686227193  0.408747527 -0.339596432  0.541818309  0.227365224
##   [76] -0.415780212  1.293464839  0.441726185 -0.443874609  0.916654988
##   [81] -0.497046133 -0.078853930  0.204304325  0.873773732  0.103123183
##   [86]  0.237065847  1.134130243  0.167458105  0.512069542  0.300963428
##   [91]  1.661537988  0.848738706  0.333087540  0.106502202  0.277223955
##   [96] -0.137345769  0.211816885 -0.021668865  0.884394118  0.634893743
##  [101]  0.301341775  0.317743880  1.051593114 -0.026562268  0.262157058
##  [106] -0.491445593  0.438787370  0.054600678  0.669218439  0.395903268
##  [111]  0.012898387  0.834788915  0.455243218  0.209838914  0.651212519
##  [116]  0.366138490  0.313329446  0.010512620  1.133036564  0.689295599
##  [121] -0.558672168 -0.278477991  0.450916106  0.729723502  0.539510600
##  [126]  0.327784170 -0.708179361  0.934263800  0.346382482  0.332433240
##  [131]  1.376946472  0.454267997  0.585347935  0.560991053  0.574241155
##  [136] -0.491202575  1.069557236  0.714694041  0.670981825  1.352047741
##  [141] -0.458765619 -0.476613298  0.443937733  0.325513766 -0.631451639
##  [146] -0.124194453  0.213706623 -0.456367352  0.338210900 -0.175020791
##  [151] -0.152397391 -0.777788367 -0.210829740 -0.087602550  0.233548969
##  [156]  1.271430006 -0.045272270  0.211825838 -0.161697077  1.122213929
##  [161]  1.848166138  0.227934592  0.400993822  0.763196006  0.340224484
##  [166]  0.984387607  0.347436583  1.086908933  1.260733932  1.226122040
##  [171]  1.264120912  0.605617539  0.088283521  0.961685144 -0.033749341
##  [176]  0.540819268  0.494590243  0.570806992 -0.050361436  0.208199477
##  [181] -0.208529987  0.331180267  0.947652861  0.202689276  0.360639955
##  [186]  0.597617030  0.342755419  0.463382155 -0.116516903  0.633987025
##  [191] -0.050652196  0.296262128  0.782724424  1.263577655  0.314473720
##  [196] -0.574564551  1.222870796  1.689697241  1.046430070 -0.670318885
##  [201] -0.032303091  1.116784952  0.007864294  0.344872423  0.806878271
##  [206]  1.304473224  0.889830433 -0.186760680  0.206070389  0.385368817
##  [211]  1.285768948  0.961022647  0.506654181 -0.004917599  0.480637605
##  [216]  0.342075544  0.310454861  0.621794674 -0.140266916 -0.372864900
##  [221]  0.416801107  0.801321765  0.520697014  0.621034802  1.310818639
##  [226] -0.398579654  0.096963438 -0.170440924  1.109286602  1.268000457
##  [231]  0.397064931 -0.443724323  0.680848554  0.274233048  0.689626117
##  [236] -0.698993346  0.788705715 -0.079801292 -0.215163771  0.639911083
##  [241]  0.226864742  0.225858122  0.296783028  0.524758583  0.048672715
##  [246]  1.485550404  1.156050711 -0.132477490  1.450546775  0.179374757
##  [251] -0.487077414  0.841975909  1.076627719 -0.297158970  1.106876880
##  [256]  0.439820101 -0.044422411  0.332103336 -0.552339805  0.917264641
##  [261] -0.291943316  0.838054576 -0.401843293  0.050178536  0.432837806
##  [266]  0.798195530  0.784510400 -0.285863216  0.781598258  1.130481842
##  [271]  0.344516917  0.961186252  1.294963322  0.180601626  0.424982181
##  [276] -0.188823128  0.789045416  0.194599229  0.211220691  0.511128889
##  [281]  0.570559322  0.316191116  0.729835693  0.208004716  1.361273010
##  [286]  0.089437902  0.141701804  0.745378241  0.300421915  0.545643103
##  [291]  0.353035860 -0.186659856  1.824878304  0.667690447 -0.554923743
##  [296]  0.621375148  0.161997919 -0.338397264  0.731398559  1.305723054
##  [301] -0.017705099  0.026948591  0.563133989  0.802326032  0.805042055
##  [306]  0.659169965  0.780237987  2.299309234  0.874007885 -0.213465087
##  [311]  0.082530576  0.568710990  0.212262655  0.598668156  0.715597821
##  [316]  0.866768418 -0.051240067  0.461192993 -0.067565217  0.464550194
##  [321]  0.456033734 -0.046421577  1.026528531  0.374159954 -0.065974313
##  [326] -0.382859213 -0.155828764  0.348833280  0.445994437  1.274707602
##  [331]  0.564016340  0.314638551  0.497547451  0.547332653  0.099256705
##  [336]  0.787829718  0.473245006  0.410688764 -0.216914857  0.582534921
##  [341]  0.598637270  1.325186515  1.046829276  0.300207306  0.475865913
##  [346] -0.127928995  0.161740241  1.083648154  1.505869658 -0.053454966
##  [351]  0.347501717  0.751178238 -0.898597030  0.122493937  1.117399182
##  [356]  0.568710990  0.824468639  0.841975909  0.563955125  0.702843061
##  [361]  0.828814731  1.568759966  0.356305589  0.670478539  0.943023217
##  [366]  0.256474009  0.413537869  0.241560535  0.681173980  0.216477875
##  [371]  0.675116619  0.392105931  0.391093635  0.412116695  0.414954588
##  [376]  0.490614661  0.739980956  0.587055267 -0.179382483  0.347697533
##  [381]  0.967436742  0.543005849  0.025659019  0.489580091  1.167353127
##  [386]  0.784265131  0.731183764 -0.075407186  1.470513471  0.487288888
##  [391]  0.211895901  0.333029875  0.322528859  1.301347336  0.513129001
##  [396]  1.335556054  1.797186718  0.380023522  0.181333211  0.938271683
##  [401]  0.202047103  1.238454397  0.432505060 -0.321101296 -0.048564904
##  [406]  0.131676779  1.455111693 -0.245258015  0.564682790  0.255813651
##  [411]  0.228890281  0.610196768  0.217182671  0.528294331  0.126461337
##  [416]  0.542898096  0.716541636  1.707094534  2.166469115  0.543544952
##  [421]  0.564245321  0.885031706  0.748955268  0.007540267  0.958105339
##  [426]  0.043907260  0.008668465 -0.428218245  0.410944926  1.177857998
##  [431]  0.004933726  0.449580099  0.252497833  0.652181896  0.863313259
##  [436]  0.171940373  1.248437964  0.031820927  0.223710367 -0.038358048
##  [441]  0.794969382  0.790436058  0.271244235  0.015148891  0.153083589
##  [446]  0.994186496  0.537518225  0.750297730  0.096281819  1.006893746
##  [451]  0.508981913  0.488540004  0.082796090  0.783976981  0.652667492
##  [456]  0.805042055  0.486176738  0.212168598  0.524003298 -0.121386462
##  [461]  0.941159671  0.875434719  0.148118463  0.411946849  1.614837415
##  [466]  0.312184257  0.650023845  0.601445310  0.135042537  0.490614661
##  [471]  0.471956065  0.056279836  0.283706696  1.681286435  0.672597264
##  [476]  0.432506864  0.291352947  0.764017397  0.930050901  0.991001651
##  [481]  0.381365476  1.102207442  0.854252195  0.866687012  0.190399285
##  [486]  0.772232279  0.338235408  1.884656263  0.940857703  0.891500960
##  [491]  1.191623498  0.087786702  0.338063845  0.325773060  0.212260957
##  [496]  1.064956359 -0.046791093  0.136228753  0.279160132  0.401988480
##  [501]  0.516604321  0.003969828  0.849568947  0.850204940  0.957853334
##  [506] -0.102212076  0.541790808 -0.043395452  0.572425334 -0.127926979
##  [511]  0.565406793 -0.254153295 -0.331168109  0.373226692  1.173543413
##  [516]  0.463377740  0.941159671  0.453579749  0.524435704  0.214158268
##  [521]  1.431404012  0.780822027  0.654097851 -0.436767243  0.384758056
##  [526]  0.111936443 -0.486827943 -0.259105875  0.630948844 -0.520557927
##  [531]  0.138165023  0.133149175  1.105613587  0.635968668 -1.271977824
##  [536]  0.491686774  0.305295101  0.501139813  1.468413180  0.180968028
##  [541]  1.462913800  0.568367003  0.524511425  1.026880475  0.062545927
##  [546]  1.129655028  0.113471867 -0.045272270 -0.134270626  0.830412722
##  [551]  0.046484945 -0.044746094  0.631713816  1.135442125  0.626635338
##  [556]  1.276866477  0.575178441  0.987448301  0.513477603  0.512365734
##  [561]  0.472258171  0.307686178  0.663128856 -0.372938664 -0.306958588
##  [566]  1.875102585 -0.309198902  0.531893308  1.028572695  0.719533331
##  [571]  0.619708574 -0.153604485  0.344822440 -0.210829740  0.636434334
##  [576]  0.049637362  0.276855131  0.773578447  0.266185077  0.202853935
##  [581]  1.100117705  0.559032390 -0.308980701  0.459021996  0.297292777
##  [586]  0.384337915  1.097944327  0.281934711  0.560142188  1.224502863
##  [591]  0.271911565 -0.216812065 -0.136282794  0.642165403  0.311625307
##  [596] -0.390479027  0.509370857  1.350465049  0.344845903  0.055973656
##  [601]  0.555176344  0.465535862  0.679356406 -0.281974960  0.454951170
##  [606] -0.815526359  0.811600944  1.748007940  0.654100314  0.729112978
##  [611]  0.306091296  0.344872423 -0.321395082  0.408412627 -0.254679541
##  [616]  0.663277324  1.220220308  0.095784066  1.526632499  1.183595517
##  [621]  0.010044321  0.258563830  0.017449778  0.575303367  1.755674497
##  [626] -0.216207173  0.068348129 -0.017253549  0.433460564  0.854746261
##  [631]  1.138044370  0.354330263  0.565295773  0.420777758  0.737759207
##  [636] -0.161778860  0.275276178  0.204657920 -0.184430023  0.278724817
##  [641]  0.076949155 -0.070920894  0.877181977  1.240832450  0.890604132
##  [646] -0.044298331 -0.555966252  0.110497854  0.243417953 -0.253587675
##  [651]  0.442916795 -1.031498136  0.128607358  0.270020302  0.407591412
##  [656]  0.260497033 -0.456746614  0.745773045  1.529500545  0.613253921
##  [661] -0.435985662  0.158749780  1.257873427  0.755039228  0.514044042
##  [666] -0.043672666  0.220946922  0.414406151  0.464174499  0.575860501
##  [671]  1.005634485 -0.425980879  0.237588620  0.250058835  0.172208097
##  [676]  0.565034443 -0.048486284  1.190377307 -0.040192238  0.639414718
##  [681] -0.136313305  1.155792975  0.700637231  0.385763494 -0.131354186
##  [686]  0.643915518  0.413125337  0.065822217  0.020774319  0.085103753
##  [691]  0.343753867  0.129243358  0.768025038  0.141276082  0.661178639
##  [696]  0.646720813  1.155478504  0.654853692  0.694471061  0.558400314
##  [701]  0.647239690 -0.507663105  0.333235170 -0.150896524 -0.035333527
##  [706]  1.470880190  0.290155199  1.078167122  0.231741827  0.798885297
##  [711] -0.131056773  0.252673594 -0.367730538 -0.663049092  1.026451381
##  [716] -0.389830155  1.659687599  0.614159466  0.905885303  0.486944923
##  [721]  0.244096856  0.100969678  1.825284321  0.184268596  0.457000699
##  [726]  0.757698277  0.732597259 -0.033486585  0.947650009  0.509151192
##  [731]  0.032242252  0.473968213  0.035104686  0.813740949  0.385719564
##  [736]  0.201991365  0.673231507  0.727274744  0.442732997  1.533605221
##  [741]  0.486198695  0.324413033  0.173041698  0.272322071  0.720111172
##  [746]  0.762001509  0.417282180  0.497697508  0.357911929  0.955044095
##  [751]  1.316308496  0.698661101 -0.061601185  0.514297994 -0.518344155
##  [756]  0.290912059  0.267177283  0.930702824  0.220853807  1.091169671
##  [761]  0.317751460  0.148816936  0.565723491  0.854810648  0.703526352
##  [766]  0.040244858  0.596166942  0.655050748  0.646384089  0.438783357
##  [771]  1.298273616  0.163499093  0.580776233  0.025323497  0.001038863
##  [776]  0.414525852 -0.300564881 -0.250583288  0.557126714  0.740328666
##  [781]  0.696600693  1.735410810  0.262330100  0.097832548  0.081949606
##  [786] -0.186298672  1.755674497  0.621216515  0.309910592  0.177799538
##  [791]  1.103661396  0.114508882  0.032242252  0.214855060  0.043774236
##  [796]  0.404470541  0.390483057 -0.334190266  1.104315846  0.324197683
##  [801]  0.008143460  0.992858475  0.310423932  0.662201660  0.224527156
##  [806]  0.112994573  0.938127475  0.969808294  0.888329397  0.609666179
##  [811]  0.358028951 -0.258933077  0.054175275  0.682971327  0.565139248
##  [816]  0.836125171  0.253977060  0.325738039  0.867734500 -0.151820137
##  [821]  0.193527320  0.307067430  0.063865360  2.015308665 -0.065111923
##  [826] -0.112942080  0.639668309 -0.206664299 -0.699694733  0.783895582
##  [831]  0.156618702 -0.017476613  0.501536247 -0.209447893  0.249910916
##  [836]  0.129799826 -0.004144226  1.338627598  1.595349844  0.904864428
##  [841]  0.355149313  0.319073698  1.609389903  1.084565026  0.738334388
##  [846]  0.218652621  0.483594735  0.956242891  1.037195579  0.063865360
##  [851]  0.637121170  1.032672164  1.539227042  0.109003551 -0.230904608
##  [856]  0.626062576  0.367638963  0.991334361  0.045533759 -0.184761567
##  [861]  0.233054423 -0.193578775  0.748927398  0.400150974  0.558606589
##  [866]  1.003318950 -0.001502404  0.821997861  0.444692018  0.491370307
##  [871]  0.548849420  0.283160179  0.331140423  0.062743298  0.610362546
##  [876]  0.340601225  0.275979671  0.638684262  0.196397679  1.403458327
##  [881]  0.471624766 -0.216812065  0.449432268  0.559343289  0.112199100
##  [886]  0.472498141  0.081936328  0.441682606  0.542938203  0.233594545
##  [891]  1.037171742  1.097937381  1.049861174  0.160308471  0.666000826
##  [896]  0.427607401  0.124296974  1.142308995  0.241764877  0.189561269
##  [901]  0.009142963  0.900677904 -0.259673755 -0.162050052 -0.077107596
##  [906] -0.127669674 -0.308659832  0.408855855  0.109466955  0.349476163
##  [911]  0.977974791  1.126003763  0.382096692  0.282602371  0.340613997
##  [916]  0.399070053  0.736982122  0.480459827  0.918834262  1.013907031
##  [921]  0.639204938  0.097462414 -0.144963860  0.878753062  0.882801637
##  [926]  1.040457655  0.454129820 -0.248897174  1.161694872  1.331675013
##  [931]  0.751592300  0.101144042  0.430213166 -0.033590142  0.661336768
##  [936]  0.910039432  0.387095096  0.121653026  0.001794231  0.082882365
##  [941]  0.458722215  1.121780130  1.132253009  0.221678731  0.533395155
##  [946] -0.171340372 -0.098472121  1.580466074  0.235841733  0.361624305
##  [951]  0.505484976  0.371131390  0.457191845 -0.115624065  0.885043002
##  [956]  0.519858945  0.752436605  0.437283417  0.525635199  0.055142432
##  [961]  0.838998637 -0.250228874  0.768785334  0.819261686  0.516406971
##  [966] -0.303522908  0.792714531  0.389925669  0.040455847  0.188300800
##  [971]  0.192398220 -0.073750039  0.122840253  1.386439203  0.243220213
##  [976] -1.120122007  0.253830052 -0.413091254  0.030254851  0.252513856
##  [981]  0.019239201  0.493096524 -0.517380070  1.636876888  1.364646026
##  [986]  0.040963302  0.225209654  1.621228069 -0.004917599  0.273709753
##  [991]  0.439134987 -0.032535385  0.362506500  0.799776298  0.237956886
##  [996]  1.030240354  0.030924002  0.764112417  1.129411222  0.748188581
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

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced

## Warning in densfun(x, parm[1], parm[2], ...): NaNs produced
```

```r
fit2
```

```
##       mean          sd    
##   0.31429693   0.20315765 
##  (0.06424409) (0.04542389)
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
## [1]  0.9048424 -0.5926199 -0.5979020  1.0503936  0.9437740  1.1442979
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
## [1] -0.0031
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8995942
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
## t1*      4.5 0.03013013    0.937294
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 3 4 7 9 
## 3 1 1 1 3 1
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
## [1] -0.0136
```

```r
se.boot
```

```
## [1] 0.8991349
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

