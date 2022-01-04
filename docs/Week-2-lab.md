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
## 3 4 5 6 8 9 
## 3 1 2 1 1 2
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
## [1] 0.0506
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
## [1] 2.785456
```

```r
UL.boot
```

```
## [1] 6.315744
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
##    [1] 5.3 4.5 4.8 4.0 4.3 4.2 4.5 3.9 5.3 6.3 5.4 5.8 5.4 3.8 6.1 5.9 3.8 4.4
##   [19] 5.5 4.8 4.8 3.7 3.9 4.8 3.9 5.0 5.1 4.1 4.0 5.4 3.6 4.5 4.9 4.2 5.7 4.1
##   [37] 4.6 4.8 4.8 3.7 3.6 5.3 4.5 4.9 3.7 3.7 5.3 4.5 3.3 3.9 5.2 4.0 5.3 4.2
##   [55] 3.6 3.8 5.7 3.3 4.9 4.7 3.4 3.6 4.9 5.1 4.3 5.0 4.3 3.7 4.1 4.7 3.9 4.4
##   [73] 3.1 5.6 4.7 5.1 3.3 5.9 5.5 3.9 4.2 4.2 4.6 5.6 2.9 4.7 3.1 5.6 3.9 4.2
##   [91] 4.3 5.8 4.6 4.3 4.1 4.5 4.9 5.2 4.6 5.7 6.3 5.1 5.6 4.2 3.9 4.9 5.8 5.3
##  [109] 5.8 4.4 5.3 6.3 5.2 3.7 4.2 4.4 4.9 3.9 4.0 4.6 4.6 6.2 3.3 3.6 6.1 2.7
##  [127] 4.5 4.7 4.7 5.2 4.0 5.1 5.1 4.8 4.6 5.3 4.0 3.0 4.2 3.0 4.8 3.0 6.1 5.7
##  [145] 3.8 4.6 4.5 4.7 6.1 4.4 5.3 5.2 4.1 4.9 4.5 5.0 4.5 2.5 5.1 5.0 5.1 5.9
##  [163] 3.4 5.2 3.5 4.7 4.3 6.8 3.2 5.0 4.9 4.1 2.1 3.7 6.5 5.9 3.8 6.2 3.9 4.5
##  [181] 4.7 4.7 4.2 4.1 3.6 4.1 4.9 4.1 5.0 4.6 5.3 5.4 4.0 3.2 3.7 4.1 4.4 4.8
##  [199] 4.9 4.2 4.9 6.0 4.5 5.7 3.8 5.4 4.5 3.5 4.9 5.7 5.4 5.1 4.7 2.2 5.2 6.6
##  [217] 4.9 5.8 4.7 3.8 2.3 4.2 3.5 3.6 2.4 4.4 3.3 5.7 3.9 5.4 4.6 2.2 5.0 4.3
##  [235] 5.0 2.5 4.1 3.7 6.5 5.2 4.7 5.3 3.2 3.4 3.7 4.3 6.5 6.8 6.2 3.7 3.5 3.4
##  [253] 4.6 3.1 5.2 4.0 5.0 5.5 3.9 4.3 5.3 4.1 5.6 5.1 4.0 3.9 4.7 5.4 6.5 5.2
##  [271] 4.8 5.0 4.3 3.1 3.3 3.3 4.5 5.4 6.5 4.0 4.2 4.1 5.2 4.6 5.1 3.2 4.4 5.2
##  [289] 3.2 3.3 5.2 3.6 6.1 4.7 4.0 5.3 4.1 5.4 4.1 5.1 4.5 5.5 4.3 3.1 4.7 3.1
##  [307] 5.1 3.4 4.1 4.4 3.6 6.4 3.2 4.8 6.6 3.2 4.3 3.3 3.8 4.8 3.7 5.1 4.7 4.4
##  [325] 3.4 5.1 6.0 4.5 4.6 3.4 6.1 5.3 4.0 5.9 5.4 5.1 3.9 4.0 3.9 2.5 4.4 4.0
##  [343] 3.6 3.9 6.2 5.5 3.5 5.5 4.8 5.0 5.4 3.5 5.1 2.7 5.7 5.6 5.0 4.2 4.4 4.1
##  [361] 2.7 4.5 5.5 5.1 4.3 4.2 3.8 4.3 3.5 3.2 4.3 3.7 4.5 4.4 4.9 4.7 5.4 5.9
##  [379] 4.5 5.0 5.5 3.1 5.1 5.0 4.2 5.6 4.9 5.1 3.7 4.8 4.0 5.5 4.8 4.6 5.0 3.2
##  [397] 4.8 4.1 4.5 4.2 3.6 4.9 4.1 6.2 5.4 3.4 4.8 4.0 3.4 5.8 5.0 5.1 4.5 4.5
##  [415] 2.6 3.6 3.0 5.8 3.3 3.8 5.0 2.4 3.8 4.5 6.5 5.0 3.1 5.9 3.8 4.8 3.3 5.8
##  [433] 5.5 3.4 4.8 4.6 3.1 3.9 3.6 2.7 4.6 3.9 2.3 3.5 4.8 4.6 4.4 5.5 3.4 5.8
##  [451] 3.3 5.2 4.2 2.7 4.4 4.4 5.0 3.4 5.0 2.7 4.3 5.5 4.6 5.2 4.2 3.0 3.7 5.7
##  [469] 4.5 3.9 3.5 4.7 4.3 4.3 3.4 4.6 4.5 3.4 5.9 4.4 3.4 5.5 5.4 5.9 7.2 4.1
##  [487] 5.4 3.5 3.4 3.3 3.2 5.0 3.4 4.4 4.8 4.3 4.7 5.1 3.4 6.2 5.3 3.7 4.3 4.4
##  [505] 5.8 3.6 4.1 5.4 4.0 4.2 5.8 5.0 3.8 4.4 4.3 3.8 3.9 4.6 4.2 5.3 3.8 4.8
##  [523] 6.4 5.1 3.7 4.2 3.7 3.9 6.0 4.9 4.8 3.4 4.0 3.3 4.8 5.0 5.3 4.5 4.1 3.7
##  [541] 5.0 4.3 4.5 3.7 3.8 5.5 5.6 4.7 4.1 4.8 3.7 5.3 4.9 4.4 4.7 3.9 5.7 5.8
##  [559] 5.5 4.7 3.9 3.0 4.1 3.8 3.1 5.3 4.7 6.0 4.8 2.5 5.1 2.4 3.9 2.8 5.1 3.6
##  [577] 4.1 4.7 6.4 3.4 5.0 2.6 3.7 4.4 5.9 4.3 3.2 5.2 6.5 3.9 2.9 3.3 4.9 3.9
##  [595] 3.5 3.3 3.9 4.8 6.2 3.9 3.5 4.5 4.0 3.3 5.3 2.5 3.6 5.9 4.9 3.0 5.2 6.0
##  [613] 4.9 3.9 5.1 3.4 4.7 3.9 4.4 4.0 3.9 4.1 5.9 5.5 3.4 4.3 3.7 4.4 4.2 4.0
##  [631] 4.2 4.6 4.4 4.7 5.9 4.6 4.8 6.2 5.1 3.7 6.4 5.8 4.2 4.8 4.9 5.5 4.5 5.0
##  [649] 4.2 4.2 4.1 4.7 4.2 5.1 4.1 2.4 4.7 4.1 4.4 4.5 2.6 4.2 4.1 2.3 4.6 4.4
##  [667] 4.0 3.8 4.2 4.0 5.1 2.6 5.2 5.0 3.4 4.4 4.3 4.5 4.8 3.2 4.7 4.3 3.6 4.9
##  [685] 4.4 3.7 3.0 4.5 5.2 5.0 3.1 5.4 2.9 4.9 5.8 5.3 5.2 3.9 6.0 4.7 4.8 4.9
##  [703] 5.3 4.8 4.2 4.7 3.9 5.9 5.3 3.4 4.1 4.2 4.8 2.1 4.8 5.0 5.4 2.8 4.3 6.1
##  [721] 3.7 3.7 5.1 5.6 4.3 6.0 5.6 5.5 3.6 4.0 4.7 4.4 4.1 5.7 5.0 2.3 4.0 5.2
##  [739] 5.8 4.9 4.7 5.8 5.8 4.3 4.4 5.4 4.0 5.0 6.2 4.9 4.2 3.3 5.0 2.4 4.4 5.1
##  [757] 4.4 3.5 4.8 4.5 4.6 4.7 3.3 3.4 4.7 5.7 4.8 4.7 4.7 4.8 5.2 4.5 4.4 5.0
##  [775] 3.6 3.3 5.0 4.2 3.8 5.7 4.8 5.9 4.7 3.6 3.9 4.9 3.9 2.8 4.5 4.6 4.9 5.9
##  [793] 3.6 3.4 5.1 4.7 4.9 3.4 4.9 4.9 4.8 4.0 3.4 6.9 3.8 4.2 4.0 4.4 4.1 3.6
##  [811] 2.7 4.3 6.3 4.4 4.8 3.2 4.6 2.7 4.4 5.2 5.2 4.8 4.7 6.8 4.5 3.0 4.1 3.8
##  [829] 4.4 3.8 5.4 4.3 4.6 2.4 4.5 4.3 4.2 2.8 5.3 4.7 3.8 4.5 4.7 3.9 5.7 2.8
##  [847] 5.3 2.7 3.9 3.8 4.7 2.9 4.9 5.9 4.2 5.0 4.0 3.5 3.0 4.4 4.1 6.1 4.3 4.2
##  [865] 4.6 3.4 5.2 3.1 6.2 4.9 5.0 4.9 4.8 3.0 5.6 4.8 4.6 4.2 6.0 4.3 3.9 2.5
##  [883] 3.1 4.3 6.0 4.2 4.0 5.2 3.0 5.0 3.8 5.2 4.1 4.7 5.3 5.9 4.1 4.8 3.4 3.2
##  [901] 3.9 3.6 3.8 3.4 4.4 3.8 4.1 3.9 4.3 4.1 5.2 5.0 4.0 4.6 5.0 2.2 2.3 4.6
##  [919] 4.2 3.6 1.5 4.8 4.7 3.3 4.4 5.8 5.9 3.8 3.9 3.8 4.3 4.8 4.1 3.4 2.2 4.0
##  [937] 3.9 5.2 3.2 4.3 4.1 3.5 6.3 3.1 3.6 6.5 3.4 6.2 4.2 3.1 4.0 3.8 5.3 4.8
##  [955] 4.4 4.3 6.4 5.0 2.7 5.5 6.3 3.6 4.0 2.8 4.0 4.0 3.0 4.2 4.8 4.0 3.4 4.9
##  [973] 2.4 4.3 3.8 5.3 4.0 3.8 4.9 5.8 5.6 4.4 4.2 6.6 4.1 3.9 6.4 5.2 4.5 3.0
##  [991] 5.7 5.5 6.1 3.9 5.1 4.1 5.0 4.6 3.6 4.9
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
## 2.5975 6.3000
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
##    [1] 4.7 3.3 3.3 6.0 4.0 4.0 4.2 6.3 4.3 4.8 5.7 4.1 4.9 3.9 5.4 3.6 4.1 6.8
##   [19] 3.4 3.7 4.0 4.9 5.1 4.8 4.2 4.9 5.8 5.2 5.1 5.6 5.4 3.0 5.0 4.2 3.5 4.8
##   [37] 3.9 6.0 4.8 4.5 2.5 5.3 5.2 2.8 4.4 4.2 4.5 4.6 5.9 5.5 3.3 4.1 5.1 5.4
##   [55] 6.2 4.6 5.9 4.3 5.6 4.7 3.1 5.5 4.4 4.4 3.3 6.5 5.5 3.6 4.1 3.1 3.9 4.1
##   [73] 5.5 3.3 4.6 3.3 4.2 4.5 4.0 3.8 2.6 2.8 3.2 3.8 4.5 5.9 4.6 4.7 4.6 6.2
##   [91] 5.9 4.3 4.6 3.9 2.8 3.9 2.9 5.5 4.7 5.7 3.6 4.3 3.6 5.0 4.3 5.4 5.2 3.9
##  [109] 4.2 4.6 5.0 4.4 5.8 5.9 3.9 4.3 5.9 4.5 4.0 4.6 6.2 3.4 4.6 4.3 5.2 3.4
##  [127] 4.4 5.6 3.8 4.1 5.1 5.1 3.8 4.7 4.6 4.2 3.7 5.3 4.9 5.8 2.3 5.0 4.9 3.8
##  [145] 4.5 2.9 4.1 6.3 5.0 3.9 4.7 5.3 5.1 2.5 2.5 4.3 4.5 5.1 4.8 4.6 5.2 4.1
##  [163] 3.3 4.9 4.4 3.6 4.5 7.5 4.6 4.2 6.4 4.1 3.4 5.4 4.8 5.3 3.7 4.5 4.7 3.0
##  [181] 4.4 4.1 3.7 4.8 5.9 5.7 4.2 4.5 5.9 6.9 3.7 5.3 5.5 4.4 3.8 5.3 5.3 5.6
##  [199] 3.9 4.0 3.7 3.4 3.1 5.6 4.3 4.4 4.8 4.8 4.2 5.5 4.7 4.6 3.0 3.3 5.0 3.3
##  [217] 5.3 4.3 4.6 4.4 4.0 3.2 5.4 3.8 5.9 2.8 5.7 4.1 4.5 5.4 5.2 5.4 4.3 3.9
##  [235] 4.8 5.1 4.1 4.7 5.1 3.5 4.3 4.0 6.6 3.9 4.2 5.3 4.3 4.7 3.8 4.8 4.8 6.6
##  [253] 3.3 4.3 4.9 5.7 4.9 6.1 4.2 3.1 4.4 3.8 5.3 5.1 5.0 5.3 6.2 4.4 4.1 5.9
##  [271] 5.3 5.2 4.3 3.5 5.4 5.0 3.8 4.5 4.0 4.3 5.3 4.4 4.1 5.0 5.7 6.0 3.6 5.8
##  [289] 4.0 5.6 5.0 6.7 5.0 4.2 5.4 4.6 4.5 5.1 5.0 4.5 5.9 2.9 5.3 4.9 5.1 4.2
##  [307] 5.3 4.3 5.6 5.6 5.0 4.9 3.6 4.4 4.8 4.9 5.6 5.5 6.6 5.4 5.1 6.5 3.2 4.4
##  [325] 2.6 4.2 5.5 6.2 5.3 5.3 5.3 3.9 5.0 5.1 3.7 2.8 3.6 4.5 4.6 6.0 4.5 3.6
##  [343] 3.5 3.4 3.6 5.2 4.2 5.5 4.6 4.9 5.1 2.8 4.2 5.2 4.8 4.0 5.1 3.7 5.2 4.6
##  [361] 3.5 6.0 4.7 3.8 3.9 3.8 4.9 4.2 5.3 3.7 2.7 4.9 4.3 5.3 3.6 5.4 5.3 6.3
##  [379] 3.4 4.8 3.6 4.1 6.8 5.9 4.4 3.0 3.7 4.6 5.5 4.3 5.1 4.9 4.1 6.2 5.4 4.1
##  [397] 4.6 5.1 4.3 4.5 3.9 6.3 5.1 4.1 4.6 5.1 4.7 4.7 3.6 4.3 3.5 4.0 3.5 4.6
##  [415] 3.9 5.2 3.6 5.6 4.6 3.8 5.7 4.3 5.2 5.4 2.7 4.3 3.4 3.4 5.0 5.8 5.1 5.0
##  [433] 5.6 5.6 5.3 5.1 4.1 5.1 4.9 4.3 5.6 4.0 5.2 4.4 3.4 4.8 4.0 6.6 4.2 4.9
##  [451] 5.6 2.9 5.1 3.4 6.7 4.4 3.1 4.3 4.9 4.0 5.0 5.0 3.7 4.8 4.5 6.5 4.2 3.7
##  [469] 3.9 4.8 5.5 4.1 4.4 5.2 7.2 4.3 5.0 3.8 3.8 3.9 4.9 5.5 4.2 5.6 3.5 3.9
##  [487] 4.2 5.0 4.6 4.5 4.9 5.1 4.2 3.9 4.3 5.2 3.7 4.1 6.0 4.9 4.0 3.9 4.9 4.3
##  [505] 4.1 3.9 2.9 5.3 5.6 4.2 5.8 4.0 6.1 5.9 2.7 4.8 3.8 3.6 4.3 3.0 4.3 6.0
##  [523] 5.5 2.6 4.1 4.8 2.5 5.7 4.1 3.0 5.1 4.3 4.0 4.8 3.9 4.1 4.3 5.1 3.8 3.3
##  [541] 2.5 3.5 3.9 5.5 4.9 3.9 4.6 5.3 3.5 5.2 5.9 4.4 4.1 3.9 4.9 4.5 4.7 4.0
##  [559] 4.8 4.7 4.8 4.1 5.0 4.7 2.8 5.8 5.0 5.3 3.9 3.4 4.1 5.1 4.4 2.4 4.7 4.4
##  [577] 5.0 6.2 3.7 3.1 3.9 5.7 4.5 5.7 3.1 4.2 4.6 6.1 4.3 4.6 4.0 5.5 4.0 4.0
##  [595] 5.8 5.2 4.4 5.0 4.7 4.2 4.5 3.5 4.2 3.4 4.8 5.1 4.4 4.2 4.7 3.9 4.4 3.9
##  [613] 5.0 4.9 5.7 4.7 4.2 4.8 5.1 4.9 2.9 5.3 3.4 4.5 4.9 4.1 2.8 4.9 3.6 4.4
##  [631] 4.5 3.9 4.0 4.9 4.2 4.0 5.3 4.9 6.2 4.3 4.8 3.5 4.4 5.4 3.5 3.1 2.8 3.5
##  [649] 4.6 5.6 5.3 5.1 2.8 4.2 4.0 4.3 4.6 4.9 3.9 3.5 4.4 3.9 3.4 5.5 3.4 4.2
##  [667] 5.1 5.9 4.7 4.3 4.8 4.8 6.1 4.2 4.5 3.3 4.8 3.8 4.4 4.1 4.5 3.6 5.2 4.1
##  [685] 5.3 4.3 4.7 4.8 4.7 4.6 3.1 4.4 4.4 4.5 5.3 4.1 3.3 3.7 4.2 5.2 5.2 5.7
##  [703] 4.9 3.7 3.0 5.2 4.4 4.0 4.7 5.4 4.5 3.3 4.1 5.5 4.5 4.8 4.1 4.7 4.9 4.8
##  [721] 6.4 3.0 3.9 4.0 4.3 5.3 4.3 3.8 4.8 4.6 5.1 4.7 6.2 4.8 5.1 4.6 4.6 2.9
##  [739] 4.0 5.3 5.4 5.5 3.1 5.7 6.7 4.6 4.5 3.8 5.3 5.4 4.7 4.7 3.7 4.2 4.1 4.3
##  [757] 4.3 4.9 4.1 5.3 4.8 4.8 3.9 5.1 4.7 4.2 3.8 5.4 4.8 4.2 3.0 5.1 5.2 4.3
##  [775] 4.3 3.3 5.5 3.3 4.7 3.5 4.4 3.3 3.8 4.1 4.6 4.6 3.9 4.9 4.8 5.2 4.8 4.7
##  [793] 3.8 5.6 4.1 4.7 4.8 3.0 5.7 5.8 4.0 3.4 3.4 4.8 6.2 3.7 4.8 4.9 4.6 4.1
##  [811] 4.9 3.5 5.7 4.7 3.5 5.4 4.6 5.0 5.1 5.3 4.4 4.5 4.2 4.8 2.2 4.8 4.8 5.8
##  [829] 4.8 5.3 4.8 2.2 4.6 5.7 4.3 6.6 4.4 3.9 4.3 5.3 6.0 2.6 4.4 5.2 4.4 5.1
##  [847] 5.1 4.6 7.0 4.0 2.4 4.6 2.8 4.0 3.8 6.3 5.1 6.5 4.5 7.3 3.3 6.0 4.3 4.8
##  [865] 5.5 4.4 2.7 5.4 6.6 4.0 2.8 3.1 4.7 4.1 2.8 5.0 5.5 2.7 4.6 4.3 4.4 3.4
##  [883] 4.3 3.4 2.7 3.5 5.0 4.3 6.3 6.2 4.1 3.7 3.0 5.6 3.9 3.3 4.2 4.6 4.5 5.0
##  [901] 4.4 5.6 4.2 4.5 5.0 3.9 3.4 5.1 5.3 3.7 3.2 2.7 3.8 5.4 4.0 5.6 4.8 6.3
##  [919] 3.3 5.7 4.7 4.0 3.7 4.7 4.4 4.5 5.1 4.7 7.1 3.6 4.4 4.4 5.9 4.5 4.6 4.7
##  [937] 3.0 4.9 3.2 4.0 6.1 3.9 4.0 5.5 3.9 4.7 4.9 6.6 4.8 3.8 4.5 3.0 4.9 6.3
##  [955] 3.9 4.1 4.0 6.0 5.2 4.8 4.9 4.7 3.8 2.6 3.8 5.4 4.4 4.1 5.5 3.6 5.0 6.6
##  [973] 5.4 5.5 3.7 3.2 4.8 5.6 4.6 5.0 6.0 4.6 5.4 3.9 3.6 3.8 5.2 5.2 4.0 3.9
##  [991] 2.8 3.0 4.1 5.1 4.5 3.6 3.8 3.8 3.0 5.1
## 
## $func.thetastar
## [1] 0.0405
## 
## $jack.boot.val
##  [1]  0.562285714  0.438095238  0.289212828  0.197361478  0.105471125
##  [6]  0.003235294 -0.091764706 -0.189565217 -0.346060606 -0.483139535
## 
## $jack.boot.se
## [1] 0.9561603
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
##    [1] 5.1 4.2 4.0 3.8 3.5 6.2 3.7 5.4 5.7 4.5 3.9 4.6 5.9 2.5 4.2 4.1 4.3 5.3
##   [19] 4.7 3.9 4.4 4.7 4.6 3.7 4.5 4.3 3.9 4.8 4.9 5.2 4.5 4.1 4.5 5.2 6.1 5.0
##   [37] 5.2 3.7 5.3 4.3 2.1 4.5 4.3 4.7 4.5 4.0 5.7 3.7 4.1 4.5 3.4 3.3 4.1 5.1
##   [55] 4.1 4.5 4.9 4.7 3.5 3.9 4.7 4.1 4.2 4.7 5.2 5.2 4.1 5.1 3.7 4.6 4.8 2.6
##   [73] 4.1 4.6 5.2 2.9 4.3 4.4 6.4 3.6 4.8 4.4 5.7 3.5 6.3 3.2 3.7 3.6 4.4 4.1
##   [91] 5.1 5.1 4.3 3.5 4.6 5.4 6.1 6.0 4.2 3.3 5.3 4.6 5.8 4.5 5.1 3.1 3.3 3.4
##  [109] 4.5 5.7 3.9 5.4 4.2 4.6 4.3 4.1 4.4 5.0 3.9 4.6 4.6 4.6 5.6 6.1 4.5 4.6
##  [127] 4.3 5.0 4.6 5.0 5.5 5.0 3.8 4.5 3.8 4.3 5.8 4.9 5.3 4.8 5.2 5.6 5.2 1.9
##  [145] 4.0 5.0 3.5 3.3 4.7 5.3 4.3 2.9 5.5 5.1 3.4 5.2 5.0 4.9 4.6 4.9 5.1 6.6
##  [163] 3.3 4.1 4.4 4.0 3.8 5.2 4.2 5.0 7.0 3.1 4.1 4.5 4.6 5.1 4.9 4.7 3.5 4.9
##  [181] 3.4 3.8 3.4 4.1 2.8 4.7 5.3 3.8 5.1 5.8 5.0 5.5 5.8 4.4 4.5 3.7 3.7 5.3
##  [199] 4.3 3.8 3.6 4.1 4.7 5.5 3.9 5.8 2.9 4.4 4.4 5.8 3.9 5.2 3.5 2.9 4.0 4.0
##  [217] 4.4 4.1 4.6 4.6 5.5 3.8 3.3 6.4 5.5 5.5 4.5 3.2 5.0 4.3 3.7 5.2 5.7 5.4
##  [235] 4.1 4.4 6.5 6.5 3.6 5.1 4.6 3.7 6.1 5.0 5.0 4.2 3.6 7.2 5.3 2.9 3.5 4.4
##  [253] 4.0 5.2 4.7 5.3 4.9 5.4 2.8 4.4 4.8 4.5 4.8 3.5 3.9 5.4 3.6 3.2 6.0 5.7
##  [271] 5.2 3.3 4.1 5.4 4.8 4.0 5.0 4.0 5.2 4.7 3.1 4.6 4.8 5.1 4.3 6.3 4.2 6.6
##  [289] 4.0 5.1 4.6 6.2 4.4 2.5 4.2 4.4 4.4 5.6 3.4 4.0 5.1 3.9 3.2 5.4 4.7 5.6
##  [307] 3.5 5.2 3.6 4.8 5.2 3.9 3.9 4.2 4.8 4.1 4.1 5.4 3.7 6.3 6.5 5.1 3.5 5.1
##  [325] 5.2 3.4 4.0 3.1 3.9 4.5 5.9 5.7 5.5 4.0 4.1 3.4 2.7 4.6 4.9 5.8 4.1 4.3
##  [343] 3.8 5.0 4.8 3.9 3.4 5.1 3.1 4.8 4.3 4.7 5.3 4.6 4.3 5.1 3.5 4.7 4.8 4.1
##  [361] 3.2 4.9 5.4 4.6 4.6 5.7 4.0 3.1 4.1 2.6 3.3 4.8 4.1 5.2 5.1 4.7 5.2 4.8
##  [379] 3.9 6.0 3.0 4.2 4.9 3.7 6.1 5.8 3.4 4.7 3.8 4.4 4.7 4.5 4.3 4.7 4.7 3.9
##  [397] 6.2 3.7 5.9 5.2 5.6 4.5 4.3 3.7 4.5 4.0 5.4 4.6 4.8 5.7 5.3 3.0 3.5 5.2
##  [415] 5.8 4.4 4.5 4.7 4.2 4.6 4.9 4.7 3.8 5.2 5.2 3.7 3.7 3.2 5.5 3.0 4.1 3.8
##  [433] 6.8 3.2 4.3 3.1 5.9 5.0 5.1 5.1 5.7 4.3 4.7 4.3 3.3 4.0 1.9 5.8 3.3 3.5
##  [451] 4.4 4.1 3.8 4.8 2.5 4.6 5.3 4.1 5.6 5.3 4.0 4.0 5.5 4.6 4.4 4.6 4.1 3.4
##  [469] 4.9 4.8 6.5 5.7 3.7 4.5 3.2 3.9 4.7 3.6 5.1 3.6 3.8 3.9 5.3 3.8 4.7 4.1
##  [487] 5.0 4.5 4.0 3.0 3.8 4.6 5.8 5.9 2.8 6.1 3.5 3.2 4.9 4.7 4.7 6.0 5.0 4.0
##  [505] 4.9 5.6 5.0 3.8 4.8 6.8 4.1 6.8 3.9 3.2 3.5 4.2 4.1 6.1 4.7 4.4 5.6 4.9
##  [523] 4.7 4.4 3.4 4.0 2.8 3.8 5.7 3.9 2.0 3.8 5.2 4.0 5.2 4.9 4.4 4.3 2.6 5.2
##  [541] 4.9 4.8 4.8 5.5 6.3 4.1 4.5 4.3 4.6 4.6 5.3 5.1 5.1 5.7 3.7 5.0 5.4 3.4
##  [559] 4.9 4.9 3.9 4.6 5.0 4.8 4.7 5.1 5.2 2.9 4.3 5.9 5.2 4.5 4.3 4.3 5.2 4.0
##  [577] 5.6 3.2 3.0 3.8 5.1 4.4 4.2 6.0 5.4 4.2 5.5 3.8 4.0 4.3 5.2 4.9 5.3 5.0
##  [595] 4.4 4.5 4.6 3.5 4.9 3.7 6.0 4.9 4.8 4.5 4.8 5.4 4.7 4.7 4.4 4.6 3.3 3.6
##  [613] 4.0 3.5 3.9 4.8 4.7 4.4 4.7 5.6 4.7 4.7 4.7 4.3 6.1 4.6 4.7 5.1 4.8 5.0
##  [631] 3.5 4.0 3.8 5.8 3.8 5.0 3.7 4.6 2.8 5.2 5.1 4.7 3.7 6.5 4.3 3.5 3.8 4.1
##  [649] 4.6 5.4 4.7 6.0 4.8 4.3 3.9 4.6 4.8 5.0 3.6 4.6 5.4 4.8 5.3 5.6 4.2 4.5
##  [667] 4.4 4.0 3.8 3.8 4.7 5.3 3.8 6.8 4.2 5.8 3.7 3.3 4.5 4.2 5.6 5.2 5.2 4.9
##  [685] 5.2 5.9 4.3 4.9 3.6 5.0 3.7 4.5 4.5 3.5 3.9 3.8 4.3 6.1 5.2 5.5 4.7 4.8
##  [703] 4.0 5.6 4.4 4.8 3.1 2.8 5.1 4.8 3.8 5.0 3.9 4.1 5.5 5.5 5.3 3.2 4.3 4.6
##  [721] 4.7 3.4 4.2 3.5 3.2 5.4 4.6 4.8 4.2 5.4 5.3 4.2 5.6 4.6 5.0 4.5 4.8 4.3
##  [739] 4.2 4.6 4.1 3.6 3.5 5.0 3.5 6.1 3.4 4.0 6.5 4.1 4.1 3.6 2.9 4.3 2.5 4.2
##  [757] 3.5 4.6 4.1 4.0 5.1 4.6 4.6 5.0 6.5 3.8 3.6 6.4 4.6 6.2 4.8 3.9 4.0 4.4
##  [775] 5.7 5.1 5.9 4.5 5.8 3.2 5.1 5.3 5.4 5.4 4.5 4.4 4.0 3.6 2.5 5.2 3.8 3.5
##  [793] 6.1 6.2 4.1 5.2 5.4 4.8 4.6 4.9 4.4 4.0 5.2 3.7 3.9 5.3 4.7 6.3 3.4 3.2
##  [811] 2.8 3.5 5.8 4.2 5.6 4.3 3.2 3.4 3.0 4.0 2.9 4.4 3.3 6.7 4.0 4.7 3.6 4.2
##  [829] 3.3 5.5 5.3 4.2 5.4 5.4 5.4 5.5 4.0 4.4 3.6 3.4 4.8 5.0 5.3 4.9 4.7 4.0
##  [847] 2.3 5.1 3.0 3.3 3.7 5.1 5.2 2.9 3.9 3.2 4.9 5.6 4.5 3.5 5.1 4.5 3.7 3.0
##  [865] 3.8 5.6 4.7 5.1 4.5 5.0 5.2 3.5 4.8 3.6 4.0 5.4 5.5 5.1 4.6 4.4 5.9 6.0
##  [883] 4.3 4.5 3.7 4.0 5.9 3.7 3.8 3.6 3.3 4.4 5.5 5.3 4.9 3.7 4.4 6.0 3.5 4.4
##  [901] 3.9 4.7 3.3 4.6 5.6 4.5 5.3 5.9 4.1 5.1 4.4 4.8 3.4 3.5 5.5 5.0 5.6 4.7
##  [919] 4.9 2.8 4.5 4.1 5.2 5.1 3.3 3.3 3.7 4.7 4.3 4.5 5.4 4.8 4.0 4.4 3.3 3.4
##  [937] 4.1 4.8 4.0 5.8 4.9 4.9 4.2 4.1 4.6 6.0 4.1 4.4 4.4 5.1 3.4 4.8 4.6 4.7
##  [955] 3.8 2.9 4.4 4.9 4.5 3.8 4.0 5.7 6.6 3.6 6.1 5.2 4.8 4.7 3.3 4.9 4.7 3.8
##  [973] 3.8 4.6 3.4 2.9 7.3 5.4 4.4 6.0 3.4 3.6 4.9 4.8 5.4 5.1 4.0 5.3 4.7 3.5
##  [991] 4.0 4.7 4.1 6.1 4.0 5.1 5.9 5.5 3.9 4.2
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.4 5.4 5.3 5.2 5.1 5.1 4.7 4.8 4.7 4.5
## 
## $jack.boot.se
## [1] 0.9178235
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
## [1] 0.9936904
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
##   4.173421   8.575298 
##  (1.796796) (3.923326)
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
## [1]  0.433538939 -0.002909652  0.864624340  0.236072451  0.209121718
## [6]  0.103502301
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
##    [1] -0.3459741927  0.0572141790 -0.3170613784  0.4780906735  0.1347084837
##    [6]  1.0950204866  0.8936279866  0.8206607618  0.1487674902  0.1541754368
##   [11]  0.8133615109  0.7877984351  2.2006720739  0.1196238233  0.6603520383
##   [16]  1.1397047688  0.9441417855  0.1933659384  1.2802358788  1.0275525316
##   [21]  0.2462731777 -0.0222446976  1.3794568090  0.6020155910  1.9384715662
##   [26]  1.6475299098  0.1320112783  0.5661593214  0.9277996436  0.8637979580
##   [31]  0.0514476621  0.0376042091  1.5376280832  0.6507752762 -0.4117712689
##   [36]  0.7662630788  1.1058444296  0.0141320642  0.1022985652  0.8986305323
##   [41]  0.4268414562  0.8728963429  0.8770170647  0.0657143180  1.1118940156
##   [46]  0.9683395405  1.7793095548  0.2515855106  0.4808239012  1.0386950372
##   [51] -0.2198101996  0.6998836969 -0.0039467151  0.0164841631  0.6576860295
##   [56]  0.7123233730  0.2875470490  0.5475155090  0.9255160332  0.0398912553
##   [61]  0.2016635971  0.7463279613  1.4790413115  1.0544553445  1.7367769147
##   [66]  0.4607622052  0.2601754233  1.5528605798 -0.8127030848  0.0678263490
##   [71]  0.7667014605  1.2423569662  0.5885729854  0.8747402574  0.4464037662
##   [76]  0.4722229445  1.9322956692  0.5346374767  1.8304316220  1.2206611665
##   [81]  0.8828309472  0.1577499498  0.3442925342  0.1830232172  0.5839696663
##   [86]  1.0827465733  0.4657484323  0.2605778857  0.2358629669  0.8775040005
##   [91]  0.8973137149  0.5180894437  1.6942105479  0.2242485947  0.7512283309
##   [96]  0.8740861232  1.5272363008 -0.0707227674  1.1160934119  0.7654618861
##  [101] -0.3237391846 -0.0273570412  1.3127606522  0.4183028090  0.2921729570
##  [106]  0.8145936443  0.4140097179  0.5033637501  1.3488836381  1.4823370418
##  [111]  0.3848032257  0.3450202701 -0.2347623162 -0.1081731940  1.2453222127
##  [116]  0.1439665466  1.2921452366 -0.0080706940  1.1361393837 -0.3092108001
##  [121]  0.5302160727  1.1364041522  0.9133092030  0.9582270971 -0.9432610933
##  [126]  0.6735502430  0.8717505156  0.2792137653  0.3918626179  0.2487118241
##  [131]  1.3969551170  0.9357067414  0.8137857618  0.1931054447  1.1625325462
##  [136]  0.1197485792  0.4765630544 -0.5270600431 -1.4424574496  0.5067029495
##  [141]  1.2311194352  0.7809292403  0.6821087655  1.5002752598  0.4749590410
##  [146]  0.6793182136 -0.0017196166  0.6961533937  1.3818716538  0.2408350063
##  [151]  1.7610661203  1.7063072460  0.6909956010  1.4481617534  0.9086229771
##  [156]  0.3738283667  0.1130803405  0.0501590190  0.0917422579  0.2580534392
##  [161]  0.6372711779  0.1812784253  0.6298285660  0.9229200606 -0.3224615473
##  [166]  0.4187493905  0.7379458650  1.3608008411 -0.0741906215  0.3119493515
##  [171]  0.9226285522  1.2434757484 -0.9001079575  0.6998485577  1.2644781293
##  [176]  0.4515584395  1.2966638839  0.9371663292  0.4790298834  1.2495345547
##  [181]  0.3537904756  0.5551982423  1.1998669721  0.0251367623  1.2930297714
##  [186] -0.0910536661  1.4543360692  0.8040797998  0.8672359638  1.0498105297
##  [191] -0.4031658585  0.8310220445  0.9627253248  2.0782279735  1.3738877698
##  [196]  0.4445951912  0.4838595530  0.5212049643  0.3587678994  0.1129060400
##  [201]  0.1527928181  0.2476597246  1.5480055633  1.2665184288 -1.2994386738
##  [206]  1.3821459249  0.6692372927  0.9155043417  1.2903060655  0.3469973359
##  [211]  0.7370861119 -0.0552786472  0.6778657523  0.3327217904 -1.2740259597
##  [216]  0.2225708208  0.7202250228  0.1872683539  0.5880791170  0.0169065943
##  [221]  0.1131742742  0.8992193467  0.6402269282  0.2280863200  0.1254515239
##  [226]  0.2114904510  1.4383284451  0.9038103757  0.7157606622  0.7256682990
##  [231]  0.0434208423  0.5180894437  1.7559437758  0.0246797281  0.3600569541
##  [236] -0.3176823928  0.9883567029  1.1722965674  0.9937088539  0.4425660623
##  [241]  0.4166153782  0.4774356030 -0.1714641559  0.0684169876  0.3959634563
##  [246]  1.5551088201 -0.2925448255 -0.8858998393  0.7975206494 -0.1962039869
##  [251]  0.8638854008  1.0668521400 -0.0037700556  0.1629611211 -0.0399815166
##  [256] -0.2202174550  1.1048464908  0.2158502224  0.8943227987  1.0565426014
##  [261]  1.3495684180 -0.0790068525  0.3859336785  1.8944334880  0.3149243507
##  [266]  0.7910351993  0.9368217026  0.8671787246  0.4838536119  1.3161299349
##  [271]  1.4075920497  0.4254785044  0.9797956425  1.0102628794 -0.3477028357
##  [276] -0.3792370973  1.7170519056  0.9272925080  0.1351249933  0.8563169184
##  [281]  1.2586605857 -0.9549577057 -0.1192390847  1.5645564799 -0.0590262136
##  [286]  1.6819789699  0.6808639295  0.4482705203 -0.0888238224  0.0540366747
##  [291]  0.4449664054  0.2675315148  1.6687878316 -0.3037584296  0.1760817095
##  [296]  1.0061516994  0.9981556676  1.2214197207  1.1446562260  0.0046630678
##  [301]  0.9484084614  1.0052791075  0.9930392385  0.0527381397  1.1655559330
##  [306]  0.9098667887  0.0673516819  0.9378497624  1.1945934552  0.3928094885
##  [311]  1.0353948726 -0.1678034393  0.8451235910  0.3769945430  0.7093301394
##  [316]  0.9252229346 -0.0805008573  1.6482856351 -0.8759525448  0.9962555030
##  [321]  0.0992461279  1.1356435789  0.3109090730  1.7208510724  0.6818868904
##  [326]  1.3503741289  0.6781023142  1.4212749125  0.4270007760 -0.9321505217
##  [331]  0.9540273724 -0.2144912777  1.2277017717 -0.4715599824  0.4173664446
##  [336]  1.7026478520  0.7864935133  0.1979509766  0.7198373438  1.4944167904
##  [341]  1.1846015796  1.0159323050  0.5614778928  0.8630339069  0.2161573259
##  [346]  1.4096143888  0.9975075786  1.2253437140 -0.0029150757  0.6033041002
##  [351]  0.3015874235  0.6591876882  1.1912118617  1.8046958880 -0.1391922962
##  [356]  1.3206708662  1.2125784926 -0.3245230301  1.9234329786  0.3662274834
##  [361]  0.6222537611  1.6464232186  0.0305285651  0.8513596774  1.0858605787
##  [366]  0.7022008354  1.4930245415  1.0487371516  0.8417578200  0.8617538944
##  [371]  0.4518920806  0.9101428698  0.2733936870  1.5949568641  1.2397851000
##  [376]  0.9561044732  0.3726774530  0.2446058298  0.0902841135  0.0036741730
##  [381]  0.3459667803  1.3479437694  1.3819104215  1.2804449898  0.1315860891
##  [386]  0.9031861259  0.4505276917  0.8521804539  1.0215023037  0.1320144438
##  [391]  0.1462409664  1.1663859229  1.3738877698 -1.3644422473 -0.6756527670
##  [396]  1.4897082839  1.4665322918 -0.3558463932  0.7139000723  0.6942380730
##  [401]  0.3767611901  1.1097050327  0.6670122702 -0.6255803572  0.7410878048
##  [406]  1.0339820420  0.2580766768  0.1677861732  0.8758923110  1.1130984277
##  [411]  1.2372570163  0.7439542009  0.5831318789  1.5588040022  0.1312996149
##  [416] -0.5158461369  0.8269861680  1.6807105680 -0.3179623703  1.1015568457
##  [421]  1.3992287532  0.7155303316  1.0939325606 -0.2444555248  1.0718396321
##  [426]  0.4099528208 -0.0768637320  0.4131101758 -1.4917376315  0.6847288058
##  [431]  0.0048474328  1.5978111931  1.8835692105  1.3380747059  0.3126772299
##  [436]  0.1692340449  0.2451392508  0.7955658529  0.5896404590  0.5238271340
##  [441]  0.7182361897  0.5314947411 -0.0858565299  0.3042999689 -0.8739590398
##  [446]  0.1440683475  0.8925432631  1.7645970870  0.3469175117  1.3400166494
##  [451]  0.4194734374 -0.1814743491 -0.1083244639 -0.7807278398  1.8806150845
##  [456]  0.2082819217  1.5770386445  0.9356250131 -0.0606613450  1.1847758857
##  [461]  0.9496475990 -0.0912639484  0.8135686974  0.5099399542  1.2204062849
##  [466] -0.2143189684  1.0654303265 -0.7428741661  0.9771396690  1.4103917107
##  [471]  0.3405320220  1.5412115294  0.4724178941 -0.5289381637  0.2383236649
##  [476]  0.7225092890  0.5843932680  1.5030211614  0.4111534827  0.5240371533
##  [481]  0.8891618661  0.3568277768 -0.8710607499  0.7154398224  0.8457377162
##  [486]  1.8914087953  0.3374150595 -0.2513861905  0.6776012570  0.7382510849
##  [491]  0.1184540913 -0.1886275788 -0.6288570177  0.2608368864  1.6498771984
##  [496]  1.0211835852  0.4310240552  0.7186013693  0.8502045052  1.0783444521
##  [501]  0.2308027157  0.4551111964  0.6828756335  1.3813334183  1.1089263474
##  [506]  1.1368775972  1.4194094669 -0.1281572196 -0.4603207670  1.5312966268
##  [511]  0.9568941735  0.3956259781  0.3523300072  1.0155002730 -0.1981932650
##  [516]  1.2029474950  1.3527626968  1.0681664249  0.6819927823  1.5132759811
##  [521]  0.8732205470  0.8679217753  0.2310463137  1.4114343103  0.2040377296
##  [526]  0.3147458269 -0.0709789983  1.3138502860  1.2895672659  0.7452864603
##  [531]  0.5538183245  1.4012076130  0.6477448110  0.8602839109  1.5120635646
##  [536]  0.1051744923  0.1931736458  1.3474343996  0.1829771325  0.8553314271
##  [541]  0.7590626645  0.5035768501  1.0880718489  0.6330155482  0.4099765993
##  [546]  1.7470092542  0.8927414434 -0.1519081975  0.0215882821  1.1263755197
##  [551]  0.3426515107  1.3177472148  0.7136710875 -0.6917808375  0.5223833053
##  [556]  0.5834315874 -0.5049756511  0.8801200244  1.8991152440  1.6837000423
##  [561]  0.9441495559  0.4555336962  1.7348229738  0.7615436595  1.4253676200
##  [566]  0.9067114136  0.5994018245  0.7717499405  1.0066715767  0.7624903555
##  [571] -0.1995756692  0.6818868904  0.6133451891  1.2376409390  0.6762824800
##  [576]  0.0075457984 -0.0038480592  0.3088096163  1.9105115764  1.4654605779
##  [581]  1.1950653788  1.3509434201  1.0071833595  0.8165370825  1.4733201426
##  [586]  0.8828617713 -1.4294649900  0.4461683619  0.4380975511  1.4347596653
##  [591]  0.9409543839  0.4827295208  0.5164140514  0.5735279317  0.8394178211
##  [596]  0.1156344093  0.4837940776  0.6437241554  0.8085938371  0.2728508289
##  [601]  1.2845094307  0.1255454198  0.7067868386 -0.6765382075 -0.3382646260
##  [606]  1.6725167876  0.8933226880  0.8247309267  0.2788889972  0.3518428012
##  [611]  0.4205019862  0.5538624712 -0.5023898382  0.8924188947  0.0628058926
##  [616]  1.0926682125  0.6572246582  1.7541005026  1.7797397008  1.5412115294
##  [621]  0.7408663506 -0.1986133909  0.9288859153  0.3132473609  0.1939804843
##  [626]  0.8407889709  0.1686109665  0.8016768202 -0.3581688046  1.1774669375
##  [631]  1.2712668277  1.1035845364  0.3949299389  1.0468883413  1.0845580986
##  [636]  0.4183495290  0.2403971482  0.7569417684  1.4083516673  0.8961063844
##  [641] -0.1528736888  1.3994548398  0.7374510419  1.1881667470  0.3086217211
##  [646]  1.1522855766  1.4175507437  1.0336646693  1.1257683206  0.9849053666
##  [651]  0.6298425720  0.4139192159  0.0949548371 -0.0167120339  0.1760817095
##  [656]  1.4152477266  0.6130707694  0.1998296138  0.3310804165  0.7851792918
##  [661]  1.2381407049  0.0955091746  0.7515987653  0.9118690611 -0.3304850974
##  [666]  0.1099499949  0.7031604151  0.6575483194  0.9224969878  0.7391839636
##  [671]  0.3539225041  0.3020449005  0.8011559640  0.8267734686  0.3780733806
##  [676]  1.0944002935 -0.2269187420  0.3863751548  0.7389495786  1.2431757381
##  [681]  0.5428402623  0.4185718876  0.3211349374  0.6633919431  1.1407391076
##  [686]  1.1836471023  0.2908416323  1.0270412688  1.0157068345  1.0435934349
##  [691]  0.7629065853  0.1847999734  0.4243715183  0.7212561867  1.0703421235
##  [696]  0.4831042160  0.2586007694  1.6814048836 -0.1584128125  0.8713937822
##  [701]  1.1169540075  0.1710583952  0.4737571634  0.8731691626  0.4721292957
##  [706]  0.6512754135 -0.1452596972  0.6109070588  0.9191906096  0.3032528840
##  [711]  0.0831855854  0.3955250727  0.4481551840 -0.2238972814  1.7726530079
##  [716]  0.9177222874  1.7588216713  0.7543161747  1.5420158169  0.8006214669
##  [721]  0.1085665695  0.0047741244  0.3778536608 -1.0019375812  0.3634114898
##  [726]  0.1701771217  1.3512089370  0.8307377440 -1.3249479214  0.0568040159
##  [731]  1.2032477640  0.7758285510  1.7243998720  1.1238347679  0.5318983844
##  [736]  1.7216844077  1.3264303688 -0.1156983127  0.9737974693  0.6043203279
##  [741]  0.2768859928  1.4543360692  1.7628954680 -0.5395186281 -0.0837924104
##  [746]  0.6728577098  0.1734030166  1.0519820226  0.5978380239  0.4936379639
##  [751] -0.6246948207  0.3250741426  0.4226225442  0.8031990475  0.7707093742
##  [756] -0.2831240247  1.0036676297  0.4587829689  0.9882231202  1.0682715848
##  [761]  0.2808869639  0.7563059094  0.5518895244  1.3068478083  1.2812752029
##  [766]  0.9936903648  0.7675095660  0.7951581294  0.7763603345  0.1591789246
##  [771]  0.4879008153  0.7603078832 -0.1272353241  0.9819933241  0.9715267702
##  [776]  1.4799546625  0.5095748840  0.5346374767 -0.0903333216  1.4549979406
##  [781]  0.1589597279  1.1058444296  0.8672220929  0.8542341480 -0.4328992045
##  [786]  1.2855223044  0.7273020914  0.0165095724  0.4811833569 -0.1533222993
##  [791]  1.5236856036  0.7155303316  1.8137836701 -0.2382461136 -0.3899165219
##  [796]  0.1548055954  1.8190725075  0.8713859956  1.1843000689  0.4752381536
##  [801]  0.4337992892  1.1451640056  0.8040635921  1.3210061031 -0.0807428816
##  [806]  1.0346002241  0.9783080997  1.1665542222  0.0311375302  0.7702105615
##  [811] -0.1126757575 -0.6499180338  0.6064478806  0.5101671137  0.3766869347
##  [816]  0.9999642392  1.0816785885  1.1866274288  1.5167750856  0.6342624077
##  [821]  0.1978796390 -0.0007583452 -0.2110942883  0.6662902834  1.5162894050
##  [826]  0.5914599434  0.6133200768  0.3733433850 -0.4915415004  1.2523888696
##  [831]  0.3856205622  0.6331491130  0.6503795714  0.4998621184  1.4026097119
##  [836]  0.5949629340  0.3192648984  1.2029660289  1.1391476979  0.6864635975
##  [841]  0.7939170971  1.4479326564  0.8292975458  1.5461207794  0.2964087820
##  [846]  0.9196438736 -0.5295791699  0.5073129336  0.7020997419  0.7644759485
##  [851]  0.7083204641  1.6554485864  0.9926413429  0.8242677959  0.4439908788
##  [856]  1.1371838397  1.5038815330  1.4697384561  0.0959913578  1.2704481253
##  [861]  0.3547475563  0.8156808486  0.8768575123  1.7015969940  0.9412812914
##  [866]  1.2284490836  0.2708764389  0.9369848664  0.4830781576 -1.1102529804
##  [871]  0.9349204843  0.6365925912  0.6575935184  0.7864935133  1.3912474772
##  [876] -0.0743261178  0.1438532467  0.1724183569  1.6289859109 -0.0921597738
##  [881]  0.4595156697  0.8778989507  1.1571510671  0.4689718349  0.1742897557
##  [886]  0.1315860891  0.8001631340  1.3000447583  0.9202395971  0.7483748017
##  [891]  0.9114798182  0.3838053345  1.0280517169  1.4070367423  0.0042169260
##  [896] -0.1960202620 -0.1007996189  0.9557401288  0.8634667763 -0.1008748178
##  [901]  0.9658026125  1.1769051365  0.9657020478  0.7813044605  0.5100885246
##  [906]  0.7722724736  0.4495897820  0.8231840232  1.4997117984  0.5977100990
##  [911]  0.0593623908  0.4167271887  0.9356239598  0.6553598837  1.0632839099
##  [916]  0.4196931801  0.1184808577 -0.6246700392  0.2868159633  0.8567614626
##  [921] -0.0646479896  1.0601704715  0.0142817653  0.5304026326  1.1357045278
##  [926]  0.5372539292  0.9449676776  0.7362180886  0.4599424472  0.8245137140
##  [931]  0.1168253636  0.9007382026  0.1472759553  0.1342700885  0.5300856884
##  [936]  0.4996936130  1.4411703140  1.1136819948  0.9150808614 -0.2401953876
##  [941]  0.0581954530  0.4330017260 -0.0850907430  0.4694795994  0.5982764147
##  [946]  1.1026999878  0.8423202318  0.4615312993 -0.1714097437  1.6454695066
##  [951]  0.7183747119  0.9565994103  0.7602461880  0.9611609385  0.1314221306
##  [956]  1.2419149473  0.1572054807  0.2079997470  0.4166153782  1.1846533811
##  [961]  0.5033512374  0.8717505156  0.2506844309  0.5191919241  0.5036219197
##  [966]  1.1746788710  0.9700055008  0.9012183476  0.3382847092  1.5158652849
##  [971]  0.9503946331  1.0219688090  1.0823530131  0.2911720442  0.8644785627
##  [976] -0.2366886297  0.1988519444  1.2475467302  1.3851647413  0.4709323308
##  [981]  0.7486090086  1.1119480627  0.3155890628  0.6492435027  0.6989622193
##  [986]  1.0353287913  0.0770185360  1.2734760857  1.2513244325  1.6615227386
##  [991] -0.1678926136 -0.4548194021  1.0060796054  0.6030562728  1.1855732313
##  [996]  1.2258163710 -0.0410042930  0.3588295583  0.8154263540  1.4253651202
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
##   0.48668647   0.24835374 
##  (0.07853635) (0.05552960)
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
## [1]  0.17325674 -0.21275824  0.85872968 -0.53262742  0.06567087 -0.42439296
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
## [1] -0.016
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8771964
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
## t1*      4.5 -0.004904905   0.9042573
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 1 2 3 4 5 6 8 9 
## 1 1 1 3 1 1 1 1
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
## [1] 0.0168
```

```r
se.boot
```

```
## [1] 0.8937094
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

