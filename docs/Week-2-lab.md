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
## 1 2 3 4 6 7 8 9 
## 2 1 1 1 1 1 1 2
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
## [1] 0.0138
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
## [1] 2.731044
```

```r
UL.boot
```

```
## [1] 6.296556
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
##    [1] 4.8 6.9 4.7 3.0 3.2 5.3 4.5 3.4 4.2 5.8 4.1 5.4 3.6 3.0 5.3 2.8 4.5 4.6
##   [19] 4.0 3.3 4.5 5.8 4.9 4.2 3.3 4.3 5.3 4.0 4.7 5.3 5.0 5.8 5.1 3.6 5.9 6.0
##   [37] 5.8 3.5 4.8 4.0 3.5 5.0 5.9 5.1 4.6 6.8 4.3 5.0 4.7 5.0 3.9 5.7 5.4 5.0
##   [55] 3.5 5.2 3.7 3.6 5.5 4.7 3.3 4.5 2.8 5.8 2.9 4.3 5.2 4.8 4.8 5.4 4.4 5.6
##   [73] 4.1 3.8 3.9 4.2 4.0 6.0 2.9 5.7 3.4 3.9 4.4 4.2 4.0 6.2 4.6 4.3 2.8 4.2
##   [91] 5.2 4.7 4.3 5.2 6.3 4.0 6.5 5.4 3.8 4.2 5.4 4.7 5.7 4.2 4.3 4.9 4.6 4.5
##  [109] 7.0 4.0 4.2 5.3 5.5 4.6 5.1 5.6 3.1 4.6 4.3 4.1 3.3 5.3 6.0 5.0 4.1 5.1
##  [127] 6.7 4.8 5.1 4.3 4.3 2.9 3.4 4.6 4.8 5.3 5.6 3.1 5.1 5.6 4.5 3.9 2.9 3.8
##  [145] 3.9 3.3 4.9 4.4 5.4 4.9 4.6 4.0 4.3 4.6 4.6 4.5 4.0 5.6 4.2 5.9 4.7 4.7
##  [163] 5.3 3.6 4.2 3.9 2.6 2.7 5.2 4.1 5.0 5.9 6.0 5.2 4.5 4.6 3.5 5.9 2.8 3.5
##  [181] 5.4 4.1 3.9 5.9 3.7 3.6 4.2 4.0 3.1 4.2 3.7 3.2 3.8 3.7 3.6 2.3 4.4 4.5
##  [199] 3.8 4.4 4.3 4.0 4.8 4.8 6.2 4.1 5.5 4.3 3.9 4.1 5.9 5.1 5.6 4.7 5.5 4.7
##  [217] 4.1 4.7 4.9 4.8 5.8 3.9 3.2 4.5 5.1 5.1 5.4 5.3 5.7 3.2 4.6 4.1 4.6 3.7
##  [235] 2.4 5.3 5.6 5.9 4.3 3.2 4.5 2.8 4.5 2.9 3.2 2.4 5.8 4.5 5.4 3.1 2.7 3.7
##  [253] 5.8 3.7 3.8 4.3 4.8 4.0 4.3 3.9 4.5 3.6 5.0 5.1 5.4 4.0 5.9 2.8 6.5 4.4
##  [271] 4.8 4.3 4.3 5.1 3.9 5.2 4.1 4.3 5.4 4.2 3.9 4.8 4.5 5.4 4.0 4.1 5.2 4.7
##  [289] 5.0 4.2 6.0 3.8 4.2 4.4 5.5 4.1 3.3 5.0 3.5 2.3 4.7 5.4 5.6 2.9 4.8 4.7
##  [307] 4.2 5.7 4.6 4.9 5.1 5.2 4.7 4.3 5.7 5.2 3.8 4.7 4.3 4.3 5.1 3.9 4.9 4.8
##  [325] 5.1 4.3 6.1 3.8 5.7 5.0 4.0 4.6 4.4 5.4 3.6 2.9 5.7 4.8 5.4 4.2 3.0 4.0
##  [343] 5.3 3.3 4.4 4.9 5.5 3.6 4.5 6.5 4.4 2.8 5.3 3.7 4.2 5.4 3.0 4.5 4.3 5.6
##  [361] 4.0 4.2 4.8 3.6 4.6 4.4 4.7 3.8 3.1 3.9 4.6 3.9 6.0 5.0 4.2 3.4 5.1 4.5
##  [379] 2.7 3.5 2.6 4.7 4.6 4.9 4.8 6.6 5.9 4.3 4.9 6.3 3.0 3.1 5.3 6.0 5.6 5.0
##  [397] 5.6 4.6 4.2 4.1 4.5 3.3 5.1 3.8 5.6 3.9 4.6 3.5 6.8 4.6 6.1 4.1 4.7 4.4
##  [415] 4.7 4.0 4.3 4.9 4.3 3.8 3.4 4.7 5.3 6.4 4.9 5.2 4.7 5.1 5.0 6.2 3.3 5.5
##  [433] 4.5 5.0 4.2 4.4 4.4 4.6 3.9 3.2 4.2 6.0 2.5 4.3 3.6 5.8 4.1 3.9 4.5 6.0
##  [451] 4.2 4.3 5.4 5.5 3.8 3.0 4.6 4.1 3.4 4.9 5.7 4.5 5.0 4.0 6.0 5.1 4.7 3.9
##  [469] 2.8 4.1 3.9 5.1 3.8 2.3 5.7 5.4 4.4 3.2 2.4 3.4 3.3 4.8 2.6 2.8 4.7 3.3
##  [487] 3.6 5.3 5.3 4.1 4.9 4.9 4.1 4.6 3.6 6.7 6.1 4.8 4.8 4.3 4.9 5.1 4.7 5.4
##  [505] 4.1 3.7 5.0 4.6 4.8 5.6 3.4 2.9 5.2 3.6 5.7 3.1 3.1 4.6 4.0 4.4 3.5 2.4
##  [523] 6.2 5.5 4.9 6.1 4.2 6.2 5.6 5.2 4.6 5.2 2.3 3.8 5.9 4.9 4.5 4.2 2.8 3.2
##  [541] 2.2 5.1 2.8 4.8 4.1 4.8 2.5 5.0 5.2 5.1 4.5 5.0 3.7 3.7 3.4 4.0 4.2 4.7
##  [559] 4.3 5.1 3.9 6.0 6.0 2.6 3.3 3.3 4.7 5.1 4.7 5.8 5.1 3.1 5.5 4.5 4.6 5.5
##  [577] 4.1 2.6 5.1 3.6 3.3 5.9 4.2 1.6 5.0 5.2 5.6 3.8 3.9 5.9 5.8 5.0 3.6 5.1
##  [595] 5.4 4.5 3.3 3.4 5.5 4.7 5.1 3.9 5.0 4.5 6.2 3.8 6.0 3.8 5.2 4.7 3.2 4.7
##  [613] 3.8 4.2 5.0 5.4 5.5 3.8 5.6 4.7 5.0 5.1 3.5 3.6 4.8 4.3 6.2 5.0 4.2 4.6
##  [631] 4.6 3.5 3.8 6.6 3.2 6.2 3.1 3.6 4.2 3.9 4.3 4.6 6.0 3.4 4.9 3.9 4.0 4.1
##  [649] 5.8 2.7 5.5 2.0 4.6 6.0 3.2 4.9 7.0 5.3 4.9 2.9 4.1 5.8 5.0 2.3 4.7 4.3
##  [667] 5.2 3.1 4.0 3.7 4.0 5.2 4.9 3.1 5.2 3.9 4.7 5.0 4.9 6.9 4.3 2.8 5.7 3.2
##  [685] 5.9 4.3 3.7 4.6 5.0 5.1 3.6 4.6 2.8 5.0 4.8 6.2 5.5 4.4 3.3 4.3 4.9 4.1
##  [703] 3.1 4.7 3.7 3.5 4.3 5.1 4.0 4.3 5.5 4.9 4.8 4.5 5.5 3.2 4.7 4.3 4.8 5.7
##  [721] 4.2 6.5 5.4 4.2 5.2 5.0 4.7 2.5 2.7 5.1 3.8 6.1 3.9 4.6 4.7 3.8 4.6 4.6
##  [739] 4.4 4.6 3.5 3.3 5.2 5.9 3.9 5.1 3.9 3.7 5.1 5.6 4.4 4.8 4.9 4.2 4.1 4.0
##  [757] 4.8 3.6 3.5 4.7 3.9 5.2 5.2 3.8 5.0 5.3 4.6 3.2 3.3 5.8 6.2 3.4 5.3 4.5
##  [775] 3.8 3.4 4.1 3.0 6.1 4.1 4.1 4.1 3.9 3.5 3.4 3.4 5.7 3.9 4.3 4.9 5.3 4.7
##  [793] 4.5 3.8 2.9 4.1 5.2 4.6 4.7 5.0 4.8 4.8 4.1 4.1 5.1 5.1 5.0 3.9 4.2 5.1
##  [811] 5.7 2.9 5.2 4.9 5.0 3.9 6.5 4.9 3.1 3.6 3.8 6.1 3.4 4.2 5.7 4.7 4.5 5.5
##  [829] 4.4 4.8 4.6 5.1 4.9 5.1 5.0 4.2 6.2 7.0 4.6 4.4 5.3 2.9 4.6 5.7 5.2 2.4
##  [847] 5.6 4.2 4.9 5.0 5.5 6.0 3.9 3.6 5.0 4.5 3.8 4.0 4.5 5.2 4.0 5.3 5.2 5.3
##  [865] 4.4 5.2 4.7 4.4 3.7 6.0 3.8 4.0 4.4 3.1 5.1 3.7 4.4 5.3 4.8 4.4 4.0 3.1
##  [883] 5.9 4.4 4.3 3.9 4.6 4.0 3.2 4.3 4.8 4.3 5.8 4.0 6.1 6.2 4.3 5.1 3.9 5.8
##  [901] 4.7 4.3 4.0 3.1 4.8 4.7 3.6 4.1 5.8 4.9 3.3 3.8 4.9 5.7 4.0 3.8 4.6 5.5
##  [919] 4.3 3.4 4.6 4.6 3.5 4.0 3.8 3.4 5.5 5.1 4.4 4.0 4.5 5.0 4.6 3.8 3.4 4.2
##  [937] 4.5 5.7 3.7 3.1 5.8 3.7 4.6 3.3 5.7 4.6 4.3 5.6 3.9 4.2 6.2 5.2 6.3 3.1
##  [955] 3.5 5.0 3.5 4.2 5.2 3.7 2.9 6.6 5.7 3.8 3.2 5.2 5.1 4.8 4.3 5.6 3.7 2.7
##  [973] 5.4 4.7 5.1 4.1 6.4 2.9 4.1 5.1 3.8 3.5 5.2 5.3 4.9 5.5 4.2 4.1 5.7 1.8
##  [991] 4.3 4.4 4.2 4.0 4.9 4.7 5.4 5.9 5.1 4.4
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
##    [1] 4.2 3.8 3.2 3.4 4.0 4.0 5.5 4.8 5.5 4.4 4.0 3.7 3.9 3.5 4.5 5.9 2.8 4.7
##   [19] 3.8 5.2 4.8 4.0 4.1 5.8 4.9 5.8 4.0 5.4 5.3 3.5 3.1 3.8 5.9 5.0 4.6 4.7
##   [37] 4.7 4.3 5.2 5.5 2.8 3.3 3.8 3.6 4.9 5.8 3.2 5.0 4.1 4.4 4.3 5.1 5.5 3.3
##   [55] 4.6 3.8 4.4 4.1 4.9 5.0 2.8 5.5 5.4 5.0 3.6 5.4 5.0 3.3 4.0 4.4 5.6 4.6
##   [73] 3.9 4.0 4.6 4.0 5.5 6.1 4.2 3.9 4.7 3.5 5.8 5.0 4.6 3.9 2.6 4.4 4.3 4.4
##   [91] 4.6 4.6 4.3 4.8 4.7 3.3 4.0 5.7 2.7 4.1 6.6 6.7 3.3 4.3 3.4 4.1 4.6 4.6
##  [109] 5.7 4.1 2.9 2.9 3.9 3.1 5.2 5.2 3.8 4.2 4.9 4.9 3.2 4.5 3.5 4.8 3.8 4.4
##  [127] 3.8 4.2 4.8 5.4 5.0 4.0 4.0 6.2 4.5 3.7 5.5 4.6 3.6 4.7 5.0 3.5 3.9 5.0
##  [145] 3.7 4.1 3.7 4.4 3.9 4.8 4.4 4.4 6.1 5.3 3.3 3.6 5.0 4.5 5.6 5.2 4.5 5.3
##  [163] 5.8 4.4 4.2 3.9 4.0 5.2 4.7 5.2 3.8 5.7 4.0 3.4 4.2 3.9 4.1 6.2 4.4 4.7
##  [181] 4.2 2.9 4.9 4.2 3.7 3.3 5.5 4.7 4.8 4.6 5.4 6.2 3.8 4.2 4.7 3.8 4.7 3.9
##  [199] 5.8 3.8 4.9 3.6 3.8 4.8 2.8 3.8 3.6 5.8 4.2 4.0 4.0 4.8 4.5 6.1 4.7 4.2
##  [217] 4.4 5.0 3.6 4.5 3.2 4.6 5.2 4.9 3.3 4.3 3.0 3.8 5.2 3.8 2.8 4.9 5.8 5.6
##  [235] 5.8 4.3 4.9 5.0 5.7 4.6 4.0 4.2 4.8 4.5 3.4 3.9 4.3 3.9 3.2 4.2 5.9 3.8
##  [253] 3.4 3.8 3.2 4.9 4.9 3.1 3.7 4.4 3.5 4.5 4.4 4.6 3.5 5.3 3.5 4.1 5.5 4.6
##  [271] 5.3 3.9 4.4 4.1 3.7 4.5 4.1 5.1 6.5 3.3 4.7 5.3 4.7 5.1 4.3 3.5 4.0 4.4
##  [289] 5.6 3.5 4.7 5.8 3.8 4.8 3.0 4.6 4.3 5.0 5.5 4.5 2.2 4.8 3.5 5.5 3.1 2.9
##  [307] 5.5 3.7 5.3 3.4 3.2 5.2 2.8 3.5 3.5 5.0 3.8 4.8 4.3 3.3 4.2 5.1 3.7 5.3
##  [325] 5.7 6.5 4.7 5.5 3.2 5.6 5.0 6.2 3.4 4.5 4.8 3.0 6.1 5.1 3.8 6.5 3.7 3.7
##  [343] 4.5 3.9 4.9 4.5 4.6 4.2 5.3 4.1 3.7 5.0 5.4 3.9 4.6 3.7 5.1 4.9 3.6 3.2
##  [361] 4.2 4.0 4.1 5.6 2.6 5.2 4.4 4.4 4.7 3.6 5.4 5.0 4.7 4.0 3.4 5.4 4.0 5.2
##  [379] 2.7 5.1 4.2 4.1 4.5 3.7 4.6 4.9 4.2 3.3 5.8 4.6 5.0 4.9 5.5 3.9 4.2 3.9
##  [397] 3.9 3.5 4.4 2.8 5.8 4.0 3.8 5.6 3.9 4.0 5.0 3.0 4.9 4.1 4.0 4.1 5.0 5.5
##  [415] 3.5 5.4 6.0 5.1 3.1 4.1 4.7 4.9 4.9 4.3 4.5 4.6 6.3 4.0 5.7 2.7 4.0 6.4
##  [433] 4.0 3.5 5.2 3.9 3.0 4.7 4.1 4.2 4.9 3.2 4.8 3.2 4.6 4.0 5.7 4.3 4.8 4.2
##  [451] 2.8 4.2 6.4 5.2 3.9 4.3 5.1 2.7 5.7 4.7 4.9 3.4 6.4 3.8 4.9 5.8 4.8 4.3
##  [469] 5.5 5.5 5.9 6.0 4.6 4.5 3.1 4.8 2.9 3.2 5.6 4.0 4.9 4.2 5.0 5.0 5.6 4.4
##  [487] 5.4 4.2 3.8 3.0 6.2 4.8 4.1 3.6 4.8 6.1 4.2 3.7 5.1 3.9 4.2 5.4 3.8 3.4
##  [505] 6.5 4.8 3.9 5.0 4.4 5.1 3.2 3.1 5.5 3.7 4.3 3.4 4.5 5.0 2.6 6.2 3.1 5.3
##  [523] 4.2 2.9 3.9 5.2 5.3 5.4 2.7 5.4 3.8 4.1 4.7 4.7 4.2 5.9 4.2 4.1 2.9 5.1
##  [541] 3.3 4.4 4.3 4.1 2.9 4.5 6.1 3.4 3.4 6.0 4.2 4.1 4.9 3.1 6.2 5.6 5.0 4.9
##  [559] 3.6 3.4 5.6 5.4 3.8 5.3 5.4 6.2 4.1 3.5 4.3 5.3 4.1 5.5 3.2 2.5 4.9 5.9
##  [577] 3.5 4.5 6.0 3.7 5.9 4.3 4.2 3.8 3.8 3.9 5.9 4.4 4.0 4.6 3.8 2.9 3.7 4.5
##  [595] 3.1 4.6 4.8 5.1 6.0 5.9 5.1 4.8 3.8 5.1 4.6 3.8 4.8 3.9 5.0 4.2 4.2 3.6
##  [613] 4.8 3.8 4.8 3.6 4.0 4.9 3.2 4.4 5.4 6.9 4.7 4.7 4.5 5.0 3.9 2.8 4.3 5.4
##  [631] 5.4 3.4 2.4 4.3 4.3 5.4 5.2 3.7 4.8 3.5 4.2 5.6 3.8 4.6 6.3 2.9 4.5 4.5
##  [649] 5.8 5.5 4.1 3.6 4.6 4.9 4.2 3.5 5.9 3.5 4.0 3.5 3.4 3.1 5.3 5.2 3.4 4.4
##  [667] 3.1 5.8 5.3 4.7 3.3 3.9 6.3 4.1 2.4 3.7 3.8 3.8 5.6 5.1 5.2 3.2 4.7 4.0
##  [685] 3.7 4.1 3.3 3.0 5.0 5.2 4.9 3.4 4.8 4.3 3.9 4.0 3.6 4.5 2.7 3.6 4.2 3.6
##  [703] 5.1 4.7 5.8 4.1 3.3 4.5 5.0 5.0 4.5 6.0 5.1 3.1 3.9 4.1 4.2 5.9 6.6 3.6
##  [721] 3.8 2.4 5.8 3.5 5.0 4.8 3.8 2.9 4.3 3.8 4.7 4.6 5.4 3.4 4.5 4.1 4.1 3.6
##  [739] 5.3 3.4 3.9 5.6 2.5 6.2 5.3 4.0 5.3 4.2 6.4 4.1 3.3 4.7 6.2 4.2 4.4 3.9
##  [757] 4.7 5.6 5.0 3.8 4.6 3.3 3.9 5.7 4.3 3.0 3.3 3.8 5.0 4.7 4.5 3.6 4.7 5.2
##  [775] 4.4 5.1 3.9 4.6 4.0 4.3 4.5 4.7 4.5 5.6 3.8 3.5 4.9 3.9 5.7 5.9 3.8 3.1
##  [793] 4.6 3.0 3.2 4.2 4.0 4.8 4.1 2.8 5.5 5.8 4.0 4.0 5.8 5.2 4.3 4.2 3.5 4.9
##  [811] 4.9 4.5 2.8 5.3 6.5 5.4 3.2 5.4 3.2 4.4 2.8 3.9 6.1 4.5 5.6 5.0 3.6 6.0
##  [829] 5.2 4.5 4.2 4.9 5.3 4.6 5.2 5.7 4.6 3.5 5.5 6.1 5.6 3.5 4.0 3.1 4.3 5.1
##  [847] 4.6 6.1 2.7 4.9 5.5 5.2 4.7 5.0 5.2 5.0 3.9 4.6 5.1 5.9 3.4 5.0 2.6 3.5
##  [865] 4.4 4.1 3.5 5.7 6.2 5.2 4.6 4.3 5.0 4.2 4.2 4.8 3.3 3.8 4.8 4.5 3.1 5.7
##  [883] 4.8 4.3 4.8 5.4 4.7 2.8 2.9 4.1 3.5 4.8 4.0 4.2 4.6 4.1 5.2 3.6 4.1 4.4
##  [901] 4.2 3.9 4.4 6.2 4.9 5.6 4.3 5.1 5.6 2.3 6.3 5.1 4.7 2.8 5.6 4.5 5.7 4.8
##  [919] 5.0 5.0 3.9 5.6 5.4 4.8 4.8 4.9 3.9 4.6 3.8 5.2 5.9 3.3 4.4 4.8 6.1 5.1
##  [937] 4.0 7.0 4.2 3.5 3.6 3.6 4.0 6.4 4.5 3.6 6.0 4.6 4.4 5.7 4.1 3.7 3.2 5.1
##  [955] 4.3 6.8 6.3 5.6 2.4 4.4 3.8 4.1 4.0 4.0 4.1 5.6 4.1 5.6 4.0 4.7 3.7 4.5
##  [973] 5.7 3.8 4.1 4.5 5.4 5.2 4.0 3.8 3.0 4.6 4.3 2.7 4.9 2.5 3.5 3.5 2.9 3.0
##  [991] 4.8 6.7 4.6 3.3 5.0 4.1 5.3 4.4 3.7 5.3
## 
## $func.thetastar
## [1] -0.0547
## 
## $jack.boot.val
##  [1]  0.53172205  0.26212534  0.21893491  0.14608434  0.05086705 -0.11301775
##  [7] -0.22543860 -0.31279762 -0.45469169 -0.54901961
## 
## $jack.boot.se
## [1] 0.9827372
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
##    [1] 5.2 3.2 4.2 3.4 5.3 6.1 4.3 4.9 3.2 2.9 6.0 3.4 5.1 5.4 5.0 3.9 3.3 4.5
##   [19] 4.5 2.9 6.2 4.0 3.8 5.4 4.8 4.8 4.4 5.2 5.4 4.5 5.2 4.5 3.5 3.4 3.0 5.1
##   [37] 5.2 2.7 4.3 3.4 3.4 3.5 4.0 4.3 4.7 3.6 4.2 4.0 4.1 4.0 5.0 3.7 4.3 3.9
##   [55] 5.0 4.0 3.1 3.9 3.8 4.4 3.9 5.9 4.3 5.6 3.6 6.7 4.1 2.6 5.7 4.6 3.8 4.7
##   [73] 4.5 4.0 5.9 4.3 4.9 5.3 2.6 5.1 3.0 4.6 4.8 4.0 4.6 5.2 4.8 4.7 5.9 5.0
##   [91] 5.4 4.1 4.8 5.3 5.0 4.8 4.9 3.5 4.6 3.1 4.5 5.6 6.0 4.0 2.8 5.5 6.3 5.0
##  [109] 4.3 4.8 3.8 3.9 4.4 4.8 6.7 2.9 6.2 4.1 5.3 3.6 4.9 3.7 3.8 4.0 5.0 5.7
##  [127] 5.5 3.8 4.4 5.8 3.4 4.7 4.8 3.1 3.9 4.3 4.9 4.1 2.2 4.8 3.0 4.9 4.6 5.7
##  [145] 3.3 2.8 3.4 3.2 3.0 4.6 4.6 4.4 4.5 2.7 5.9 5.8 5.6 4.0 4.1 5.4 6.6 5.2
##  [163] 4.4 4.2 6.1 3.8 4.2 5.9 2.8 4.8 4.9 4.4 3.6 4.8 3.7 4.3 4.9 6.1 4.6 4.2
##  [181] 4.4 5.6 6.1 3.2 4.2 2.6 2.4 3.7 4.0 2.6 4.6 4.6 5.7 4.6 4.4 5.7 5.7 4.7
##  [199] 5.0 4.3 3.9 3.2 6.2 3.3 3.2 4.7 3.1 6.0 5.9 4.6 4.7 4.6 3.8 3.2 6.3 4.5
##  [217] 5.9 5.7 4.4 5.0 4.0 4.6 3.9 4.0 4.4 4.9 5.1 4.2 5.4 5.7 3.8 3.5 5.7 5.6
##  [235] 3.9 4.3 4.3 2.9 6.0 5.3 3.7 4.9 4.1 2.2 4.4 3.3 4.2 3.4 3.3 3.7 4.9 3.1
##  [253] 3.4 5.3 3.4 6.0 5.0 4.6 4.2 3.8 4.2 3.7 4.1 4.9 3.5 2.9 3.9 4.9 6.0 5.3
##  [271] 5.2 4.5 6.0 4.3 3.9 4.0 3.9 3.3 3.5 5.0 5.0 5.7 5.3 3.2 3.8 3.0 5.6 3.2
##  [289] 5.4 4.2 4.9 3.9 4.4 3.7 5.2 4.1 5.1 5.5 5.1 3.7 3.2 4.6 5.5 4.9 4.3 5.5
##  [307] 3.3 5.5 5.6 4.9 3.4 4.3 4.9 4.9 4.2 6.0 3.3 3.8 4.2 5.0 4.0 5.5 5.8 5.2
##  [325] 4.5 4.5 3.7 4.6 6.2 4.2 4.1 5.7 4.1 5.0 4.5 4.1 5.1 5.4 4.1 5.9 4.9 2.9
##  [343] 3.6 5.6 2.9 5.2 5.0 4.6 4.3 3.7 3.5 4.1 3.9 6.0 4.7 5.7 3.0 5.1 4.1 5.9
##  [361] 4.7 5.3 5.8 5.5 4.3 4.4 5.7 4.1 4.9 4.0 3.7 4.5 3.3 4.9 4.4 3.8 4.2 5.2
##  [379] 4.9 4.0 3.9 3.6 4.7 5.3 3.9 3.9 4.7 4.0 5.2 4.6 5.0 3.5 3.7 4.0 5.5 4.7
##  [397] 4.0 3.9 5.3 4.1 6.0 3.8 4.4 5.0 4.4 5.0 5.4 3.6 4.6 3.6 4.7 4.9 4.6 5.4
##  [415] 3.8 4.6 3.2 5.8 4.7 4.4 4.6 3.7 5.6 3.9 5.2 4.8 4.5 4.5 3.9 4.6 4.0 3.7
##  [433] 4.9 4.8 4.1 4.6 4.6 4.2 4.0 4.9 4.2 3.3 5.1 4.8 3.0 4.1 2.8 3.5 4.2 4.6
##  [451] 4.6 5.6 4.5 5.3 5.7 4.4 4.0 5.2 6.2 5.0 5.1 5.5 3.4 3.8 4.3 5.0 2.7 2.4
##  [469] 5.0 5.4 4.4 4.1 2.7 4.6 5.3 4.6 3.9 5.2 4.2 3.4 5.0 5.8 5.3 5.0 4.1 4.9
##  [487] 5.4 5.0 4.5 5.2 4.5 4.0 4.1 4.7 3.9 5.5 3.7 3.8 3.6 4.6 2.8 3.5 5.0 5.6
##  [505] 4.4 5.3 4.9 4.4 5.8 5.4 5.7 3.8 4.3 4.8 4.2 3.9 4.6 5.5 4.5 3.9 3.5 5.9
##  [523] 3.8 3.6 4.6 4.0 5.6 2.1 4.5 4.7 2.2 5.6 5.8 3.5 3.8 3.7 4.6 4.4 4.7 5.1
##  [541] 3.8 3.7 5.0 3.2 4.7 3.8 3.8 5.2 4.4 5.7 3.0 3.2 3.8 4.6 6.8 3.5 4.8 5.0
##  [559] 4.5 5.2 4.7 5.4 4.2 5.4 3.8 4.6 4.5 4.9 2.6 4.5 5.3 4.6 3.1 4.0 4.2 4.4
##  [577] 4.7 4.3 5.8 3.4 5.3 4.6 3.2 4.7 4.6 4.2 4.2 4.6 2.9 5.2 6.0 4.6 5.2 4.7
##  [595] 4.9 4.1 4.8 4.3 3.6 4.0 4.8 3.6 5.3 5.5 4.5 4.1 5.6 4.7 3.3 5.4 6.7 4.2
##  [613] 5.0 6.5 4.7 5.1 4.3 4.6 4.4 4.5 5.0 3.1 3.3 4.2 4.3 4.9 5.2 3.5 5.0 5.2
##  [631] 4.2 4.6 4.7 4.0 6.6 6.3 5.5 4.4 2.8 5.9 5.1 3.5 4.3 4.1 4.7 4.2 5.2 4.9
##  [649] 4.2 3.5 5.5 5.3 5.5 4.6 3.2 5.1 5.0 6.1 4.5 6.1 4.3 4.6 3.9 6.1 4.2 4.8
##  [667] 4.2 5.3 4.6 6.4 5.6 4.0 4.1 4.5 3.8 4.7 4.0 5.2 3.8 4.0 4.9 4.6 4.1 3.8
##  [685] 2.8 6.5 2.9 4.4 4.4 4.7 3.7 5.5 3.6 3.7 2.9 2.5 4.5 5.1 4.1 3.8 4.4 5.4
##  [703] 5.2 3.5 3.9 6.2 5.0 3.7 5.2 4.0 6.1 5.6 5.1 6.2 3.6 5.1 3.2 4.2 4.2 3.3
##  [721] 5.3 5.1 4.8 4.7 5.3 5.2 4.3 3.7 5.9 4.6 5.4 5.6 5.1 5.5 6.1 4.8 5.3 5.5
##  [739] 4.3 5.0 4.8 6.1 4.9 3.4 5.6 4.6 3.4 4.4 5.5 4.2 7.0 4.2 3.9 4.9 5.0 3.9
##  [757] 4.9 4.2 5.1 5.3 3.3 4.3 4.9 5.4 5.7 4.0 6.0 4.0 3.2 4.4 3.7 4.1 5.5 4.2
##  [775] 4.9 4.4 4.8 5.8 4.5 4.6 3.2 4.1 6.3 4.0 5.1 5.1 4.3 4.0 4.3 5.4 4.6 5.5
##  [793] 4.0 5.2 3.6 3.9 4.1 5.3 6.0 4.9 3.8 3.3 5.2 3.9 5.3 3.2 5.0 4.9 5.0 4.5
##  [811] 4.1 4.0 3.8 4.2 4.1 4.5 4.3 4.7 5.0 5.1 4.7 4.0 4.9 2.7 4.4 4.1 6.3 3.4
##  [829] 6.5 5.2 4.6 5.4 6.0 3.2 3.6 3.2 4.5 4.8 4.9 4.2 5.6 4.6 4.5 4.9 2.5 6.0
##  [847] 5.0 4.0 5.8 5.5 3.6 4.5 4.4 4.8 3.9 4.2 3.6 5.2 4.3 5.8 5.2 4.3 3.1 4.5
##  [865] 3.7 4.2 4.6 3.6 4.4 3.8 4.6 4.8 4.2 4.8 3.7 5.1 4.5 5.0 5.1 4.6 4.7 4.4
##  [883] 5.5 4.2 3.9 4.1 4.2 5.4 3.3 6.1 3.9 4.2 5.6 5.1 5.2 5.0 5.6 4.0 6.0 3.6
##  [901] 3.2 5.9 5.5 3.8 5.7 4.4 5.2 5.4 5.2 4.6 2.7 7.0 3.8 4.2 4.7 7.0 4.9 3.4
##  [919] 5.7 3.2 3.9 5.1 4.0 5.2 4.6 4.5 3.6 5.0 4.8 4.0 4.9 3.9 3.4 3.7 5.4 4.1
##  [937] 4.9 5.5 3.9 6.4 4.1 6.2 3.3 4.6 4.1 5.4 5.7 5.5 3.8 4.0 5.4 4.9 4.5 5.9
##  [955] 2.8 4.1 5.8 4.1 5.5 4.6 3.5 5.5 4.7 4.5 4.9 5.5 5.8 3.3 3.5 4.6 4.6 3.4
##  [973] 3.3 3.9 4.3 4.3 4.5 2.7 4.9 5.5 6.4 3.9 4.2 5.4 4.5 5.7 5.2 5.6 4.0 5.0
##  [991] 4.2 3.8 6.1 5.1 4.7 4.7 4.4 5.4 4.3 2.3
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.5 5.5 5.3 5.2 5.1 5.0 4.9 4.7 4.7 4.5
## 
## $jack.boot.se
## [1] 0.9785704
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
## [1] 0.6329768
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
##   4.675650   5.056363 
##  (2.020836) (2.307098)
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
## [1]  1.6250711 -0.3991028  0.9363022 -0.6907259  0.7635212 -0.3096286
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
##    [1] -0.0159962916 -0.1257602855 -0.0918818680  0.8303692244  1.0534317218
##    [6] -0.3529803589 -0.8206120954  1.3017160704  0.0103177533  0.9516662110
##   [11]  0.6630240439  0.5755444824  0.2945205216 -0.2607867793  0.4573248800
##   [16]  0.1272932561 -0.3511822518  0.7613983763  0.5745604906  0.8543534070
##   [21]  1.0053054129  0.2128977126  0.1615062205  0.5929348515  1.0308869655
##   [26]  0.8996019083  0.5764521355  1.0519987261 -0.3308843458  0.0361604864
##   [31]  0.5891524103 -0.4045984153  0.5262966137  1.1585839220  0.2972192961
##   [36] -0.0123484161  0.7512686863  0.3908718475  0.6875105922  0.0865393050
##   [41]  0.7416722553  0.9370308024  0.1882031212 -0.4004622445 -0.0132695175
##   [46]  0.9115263570  0.6617993865  0.0382475276  0.9359990472  0.1627582445
##   [51] -0.3974128513  0.8906331473 -0.1892861799  0.5746155824  0.7044614138
##   [56]  1.6183706410  0.2860106052  0.3735498208  0.7360873582  0.3093918082
##   [61]  0.9742686851  0.2548132786  1.6201286752  1.2783712591  0.5725421191
##   [66]  0.9961173970  0.0495577669  0.9105934514  0.0834368526  0.1987669319
##   [71]  0.0916162787  0.5217458075  0.4746914148  0.7021590846  1.1618779277
##   [76]  0.2355241540  0.2518042814 -0.3003133308  0.8036164182 -0.1870292264
##   [81] -0.7220427132  0.5898252961  0.7134456944  0.0889007474  0.5452869584
##   [86]  0.1351658072 -0.0847467674  0.7704656772  0.1430483910  0.6524112972
##   [91]  0.9478214568 -0.6997760913  0.3260435892  1.2170226246  0.3512520278
##   [96]  0.0136761032 -0.0351295776  0.7616787740  0.9091272841 -0.0424914522
##  [101] -0.3406838379 -0.0433402353  0.2111126209  0.4712900728  0.8253696779
##  [106]  0.1918215454 -0.9739116194  0.9145195808  0.7989385775  0.8101032884
##  [111]  0.1090395907  0.0579520742  0.1116746671  0.7241249164  0.7568935123
##  [116]  0.6102335114  0.3041139262  0.6363484103  0.1867713410  0.5162820951
##  [121]  1.4861060317  0.6703250868 -0.2387395120 -0.1592966281  0.4554350647
##  [126]  0.1798862346  0.1116685746  0.3182557487  0.9396445815  0.2170804260
##  [131]  0.4389937734 -0.8946290491 -0.1101935280  0.4668560561  0.2809729123
##  [136]  0.3695625082 -0.0045940669  0.5541413266  0.8621619013  0.8329157903
##  [141]  1.6653539723  0.4355823317  0.6821825787  0.9802289950 -0.2284108327
##  [146]  0.4228544045  1.3178555040  0.0479116656  0.3681295727  0.3016802901
##  [151] -0.8872597958  0.2945205216  0.8036184281 -0.5305126290 -0.5856996877
##  [156]  0.5246652814 -0.1916324506  0.7041675953  0.3313133236  0.3211211967
##  [161] -0.1937597405  0.4279697214  0.6338369221  0.4120188083  0.6034328298
##  [166] -0.4822173792  0.3103128676  1.2585892714  0.7700401635 -0.4233903301
##  [171] -0.4410901696  0.3672732849  0.5077546318 -1.4415653652  0.3953778420
##  [176]  0.9964271756 -0.5625302904 -0.7414454039  0.5526061782 -0.3237940252
##  [181] -0.2829867624  0.8314385515  0.4869840991  0.5772669234 -0.4329461850
##  [186]  1.3126135576 -0.4261146882  0.6403776736  0.9721142223  0.3897598745
##  [191]  0.6053127022  0.3334091709  0.2334013785  0.6477115193 -0.3965020531
##  [196]  0.7234869174  1.0439019271 -0.5016595220  0.5391087437  0.7195198379
##  [201]  0.6228405386  0.4771569596  0.6429422378  0.2638370765  0.2270240670
##  [206]  0.3150089478 -0.0899046035  0.4722522879  0.4536634442 -0.0401114325
##  [211]  0.6079903083  0.8365604958  1.1494729025  0.5931604006  0.6912527865
##  [216]  1.0007790818  0.0565562726  1.4155318916  0.8695932728  0.9565507539
##  [221]  0.2075425788  0.4146745530  0.4771569596  0.7950486877  0.3573049919
##  [226] -0.3138327437 -0.3241427734  0.6622705179  0.1826237062  0.2778000667
##  [231]  0.3865536082  0.7029232489  0.5732134651  1.4724999757  0.5830762978
##  [236]  0.8035722470  0.4511146002  0.2098744826 -0.6436603427  0.5770097818
##  [241]  0.3303909923 -0.1053228739 -0.4658097328  1.2039873308  1.0689910961
##  [246]  0.5325528423  0.4427690491  0.6920396071 -0.4387312012  0.3057512089
##  [251] -0.4733407543  0.5733462011  0.3457752585 -0.1833270298  0.1093603999
##  [256]  0.0241641578 -0.1444026240  0.2386044718  0.5039320212  0.9605971188
##  [261] -0.0591219953  0.0511885258  0.3630453049  1.4898222124  0.5160947074
##  [266] -0.6677068430  0.3407996070 -0.9093584634  0.5737305248 -0.4636510630
##  [271]  0.2904511914  0.1348152187  0.2711895978 -0.6078829286  0.8455929939
##  [276]  0.7186617724  0.5706943255 -0.1527942298  0.5168858012  0.1340473678
##  [281]  0.2535033104  0.9752217740  0.9538985057  1.3911436589  0.9659208279
##  [286]  1.4710438521  0.6433745680  0.3135308353  1.5627927063  0.9056983902
##  [291]  0.7814796194  0.4240098247  1.0641526346 -0.1482897481  0.7465572331
##  [296]  0.2676384664  0.8408961370  0.3033625028  0.7200422439  0.4674126874
##  [301]  1.2532713229 -0.1931153759  0.3720322724  0.4343836251 -0.2238121631
##  [306]  0.2289556371 -0.1704971746  1.0585313156  0.3824239763  1.5576512715
##  [311]  0.5042946802 -0.0083990964  0.8776951963  0.5504097417 -0.2941446789
##  [316]  1.0889310808  0.4098812368  1.0068643234  0.4093313905 -0.1958608009
##  [321] -0.6564398908  0.1987268316  0.3321814317  0.9901484189 -0.6445368776
##  [326]  1.3017160704 -0.6195533449  0.7924221842 -0.0098149318  1.1814500652
##  [331] -0.1317525459  0.3713441897 -0.1906437742  0.9822523112  0.1552142335
##  [336]  0.6838048718  0.6135836236  0.3962795149 -0.0805387539  0.5337505015
##  [341] -0.3071139008  0.2098694592  0.7518709875  0.7572123875  0.2090966477
##  [346]  0.3568605367  0.3078255672  0.0961877465 -0.6459202476  1.0344750321
##  [351]  0.1900402343  1.1208112811 -0.9963739101  1.0693580795  1.3175417619
##  [356]  0.5304665758  0.7533547549  0.0008482608  0.0938951896  0.4234941529
##  [361]  0.8355710675  0.5907008333  0.8221031843  0.6912527865 -0.0268217445
##  [366]  0.4913029958  0.9369924593  1.2455401333 -0.3687454887  0.4604781933
##  [371] -1.0649347631  0.7721311625  0.5856751409  0.4870956388  0.0690000742
##  [376] -0.4322286234 -1.2705913854  0.3308925458 -0.8267707267 -0.7503021242
##  [381] -0.4885809646  0.6835734942  0.1184121848  0.2294699102 -0.4636084669
##  [386]  0.4291050074  0.4592537232  0.3356926976  0.6413312815  0.6449908180
##  [391]  0.3008321973  0.2465030529 -0.2211919100  0.6295073390  0.7808176209
##  [396]  0.6578440917  0.5669133110  0.8830286210  0.1233553097  0.0227571230
##  [401]  1.3711600584  0.0275192307  0.5336382081  2.1363770039  0.5518561292
##  [406]  0.1086850146  0.7080057823 -0.6069614958  1.1450532655  0.6342259737
##  [411]  0.0711591353  0.2940395403  1.5750439291  0.0628610360 -0.3028463739
##  [416]  0.6074177701  0.5301029222  1.0808693565  1.0381796458  0.4337499584
##  [421] -0.3978848042  0.4744965184  0.8099247477  0.6070034169 -0.9398018303
##  [426]  0.0890164297  0.5399594991  0.4770740983  0.5114229166  0.1314931865
##  [431]  0.9909116888  1.1930172300  0.9054279868  0.3462305508  0.6063091403
##  [436]  0.2099267896 -0.0091864761  0.9405538838  0.0887732577  0.3838629416
##  [441]  0.2302129763 -0.0594790663  0.0346765410  0.4901562052  0.2394461285
##  [446]  0.5718297216  0.8201824333  0.6072295561  0.3981859622 -0.0357572476
##  [451]  0.9979191836 -0.1204561230  0.6948976193  1.5645254519 -0.1123286108
##  [456]  0.9977648131  0.7085433953  0.8980998151  0.8624346643  1.3795572852
##  [461]  0.6128769153  0.7966049214 -0.2052561753  0.6729381381  0.7672617472
##  [466]  0.4038852500  0.8170225903  0.8736395595  1.6696671918 -1.0868611550
##  [471]  0.9134480561  0.7752136395 -0.4262484853  0.5531112338  0.5799357253
##  [476]  0.6790828626  0.8908443864  0.3544199072  0.0443713446  1.1113953066
##  [481] -0.2938905118  0.0067212173 -0.9524187039  0.2275722410  0.2705326410
##  [486]  1.0372320426  0.6735640658  0.2842431338  0.1122213219 -0.0516649922
##  [491]  0.3994553283  0.9050151933 -0.1244505645  0.6779194738 -1.1777275449
##  [496]  0.4270207344  0.6413620563  0.2556337955  0.8959860031  0.1019355098
##  [501]  1.1135754854  0.0129873825 -0.6497663207  0.1004922001  0.5505970542
##  [506]  0.2238548275  0.3883850921  1.0499508901  0.0419550281  0.0428745344
##  [511]  0.6487361658 -0.3207224688  0.0038262247  0.3197113186  0.6449908180
##  [516]  0.5297157608  0.4239390761  0.2518042814  0.9417636663  1.0609621824
##  [521]  0.4983315093  0.8721569163  0.4665110805  0.0888967130 -0.2321657241
##  [526]  0.4301932806  1.4474118795  0.8023518769 -0.2650511936  0.7736884783
##  [531]  1.4284477670  1.0532577317  1.0030886998  0.5479623977 -0.2638174358
##  [536]  0.4773120024  0.1594823658  0.7882500815  0.3103585141  0.1464742854
##  [541] -0.5669270769 -0.3492437809  0.6102335114 -0.0769215818  0.0862669966
##  [546]  0.9345347824  0.5839674505  0.7344708979  1.2187170455  1.2016154992
##  [551]  0.5208565711  0.5759931749  0.8096613586 -0.6775001288  0.4452348215
##  [556]  0.2808673444  0.9849049850 -0.5389132392 -0.0083350877  0.9247912204
##  [561]  0.5351293909  0.5533114834  0.2378073601  0.8232835400  1.2639766979
##  [566]  0.4507712227  0.6712557848  1.2676142814  0.4472325591  0.6879537282
##  [571]  0.3405447319  0.4038693413  1.1810033133 -0.4356639114  1.0641526346
##  [576]  0.0789287446  0.2593356094  0.4600414275 -0.4540642754  0.6509162552
##  [581]  0.4405863500  0.7104110679  0.2906558145  0.7552796864  1.3132342877
##  [586]  0.2735024938 -0.1794451833  0.9800877512  0.6488201212 -0.1720087722
##  [591]  0.8708459486  0.8452267110  0.6568447173  0.2750166464 -1.0558183708
##  [596]  1.2234892650  0.1428310275  0.5249050323  0.3040479727  0.3995426231
##  [601]  0.4413524874  0.4024634105  0.1758922126  1.1290126966  0.4743266546
##  [606]  0.0324115771 -1.1192950813  0.2544409766  0.2629786790 -0.0162257397
##  [611]  0.4836620621  1.0814763770  0.8531926479  1.0622673336 -0.6138163154
##  [616] -0.4319262843  0.8781772905 -1.2301576628 -0.5518454916  0.4922347303
##  [621] -0.3030890312  0.9503415990  0.1940660433 -0.4870492603  0.2920753241
##  [626]  0.1789804972  0.3622682379  0.8356554583  0.3604211425  0.4638024556
##  [631]  0.7936212923  0.3590702220 -0.1404433075  0.5132094595 -0.8637606285
##  [636]  0.5038434427  0.1397386892  1.2036470115  0.3287306144  0.4440790234
##  [641]  0.4412691695  0.4277030076  0.0992152065  0.7443132815  0.3586452872
##  [646]  0.6695052963  0.8459512707  0.2456014495  0.5304665758  0.6388423355
##  [651]  0.7966049214 -0.3023718976  0.1761455072  1.3123542714  0.3827212094
##  [656]  0.4653676516  0.4274168926  0.4481459075  0.9345917841 -0.1558300648
##  [661] -0.2474390158  0.7188267346  0.7965242533 -0.1421444614  0.4841391928
##  [666] -0.1461443112  0.5703157072  0.6398403644  0.4006391416  0.3669235580
##  [671]  0.9213661742  0.5739098469  0.8705703208  1.0995134869  0.4744965184
##  [676]  0.0898938297  1.2879539452  0.4062646117 -0.4285285937  0.2387803945
##  [681]  0.2336662980  1.3744947472  1.2110325272  0.3216034888 -0.2621104187
##  [686] -0.6564398908  0.0712309350  1.0435822585  0.4180258400  0.3345230148
##  [691]  1.3873496016  0.9969024798  1.2969270634  0.7993239357 -0.2335332947
##  [696] -0.0340592995  0.1186077188  0.4143547077  1.2836577913  0.0503322628
##  [701]  0.8406166804  0.3604211425  0.7241249164  1.1373256433 -0.1050971731
##  [706]  0.1856909687  1.1110256301  1.1751133061  1.0525596897 -0.0893989857
##  [711]  0.6225430284  0.3004350867 -0.0081291561 -0.2143268817  1.6636772181
##  [716] -0.3962982232  0.3090894013  0.6771905997  0.5586057353  0.5352779544
##  [721]  0.4609379464 -0.2363690220  0.3604211425 -0.8416437860  0.2331792458
##  [726] -0.4351378345  1.0614312958  0.1275104067  0.4904897896  0.6210543792
##  [731]  0.4033766457 -0.3423742674 -0.4353373266  0.2566493808  0.6123421193
##  [736]  0.2919503914  0.3382579270 -0.3928187844  0.8291876227  1.0943309513
##  [741]  1.2752388450 -0.2471642220  1.6750647187  0.5093367112  0.6083865395
##  [746]  0.5132535419  0.6329768211 -0.4341195408 -0.0683605767  0.6091980844
##  [751]  0.6329768211  0.8076950967  0.0343627297 -0.0987288678 -0.3005326465
##  [756]  0.9213762576  0.4333077708 -0.9316244465  0.8856251209  0.7784152615
##  [761] -0.4456415726  0.1686336653  0.4653400091  0.6245423196 -0.0345929071
##  [766]  1.0847747162  1.4067463346  0.5277980379  0.6253277816  0.3492825497
##  [771]  1.3082394245  0.1199476468  0.4237627581  0.7236281837 -0.5273775257
##  [776]  0.7387865955  0.5931667283  0.2612016213  1.5262351208  0.0543657405
##  [781]  0.7168977166  0.5395521077  0.1703947182  0.9743523531  0.6211519509
##  [786]  0.3876126232  0.2302129763 -0.0040028795  0.5675841134 -0.4055947897
##  [791]  0.9385924095  0.1981372898  0.8599616743  0.0808946235  0.4039489374
##  [796]  1.5066196819  0.1967992606  0.1047952233  0.8455026405 -0.5679330732
##  [801]  0.2220264021 -0.6385126377  0.7421938251  0.8705338816  1.0957433605
##  [806]  0.4286700126  0.6589562351  0.3352837121  0.6909415956 -0.1025762206
##  [811]  0.3209472240  0.7019030262  0.6244016675  0.5676880049  0.5851528786
##  [816]  0.8223657973  0.1918215454  0.5239492307  0.3621621492  0.9109275104
##  [821]  1.2814176680  0.8196944525  0.6139790735  1.0028665784  0.4302898574
##  [826]  0.4625331854 -0.2046195504 -0.1768672819  0.5165501549 -0.2830173089
##  [831]  0.4490750606  0.4650030521  1.0951859871  0.3010659214  1.8051708440
##  [836] -0.0179153401  0.4941068361 -0.3599386897  0.6734128062  0.2339637293
##  [841]  1.3647491334  0.1993443337  0.7858494606 -0.8468107844  0.7404011190
##  [846]  0.5636965232  0.4177001100 -0.5120256184 -0.8496982147  0.4600437513
##  [851]  0.6711617698 -0.0984573903  0.6521641516 -0.2793273536  0.2766228939
##  [856] -0.8731535550 -1.1686577302 -0.8717470658  1.1167619554 -0.6823619984
##  [861]  0.1310475316 -0.4447130652  0.5931604006 -0.4242546064  0.6533396253
##  [866]  0.3804755551 -0.0293581237 -0.6320933981 -0.2335738134  0.4713846778
##  [871]  0.3084069786 -0.5649497884  0.1712173658 -0.0038340272 -0.6527594401
##  [876] -0.1676142977  0.7821929036  0.4905834371  0.6771905997  0.1253817818
##  [881]  0.7101418739  0.8898787031  0.6768880180  0.0230928816 -0.1002140387
##  [886]  0.4180854856  0.8597094591  0.5260752868 -0.4890630192 -0.3705893117
##  [891]  1.2508617538 -0.8869035222  0.7734551953  0.6040836679  0.7429381040
##  [896]  1.3225717699 -0.1232397875 -0.5988838227  0.2290806846  0.2905884660
##  [901]  0.6721834686  0.0952940433  0.3296490628 -0.7242002692  0.4033106790
##  [906]  0.1232110584  0.7056768047  1.0203745139 -0.2030314652  1.0043217357
##  [911]  0.3134285652 -0.2148263130 -0.3517923641  0.2195288537  0.0049035898
##  [916]  0.6595131971  0.3774633506  0.2142703339  1.1126048026 -0.3722615751
##  [921]  0.4449594782 -0.1109883479  0.9111876952  0.1638457996 -0.1682269187
##  [926]  0.6214233290 -0.6742250438  0.8222986149  0.5438454255  1.2647272530
##  [931] -0.0204456197 -0.5088038541 -0.0256506274  0.7441200466 -0.2804708292
##  [936]  0.8022144989 -0.1524554708  0.4294493570  0.4611763463  0.5408385872
##  [941]  0.0427032065  1.5294986812  0.5718744589  0.5633604682  1.3924998611
##  [946]  0.5185757348  1.0419563808  0.8660630976 -0.3080684873 -0.2519306445
##  [951] -0.0594790663  0.6307492265 -0.5959926265 -0.1529054575  0.4667063301
##  [956]  0.6749926925  0.4240085199  0.3991771560  1.0014834759 -0.0479209127
##  [961]  1.4949848251  1.0337214227  0.8583069724  0.9286032027  0.1332936210
##  [966]  0.7202908199  0.3002122275 -0.4502447405 -0.5402989417  1.8423525185
##  [971]  1.0431881937  1.0793842086  0.4272372257  1.0969601149  1.0584501398
##  [976]  0.1151862711  0.1558153353  1.0031940057  1.0028608024  0.0978107043
##  [981]  0.2394254485  0.5635684745  0.4725036432  0.2268061279  0.6225902124
##  [986]  0.8037762062  0.2683633159  0.5475890750  0.8730220220  0.3701119279
##  [991]  0.1134305406  1.0655101874 -0.6527594401  0.6323436533  0.2834271865
##  [996]  0.9736243828  0.9694392024  0.4495015832  0.8280509126  0.5282455518
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
##   0.92470603   0.42958439 
##  (0.13584651) (0.09605601)
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
## [1]  1.05961887 -0.02267093 -1.33632671  0.06166945 -0.05948078  0.95352992
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
## [1] 0.0185
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8843584
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
## t1*      4.5 -0.01231231   0.9017232
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 3 4 5 6 8 
## 1 1 2 1 1 1 3
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
## [1] -0.0183
```

```r
se.boot
```

```
## [1] 0.9083833
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

