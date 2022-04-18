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
## 1 2 3 5 6 7 8 9 
## 1 1 1 2 1 1 1 2
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
## [1] 0.0611
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
## [1] 2.742755
```

```r
UL.boot
```

```
## [1] 6.379445
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.7   6.4
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
##    [1] 5.0 4.3 3.4 5.1 4.8 3.4 5.7 5.6 3.8 4.2 6.2 5.0 5.6 5.0 4.0 4.6 5.5 6.0
##   [19] 4.8 5.2 4.5 4.5 3.0 4.4 3.5 5.3 3.9 5.6 4.2 3.4 6.9 4.2 5.7 6.1 5.2 4.5
##   [37] 5.4 5.7 4.1 5.2 5.1 5.1 4.0 4.3 5.1 5.0 3.4 4.8 3.0 4.0 4.2 4.6 4.0 5.4
##   [55] 3.1 4.9 3.0 4.7 4.2 5.0 4.1 4.4 5.1 5.2 5.3 4.4 3.6 4.6 6.0 4.2 4.5 3.7
##   [73] 5.4 4.6 4.3 4.7 3.6 4.6 4.9 3.4 4.8 5.6 5.1 4.2 3.7 3.5 5.4 5.8 4.4 4.7
##   [91] 5.7 4.1 4.3 4.8 3.9 3.7 3.9 3.4 4.4 4.1 6.0 4.5 5.3 4.9 5.1 4.3 3.1 3.3
##  [109] 4.3 4.1 6.5 5.2 4.9 4.3 3.1 4.1 2.8 4.1 3.7 3.8 4.6 4.0 4.5 2.9 5.7 5.5
##  [127] 4.3 5.7 5.4 5.3 4.6 4.4 4.0 4.6 4.2 4.5 5.1 3.7 4.8 4.2 5.4 4.5 3.9 4.8
##  [145] 4.3 4.7 4.8 4.8 4.9 4.5 5.2 4.0 4.4 4.4 4.4 4.6 5.3 3.8 4.9 4.9 6.0 5.5
##  [163] 4.1 3.4 4.1 4.9 6.9 6.1 4.1 3.1 3.7 4.2 5.1 3.1 5.5 4.0 4.8 5.0 5.7 5.7
##  [181] 4.6 3.5 5.1 4.6 4.5 4.9 3.8 2.9 4.3 4.3 5.5 5.1 3.5 3.3 4.5 4.9 4.0 4.5
##  [199] 5.3 5.1 2.5 3.4 7.2 5.0 4.4 4.5 5.0 4.0 5.5 4.5 3.9 5.1 4.6 4.4 4.3 4.9
##  [217] 6.1 5.9 3.6 4.6 4.1 4.9 5.6 5.3 5.1 5.8 4.5 6.6 5.4 4.6 4.6 4.1 5.5 3.2
##  [235] 4.6 5.7 5.5 5.4 5.4 4.2 3.9 4.7 4.3 4.1 4.3 2.7 4.8 5.1 5.2 4.0 4.6 5.3
##  [253] 4.4 5.9 3.3 3.9 4.0 3.6 3.5 4.3 3.8 3.7 3.2 4.2 2.7 4.2 4.3 6.2 3.7 4.5
##  [271] 4.1 4.3 3.7 5.3 5.3 4.9 5.5 4.2 7.4 5.1 5.4 3.2 3.2 5.1 6.1 5.3 5.2 3.2
##  [289] 3.7 5.4 4.4 6.2 5.7 4.7 5.0 6.9 5.2 4.1 4.3 2.4 4.7 3.6 3.2 4.7 4.2 4.3
##  [307] 3.4 5.8 6.2 4.6 5.7 5.1 4.1 5.1 4.0 5.5 4.3 4.4 5.9 4.7 3.9 5.2 5.2 4.8
##  [325] 5.8 4.1 5.0 2.9 5.6 5.4 4.8 5.0 6.6 4.2 4.9 5.0 3.0 5.3 4.3 4.3 3.4 5.4
##  [343] 5.3 7.1 6.8 3.4 4.6 4.3 4.7 3.5 5.1 4.9 6.9 4.0 3.5 2.3 4.6 2.9 3.5 3.3
##  [361] 6.6 4.7 4.4 4.2 6.4 6.0 3.7 5.0 5.7 4.6 6.0 3.6 4.7 6.0 4.7 3.8 5.8 3.2
##  [379] 5.3 4.2 3.6 3.8 4.5 4.2 4.5 4.7 4.2 5.3 4.8 6.0 3.6 4.6 5.0 5.2 3.4 6.6
##  [397] 4.2 5.0 4.3 5.1 4.3 5.2 4.5 5.6 4.8 6.2 4.8 4.4 4.9 4.3 4.3 4.8 6.1 5.7
##  [415] 4.3 4.2 5.9 6.1 4.7 4.7 5.5 6.4 3.3 5.9 4.1 3.0 4.5 4.5 4.4 4.6 5.0 4.3
##  [433] 3.2 3.3 4.2 4.3 5.4 4.5 2.8 4.2 3.2 3.6 4.6 5.9 3.5 2.8 4.4 3.9 4.4 5.0
##  [451] 3.3 5.4 5.9 3.1 6.0 3.9 4.1 5.8 5.1 5.1 5.9 4.1 4.9 5.0 3.9 4.7 3.0 4.1
##  [469] 3.0 5.4 3.7 5.2 4.1 4.3 5.9 4.9 5.4 3.1 5.8 4.9 5.8 4.6 3.8 5.8 4.1 4.2
##  [487] 4.2 3.3 4.2 4.6 5.6 3.6 4.9 5.6 5.1 5.3 4.6 6.0 4.9 4.5 4.8 2.9 3.7 4.6
##  [505] 5.4 4.8 5.3 4.5 4.5 3.3 5.1 5.6 5.0 3.7 4.7 4.2 4.7 4.1 6.0 4.7 4.1 4.8
##  [523] 4.6 5.3 5.4 4.5 3.7 3.9 4.7 4.7 5.3 4.3 4.1 5.9 3.4 3.1 5.2 3.4 4.2 3.1
##  [541] 4.3 5.5 4.1 4.9 4.2 5.4 3.3 5.0 3.2 4.0 5.2 4.9 4.9 5.9 5.2 4.4 2.0 5.6
##  [559] 4.4 4.2 5.2 4.5 7.3 5.9 4.0 3.8 3.8 2.5 4.9 3.5 4.8 3.8 4.7 4.2 3.4 5.3
##  [577] 4.4 5.5 4.0 4.5 3.8 4.8 5.2 4.0 4.7 3.9 3.6 4.1 4.5 4.8 4.2 4.3 4.3 4.5
##  [595] 3.4 4.9 4.2 5.8 4.7 4.2 2.9 2.8 3.9 4.0 4.2 5.0 6.0 4.6 5.2 5.8 4.8 3.7
##  [613] 4.1 3.9 4.6 5.9 3.6 7.4 3.3 4.5 5.2 4.2 5.1 3.7 4.4 5.0 3.7 3.3 3.1 3.3
##  [631] 3.6 5.0 4.1 5.1 5.2 3.9 5.3 4.8 4.1 5.7 5.4 5.9 4.0 4.6 3.0 4.8 5.2 4.7
##  [649] 2.9 4.7 4.9 2.9 4.1 4.5 3.1 5.2 5.3 4.6 5.1 3.2 3.6 3.6 3.9 5.9 5.2 4.7
##  [667] 4.5 3.6 4.0 4.2 4.4 4.8 5.1 5.2 5.0 5.6 3.9 3.9 3.5 3.0 3.5 5.3 4.6 2.1
##  [685] 6.1 5.2 3.8 5.3 6.0 3.4 4.3 3.8 5.4 4.7 5.5 5.5 3.4 5.7 5.2 5.1 5.0 3.7
##  [703] 3.4 4.5 3.4 5.6 4.4 5.1 3.8 4.5 5.0 4.8 5.1 4.5 4.4 4.9 4.9 3.3 4.2 4.2
##  [721] 3.5 6.6 3.7 2.5 3.2 4.4 4.6 5.7 3.6 2.6 4.7 2.8 3.9 3.6 5.4 4.5 4.2 2.5
##  [739] 4.3 4.2 4.9 3.9 4.4 4.3 4.5 4.2 2.7 5.5 4.0 5.2 4.2 5.2 3.8 4.1 5.1 3.1
##  [757] 4.4 5.1 5.2 5.0 4.6 5.6 3.3 4.8 3.9 2.9 2.3 3.8 4.3 3.8 5.7 6.1 2.6 4.3
##  [775] 6.4 4.7 4.9 4.3 4.3 4.0 2.3 5.4 3.9 5.2 3.9 5.5 4.1 4.8 2.4 5.7 4.8 6.6
##  [793] 4.2 3.3 4.2 4.1 6.1 5.0 4.6 4.7 4.7 2.9 4.6 5.4 3.1 4.6 4.8 4.0 4.6 5.2
##  [811] 4.8 4.5 6.1 4.8 4.7 5.6 3.2 3.5 4.6 4.3 5.2 5.1 5.9 5.6 4.4 3.4 5.4 3.0
##  [829] 4.6 4.4 3.9 4.9 2.4 4.3 4.5 3.9 5.3 5.3 4.1 5.0 4.4 3.8 5.3 4.1 5.5 4.0
##  [847] 4.7 3.3 5.5 4.5 4.4 4.5 4.4 3.5 3.6 4.7 3.6 4.6 4.0 4.2 2.4 3.0 4.2 4.4
##  [865] 4.6 3.8 4.7 4.7 4.5 3.1 4.2 4.7 3.8 5.7 5.4 5.0 5.9 3.6 6.1 5.0 5.9 4.9
##  [883] 4.2 4.1 5.7 3.7 4.9 4.0 3.7 3.5 3.4 4.5 5.0 4.2 4.1 2.8 3.3 5.6 2.8 5.3
##  [901] 2.8 3.7 4.8 4.8 4.4 5.2 4.3 4.5 5.6 3.9 2.9 3.7 4.9 2.7 5.5 3.5 4.2 6.1
##  [919] 4.0 4.7 4.6 3.8 4.6 3.8 4.3 5.6 6.2 5.4 3.7 6.3 6.0 6.5 4.1 3.2 4.3 3.9
##  [937] 4.9 4.4 4.4 5.1 4.8 3.1 3.8 6.1 4.6 4.5 3.4 4.4 3.4 5.9 5.1 4.5 5.1 3.6
##  [955] 3.5 4.8 4.9 3.7 4.7 4.2 2.6 2.5 4.6 4.6 3.4 4.6 4.9 3.7 4.5 4.0 4.4 4.6
##  [973] 3.8 3.8 4.5 4.7 5.3 4.8 5.0 5.4 5.3 5.1 3.2 6.8 4.5 6.9 4.4 3.1 4.8 4.8
##  [991] 3.8 3.9 5.2 4.5 4.8 5.4 6.2 4.1 5.5 3.7
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
##   2.8   6.2
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
##    [1] 4.6 6.0 5.0 4.3 2.3 5.2 4.3 4.0 3.9 4.8 5.8 3.7 3.3 4.5 3.7 4.5 3.9 4.0
##   [19] 4.4 3.5 5.8 4.1 2.8 5.9 4.9 5.4 2.0 4.2 3.7 4.0 3.4 3.6 4.7 4.7 4.8 4.2
##   [37] 3.7 4.7 4.3 5.2 4.7 3.7 5.4 3.9 5.7 3.7 4.5 4.1 4.9 4.0 4.6 4.0 4.5 5.8
##   [55] 3.4 2.9 5.5 4.1 3.6 6.2 4.5 5.0 4.1 4.1 4.9 4.4 2.4 4.8 3.5 4.1 4.8 4.4
##   [73] 5.2 4.4 5.4 4.4 6.5 4.2 5.3 4.7 5.1 3.6 4.8 4.9 5.2 3.0 4.4 3.9 5.2 5.5
##   [91] 4.5 4.7 4.1 5.6 5.2 3.0 6.1 4.5 4.8 3.8 5.5 4.7 3.9 5.2 4.5 5.8 2.8 3.5
##  [109] 4.5 5.2 3.8 4.3 4.6 5.5 6.6 3.7 3.3 4.7 5.3 5.1 3.7 4.3 3.0 4.3 4.9 2.9
##  [127] 5.3 4.5 4.5 4.9 4.7 4.2 4.1 3.7 6.5 4.1 4.3 5.2 4.7 3.7 5.2 5.7 4.5 4.5
##  [145] 3.4 6.2 3.7 5.3 3.5 4.7 4.2 3.7 6.4 4.9 3.8 4.3 3.1 6.2 6.1 5.6 5.2 3.1
##  [163] 3.9 3.5 3.6 4.0 5.5 3.4 4.4 5.8 5.3 4.1 4.4 5.3 4.5 2.7 4.4 4.4 3.2 6.1
##  [181] 4.0 4.6 4.7 4.0 4.5 5.5 6.2 6.0 4.7 4.7 4.8 4.1 5.9 4.7 4.4 4.3 5.3 4.1
##  [199] 5.3 5.0 4.1 4.7 4.2 3.7 6.2 3.7 2.7 3.5 3.6 5.6 5.0 3.5 4.1 2.3 6.2 5.6
##  [217] 5.0 3.4 5.4 5.2 3.7 5.3 4.6 4.1 3.5 4.0 5.3 3.6 5.2 4.4 4.4 5.1 4.9 6.4
##  [235] 4.8 4.6 5.0 4.0 4.5 5.3 5.7 5.2 4.0 3.5 5.2 4.7 3.7 3.3 6.1 3.3 5.8 4.3
##  [253] 3.2 3.6 3.8 4.8 6.4 5.8 4.9 4.8 3.9 5.2 3.3 5.2 5.2 3.5 4.6 4.3 5.6 4.5
##  [271] 4.7 4.8 4.9 2.8 5.4 4.2 3.2 4.0 4.5 4.4 5.8 4.3 4.6 4.6 4.5 5.0 3.3 4.2
##  [289] 3.6 4.7 6.9 3.7 5.2 5.0 2.9 4.7 4.1 3.2 4.1 5.6 4.4 5.6 4.9 3.8 5.2 4.5
##  [307] 6.8 4.9 4.3 3.1 4.9 3.7 2.9 4.8 2.2 5.7 3.6 4.6 4.4 6.9 4.7 4.1 6.3 4.1
##  [325] 4.8 4.4 5.5 4.6 3.8 5.1 4.5 4.1 4.5 3.5 5.5 4.1 3.7 4.3 6.1 4.9 3.5 4.9
##  [343] 3.6 5.4 3.3 2.4 4.5 5.0 4.2 4.5 3.2 4.5 3.1 3.8 4.8 4.6 5.7 5.1 5.7 5.3
##  [361] 3.9 4.4 2.5 4.6 5.6 3.7 3.7 4.7 5.4 4.4 5.7 3.3 4.3 4.3 5.4 4.0 6.0 3.8
##  [379] 4.5 4.0 5.4 3.2 4.5 4.1 5.3 5.8 4.5 4.9 3.6 4.6 4.5 4.1 4.0 4.3 3.9 2.6
##  [397] 3.6 3.9 4.8 4.6 5.6 4.3 6.2 2.7 4.7 6.0 4.9 3.7 5.8 3.8 3.8 3.3 3.3 6.9
##  [415] 5.0 3.9 3.5 4.9 5.0 4.0 5.7 4.4 5.3 4.2 5.1 4.8 4.7 5.1 5.0 5.0 4.6 5.3
##  [433] 4.7 6.0 3.6 3.9 4.2 6.6 5.0 4.8 5.4 3.5 3.6 3.0 5.8 3.0 5.6 4.8 4.6 3.8
##  [451] 5.6 4.7 4.6 4.5 2.9 3.9 3.5 3.0 5.3 3.8 5.4 4.3 4.0 4.8 4.2 4.4 4.7 5.6
##  [469] 4.2 5.2 4.1 3.7 4.4 3.4 4.8 4.6 4.9 5.1 6.0 4.1 5.5 6.1 5.9 4.2 4.7 5.1
##  [487] 4.5 6.3 3.7 4.8 5.4 3.3 5.1 5.7 3.9 4.7 5.1 4.7 4.2 4.2 4.4 6.6 4.4 5.2
##  [505] 3.4 3.0 4.3 5.1 5.1 4.1 4.4 6.3 5.0 4.3 5.1 5.8 4.7 3.8 5.5 4.0 3.2 4.8
##  [523] 3.4 4.8 4.2 4.7 4.5 5.1 3.8 5.1 6.9 3.6 6.1 4.5 2.2 3.7 4.6 6.0 3.8 5.0
##  [541] 5.3 5.2 4.6 3.4 3.6 4.9 4.3 4.3 4.6 3.9 5.1 4.7 4.6 3.2 3.8 4.1 2.7 4.0
##  [559] 4.2 5.2 5.8 5.4 3.8 4.9 2.7 1.3 5.0 4.9 4.9 4.8 4.0 4.9 3.5 3.6 3.3 5.4
##  [577] 4.8 4.8 4.6 3.5 5.6 5.7 5.9 4.4 5.3 5.4 4.6 6.4 4.5 4.5 3.3 6.4 3.6 6.5
##  [595] 5.4 4.7 5.0 4.9 2.8 5.3 5.0 3.8 5.0 3.1 4.3 4.8 6.3 4.1 4.8 4.0 6.7 3.6
##  [613] 4.6 4.9 3.3 4.4 3.9 4.8 4.8 6.2 4.9 3.0 3.8 4.7 5.0 5.0 4.7 4.8 5.1 4.7
##  [631] 4.3 4.8 3.7 4.6 3.7 4.8 5.5 4.2 3.9 4.5 5.5 3.0 4.1 5.1 2.7 3.6 4.1 3.6
##  [649] 3.5 6.6 4.8 6.0 4.6 5.2 4.2 3.6 4.2 3.4 3.6 3.8 4.2 4.4 4.0 4.7 6.3 3.6
##  [667] 4.4 5.4 4.5 3.9 4.8 4.4 4.5 5.1 3.5 3.6 4.8 3.9 3.3 4.1 6.1 7.1 4.2 5.8
##  [685] 3.7 4.9 4.9 4.0 3.9 4.0 4.9 6.0 4.6 3.5 5.0 4.0 5.2 5.5 2.1 5.0 2.2 5.1
##  [703] 2.6 4.8 4.0 2.9 3.7 4.1 1.7 5.3 4.0 3.9 4.6 5.1 4.9 2.9 3.9 4.6 4.5 5.5
##  [721] 4.7 5.4 4.4 3.4 4.4 4.1 3.2 5.8 4.7 4.7 4.6 4.4 3.9 5.9 4.3 4.1 3.3 2.8
##  [739] 3.6 4.5 3.9 4.1 3.0 4.6 5.3 4.7 4.2 3.9 3.5 6.0 4.4 6.0 5.1 5.2 4.5 5.2
##  [757] 3.5 3.6 3.9 4.7 5.1 4.3 3.8 4.2 6.0 5.3 4.1 5.5 5.5 3.5 4.9 3.5 5.1 4.3
##  [775] 4.7 6.2 6.1 3.3 6.2 4.4 4.7 3.0 2.9 3.6 5.2 4.0 4.0 4.7 4.1 2.5 4.4 5.1
##  [793] 6.3 6.3 5.6 3.1 4.3 5.1 5.5 5.6 4.6 6.0 4.1 3.0 5.0 5.3 5.1 5.4 4.3 4.7
##  [811] 4.9 4.1 4.3 2.4 4.0 5.4 3.3 3.3 2.5 4.6 3.7 4.5 4.6 5.5 5.5 2.3 4.7 2.2
##  [829] 3.6 2.7 4.4 5.2 5.1 4.3 4.9 4.5 5.3 5.5 5.4 3.5 3.2 5.1 5.1 6.5 5.8 5.2
##  [847] 3.5 5.1 5.9 3.1 3.6 4.6 4.1 3.9 4.7 4.5 3.5 5.9 5.2 4.5 4.9 4.5 4.2 4.1
##  [865] 5.1 3.1 4.4 5.9 5.0 5.3 4.3 4.5 3.6 5.3 6.6 6.2 2.6 4.4 6.7 5.5 2.9 3.7
##  [883] 4.4 3.1 3.6 4.0 4.2 4.6 4.2 4.2 4.8 4.8 5.1 4.4 5.7 4.6 4.9 5.3 4.8 3.4
##  [901] 4.0 4.4 4.5 4.4 3.4 5.3 4.9 3.4 4.6 4.2 5.1 4.9 3.5 4.4 3.2 5.6 5.6 4.3
##  [919] 4.5 4.4 5.1 4.8 3.5 5.0 5.7 5.1 4.9 4.9 3.9 5.9 3.4 3.2 4.5 4.0 4.3 5.8
##  [937] 3.6 5.4 6.2 4.5 3.5 5.3 3.1 3.0 5.3 3.3 4.2 3.1 5.0 4.5 2.6 5.6 4.8 4.6
##  [955] 5.8 4.9 5.6 3.9 6.1 5.8 5.2 3.8 4.6 4.9 4.2 3.7 3.9 5.4 5.1 4.8 4.3 3.3
##  [973] 3.1 3.8 3.5 4.2 4.9 4.6 4.1 4.3 5.1 4.5 3.6 4.4 4.0 6.0 5.3 4.9 5.3 5.6
##  [991] 6.7 3.4 4.9 3.6 5.5 3.8 4.4 4.5 2.4 3.9
## 
## $func.thetastar
## [1] 0.0084
## 
## $jack.boot.val
##  [1]  0.49011299  0.41278409  0.31253406  0.11267218  0.11416185 -0.02044199
##  [7] -0.13937677 -0.29826087 -0.36559767 -0.43236994
## 
## $jack.boot.se
## [1] 0.9294908
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
##    [1] 5.0 5.5 4.6 3.2 4.8 5.3 5.5 3.6 3.2 4.3 4.8 4.5 3.8 4.2 5.3 5.2 3.1 4.1
##   [19] 3.8 4.4 4.8 3.7 5.7 3.4 3.4 5.9 6.5 4.5 4.3 5.1 6.2 3.2 5.9 4.3 5.0 3.9
##   [37] 4.4 4.5 5.2 3.9 6.5 3.4 4.2 4.4 5.3 4.3 5.1 5.5 5.3 4.3 4.9 4.4 5.7 5.2
##   [55] 3.6 5.9 5.9 4.7 4.1 3.8 4.7 3.4 4.9 4.5 5.7 5.5 4.9 4.6 4.9 3.9 6.7 5.4
##   [73] 3.3 5.0 5.1 4.4 1.9 5.1 4.2 4.6 5.7 3.3 4.2 2.9 4.5 5.0 5.6 4.7 3.3 4.4
##   [91] 4.5 3.5 6.5 5.2 4.9 4.9 5.5 2.8 6.1 3.5 4.4 6.7 5.1 5.1 4.7 5.1 4.0 5.4
##  [109] 3.4 5.7 3.9 4.8 2.8 3.7 3.8 4.0 4.8 6.5 4.5 4.6 5.0 5.5 3.5 5.0 4.7 4.8
##  [127] 4.6 4.3 3.5 5.0 3.6 3.6 4.4 4.8 4.6 5.5 3.4 5.8 4.8 4.8 4.7 3.0 5.0 2.6
##  [145] 6.0 3.4 4.4 3.8 6.0 5.0 4.4 4.3 4.1 5.7 5.5 5.5 5.1 3.6 5.7 3.1 5.1 3.7
##  [163] 5.5 5.7 5.1 5.7 7.1 3.2 3.8 6.8 5.1 5.3 5.4 4.5 5.2 5.4 4.1 4.4 5.3 4.7
##  [181] 3.6 4.8 5.5 4.0 5.7 3.8 3.4 5.5 6.0 3.8 3.5 4.0 4.5 5.5 5.3 5.4 4.5 4.7
##  [199] 4.3 4.7 4.3 4.6 4.0 4.2 5.0 5.0 2.2 2.6 5.3 3.4 5.0 4.9 5.5 3.9 4.8 5.1
##  [217] 3.8 4.9 5.0 4.7 3.5 5.2 5.7 5.1 4.4 4.6 4.3 5.1 4.7 6.3 3.3 4.4 3.4 4.9
##  [235] 3.3 4.7 5.0 6.5 2.9 3.8 3.8 5.9 4.1 4.2 3.2 4.4 4.1 4.1 3.9 6.7 3.4 5.1
##  [253] 5.0 3.1 4.6 3.1 4.3 2.8 3.0 4.3 2.6 2.5 4.4 3.8 5.2 5.7 4.3 4.2 4.0 5.9
##  [271] 6.4 5.1 5.9 4.1 4.0 5.4 3.8 4.5 5.9 4.8 5.4 4.8 4.7 5.0 5.1 5.6 3.6 4.7
##  [289] 3.4 4.2 2.9 5.3 4.1 5.0 5.5 5.3 4.9 5.7 3.2 4.5 4.8 5.3 4.5 2.7 4.2 5.2
##  [307] 4.8 4.1 4.6 5.4 3.9 5.1 3.2 5.2 4.0 3.1 5.3 3.3 4.7 5.8 3.6 4.2 7.0 5.2
##  [325] 4.1 4.5 3.5 3.0 5.3 3.9 4.2 5.6 4.0 5.2 6.3 5.7 4.9 3.7 5.2 4.1 2.4 3.9
##  [343] 3.7 6.5 5.6 4.2 4.8 3.7 5.1 3.5 3.6 4.3 4.6 5.3 2.7 4.8 4.3 5.0 3.1 4.5
##  [361] 4.6 2.4 3.3 4.0 4.9 4.2 5.2 4.2 3.3 4.3 6.1 4.7 3.5 6.9 4.1 4.2 4.5 4.3
##  [379] 4.8 4.8 4.7 5.1 5.0 5.3 3.4 4.0 4.7 2.3 4.7 4.2 5.3 2.7 3.5 4.5 4.4 5.9
##  [397] 3.8 5.4 4.7 4.6 5.6 4.7 4.9 5.0 4.9 3.2 5.8 4.8 4.9 4.1 4.7 4.8 3.9 6.5
##  [415] 5.2 4.8 3.8 4.0 3.6 4.3 4.1 4.7 3.0 5.2 5.6 4.4 2.7 5.2 4.0 5.7 4.9 3.3
##  [433] 4.7 6.0 6.1 5.6 4.7 4.5 5.3 3.2 5.4 4.1 4.5 4.4 5.4 3.9 3.4 5.4 4.5 4.0
##  [451] 4.2 4.0 3.8 3.8 5.3 5.0 4.4 6.0 2.8 4.4 3.6 4.3 3.2 6.3 3.5 5.5 3.4 4.4
##  [469] 4.5 3.0 5.5 3.4 3.4 4.7 4.5 4.0 4.0 5.1 2.6 5.1 4.7 5.4 4.7 4.7 3.3 4.5
##  [487] 4.4 4.5 5.1 4.8 3.3 4.5 3.6 6.1 3.8 6.2 3.8 4.1 4.0 4.9 3.5 4.6 3.3 4.5
##  [505] 5.3 6.1 5.4 6.3 3.7 4.8 4.4 4.9 5.1 4.1 4.2 5.5 3.8 4.3 3.3 4.1 4.1 4.6
##  [523] 3.9 3.5 5.3 6.3 2.9 6.6 4.8 4.2 3.7 4.2 5.6 3.9 3.7 4.8 2.7 4.9 3.7 4.5
##  [541] 3.3 3.6 3.1 3.5 3.9 4.6 4.0 3.4 4.0 5.4 3.3 3.8 6.2 4.2 5.5 5.4 5.4 4.3
##  [559] 4.8 4.4 2.5 4.9 5.2 4.9 5.1 4.1 3.8 3.6 2.8 6.2 3.8 5.0 5.2 5.4 3.9 6.3
##  [577] 4.1 4.1 5.3 4.6 5.5 5.0 4.0 3.9 3.4 3.9 4.6 4.7 4.5 3.2 5.6 4.3 5.0 4.8
##  [595] 2.9 3.9 5.0 4.8 4.0 4.9 6.3 5.4 4.7 5.4 4.8 4.2 4.0 4.0 4.2 3.0 3.5 4.0
##  [613] 4.1 5.0 4.2 5.0 5.0 4.3 4.7 3.7 5.0 4.0 4.5 3.5 4.4 4.4 5.2 5.7 5.6 4.3
##  [631] 5.3 5.0 4.6 5.5 4.8 3.6 4.7 6.0 4.9 6.6 3.7 4.2 4.6 4.6 4.6 3.1 4.0 4.9
##  [649] 2.5 4.8 3.7 3.9 5.2 4.3 5.1 3.9 5.2 2.8 6.0 4.4 4.2 6.3 4.5 3.6 4.4 5.1
##  [667] 4.0 3.4 4.1 3.6 3.7 3.9 4.0 4.8 4.2 3.1 4.3 3.9 3.6 2.8 4.4 4.6 4.3 4.8
##  [685] 4.6 4.1 4.6 5.6 4.2 3.8 5.2 6.1 6.0 4.6 3.1 4.7 6.5 5.4 4.0 6.0 3.9 5.7
##  [703] 5.4 4.7 5.7 4.7 3.7 4.4 3.9 6.0 3.3 5.4 3.8 3.7 3.2 5.1 4.3 2.9 3.7 6.3
##  [721] 4.6 3.9 4.2 5.3 4.3 3.9 4.1 5.1 4.1 4.8 6.0 3.2 4.3 6.1 5.3 4.5 3.6 5.1
##  [739] 4.2 4.6 5.4 4.9 4.3 3.7 3.4 5.0 5.9 3.7 4.6 6.7 5.1 5.4 5.0 4.1 4.0 4.1
##  [757] 5.8 4.7 4.0 2.2 4.3 4.5 3.6 3.7 4.6 3.3 3.8 4.1 2.5 3.8 3.3 5.2 3.4 3.8
##  [775] 5.2 5.7 4.3 5.3 5.3 3.3 6.0 4.8 2.8 2.8 5.5 4.5 2.4 5.0 3.4 2.9 2.6 4.9
##  [793] 2.8 5.4 4.1 2.9 4.0 5.9 3.8 2.7 4.8 5.3 4.3 3.6 3.9 4.5 4.4 3.0 4.4 3.8
##  [811] 5.4 4.8 4.1 4.3 5.8 3.6 3.1 5.0 3.5 5.3 4.0 5.2 4.3 4.9 4.1 4.0 5.9 4.7
##  [829] 5.5 4.8 4.1 4.4 4.6 5.5 5.6 4.8 4.6 4.1 5.6 3.7 4.0 3.4 4.9 5.5 5.5 3.7
##  [847] 4.2 3.2 5.9 5.3 3.7 4.0 4.7 5.5 5.9 5.1 4.8 4.8 3.8 3.1 5.2 4.5 3.9 3.7
##  [865] 5.1 4.7 4.1 4.8 4.6 5.1 4.0 3.1 3.3 5.7 3.3 4.6 5.1 5.2 2.8 4.0 5.1 3.8
##  [883] 2.9 5.0 3.9 4.3 4.1 1.7 3.8 3.5 3.0 5.2 3.9 4.0 6.4 5.2 3.4 5.0 4.5 4.6
##  [901] 4.9 4.3 4.6 3.3 5.3 5.6 5.5 3.9 5.0 4.5 6.1 4.7 4.5 6.3 4.7 3.7 5.1 5.8
##  [919] 3.8 4.7 3.5 5.4 4.7 6.1 6.3 3.9 3.9 4.1 4.3 4.6 2.4 4.4 4.2 4.9 4.6 5.1
##  [937] 4.7 4.3 5.2 5.3 3.8 4.1 2.8 3.9 5.0 3.9 4.2 3.8 4.2 4.0 3.2 5.1 5.8 4.3
##  [955] 5.3 4.4 3.2 4.5 4.9 4.7 3.6 2.8 4.0 4.4 4.7 5.4 5.8 4.6 4.7 3.2 4.5 4.5
##  [973] 5.4 5.3 4.7 4.9 3.6 3.8 4.6 3.9 5.0 2.6 4.9 4.2 4.0 5.6 4.3 3.9 4.8 4.7
##  [991] 4.3 4.4 3.2 3.6 5.0 5.5 5.6 5.2 3.5 4.8
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.500 5.396 5.400 5.200 5.200 5.088 4.900 4.700 4.500 4.500
## 
## $jack.boot.se
## [1] 1.05633
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
## [1] -1.185985
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
##    9.016571   11.732187 
##  ( 3.959974) ( 5.298706)
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
## [1] 0.30490900 0.04864667 0.84901484 0.81918762 0.39704130 0.73207590
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
##    [1] -0.18533573 -0.53640638 -1.20596567 -1.15499984 -1.14131762 -0.26273283
##    [7] -0.96228015 -1.23915793 -0.58441427 -0.89084653 -0.40955261 -0.72052721
##   [13] -1.17347701 -1.60688495 -0.94766794 -0.60576238 -2.28151653 -1.40781986
##   [19] -0.97563932 -1.47360774 -0.58750352 -1.33846968 -0.75012766 -1.22923040
##   [25] -1.73993302 -0.15059752 -0.48846133 -0.71745953 -1.40890160 -1.23083765
##   [31] -0.50985073 -1.90675635 -0.83845343 -0.92436755 -0.94536665 -1.46109101
##   [37] -1.76783123 -0.55550010 -1.44940192 -0.53557548 -2.60543391 -0.34268648
##   [43] -1.24678265 -0.93960663 -1.25043324 -0.44390749 -1.17774335 -0.58605266
##   [49] -0.37057003 -0.66758281 -1.84417397 -0.55385319 -1.59359205  0.61661845
##   [55] -0.95536017 -1.45962578 -0.46897121 -1.25068541 -0.99388462 -0.91612712
##   [61] -1.80418957 -0.92320199 -0.53871113 -1.40978670 -0.77993591 -0.95126224
##   [67] -1.02664743 -0.93790080 -0.49724338 -0.98433892 -0.90977006 -2.60095278
##   [73] -0.52461422 -1.03831302  0.94375260 -1.02669665 -2.13782722 -1.25130590
##   [79] -1.32488545 -1.00708224 -2.06875629 -0.98792088 -0.92641146 -1.82077686
##   [85] -1.16072947 -2.09345960 -1.09901842 -0.35622278 -0.73710197 -2.08283008
##   [91] -0.62157265 -0.53033380 -0.16097986 -0.69456167 -2.37623736 -0.38339990
##   [97] -1.53308303 -1.48976632 -1.50755900 -0.74425179 -0.62143507 -1.26847417
##  [103] -0.47551091 -0.11233102 -1.04726292 -0.87553670 -2.10563508 -0.32728447
##  [109] -0.73270829 -1.77366608 -0.93712876 -0.82445104 -0.76015442 -2.29777710
##  [115] -2.09905137 -1.25343063 -2.15399653 -1.06456139  0.21155983 -1.80732562
##  [121] -1.12099779 -0.86769022 -0.73546399 -1.45230737 -0.76653120 -1.05595375
##  [127] -2.19244523 -0.67770214 -0.95922904 -1.73158368 -0.34636455 -0.15610979
##  [133] -1.43996295 -1.18345966 -0.36195023 -0.61561514 -1.77217026 -0.94355899
##  [139] -1.83205648  0.08251150 -1.73451092 -1.01941128 -0.97649551 -0.96404591
##  [145] -0.64385955  0.26362125 -0.82379525 -0.38608195 -1.23945383 -0.81095746
##  [151] -1.52866342 -1.46519475 -0.71521575 -1.32436350 -0.34078256 -0.77505336
##  [157] -1.06554165 -0.32004144 -1.16134647 -0.50946814 -0.64627439 -1.53455006
##  [163] -0.23148087 -0.35065545 -0.55501888 -1.65356382 -2.57496813 -2.42575278
##  [169] -1.24524056 -0.93715412 -1.26405374 -0.06720283 -1.23632039 -0.31995101
##  [175] -0.94100920  0.67411874 -0.70553806 -1.44323260 -0.50273296 -2.11526879
##  [181] -1.50299252 -0.11876004 -1.26335183 -0.52722941 -1.47554860 -1.43628408
##  [187] -0.75437696 -0.60412962 -0.03511555 -0.31638578  0.11997978 -0.16008610
##  [193] -0.85897116 -0.76774745 -2.44126560 -0.94484336 -0.58078772 -1.24844368
##  [199] -0.97518533 -2.12599108 -1.39592197 -0.95042443 -0.79699177 -0.92802062
##  [205] -0.99846549 -0.81440683 -2.48922796 -1.27661735 -1.08085567 -2.01220681
##  [211] -1.05407654  0.01358718 -2.15056097 -0.80032860 -0.89976126 -2.38161208
##  [217] -0.83340056 -0.21523920 -0.42367693 -1.64547539 -0.16382340 -1.92997475
##  [223] -0.55939274 -1.51827733 -0.89387460 -0.95719343 -0.79862638 -1.02634922
##  [229] -0.96823704 -0.69346720 -0.68597564 -0.95213752 -1.27066722 -0.55303017
##  [235] -0.55437067 -2.17088006 -1.56814193 -0.89151708 -2.09447014 -2.27675208
##  [241] -0.16159672 -0.66317260 -1.10611890 -2.63155305 -0.98806852 -1.77322424
##  [247] -0.96442564 -0.71307346 -1.21190226 -0.85638345 -0.47301108 -1.73426028
##  [253] -0.06853430 -0.54367987 -0.81205744 -1.45351533 -0.96211230 -0.79140766
##  [259] -0.47186020 -1.43287802 -1.45257333 -2.09400035 -0.08363596 -0.72089447
##  [265] -0.64961772 -0.39463327 -0.66267287 -0.15913010 -0.89187859 -0.84493029
##  [271] -0.56520167 -0.43111545 -0.24813991 -0.51374119 -0.13263142 -1.40587182
##  [277] -0.64524811 -1.05677637 -1.15499984 -0.25611608 -2.12666960 -0.56286990
##  [283] -1.62794435  1.00400730 -1.15642405 -1.49948406 -2.50075381 -0.62361565
##  [289] -1.19404815 -0.92474166 -2.04835028 -1.31572876 -1.29311444 -1.20574391
##  [295] -0.89456671 -0.64730343 -0.93543835 -0.14589107 -1.63064911 -1.51772267
##  [301] -0.76231269 -0.93339793 -0.47248955  0.55568266 -0.64337846  0.24441459
##  [307] -2.50960988 -2.51730130 -0.24989120 -1.41521841 -1.18393764 -0.65682900
##  [313] -2.03679478 -1.25583808 -1.50390272 -0.99202970 -0.47215735  0.03459529
##  [319] -0.36508693 -2.44958909 -2.60037215 -1.45908993 -0.28124537 -0.79545150
##  [325] -0.87689854 -0.77644469 -0.12005967 -0.53160689 -0.58361029 -0.53030656
##  [331] -0.96097664 -1.50673976 -0.85703499 -1.43771323 -1.87337324 -0.63616712
##  [337] -1.68418233 -1.65122919 -0.84044387 -0.96752726 -0.75115828 -0.57342217
##  [343] -1.99559615 -0.66007658 -0.72487969  0.04182835 -2.32913747 -2.31664437
##  [349] -0.48470184 -0.42736679 -1.25855043 -1.28567392 -0.88897809 -1.43784703
##  [355] -0.28366165 -0.59598232 -2.17848695 -2.19580565 -1.22465399 -0.14993482
##  [361] -0.55095026 -0.06580072 -0.99212402 -0.32996879 -0.83735051 -2.03215003
##  [367] -1.34899550 -1.00387727 -2.26119493 -1.27892004 -0.99495564 -0.31240881
##  [373] -0.91118349 -1.75052649 -0.80737565 -1.47747470 -0.72562662 -1.68155361
##  [379] -0.34389640 -0.88833335 -0.62587402 -0.24440240 -1.77763785 -0.94525946
##  [385] -0.95069712 -0.47322992 -0.45986604 -1.26318140 -0.53730356 -0.61891828
##  [391] -0.79369178 -0.39035627 -1.70144729 -0.31403434 -0.73386722 -0.18482268
##  [397] -2.31640486 -1.07538991 -0.83638001 -1.46547907 -1.35693909 -0.48435979
##  [403] -0.99158008 -1.17675743 -1.44671672  0.23865641 -1.02331178 -1.18256889
##  [409] -0.80403677 -0.99872996 -0.71017835 -2.20195852 -0.96930109 -0.61577539
##  [415] -2.25386695 -0.32624889 -1.41586853 -1.21689916 -2.60226088 -0.94400441
##  [421] -0.61224195 -0.33437192 -0.80902973 -1.16717996 -0.88341349 -2.09553130
##  [427] -1.50076370 -0.68026912 -2.08681232 -1.33495927 -0.70908226 -0.98265725
##  [433] -1.21531048 -1.72467686 -0.18250569 -2.54258220  0.05636547 -0.19937888
##  [439] -1.05529473 -0.56497325 -2.37312804 -1.13815202 -0.44261412 -0.50188315
##  [445] -0.75826214 -0.71839379 -1.46625955 -1.51176602 -1.21052768 -0.50824043
##  [451] -0.59236354 -0.17211401 -1.27224158 -0.85604908 -0.80725512 -1.19217999
##  [457] -1.37844302 -1.50583064 -0.95056147 -1.37806543 -0.21859526 -1.38794219
##  [463] -2.63890201 -2.20803051 -1.24061956 -0.84693844 -1.44409708 -0.74562878
##  [469] -1.51708626 -0.31475874 -1.02592125 -0.77492216 -1.08366496 -0.48219126
##  [475] -1.52287687 -0.98052487 -0.45062377 -0.93321822 -1.20350625 -1.68519378
##  [481] -0.49319654 -1.69060478 -0.96032766 -1.40363790 -0.94929239 -1.15399841
##  [487] -1.39993468 -0.81559231 -0.73291018 -2.01751145 -0.70098617 -0.72487034
##  [493] -2.21422540 -0.31422834 -0.78721967 -2.01154155 -1.19147449 -0.50406884
##  [499] -0.92498745 -1.83182264 -0.84550360 -0.74807220 -1.37915678 -0.91166837
##  [505] -1.50716309 -0.75433744 -1.22161126 -0.58073319 -1.69455827 -0.27005706
##  [511] -0.47640380 -2.09918904 -1.16943243 -0.59774574 -0.14059532 -0.13899968
##  [517] -0.77565039 -0.29933963 -1.26718553 -0.72066627 -1.02387802 -2.07770683
##  [523] -0.50989665 -0.75803717 -1.48322375 -2.19839128 -1.15835290 -0.55870989
##  [529] -0.97676469 -2.14033758 -0.91920511 -0.72652044 -2.15640631 -1.38443142
##  [535] -1.41794704 -1.42226542 -1.47756931 -0.60002145 -0.14326362 -0.71199732
##  [541] -1.17895882 -0.56846489  0.22678850 -1.18902258 -0.90143590 -1.47434666
##  [547] -0.58818791 -0.18560623 -0.56218495 -0.31079678 -0.12724578 -0.70213804
##  [553] -0.98816192 -0.19929428 -1.40310734 -0.53051125 -0.60760555  0.67384994
##  [559] -1.18455959 -0.44993057 -1.40611614 -0.86989418 -0.78148180 -1.62466287
##  [565] -1.45916932 -0.13291784 -0.65238331 -1.19053999 -0.84052638 -1.09529429
##  [571] -0.67123063 -1.46579474 -1.54499119 -0.09906501 -0.86341526 -0.92584493
##  [577] -0.17659401 -1.20470703 -0.58282673 -0.49430275 -1.42378144 -0.78512002
##  [583] -2.14593038 -2.04339939 -0.94445511 -0.52713411 -0.62039224 -1.48034500
##  [589] -2.12601798 -1.94503632 -0.76136640 -1.44495925 -1.77492581 -0.94116210
##  [595] -0.57955757 -0.94281846 -0.38510118  0.19507728 -0.45396880 -1.23723858
##  [601] -0.93717103 -1.08085567 -1.51715036 -0.96027024 -0.22871133 -0.81191821
##  [607] -1.13552483 -0.53722794 -1.63188898 -1.18888437 -0.99335068 -0.52024441
##  [613] -1.69335776 -2.01632050 -1.17625038 -0.56938393 -1.89763360 -0.87702331
##  [619] -1.52352877 -1.22863615 -0.92337560 -1.70182682 -1.77783591 -1.26347227
##  [625] -0.61649190 -1.47313305 -1.23340240 -0.37013840 -0.89210296 -1.08719632
##  [631] -1.40445421 -0.88047444 -0.83484555 -0.54398631  0.01068657 -0.16887336
##  [637] -1.80764590 -2.36333272 -1.49266985 -0.76446672 -1.07669750 -1.23213738
##  [643] -0.65333998 -0.44832903 -1.38668598 -0.95286672 -0.46232553 -0.96039994
##  [649] -1.48651592 -1.20000004 -0.93382021 -0.33858095 -0.21454236 -0.56562913
##  [655] -0.55460154 -0.54318200 -0.85035575 -1.39857976 -0.88583624 -1.36995093
##  [661] -0.85589504 -1.40166603 -1.44523056 -0.86074964  0.08004939 -0.53099630
##  [667] -2.12734414 -0.57896563 -1.40317750 -1.48084685 -0.69858380 -1.23235994
##  [673] -0.86264048 -1.43777798 -0.88266289 -0.92103673 -1.40534166 -2.12188289
##  [679] -0.38967842 -0.18992717 -0.69046434 -1.25543502 -1.21571141 -0.49718201
##  [685] -1.36537096 -0.63333832 -1.36696345 -1.06345582 -0.75947440 -0.24440240
##  [691] -1.15776924 -0.34662915 -1.47229603 -1.44343083 -0.80964644 -0.42145336
##  [697] -1.07607236 -0.94257721 -0.90427990 -1.02152081 -0.49414271 -1.12487794
##  [703] -0.88806304 -1.37818173 -0.15449955 -1.52068772 -0.57380560 -0.89387460
##  [709] -2.06662853 -0.74730833 -0.51301093 -0.22350076 -0.92219758 -0.57718810
##  [715] -1.69028113 -0.72548171 -0.46796436 -1.41798565 -1.63002961 -1.56623177
##  [721] -0.56345641 -0.83588216 -0.09051453 -0.27332275 -0.19011327 -0.68548245
##  [727] -0.74864151 -0.38053756  0.19644327 -1.79055024 -0.38831746 -0.70213804
##  [733] -1.42820283 -1.64800397 -0.25283309 -0.30968515 -0.70860407 -0.57665449
##  [739] -0.11449001  0.10677858 -0.11368498 -0.59389308 -1.19585807 -0.68981111
##  [745] -1.75152773 -0.60127234 -0.84727457 -0.76804454 -1.50755900 -0.45981695
##  [751] -0.53766794 -0.94609953 -1.39519123 -0.98429510 -0.56315426 -2.17808694
##  [757] -0.86836898  0.01725574  0.17052879 -0.95084065 -0.34918421 -0.20091545
##  [763] -0.74935695  0.53907356 -0.56430588 -0.38793668 -0.57218799 -0.67625748
##  [769] -2.11025887 -1.51277856 -2.59703272 -1.50372205 -0.73458736 -1.22483429
##  [775] -0.51340893 -1.67099540 -0.20133799 -1.49727128 -0.95212005  0.05210247
##  [781] -2.09271662 -1.53496445 -0.18513398 -0.84199240 -2.04574417 -0.44588873
##  [787] -1.43736779 -2.24056835 -1.10757651 -0.76752427 -2.03617882 -0.58321456
##  [793] -1.01730697 -1.21857194 -0.71303694 -0.86605084 -0.47619580 -2.05563895
##  [799] -1.06345582 -2.13490085 -0.79869363 -0.55391698 -1.52617212 -1.03764938
##  [805] -0.99485004 -1.25560149 -0.57464678 -1.01527792  0.18277306 -0.85500169
##  [811] -0.35044558 -1.18262727 -0.19289272 -1.57431828 -1.48581114  0.22202974
##  [817] -0.55538982 -0.38781902 -0.95446628 -0.52160637 -0.99412206 -1.47135466
##  [823] -1.28227199 -0.63557940 -1.49869930 -0.76767789 -0.89599424 -0.94014165
##  [829] -1.14225802 -1.53694642 -1.25816765 -0.75318554 -0.72446602 -1.32572123
##  [835] -0.75999747 -1.68667837 -0.75237355 -1.50732388 -0.27104409 -1.45054740
##  [841] -2.20500941 -1.42498272 -2.30330706 -0.92313747 -0.54479673 -0.71666859
##  [847] -0.37640260 -2.60587838 -0.12534516 -2.35201022 -0.34619182 -1.33394735
##  [853] -0.57754776 -0.71112823 -0.90797594 -0.61933044 -0.76538166 -0.24987660
##  [859] -1.08485897 -1.04043484 -0.10410419 -0.41886469 -0.28785688 -0.50166530
##  [865] -1.47234407 -1.04775466 -0.55926633 -0.43714122 -0.35050751 -1.23871789
##  [871] -0.29418552 -0.48484836 -0.47452334 -0.80983886 -0.71341343 -0.14300954
##  [877] -0.87928682 -1.65331646 -1.70996267 -0.93651812 -1.78356467 -0.52114509
##  [883] -2.05455936 -0.92655049 -1.06600012 -2.09629659 -1.12093722 -0.78770361
##  [889] -0.40814641 -0.99692431 -1.31152119 -0.54026216 -0.98098224 -0.27521287
##  [895] -1.22126006 -0.91321652 -0.69706480 -0.12253346 -0.81030255 -0.92675456
##  [901]  0.12443232 -1.38388665 -1.46386096 -0.90442610 -0.86347195 -0.32684338
##  [907] -0.51641614 -0.62261846 -1.52305653 -0.97695254 -1.46503835 -1.24353360
##  [913] -0.29564935 -1.13985133 -1.43464167  0.15727082 -0.86607532 -1.39769286
##  [919] -0.79001962  0.17999868 -1.78722380 -0.19200127 -1.08215888 -0.48317768
##  [925] -0.27166709 -0.96430358 -1.43289480 -0.25190029 -0.41813803 -0.46023697
##  [931]  0.03801875 -0.45803455 -0.71919315 -0.87137740 -1.17755483 -0.77994927
##  [937] -1.47547845 -0.54398631 -2.13527571 -1.78066365 -1.19390222 -2.04828652
##  [943] -1.51553715 -1.45458691 -0.79545735  0.44221617 -0.56041211 -1.33928851
##  [949] -1.04123897 -0.75978764 -1.21891774 -0.85427065 -1.08108633 -1.28739881
##  [955] -0.51322668 -0.58920099 -0.74818157 -1.24137507  0.10632575 -0.97203845
##  [961] -0.45893645 -1.47027312 -1.67323944 -0.69250116 -1.43263669 -0.12290988
##  [967] -2.06315223 -2.55328744 -0.92807833 -1.26369978 -0.92385882 -1.04546725
##  [973] -1.28026132 -0.46261608 -0.53270952 -0.71438910 -0.78356774  0.55407893
##  [979] -1.06060170 -0.27491436 -0.80059393 -1.01872844 -1.77521055 -1.37014118
##  [985] -0.49056779 -0.73143838 -0.73466454 -2.52209186 -1.36754518 -0.41326192
##  [991] -0.72054009  0.20410053 -1.05477244 -0.73147716 -1.38330805 -0.57540227
##  [997]  0.18208804 -2.00055242 -0.71057577 -1.05868713
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
##   0.76852854   0.21211489 
##  (0.06707662) (0.04742671)
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
## [1] -0.5533450 -0.5688113  0.0398903 -0.5396038  0.1252605 -0.1999602
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
## [1] 0.0266
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.8957202
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
## t1*      4.5 0.06676677   0.9192206
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 3 5 6 7 9 
## 1 1 1 2 1 2 1 1
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
## [1] 0.0447
```

```r
se.boot
```

```
## [1] 0.9126252
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

