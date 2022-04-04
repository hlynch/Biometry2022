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
## 0 1 5 6 7 8 9 
## 1 1 1 3 1 1 2
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
## [1] 0.003
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
## [1] 2.711764
```

```r
UL.boot
```

```
## [1] 6.294236
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
##    [1] 3.6 6.5 4.7 4.9 4.2 4.0 4.0 4.2 4.3 5.9 4.3 4.4 4.7 4.8 4.9 5.2 4.3 5.1
##   [19] 5.5 6.5 3.8 3.8 4.6 4.0 1.9 4.5 5.1 3.4 4.7 3.4 4.1 5.2 4.4 5.5 5.5 5.1
##   [37] 5.7 5.5 2.8 4.6 3.2 3.5 4.9 4.6 3.5 5.5 5.0 4.6 3.7 4.8 3.3 4.1 4.3 6.7
##   [55] 4.1 4.0 3.7 4.9 4.6 4.8 3.8 4.9 4.3 5.7 4.9 4.0 5.0 4.8 3.6 5.9 5.1 3.5
##   [73] 5.4 3.6 4.9 3.2 4.8 4.7 2.6 4.5 4.5 5.4 4.8 5.5 4.6 5.4 4.2 5.0 4.3 4.5
##   [91] 4.3 4.4 3.5 4.8 4.6 5.3 5.0 5.3 5.3 3.3 6.2 5.2 2.6 5.9 5.4 3.8 5.0 4.4
##  [109] 3.2 3.6 5.0 5.8 2.8 4.3 5.5 4.1 4.9 3.6 5.6 4.8 4.2 3.6 4.0 2.7 2.5 4.0
##  [127] 3.9 4.8 3.7 3.6 4.6 5.1 4.8 4.0 3.9 4.7 5.5 3.7 4.4 6.5 4.4 4.6 3.3 5.5
##  [145] 4.8 5.1 3.4 4.7 3.3 4.8 5.2 3.3 3.4 5.2 3.6 3.4 4.9 4.3 6.2 4.3 4.9 2.1
##  [163] 6.4 3.1 4.7 3.3 3.7 4.2 3.2 3.9 4.5 4.8 6.1 4.3 3.7 6.0 5.0 4.7 4.5 5.2
##  [181] 6.4 5.0 4.6 4.0 3.3 5.7 3.3 5.1 4.7 4.7 4.4 4.3 4.4 4.1 4.1 5.1 4.4 4.3
##  [199] 5.4 3.5 3.5 4.8 3.1 5.9 5.1 3.9 4.4 4.3 5.4 4.2 3.2 4.4 5.3 5.3 4.1 2.6
##  [217] 5.2 4.3 4.5 3.5 4.4 4.9 6.1 3.5 3.7 5.0 3.4 4.0 4.7 4.1 3.1 3.5 5.1 3.4
##  [235] 4.0 3.6 5.9 5.9 4.5 4.8 2.4 3.7 3.1 4.3 4.7 3.6 3.6 5.7 4.6 4.6 4.3 2.9
##  [253] 4.4 2.9 5.2 3.3 3.8 4.1 3.6 3.9 3.6 3.9 5.2 5.0 4.6 4.2 4.7 5.6 5.0 5.1
##  [271] 4.8 3.4 3.9 5.9 4.5 5.9 3.9 4.0 6.3 5.5 5.7 3.1 3.2 4.3 4.4 5.0 5.5 6.9
##  [289] 3.5 5.6 3.8 3.3 4.1 4.2 2.6 4.4 3.8 4.6 5.2 3.9 6.0 4.2 4.1 4.8 4.5 4.1
##  [307] 4.7 5.0 4.1 5.1 3.7 4.3 6.6 4.1 5.8 4.5 5.1 3.6 3.3 4.1 4.7 4.5 4.5 4.0
##  [325] 5.9 4.0 4.6 4.2 3.2 6.9 3.7 4.4 5.6 4.2 4.6 5.5 3.5 5.9 4.8 5.0 5.7 4.8
##  [343] 4.9 3.8 6.5 4.8 3.8 3.9 3.1 4.9 5.6 5.0 5.0 2.3 4.3 4.8 5.4 6.5 5.6 4.5
##  [361] 2.9 5.2 4.6 4.1 3.7 4.4 4.4 2.9 4.7 6.7 4.0 4.7 4.9 4.2 5.0 5.8 4.6 3.2
##  [379] 3.7 4.3 3.8 5.3 3.6 6.3 4.4 4.7 5.3 4.7 3.2 4.6 5.8 5.3 5.5 4.4 5.6 3.7
##  [397] 3.8 3.8 4.4 3.8 3.8 4.3 4.5 4.8 2.9 2.9 3.2 4.8 4.0 3.2 5.2 3.8 2.5 6.0
##  [415] 4.5 1.8 5.6 4.3 3.7 6.3 5.3 2.7 4.7 4.0 6.5 5.2 4.8 3.2 6.1 4.4 4.0 5.2
##  [433] 6.4 5.4 3.6 4.9 5.2 4.1 2.6 4.1 4.7 5.4 4.9 3.2 5.5 4.1 4.3 3.3 2.9 4.7
##  [451] 3.5 5.5 4.6 5.1 4.0 4.0 4.8 2.9 4.1 3.3 4.5 3.2 6.1 5.9 6.2 2.7 4.3 4.6
##  [469] 4.0 3.6 3.2 5.1 6.2 3.6 3.9 4.9 3.9 6.5 5.5 6.1 4.3 4.5 3.5 3.5 5.7 5.2
##  [487] 5.0 4.2 5.2 4.3 4.0 5.3 4.0 6.8 4.4 4.0 4.3 5.3 5.1 4.1 3.9 3.9 5.9 4.5
##  [505] 5.5 5.9 3.8 5.3 4.3 5.4 6.3 3.6 3.9 5.0 4.5 3.7 3.9 4.2 4.4 3.8 5.6 5.8
##  [523] 5.4 6.8 4.0 2.8 4.5 4.9 5.5 2.2 3.6 3.9 5.4 4.1 5.0 3.1 3.4 4.4 4.4 5.2
##  [541] 3.2 5.5 4.6 2.9 3.2 4.5 4.6 3.8 5.2 5.7 3.6 6.1 4.8 4.3 3.7 3.7 4.7 5.0
##  [559] 4.6 3.9 4.7 5.7 5.0 3.8 5.0 4.9 4.5 5.0 4.5 4.8 4.5 6.3 5.0 3.7 6.6 4.3
##  [577] 4.9 3.9 5.3 2.8 5.3 4.5 6.6 4.5 3.8 4.6 4.8 3.6 4.2 5.4 4.5 4.9 5.6 3.8
##  [595] 3.5 2.9 5.0 4.1 4.0 5.4 4.8 6.1 5.8 4.0 3.4 4.7 5.2 2.7 4.5 3.8 3.3 4.0
##  [613] 4.3 3.7 4.7 4.3 4.4 4.3 4.9 5.3 4.5 5.0 3.9 6.7 6.5 3.3 3.9 4.5 4.2 4.0
##  [631] 4.3 4.7 4.8 4.0 4.7 3.5 3.1 2.9 4.7 5.1 5.1 4.0 2.5 4.1 3.8 3.4 3.1 4.5
##  [649] 4.0 3.6 3.3 4.7 4.6 5.5 4.4 4.7 2.7 4.9 3.5 5.6 5.5 6.2 3.8 4.2 3.6 4.6
##  [667] 3.0 4.7 4.1 6.0 4.1 3.6 3.6 4.7 4.4 5.4 5.0 3.9 3.0 4.1 5.3 5.4 3.1 4.9
##  [685] 4.0 5.6 3.7 3.9 3.5 4.9 7.1 3.8 3.3 5.6 4.0 4.5 3.9 5.1 7.0 4.9 4.5 3.5
##  [703] 4.1 6.0 6.4 5.5 5.2 4.7 5.3 5.7 3.3 5.1 5.3 5.5 6.6 3.8 5.0 5.2 4.4 4.0
##  [721] 4.5 3.4 1.7 3.6 2.4 3.7 4.5 4.7 3.3 4.1 5.3 5.8 5.1 6.7 5.5 5.2 5.7 3.9
##  [739] 3.5 5.2 5.5 2.6 3.9 4.0 5.3 5.6 4.8 3.5 4.7 3.8 4.9 5.1 3.7 4.3 3.0 4.7
##  [757] 4.8 4.4 4.3 4.9 4.8 5.3 4.5 3.9 5.8 4.6 4.7 3.0 6.1 3.2 4.6 3.7 3.2 5.3
##  [775] 2.7 5.2 3.7 3.8 4.0 3.4 5.8 5.0 4.9 4.4 3.8 4.4 5.2 4.4 4.9 5.9 5.2 4.7
##  [793] 3.2 6.2 3.5 4.0 3.3 4.7 4.1 6.2 5.3 3.2 3.2 3.4 4.2 5.1 2.2 3.5 4.6 4.5
##  [811] 3.6 6.0 6.1 4.7 2.4 3.9 3.2 4.5 4.6 5.6 4.9 3.7 4.7 5.3 3.8 5.4 3.2 4.7
##  [829] 5.0 6.8 5.4 5.1 4.7 3.5 5.6 4.2 4.3 4.3 5.1 5.1 3.8 4.9 4.8 5.1 2.4 4.6
##  [847] 3.4 3.4 5.5 4.1 3.5 3.1 6.3 4.6 4.9 5.9 5.8 5.6 6.0 5.1 5.2 5.8 4.0 6.0
##  [865] 3.7 4.0 3.9 5.0 5.4 3.3 4.7 5.4 5.4 4.4 4.5 6.5 2.3 4.0 5.2 6.0 4.2 3.7
##  [883] 3.3 3.6 4.9 5.2 5.5 4.2 5.5 3.8 3.9 4.1 4.7 4.8 5.2 4.3 4.5 6.3 3.1 4.7
##  [901] 5.7 3.8 4.4 4.8 4.5 5.7 3.4 4.8 2.7 4.6 3.3 5.0 4.0 3.4 6.0 5.4 3.0 4.2
##  [919] 4.9 5.2 5.9 4.2 5.1 5.4 3.9 4.6 2.5 6.0 3.9 4.9 4.9 4.3 5.1 4.8 5.3 5.3
##  [937] 5.4 5.2 4.5 4.9 6.2 4.5 5.1 4.5 4.6 4.9 4.8 5.1 4.5 5.7 3.3 6.6 5.0 4.0
##  [955] 4.5 5.1 4.2 3.8 3.7 5.3 5.4 5.1 3.3 4.5 5.2 4.0 3.9 3.7 3.5 3.9 2.5 5.1
##  [973] 6.5 4.5 5.8 4.0 3.1 2.6 4.6 4.0 5.8 5.7 6.3 4.2 5.2 3.7 6.1 3.1 5.9 4.8
##  [991] 5.1 7.6 3.6 3.8 5.9 4.6 4.7 4.2 6.1 3.3
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
##   2.7   6.5
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
##    [1] 3.9 3.5 3.4 5.3 4.6 5.1 4.2 3.3 4.8 5.3 4.0 5.4 3.9 5.4 5.0 3.5 6.4 5.6
##   [19] 3.8 5.0 6.2 3.5 4.6 4.2 4.6 6.7 5.6 4.8 5.8 5.4 4.1 3.2 5.9 4.6 4.4 3.5
##   [37] 4.6 6.1 4.6 5.0 4.9 3.1 1.8 5.4 4.6 3.2 4.3 6.0 5.2 4.0 3.8 4.7 6.2 5.9
##   [55] 3.9 3.8 4.4 4.5 3.9 3.5 4.3 5.2 5.0 3.4 4.2 5.4 3.4 5.0 4.1 3.9 4.6 5.4
##   [73] 5.4 3.4 5.1 3.6 3.8 6.1 4.4 5.1 4.5 5.0 3.0 3.3 5.5 4.5 5.2 3.9 3.5 4.8
##   [91] 5.5 3.8 4.2 4.9 3.3 2.9 4.8 2.5 5.2 4.1 4.8 3.3 4.6 3.5 4.0 3.2 4.3 4.5
##  [109] 4.4 5.4 3.7 3.7 4.9 2.5 5.9 4.7 4.6 4.2 4.4 4.6 3.5 5.5 5.7 5.8 5.9 5.6
##  [127] 3.9 6.5 4.9 5.1 4.3 4.0 5.6 5.2 4.4 3.1 5.3 4.3 4.0 5.5 4.0 3.8 6.2 4.2
##  [145] 5.6 3.4 4.3 4.8 4.4 5.5 2.8 3.1 4.7 4.1 3.4 5.3 3.6 5.2 5.6 4.5 4.9 5.6
##  [163] 4.6 5.0 4.5 5.3 3.7 6.0 4.3 5.6 4.4 3.6 6.4 5.0 3.4 5.6 5.5 5.8 3.4 3.9
##  [181] 4.4 3.4 4.1 5.2 5.5 4.7 5.5 6.4 4.0 3.9 5.5 4.8 5.8 5.2 4.6 5.7 3.8 5.9
##  [199] 3.0 5.7 4.2 5.2 4.7 4.1 4.4 3.5 1.9 3.0 3.0 4.9 5.3 5.6 4.5 4.9 3.8 5.0
##  [217] 4.9 4.7 3.7 2.7 4.4 5.1 3.2 4.1 5.4 4.7 5.7 5.4 3.3 3.6 6.1 3.7 3.9 3.8
##  [235] 3.2 4.3 3.7 2.7 4.1 5.6 6.4 3.7 4.5 4.9 6.2 3.9 4.6 3.6 5.7 4.2 5.5 2.9
##  [253] 3.5 4.5 6.2 6.5 5.7 4.3 6.5 5.5 4.0 4.4 5.4 5.0 4.6 6.3 6.4 5.8 5.2 6.1
##  [271] 4.4 5.7 4.8 4.5 3.7 4.3 3.3 4.0 5.0 5.1 4.4 3.7 6.2 5.3 5.0 3.4 3.9 4.4
##  [289] 2.7 3.7 4.8 5.3 4.7 1.6 5.8 4.0 2.6 4.4 5.4 4.4 5.0 5.3 6.6 5.9 4.3 3.6
##  [307] 4.2 6.3 5.4 5.3 4.5 5.7 3.1 5.2 5.3 2.7 2.9 4.0 3.8 3.5 4.1 4.5 4.2 5.4
##  [325] 5.1 5.4 4.0 4.2 3.3 5.3 5.4 5.4 4.6 4.6 4.1 4.1 5.6 5.3 4.6 4.5 4.5 5.0
##  [343] 3.4 3.3 5.1 4.3 3.5 5.4 4.1 4.3 3.5 4.0 3.4 3.8 3.1 3.0 5.9 4.0 3.8 5.7
##  [361] 7.2 3.9 3.9 4.2 4.4 4.3 5.4 3.9 4.2 4.3 4.3 5.0 3.6 4.7 2.1 3.3 4.4 4.3
##  [379] 4.8 3.8 5.2 3.3 5.1 2.0 5.1 4.8 5.1 4.3 4.7 4.2 3.3 4.1 5.0 3.6 5.6 3.0
##  [397] 6.5 3.1 4.6 5.3 5.1 3.6 4.3 4.6 5.3 5.1 4.7 5.6 3.3 5.2 6.0 4.4 3.9 5.3
##  [415] 3.0 2.5 6.2 4.4 3.4 4.6 4.0 4.2 5.7 5.1 4.6 4.3 2.9 3.2 4.2 4.2 5.6 3.5
##  [433] 3.9 6.6 5.6 5.2 4.5 4.1 3.6 4.2 3.5 5.1 5.0 4.1 3.8 5.4 5.6 5.8 4.2 3.8
##  [451] 4.2 5.3 3.6 5.9 3.7 4.2 4.0 4.3 3.7 5.2 3.5 6.3 3.5 5.9 4.4 2.9 3.3 4.8
##  [469] 3.8 4.0 2.8 5.6 3.6 4.3 5.8 5.9 4.4 5.1 5.9 5.2 5.3 4.1 5.9 5.3 4.7 3.5
##  [487] 4.8 5.2 4.8 6.5 4.1 4.2 4.8 5.1 4.2 4.7 5.7 4.7 5.0 4.4 4.6 5.8 3.5 5.0
##  [505] 5.1 5.8 5.7 4.3 4.4 4.0 4.0 4.2 4.2 6.0 4.3 4.3 5.5 3.5 2.6 4.5 5.1 3.3
##  [523] 4.7 3.5 4.3 3.6 3.1 4.2 5.7 4.7 5.8 5.7 4.0 4.5 4.0 5.0 5.2 3.8 4.7 5.4
##  [541] 4.9 4.4 5.5 3.0 4.7 5.4 5.2 4.3 4.1 4.0 5.8 5.4 4.5 3.7 3.6 4.3 3.2 3.8
##  [559] 3.9 6.5 3.4 4.8 3.2 3.2 4.2 4.0 3.2 4.7 3.5 5.2 4.7 4.5 4.9 5.5 5.1 2.9
##  [577] 3.0 4.3 3.8 4.3 5.4 4.4 3.1 5.4 4.2 4.9 5.1 4.2 5.4 5.2 5.8 6.0 4.7 4.4
##  [595] 5.0 4.5 4.0 4.0 4.2 4.4 4.8 4.7 4.8 4.4 3.5 3.0 2.4 5.1 4.6 6.3 3.2 5.1
##  [613] 3.6 5.1 4.8 5.6 5.2 3.4 4.5 5.2 2.9 5.6 4.1 5.4 4.8 5.8 3.4 5.6 3.5 5.3
##  [631] 3.6 6.5 5.7 5.4 4.6 5.6 5.8 5.5 4.6 5.4 4.6 3.3 3.8 2.9 3.7 5.2 3.3 5.5
##  [649] 4.1 2.5 5.8 2.9 5.5 4.7 5.2 4.8 5.1 4.3 3.1 5.2 3.8 5.3 4.0 3.5 6.9 5.0
##  [667] 5.7 3.7 5.2 3.9 3.1 3.9 5.2 5.3 5.4 5.0 4.8 5.2 3.6 4.7 4.2 3.8 3.1 4.5
##  [685] 5.0 2.9 5.6 4.9 4.1 4.9 3.7 5.2 5.4 4.7 4.7 3.6 4.1 5.1 5.6 6.4 6.6 3.0
##  [703] 5.7 5.8 5.4 5.5 5.7 5.4 3.3 5.0 4.2 4.1 4.1 3.0 2.3 2.8 2.9 3.8 2.9 3.6
##  [721] 4.8 4.5 4.3 5.7 4.1 4.1 5.3 4.9 5.8 5.8 6.0 4.7 4.3 4.7 4.0 4.6 5.1 5.4
##  [739] 3.9 4.2 3.7 2.8 4.4 4.1 4.7 5.2 6.0 4.9 4.6 4.6 4.7 4.7 5.0 4.1 3.8 4.3
##  [757] 3.9 3.7 3.3 4.9 5.2 4.2 5.7 3.5 4.9 5.2 3.3 5.4 5.0 4.9 5.6 3.9 4.6 4.2
##  [775] 6.1 4.2 5.6 3.2 5.1 3.0 4.7 4.7 4.6 3.9 4.3 5.5 4.4 5.0 6.1 4.6 4.0 4.9
##  [793] 5.6 3.8 5.3 3.4 3.3 5.0 4.5 3.4 5.4 5.9 5.1 3.6 4.0 6.1 4.6 4.0 4.4 4.9
##  [811] 3.6 4.5 3.4 3.7 4.9 4.1 3.3 3.8 2.8 4.3 4.4 3.6 5.5 4.0 4.0 4.4 4.6 4.0
##  [829] 6.3 3.3 4.2 4.4 3.3 3.8 4.1 3.9 2.6 5.1 3.6 4.8 5.0 5.6 4.2 4.9 4.2 3.2
##  [847] 4.0 5.4 4.8 3.0 4.2 4.2 4.7 3.8 4.8 5.0 4.4 5.6 4.6 4.6 4.4 3.3 4.2 4.0
##  [865] 4.4 5.4 4.1 4.8 3.7 5.5 4.8 3.5 4.0 6.4 3.6 4.0 4.1 2.1 4.1 4.4 4.2 3.6
##  [883] 5.9 4.8 3.9 4.9 4.2 5.0 5.5 3.6 4.2 3.5 4.3 3.4 5.8 4.6 3.3 4.8 5.0 5.3
##  [901] 3.6 4.9 3.9 3.6 4.5 4.8 6.2 4.4 5.1 5.0 4.6 4.3 4.5 4.0 4.2 4.9 2.1 5.3
##  [919] 5.6 5.1 4.5 4.7 3.1 4.7 5.0 1.9 5.7 4.0 4.8 4.4 6.2 5.7 5.1 4.0 5.1 3.4
##  [937] 4.6 5.6 4.1 5.0 4.5 4.8 4.3 5.8 3.5 2.2 6.4 5.0 3.9 5.5 4.5 5.1 5.4 3.9
##  [955] 3.7 4.7 5.4 4.4 5.5 4.3 4.5 5.0 5.9 4.2 3.5 3.6 3.6 4.1 6.3 4.9 4.2 3.2
##  [973] 4.9 5.3 5.1 5.3 4.1 4.0 2.7 3.9 5.5 3.9 3.7 3.6 3.0 5.3 3.5 4.2 6.4 4.3
##  [991] 3.6 3.4 6.0 6.0 6.0 3.4 4.9 3.6 5.2 3.9
## 
## $func.thetastar
## [1] 0.0178
## 
## $jack.boot.val
##  [1]  0.49736070  0.42042440  0.40609756  0.18166189  0.14808260 -0.03495935
##  [7] -0.17718750 -0.22619718 -0.45276243 -0.52625369
## 
## $jack.boot.se
## [1] 1.041007
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
##    [1] 3.5 4.9 6.0 5.3 4.5 3.9 5.4 4.8 4.8 4.1 5.5 5.7 4.8 4.1 2.8 4.3 4.5 3.3
##   [19] 5.2 4.7 4.2 4.3 4.3 3.3 4.8 5.3 6.0 3.9 7.2 5.3 5.0 4.9 5.9 3.5 3.9 4.6
##   [37] 3.2 5.0 4.8 5.7 5.3 4.5 4.7 4.0 4.8 4.6 4.8 5.7 4.6 5.1 3.8 4.9 6.3 3.2
##   [55] 5.2 4.8 5.9 3.1 3.6 3.6 4.3 5.8 3.4 4.5 4.5 5.2 4.5 4.3 3.0 3.2 3.7 2.8
##   [73] 6.5 4.1 5.1 4.6 3.5 3.3 3.9 3.2 4.9 5.4 5.3 3.4 3.7 4.0 4.8 4.5 3.6 4.9
##   [91] 5.9 2.3 5.6 4.6 4.9 4.6 5.4 4.3 2.7 4.1 5.1 5.4 3.9 4.1 2.7 6.3 5.0 3.5
##  [109] 3.1 5.2 4.1 5.0 5.0 4.3 4.5 4.2 4.9 4.0 4.7 4.1 5.0 4.2 3.4 4.4 4.2 4.3
##  [127] 4.6 3.3 5.2 4.5 4.8 5.4 3.7 5.2 5.2 4.9 4.4 3.2 4.5 5.0 4.2 4.6 3.9 4.7
##  [145] 5.0 5.1 5.5 4.3 4.3 4.5 5.6 3.6 5.8 4.7 4.2 3.6 6.2 5.4 4.5 4.4 5.9 4.4
##  [163] 4.2 4.4 4.8 4.6 5.7 4.8 4.8 3.4 3.8 4.8 3.9 5.1 5.4 4.9 3.9 3.0 4.0 5.0
##  [181] 2.9 5.5 3.3 4.4 4.4 3.6 3.0 3.5 5.5 4.5 5.2 3.8 4.4 4.4 4.7 3.6 3.3 4.9
##  [199] 4.8 3.7 5.3 6.2 2.9 5.6 3.0 3.6 6.6 4.0 4.4 7.1 3.1 4.6 3.8 5.0 5.0 4.1
##  [217] 4.7 3.9 5.1 5.3 2.8 5.8 5.6 5.4 4.1 3.8 3.3 3.5 5.0 3.8 3.6 4.9 4.0 4.2
##  [235] 4.6 4.4 4.8 4.9 4.3 3.5 3.6 3.6 5.3 4.4 3.9 4.7 2.9 4.9 4.2 3.4 4.6 4.9
##  [253] 5.3 4.7 3.7 4.9 5.4 4.2 3.5 5.5 4.3 4.4 2.4 4.4 3.3 6.2 5.2 6.3 5.9 4.9
##  [271] 3.9 4.7 4.6 4.7 5.8 3.7 3.8 5.3 5.9 3.6 6.1 4.9 3.6 4.6 4.6 4.0 4.7 4.8
##  [289] 5.7 4.7 4.4 3.8 5.1 3.7 4.4 4.8 4.6 5.6 5.4 6.4 4.8 4.0 5.6 5.2 4.2 5.8
##  [307] 5.5 4.8 4.5 4.9 5.2 4.2 5.1 3.9 3.5 4.2 6.8 6.4 5.4 4.3 4.5 4.3 4.5 4.7
##  [325] 3.4 6.1 5.1 5.3 3.4 5.6 2.9 4.8 4.0 4.4 2.9 6.2 4.1 4.8 5.1 4.3 3.5 4.4
##  [343] 5.8 3.3 6.1 3.0 4.4 3.3 5.1 3.9 4.8 5.6 3.8 4.6 3.0 6.2 2.9 4.4 3.1 5.3
##  [361] 3.6 6.2 5.4 4.9 5.3 3.9 3.5 5.5 4.5 4.8 3.9 4.5 3.5 3.0 4.1 4.6 2.6 4.0
##  [379] 3.8 2.0 3.9 5.2 3.3 3.1 6.1 4.4 5.7 6.0 5.0 3.9 3.9 5.7 3.2 6.0 3.7 5.9
##  [397] 4.6 5.9 3.1 5.2 5.3 4.2 4.9 5.4 4.8 3.8 4.8 3.4 5.5 4.0 3.9 4.9 4.7 5.3
##  [415] 5.5 3.9 3.4 4.4 4.8 3.8 2.5 4.2 3.8 4.1 6.0 5.9 4.2 2.5 4.6 5.0 4.6 5.7
##  [433] 4.6 5.8 4.4 4.6 3.7 5.9 5.5 4.1 4.2 3.4 4.7 4.6 6.0 3.5 4.9 4.7 3.5 5.6
##  [451] 6.0 5.4 3.9 5.6 5.2 5.0 5.3 3.4 3.7 6.3 5.9 4.8 5.2 3.8 4.5 4.9 5.3 4.5
##  [469] 6.6 3.5 4.4 4.8 4.8 4.5 5.7 3.9 5.8 3.2 4.2 6.3 4.9 4.6 5.8 5.4 5.6 4.4
##  [487] 3.6 3.5 4.3 4.5 4.4 5.2 4.9 5.1 4.4 4.6 3.4 5.7 4.4 4.0 7.0 6.1 2.9 3.4
##  [505] 4.5 4.0 5.2 5.5 5.2 4.7 2.8 2.8 4.0 3.0 4.3 4.5 5.4 4.5 4.8 4.1 2.8 5.3
##  [523] 4.1 5.2 3.1 5.5 5.2 3.6 4.7 5.5 3.4 3.5 4.9 4.5 3.4 2.7 5.4 4.3 4.6 2.9
##  [541] 5.9 5.1 6.1 4.2 4.7 4.6 5.2 5.6 4.9 4.9 4.1 5.2 4.8 4.2 2.4 3.3 4.5 6.1
##  [559] 4.8 4.4 3.8 4.2 5.5 4.2 5.7 5.0 5.8 3.6 5.5 2.3 4.1 6.0 4.0 4.1 6.0 4.6
##  [577] 4.0 4.9 3.8 3.9 4.2 3.8 3.8 5.2 5.6 3.7 4.0 3.2 4.0 4.2 4.8 3.9 5.1 6.3
##  [595] 4.5 7.6 3.7 4.6 4.8 6.0 5.3 5.9 5.0 4.8 4.3 3.7 4.7 4.7 3.9 4.3 4.2 3.9
##  [613] 4.0 5.3 4.6 5.2 4.0 5.8 4.0 4.9 4.1 6.0 3.3 3.7 5.7 4.0 3.1 5.5 3.3 5.2
##  [631] 5.4 4.3 3.4 5.2 4.4 4.1 5.3 5.8 4.6 5.4 4.1 4.4 4.6 3.3 4.1 5.2 4.3 5.4
##  [649] 3.4 6.2 5.3 4.4 4.5 4.6 5.8 4.9 5.6 5.0 4.5 3.8 4.3 4.3 2.8 4.4 5.3 4.7
##  [667] 3.7 4.0 5.6 3.5 5.0 2.3 5.1 4.3 5.1 3.4 5.8 4.4 3.9 5.3 3.7 5.1 5.7 3.3
##  [685] 3.6 4.6 4.2 5.1 2.9 5.1 4.6 4.1 5.8 4.5 5.0 3.1 6.2 3.9 5.6 4.6 3.8 5.4
##  [703] 4.6 3.4 5.5 5.9 4.7 4.6 4.5 5.3 3.8 3.7 3.7 3.7 5.4 5.0 4.2 4.1 4.4 3.0
##  [721] 4.6 3.1 6.0 5.6 5.8 3.8 4.0 4.2 5.1 4.3 5.4 3.4 4.1 3.3 4.6 3.9 3.9 4.4
##  [739] 6.2 3.3 4.0 7.2 4.3 3.8 4.7 2.2 4.7 6.3 3.9 5.1 6.3 3.7 2.7 3.1 3.8 3.8
##  [757] 3.8 5.4 4.2 2.7 4.9 6.6 3.4 4.3 3.3 3.3 5.6 3.4 6.4 3.5 5.7 5.3 3.6 5.4
##  [775] 3.7 4.8 4.8 5.0 4.0 4.4 5.3 5.4 5.0 3.3 4.2 5.8 4.9 4.0 5.2 4.5 4.0 4.8
##  [793] 5.0 5.3 5.7 4.0 4.4 4.3 6.2 3.9 4.8 4.1 3.2 4.1 4.3 4.9 4.2 3.8 3.7 4.7
##  [811] 5.5 4.4 4.2 4.8 5.0 3.0 5.0 3.1 5.4 5.3 4.1 3.6 3.6 3.3 3.4 5.9 4.6 3.7
##  [829] 3.4 4.5 4.7 5.1 4.4 4.9 5.9 6.4 5.5 4.0 4.0 4.1 3.6 6.2 4.1 4.2 3.5 3.7
##  [847] 5.1 5.5 4.1 5.3 5.8 4.6 5.9 4.8 3.9 4.0 3.9 5.1 5.0 3.4 5.2 4.7 5.8 3.5
##  [865] 4.7 4.7 4.1 5.6 3.8 5.3 4.7 3.2 4.6 6.6 3.4 2.9 6.4 4.5 4.3 5.0 4.1 5.7
##  [883] 3.2 6.8 6.2 4.7 5.9 4.4 6.3 3.5 4.1 5.1 3.7 4.0 3.2 3.4 3.5 4.5 5.0 4.1
##  [901] 2.9 4.2 5.6 3.9 6.4 4.2 5.2 5.7 4.9 3.6 3.3 5.9 4.5 4.8 5.4 4.0 4.5 4.2
##  [919] 2.8 4.7 2.2 3.7 4.3 4.5 3.5 4.9 4.6 3.0 5.4 3.8 5.1 4.6 3.3 6.5 5.4 3.6
##  [937] 4.7 6.3 4.8 4.4 3.0 3.8 4.9 5.8 4.7 3.3 4.3 5.6 5.2 3.4 5.0 5.3 4.7 5.9
##  [955] 2.7 3.6 3.1 4.7 4.4 5.2 5.8 4.9 4.5 5.5 6.0 4.3 4.0 4.3 4.1 4.5 5.3 3.9
##  [973] 3.9 5.3 5.4 6.1 5.0 4.0 5.2 3.6 5.1 3.6 5.7 4.0 5.3 5.3 5.3 5.1 4.4 3.7
##  [991] 3.5 4.0 5.6 4.8 3.8 4.7 2.1 4.3 2.8 4.9
## 
## $func.thetastar
## 72% 
## 5.1 
## 
## $jack.boot.val
##  [1] 5.5 5.5 5.4 5.2 5.2 5.1 4.9 4.7 4.7 4.5
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
## [1] 0.183814
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
##   11.171291   16.665740 
##  ( 4.923157) ( 7.511898)
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
## [1]  1.6711384  0.1993139 -0.1883999 -0.2451246  0.6931862  0.4674812
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
##    [1] -1.1293097395  0.0005590591  0.0668611568  0.4802069788  0.3003459890
##    [6] -0.5290794470 -0.4049092643  0.2538719067  0.2355535822  0.0918412778
##   [11]  0.3103560455  0.0918412778  0.1506687081 -0.7317328916 -0.2581148843
##   [16]  0.0841459378  0.0330915993  0.3517699956 -0.4021698509  0.0095003776
##   [21]  0.1918264957 -1.1337348079  0.1066672475  0.1667693262  0.5966881879
##   [26] -0.1142455090 -0.0217013992  0.3797992212 -0.4510030030  0.9343214362
##   [31]  0.7580278005  0.3732245786  0.3275247312 -0.1526885316  0.4248723495
##   [36]  0.5257918372  0.5356918188 -1.4109121983  0.2208227045  0.7731594295
##   [41] -0.9956569161  0.2888660302  0.7892752768 -0.0575200131  1.1135071476
##   [46] -0.2917509034  0.2932355098  0.5192704162  0.9729041276 -0.2615894146
##   [51]  0.5057396583  0.4299038023  1.1310966307  0.2968039916 -0.4938751492
##   [56] -0.2454122701  0.6814575662 -0.0195275194  1.0499705373 -0.2507355365
##   [61]  0.8546039087  0.0322500087 -0.0585029669  1.0107132030 -0.5231432019
##   [66]  0.0542806753  0.2710259972 -0.0333734993 -0.2921053008 -0.6804031838
##   [71]  0.1421622869  0.4694140686  0.7154435306  0.3352168098  0.0358039339
##   [76]  0.8734860564 -0.1062346156  0.4504541898  0.4327766589  0.1215399943
##   [81] -0.7624019296 -0.3480879648  0.4225946918 -0.4201072809  0.5781341507
##   [86] -0.8214195555  0.6683243943  0.5250474718  0.3765395676 -0.5912526004
##   [91] -0.8910469703  0.4604559235  0.1588472159 -0.2551548808 -1.2025210994
##   [96] -0.0651363900 -0.1684002709  0.3557195874  0.2325672393  0.4295008322
##  [101]  0.7289055678  0.1530449076 -0.0653172742  0.9529392301 -0.2614864580
##  [106]  0.8732950938  1.4523886055  0.1823507508  0.0308553612  0.0425557006
##  [111]  0.5007457623 -0.2244675058 -0.1793856525  0.3224515578 -0.9054263433
##  [116] -0.3110893139 -0.0203908590 -0.3896168541 -0.5846832083 -0.0272866493
##  [121]  0.5959670881  0.2271005499 -0.1861419422  0.2045078969  1.8410599386
##  [126] -0.6189781402 -0.2899812378  1.8932073415 -0.3839124163  0.4719380489
##  [131]  0.4827946854  0.8804500728 -0.1150110012  0.2042570718  0.3967578814
##  [136] -0.7807103241  0.1821153394  1.3738263776  0.6569337909 -0.2401343705
##  [141] -0.1021345014 -0.2256632693 -0.0010767476  1.1639170133 -0.4138710742
##  [146]  0.0489406016  0.2500757975 -0.6679431193  0.4016346170  0.6794219699
##  [151]  0.5020352780  0.8366669149 -0.0504351613  0.4453503522 -0.2298240336
##  [156]  0.5622384321  1.6543904356  0.6752955365  0.2643903007 -0.1889315493
##  [161]  0.5759151924  0.1144971863 -0.0671334043  0.9300933223  0.5951165304
##  [166]  0.2440381623 -0.2080218054 -0.5253418034  0.0713382489  0.3334601153
##  [171]  2.1170495888  0.5161209907  0.0671028761 -0.2328734599  0.7617869404
##  [176]  1.0571166601  0.7587683091  0.6043126438 -0.6420812553  0.8507484330
##  [181]  0.2136090528  0.5798607114  0.6587687089 -0.0924451331  0.3025408275
##  [186] -0.6623731268  0.5574947013  0.1562343659  0.4561675437  0.0216882897
##  [191]  0.2198592920  0.7430881226 -0.3789586455  0.3093214389  0.5669262439
##  [196] -0.3862195298  1.0435688363 -0.0146404611 -0.6589115223 -0.5170732708
##  [201] -1.1149194242  1.2285145502  0.4192064156 -0.6325100818 -0.5618303047
##  [206]  0.8610201815  0.7115452803 -0.0580274118  0.1163603010  0.0279892046
##  [211]  0.4627989467  0.1802451608  0.0718765889 -0.0035338106  0.6989544412
##  [216]  0.1715074194  0.2245719372  0.0690018517 -0.3645640314  0.3085323199
##  [221]  0.2042965028  0.4627674835  0.4747884877 -0.0687607149 -0.4479634915
##  [226]  1.2753042603 -1.0278146659 -0.0810647834 -1.0406488301 -0.0244230183
##  [231]  0.5164345707 -0.6042717264  0.0006866663 -0.1567010548  0.3985770564
##  [236]  1.0390347194 -0.3495113848 -0.1953682688  0.6696194133  0.4657399330
##  [241]  0.0072406364 -0.1069041854  0.1027825315  0.5699120150 -1.1766248342
##  [246] -0.2916748625 -0.0903218430 -0.2277501787 -0.0004296908  0.0109695816
##  [251]  0.7851658619 -0.0758965781  0.1104796155 -0.2507356298 -0.5361255470
##  [256]  0.3400690297 -0.3968947776  0.4295752419  0.1050215344  0.3621510554
##  [261] -0.2312457350 -0.1820541228 -0.3599441181 -0.2594743082 -1.3193403544
##  [266]  0.0334930931 -0.5905718280 -0.2425639264 -0.5260580987  0.2479002389
##  [271] -0.5491878564  0.6468746582 -0.1393673672  0.4445915604 -0.7324683044
##  [276]  0.0114910707  0.5965259333 -0.2271523114  0.5427391811  0.0743558705
##  [281]  1.0764395644  0.8836192687 -0.8826829066 -0.1742753442  0.6352528142
##  [286]  0.5945067863 -0.1388971546  0.2241318233  0.3456476006  0.5504112287
##  [291]  0.7662856360 -0.1702715427 -0.1678997627 -0.5684665988 -0.0527798677
##  [296]  0.8955131548  0.5990917235 -0.8743110272  0.2596101920 -0.5897061614
##  [301] -0.1759710373  0.4877998128  0.4094880898  0.8983110252  0.5452470522
##  [306]  0.5135948851  0.0598799972  0.2963494847  0.0408853479  0.2555233575
##  [311] -0.0966444056  0.1562074933 -0.3455244175 -0.4521447430  0.7510841277
##  [316]  0.2522042171 -0.0907074519 -0.3839676522 -0.2186586341 -0.5821061457
##  [321] -0.0672014493 -0.6009293593  0.6717987039 -0.0118757429  1.0302271697
##  [326]  0.3021037515 -0.4583878494  0.0668705754  1.0166410062  0.1839178685
##  [331]  0.3782516813 -0.6424493795 -0.7215938812  0.5000232608 -0.4466146468
##  [336]  0.3729546231  0.5166987783  0.8086585562  0.1818528933  0.0655800175
##  [341]  0.8252003870 -1.3684774308  0.0779325670 -0.4519138196 -0.1643674195
##  [346]  0.1968980208 -0.0366591101 -0.0169058805 -0.6297588993  0.4372745884
##  [351]  0.7290970284 -0.1308964578 -0.4242326190  0.3299072682 -0.6375017215
##  [356] -0.4875639494  0.4003677448  0.5355683440  0.3678789867 -0.1361299389
##  [361] -0.2971259068  0.3480030768  0.6310500482  0.2081514818  0.3135563451
##  [366]  0.6633506506 -0.3161948700  0.1655088824 -0.4258763573 -1.0583806429
##  [371]  0.1657883197  0.0851334265 -0.2754191486  0.1664589266  0.2761771748
##  [376]  0.3535031719 -0.5169430611  0.0528495825  1.4051916418  0.6200074531
##  [381]  0.4840249141  1.0075037635  0.0718059576 -0.2868402908  0.6361693827
##  [386] -0.2457194234  0.3011649791 -0.2740102386  0.0507935783 -0.4493345750
##  [391]  0.1743748040  0.8643857978 -0.2803317070  0.4170745800  0.5220793887
##  [396] -0.3226608546  0.2491442354 -0.2110481956  0.5386672519  0.5627576656
##  [401]  1.2574427573  1.1470224091  0.4399374284  0.7670836954  0.1423240648
##  [406]  0.4642045077 -0.6572713020 -0.0546009090  0.7718324288  0.2980937829
##  [411]  0.2720751429  0.0739050476  0.2366168047 -0.7289258594 -0.2606477763
##  [416] -0.0781901843 -0.6321023494  0.5084851698 -0.5158183035 -0.5368552951
##  [421]  0.3909838350 -0.9183667341 -0.2686778882  0.4951373443 -0.2475519024
##  [426] -0.4479634915 -0.4207273849  0.3746808081 -0.2401343705  0.5359925459
##  [431]  0.3016667015 -0.3092366407  0.4402242273 -0.7420122866  0.2237689462
##  [436] -0.2981441547 -0.2820900775  0.3343995952 -0.2053546243 -0.7817478636
##  [441] -0.9161652012 -0.6090954138  0.1189929201 -0.1801020685 -0.2286058808
##  [446] -0.1228111835  0.3042817788  0.0266475153 -0.2805483772  0.6808292719
##  [451]  0.3756144290  0.2508490951  0.6495991280 -0.5469579850  0.2034588868
##  [456]  0.0564296854 -0.1269336211 -0.0844449164 -0.2227768291  0.7013788673
##  [461]  0.2504885103 -0.1195645989  0.1291029105 -0.0754486152  0.1433665736
##  [466]  0.4753673015 -0.2981441547 -0.6577660729  0.1383164822 -0.9316579285
##  [471] -0.6880080399 -0.0505231649  0.4451395237 -0.7118489408  1.0525685415
##  [476]  0.1218445884 -0.2661241757  0.1883104271 -1.3779838876 -0.1962191487
##  [481]  0.3808145638  0.1127375981 -0.9062011129  0.0981545305 -0.0664911369
##  [486]  0.3761666603  0.5748050260  1.1415902737 -0.3341488741  0.9515289478
##  [491] -0.7389365082 -0.0197315153 -0.3744244487 -0.5935978770  0.5816008470
##  [496] -0.2445247210  0.5779100498  0.0536237345  0.2252366364  0.6638262723
##  [501]  0.7473691094  0.2404088511  0.3449649349  1.0574191837  0.2287932781
##  [506]  0.2602774518  0.7475078314 -0.3664055907 -0.2658861566  0.6763332478
##  [511]  0.2352104523  0.3742587476  0.1838140089  0.2760992923  0.5283838265
##  [516] -0.1640557741  0.8415966489  0.0518842585  0.4123346491 -0.7301019101
##  [521]  0.5249044844  0.0413251716 -0.3696646722  0.4285804429  1.1863852458
##  [526]  0.3639164197  0.4457975409 -0.7464233362  0.3079183925 -0.2595329586
##  [531]  0.2036923490  0.2728611260 -0.6663328968  0.4295104470 -0.4530047944
##  [536]  0.2518710895 -0.3666076116 -0.4804495280 -0.9839508532 -0.3802715810
##  [541] -0.0089589613  0.4711812920  0.2934695060 -0.3049141882  0.5026300991
##  [546]  0.0549833441  1.1372483003  0.2479878451  0.1017993925 -0.7776569997
##  [551] -0.4263511370 -0.3124521159  1.2592471194 -0.0966812654 -0.0564691794
##  [556]  0.5767820858  0.2733112092  0.1953156307 -0.4782680228  0.6134885592
##  [561] -0.7226638852  0.0230149364 -0.1889459833  0.4923783824  0.1985100251
##  [566] -0.2342236002  0.0133730930  0.3713556846  0.1094010801 -0.4022494850
##  [571]  0.2157800457  0.7547161379  0.2629790352 -0.0118708131 -0.1553469433
##  [576] -0.1137626514 -0.7863239863  0.5260488859  0.2086053828  0.6629409088
##  [581]  0.2000218006 -0.0415152719 -0.3436067143 -0.7319556027 -0.7249258696
##  [586]  0.4814513578  0.5524358337  0.2428829336 -0.3888192928 -0.1579203527
##  [591]  0.3043261408 -0.0063815242 -0.0509575034  0.6520774855 -1.2740414392
##  [596] -0.1677878008 -0.1678910881 -0.4378226300 -0.1715933709 -0.5244710822
##  [601] -0.3165653308 -0.2931910495  0.4964202846  0.4138715945  0.4992851504
##  [606]  0.9594635212 -0.5448176946 -1.3884312689  0.8732044189 -0.3925451461
##  [611]  0.3391883830  0.4071961320 -0.0858942897 -0.8743110272 -0.1455263629
##  [616]  0.4137315321 -0.9185269261 -0.3421630747  0.4139776857  0.9384976335
##  [621] -1.3884312689  0.2631300649 -0.2242378040  0.3555840936 -0.6827804939
##  [626]  0.2479244957  0.5511469910 -0.6329012965  0.2585345931  0.6024017776
##  [631] -0.0547486380 -0.1506693317 -0.0658861684  0.8614158818 -0.2738246806
##  [636]  1.1213649580 -0.5818098742 -0.0190907892 -0.6227772387  0.0495761923
##  [641]  0.1065535351 -0.7014093765 -0.0253525651 -0.6200311110  0.3194484567
##  [646] -0.2622618785 -0.3854951956 -1.0278146659  0.6633020591  0.1990806835
##  [651]  0.1351324757 -0.4635371575 -0.0604219298  0.0242485417  0.6752955365
##  [656] -0.1084474225 -0.2834928620 -0.4541827375 -0.1503099841 -0.5844410319
##  [661]  0.3577909917  0.2062783990  0.0215417505 -0.9583592542 -0.3680313053
##  [666] -0.7993816510  0.4009098670 -0.6614041212  0.1385533875 -0.1252193524
##  [671]  0.4168615178  0.6255928537 -1.4335330150 -0.0824412776 -0.2613083543
##  [676]  0.4677579210  0.2835757130  0.5046676017 -0.0284003330 -0.3587906686
##  [681]  0.7829746568 -0.4445117274  0.4497409266  0.0551488843 -0.0383026928
##  [686]  0.5459638061 -0.8790947510 -1.1067782157 -0.1942974988 -0.7421324499
##  [691]  0.5649014961  0.3758394824  0.5020287457  0.5252634844  0.5831093737
##  [696]  0.2897007284  0.5804371892  0.4395482124 -0.5703893869  1.9536863855
##  [701] -0.4798845341  0.1990788598 -0.0292087451  0.5287528201  0.1085873447
##  [706] -0.0776996194 -0.2677840271  0.2933514117  0.1977551478 -0.0086039184
##  [711]  0.5196932093 -0.9879683935 -0.5622654600  0.4500387262  0.6091343972
##  [716] -0.7986626401  0.3831739938 -0.5249827951  0.3111816570 -0.1692896998
##  [721] -0.2070252037 -0.0256612073 -0.3703913543 -0.0718982542  0.2286969639
##  [726]  0.6004168026  0.4278128644  0.7231763134 -0.1709510626  1.5327884587
##  [731] -0.2371732684 -0.3082632252  0.2991162705 -0.4019755108 -0.3972926370
##  [736] -0.2969317720  0.2835757130 -0.2890125210  0.2101866748  0.1904754561
##  [741] -0.2118783542 -0.1448453182  0.7937024364  0.7438407391  1.2741039188
##  [746]  0.2254106030 -0.0537376706  1.1860560369  0.5048566815  0.3831739938
##  [751]  0.5006807477  0.6848397169 -0.4481211658 -0.0866799069  0.6131428384
##  [756] -0.0230115379  0.9428338312  0.6768630444  0.1791849849  0.3523177014
##  [761]  0.1026827713 -0.7998795393 -0.0573870995 -0.1808564466 -0.5713387097
##  [766]  0.5927867052 -0.0166174551  1.3551370928  0.3627748003  0.1989192414
##  [771]  0.2085888992 -0.3802715810 -0.5397119330 -0.0102388170 -0.1771647576
##  [776]  0.0877416079 -0.0623393316 -0.1914265806 -0.0192857285 -0.7014069754
##  [781] -0.0190298907  0.3569546189 -0.2187058859  0.4465453126  0.8268285392
##  [786] -0.1266746306  0.7862208295 -0.3329949798 -0.7437223956 -0.4802450802
##  [791] -1.3674635247  0.1559516912 -0.3478540849  0.4549191351 -0.3294037848
##  [796]  0.5704943770  0.4142537218  0.7908029087  0.7358709063 -0.0366454218
##  [801] -0.6227543753 -0.2322039512  0.2065384007 -0.2994843539  0.8558532411
##  [806] -0.2645962180  0.5362867756  0.6646486922  0.5810212859  0.3244949012
##  [811] -0.5893027523  0.9058112285  0.1132791524  0.0352046768 -0.5611456794
##  [816] -0.4438798910  0.2592488648  0.6010944982 -0.0872413164 -0.6416857261
##  [821]  0.4590884542  0.5349512100  0.0226820867 -0.4606889485  0.4296573937
##  [826] -0.4856805631 -0.1060017048  0.1757655383  0.0669864279 -1.3001395068
##  [831] -0.0370101292 -0.7035540191  1.0459841302 -0.3732014016  0.4814087210
##  [836]  0.7220752956 -0.4562480601 -0.3279135708  0.8997069540  0.5575298757
##  [841]  0.7434617352  0.2180856088  0.0637853074  0.2598045591  0.3863502497
##  [846]  0.1667599943 -0.3556301747  0.0315866221  0.8274270033 -0.2558591283
##  [851] -0.2506952464 -0.4124165938  0.2690674806  0.2696385666 -0.0901161759
##  [856]  0.2843895016 -0.3504108556 -0.0138358837 -0.0037211190 -0.3199520520
##  [861]  0.2703645604  0.2014693531  0.3400011201 -0.0662552909 -0.8739841650
##  [866] -0.0294629826  0.5684802665 -0.0129650612  0.5153085496 -0.3586032608
##  [871] -0.2282646482  0.3061695523  0.8193165076  1.3253564001 -0.4677778578
##  [876] -0.3903108824  0.3753138885  1.4957124178  0.9488242629 -0.0504517965
##  [881] -0.6468327326 -0.5305979427  0.2260586834  0.3972516776  0.4346123863
##  [886]  0.5246161401  0.0803722177  0.2477973699  0.6717712970  0.1046572513
##  [891]  0.1106306804  0.1937017719  1.1006468763 -0.2342236002  0.0272216018
##  [896]  0.0782473715 -0.2786719103  0.1422395348 -0.5092373901  0.3574966712
##  [901]  0.2771108125 -0.1526602811 -0.7229176235  0.9732460300  0.0206080691
##  [906]  0.4582291108  0.0632137820  1.0303781203  0.1803102727  0.0662491767
##  [911] -0.0007334373  0.3383379616  0.0833045225  0.3964266118  0.2711724593
##  [916] -0.3003962214  1.0346205957  0.1417638204  0.2593834988  0.2438160289
##  [921]  0.4878317872 -0.6092253559 -1.0494828824  1.4975388773 -0.0566210977
##  [926] -0.9666997927  0.1807380727  0.1254038556 -0.7083500511 -0.1080362770
##  [931] -0.8095035371  0.4345280176  0.1179070540  0.0924730730  1.1144130251
##  [936]  0.0992985494  0.2742422992 -0.7488637901 -0.3369371649  0.9817780667
##  [941] -0.5919242684  0.2339049684 -0.6703817803  0.6564725979  0.6159490427
##  [946]  0.4105332329 -0.5251252624  0.0441450024  0.5321998181  0.3968195859
##  [951]  0.8872100867  0.4285468008 -0.0622768090  0.2229297481  0.4432737601
##  [956]  0.2310196178 -0.1493103171  0.8821937405  0.1823370327 -0.7437504164
##  [961] -0.9936488603  0.8699229276  0.4981204386 -0.5501339905  0.3944221126
##  [966]  0.5861848222  0.7454363318  0.2632330493 -0.2047897600 -0.6301119247
##  [971]  0.5194440138 -0.3000908698 -0.4281807729 -0.4414826245  0.5526432403
##  [976]  0.2977163888 -0.1349686421  0.2069143265 -0.6985246467 -0.7332681854
##  [981]  0.0408096470 -0.7104366255 -0.6487694905 -0.2194271591  0.8494636430
##  [986]  0.2327113550  0.4939655954  0.5057932870  0.0939226417 -0.0703396512
##  [991] -0.1581628988 -1.2804012920 -0.0563318998 -0.4342799676  0.4732231803
##  [996]  0.2623830203 -0.2377868987  0.0255258170  0.0744834343  0.9495152197
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
##   0.67029904   0.19443568 
##  (0.06148596) (0.04347288)
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
## [1] -0.524969806 -0.003688268  0.960379204 -0.040438360  0.515575874
## [6] -0.239159110
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
## [1] -0.0363
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8895412
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
## t1*      4.5 -0.007407407   0.9315152
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 3 5 7 8 9 
## 1 2 1 2 1 1 2
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
## [1] -0.0291
```

```r
se.boot
```

```
## [1] 0.9215055
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

