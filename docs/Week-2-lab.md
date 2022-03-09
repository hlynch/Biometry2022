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
## 1 4 5 6 7 8 9 
## 1 2 2 1 1 1 2
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
## [1] 0.0264
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
## [1] 2.772376
```

```r
UL.boot
```

```
## [1] 6.280424
```

Method #2: Simply take the quantiles of the bootstrap statistics


```r
quantile(xmeans,c(0.025,0.975))
```

```
##  2.5% 97.5% 
##   2.8   6.4
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
##    [1] 4.6 3.4 5.3 4.5 3.6 3.6 6.1 3.3 5.3 5.4 5.8 5.3 3.2 3.4 4.1 4.4 5.4 5.7
##   [19] 4.4 4.8 5.9 4.6 4.5 4.8 5.1 3.8 4.1 3.4 4.2 3.7 4.8 4.2 5.3 3.0 4.3 4.2
##   [37] 5.3 3.1 5.9 5.1 3.1 3.8 2.7 5.3 4.6 3.9 4.3 4.1 3.3 4.9 3.8 4.5 4.2 5.1
##   [55] 4.7 2.9 4.8 5.4 4.1 4.9 3.2 3.8 5.1 3.8 3.2 4.3 5.0 3.5 3.0 4.8 4.3 5.3
##   [73] 3.6 2.7 4.3 4.1 4.3 5.2 5.2 4.4 3.1 4.0 5.7 5.5 5.7 4.9 3.7 4.4 4.0 4.5
##   [91] 3.9 4.6 4.7 4.5 5.1 5.4 5.2 4.8 4.9 4.4 5.4 6.2 6.2 5.0 4.3 3.4 5.8 2.9
##  [109] 4.6 4.0 6.5 4.9 5.1 5.4 3.9 3.4 4.4 6.0 5.2 5.2 5.0 5.3 3.4 4.1 4.8 4.7
##  [127] 3.4 5.8 2.9 4.0 6.0 5.2 5.3 4.5 3.7 4.1 4.5 6.6 3.6 4.6 4.2 4.1 4.7 4.7
##  [145] 3.6 4.9 6.6 4.4 5.3 5.3 3.5 4.4 5.1 1.2 3.8 4.4 5.2 5.0 3.5 4.5 5.4 4.5
##  [163] 5.1 3.6 4.6 4.5 4.6 5.0 6.4 4.6 5.1 3.3 5.6 4.3 3.7 3.6 4.6 5.6 4.5 4.5
##  [181] 3.5 4.0 4.6 4.2 4.8 5.3 4.3 4.4 3.9 4.1 5.2 4.4 3.3 4.7 5.5 4.3 3.1 4.8
##  [199] 3.7 4.5 4.6 5.6 5.5 3.7 5.0 4.4 4.0 4.4 4.0 4.3 5.0 4.7 3.3 4.0 5.1 5.2
##  [217] 4.2 5.3 4.4 4.4 4.5 4.2 4.6 4.5 4.2 5.1 2.5 5.6 4.4 4.6 5.5 4.3 5.0 5.0
##  [235] 4.8 5.0 2.9 4.6 4.8 4.8 5.3 5.9 3.2 4.8 5.3 4.0 5.5 4.4 4.2 3.6 4.9 3.5
##  [253] 4.7 4.8 6.3 5.3 4.2 3.7 5.6 5.4 4.4 5.0 4.1 5.9 4.6 3.7 5.2 4.0 5.9 5.5
##  [271] 4.0 5.1 5.5 5.2 6.0 3.7 5.1 4.9 3.9 3.7 3.9 3.2 4.9 6.5 4.8 4.1 4.3 5.0
##  [289] 2.5 4.4 4.7 3.9 4.1 4.9 2.2 4.0 4.0 4.8 3.7 5.1 5.2 5.4 6.0 4.2 4.0 3.1
##  [307] 4.5 5.0 5.7 4.3 5.5 3.4 5.1 4.3 5.2 3.7 6.3 4.1 4.9 4.8 4.4 4.2 2.7 4.0
##  [325] 2.1 5.6 5.0 3.8 4.1 4.9 6.1 4.5 6.1 4.4 4.4 4.4 4.3 3.3 4.2 5.7 4.2 4.2
##  [343] 4.1 5.4 3.1 5.5 4.7 6.4 3.5 4.6 2.8 5.5 3.1 4.5 4.7 6.8 4.7 4.6 3.6 3.7
##  [361] 3.1 3.8 4.8 5.2 4.0 2.0 5.7 3.4 4.5 4.7 4.2 2.6 5.5 5.1 4.5 5.1 3.1 5.5
##  [379] 4.7 4.7 4.5 3.8 3.3 5.4 5.1 5.4 5.1 3.0 3.8 4.6 5.0 2.1 3.0 3.6 4.2 4.4
##  [397] 4.3 5.9 4.1 3.8 5.1 4.3 2.6 3.6 6.0 3.3 5.0 4.8 4.3 4.0 5.7 5.4 5.3 4.7
##  [415] 4.7 5.6 4.1 3.3 3.1 5.0 3.8 6.1 5.2 3.6 4.1 3.4 3.2 3.4 3.0 4.0 5.9 4.9
##  [433] 6.1 3.4 5.3 3.4 4.8 5.5 2.3 4.0 5.6 4.5 4.1 4.5 5.3 4.9 4.1 4.4 3.7 4.9
##  [451] 4.4 5.3 4.3 5.9 5.0 3.1 3.2 5.4 5.4 5.2 4.9 5.0 3.9 4.8 2.8 5.6 4.8 4.6
##  [469] 3.7 4.6 3.4 3.9 4.1 5.4 4.4 4.2 3.2 3.9 5.4 5.8 6.3 6.8 4.7 3.5 5.2 6.1
##  [487] 4.6 4.7 4.9 4.2 4.1 2.9 5.2 4.8 5.0 5.0 5.7 4.9 3.5 4.8 3.0 4.6 3.7 3.6
##  [505] 5.0 3.8 3.9 3.8 5.2 2.9 4.6 3.3 5.2 3.0 4.4 4.4 5.7 5.0 3.4 5.8 4.6 5.6
##  [523] 3.9 3.5 4.3 4.4 4.2 5.1 4.3 5.3 4.9 3.8 5.3 5.0 5.0 4.8 3.7 4.6 3.2 5.5
##  [541] 3.2 3.8 3.7 5.7 3.0 4.4 4.9 5.7 4.8 3.6 4.3 4.8 4.6 6.5 6.5 5.3 4.3 4.6
##  [559] 5.1 4.5 5.6 4.0 4.5 4.1 4.9 4.8 5.6 3.0 3.9 4.5 2.7 3.9 3.9 2.1 6.2 5.4
##  [577] 4.2 4.8 5.3 2.9 4.0 5.2 4.8 5.3 5.4 4.6 5.6 4.4 4.7 4.9 4.3 5.7 3.9 4.3
##  [595] 3.8 5.4 4.5 5.1 5.3 3.0 3.6 4.7 3.2 5.7 5.2 4.3 3.6 4.7 4.0 4.7 3.2 3.8
##  [613] 4.2 4.7 4.6 4.0 4.9 6.1 4.3 2.2 5.5 4.5 4.2 5.5 4.4 4.6 2.9 6.7 4.4 4.7
##  [631] 4.0 4.7 4.6 5.8 4.1 4.6 3.3 3.7 6.1 4.7 4.9 4.0 4.8 3.3 3.2 3.7 4.7 3.8
##  [649] 3.8 3.3 5.8 3.0 4.2 4.9 4.0 3.8 3.1 4.4 4.8 3.5 3.9 4.4 4.0 4.5 4.9 4.0
##  [667] 6.9 3.6 5.5 5.1 5.4 3.5 3.3 4.8 3.9 6.0 3.3 5.8 5.1 5.3 3.9 5.1 4.9 5.7
##  [685] 4.5 4.7 5.4 7.2 5.1 6.2 4.0 3.8 4.7 3.5 4.4 5.4 4.3 5.8 4.8 5.0 3.6 4.1
##  [703] 4.2 5.5 5.6 4.4 5.3 4.6 5.6 4.0 5.1 4.5 4.8 3.9 4.8 4.2 5.0 5.3 3.0 4.2
##  [721] 5.5 5.8 4.7 5.9 4.1 4.8 4.2 3.2 4.2 3.4 5.0 4.7 5.5 5.3 3.4 5.2 4.1 4.0
##  [739] 4.0 4.3 4.0 6.5 3.9 5.3 4.0 4.9 4.6 2.4 3.4 4.1 2.6 3.7 4.8 4.1 4.1 3.4
##  [757] 5.4 6.8 3.9 3.7 6.8 4.5 6.0 3.8 5.6 5.2 4.7 4.6 5.3 3.9 4.7 4.7 4.2 3.5
##  [775] 3.8 3.7 5.2 4.0 3.9 6.4 3.6 4.9 2.9 4.2 4.3 4.1 3.8 3.4 3.2 4.1 4.9 3.6
##  [793] 2.0 5.2 3.7 3.9 3.9 4.7 3.5 5.8 3.9 3.8 4.1 4.0 6.0 4.6 4.0 4.4 4.7 7.0
##  [811] 6.1 4.6 4.4 4.5 4.6 6.3 4.7 3.8 4.5 4.0 4.5 4.5 4.2 6.8 4.2 4.8 5.2 4.7
##  [829] 5.5 4.4 2.5 3.8 5.6 4.0 4.1 3.3 4.3 2.4 4.2 3.4 2.9 4.5 4.7 1.8 5.6 3.8
##  [847] 4.3 3.9 3.5 4.2 4.7 4.7 6.7 5.1 5.1 4.2 4.1 3.8 3.8 3.4 3.2 4.3 4.6 3.5
##  [865] 5.8 4.4 4.5 4.7 5.0 4.2 3.9 5.2 4.2 3.9 4.9 3.6 5.6 6.3 5.1 4.3 3.9 2.7
##  [883] 3.5 3.9 4.0 4.0 4.2 3.1 4.0 4.5 3.2 4.2 2.2 3.1 3.7 3.6 5.8 4.9 3.8 5.5
##  [901] 4.0 5.8 5.4 4.1 4.7 4.8 3.5 4.5 5.7 3.3 4.5 4.0 5.2 3.5 3.4 4.8 4.7 5.2
##  [919] 4.2 5.0 5.4 6.3 4.9 5.6 6.2 5.1 4.0 4.5 4.5 6.1 4.7 5.7 4.8 4.9 6.5 3.7
##  [937] 2.8 4.2 3.3 4.4 4.4 4.1 6.9 2.7 5.7 5.2 3.2 2.3 4.3 5.4 5.6 5.0 3.1 5.4
##  [955] 5.2 4.4 4.3 6.3 5.3 3.4 5.5 4.6 5.7 5.1 3.4 5.0 4.3 4.4 4.5 4.6 5.0 4.7
##  [973] 5.1 2.6 4.3 5.8 3.5 4.5 3.8 5.3 4.6 3.8 5.7 4.3 5.0 4.0 6.0 4.0 4.1 5.5
##  [991] 5.9 4.4 4.7 4.2 4.9 4.5 5.0 3.9 4.7 5.0
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
##    [1] 5.0 4.4 4.8 5.3 5.3 4.5 3.7 5.1 4.3 3.5 4.5 3.6 3.8 3.7 5.8 4.7 4.4 3.5
##   [19] 3.8 2.9 4.8 5.0 4.0 5.2 6.6 4.6 3.9 4.8 6.0 5.2 4.7 4.2 3.8 3.8 3.7 2.8
##   [37] 5.3 3.9 2.3 5.4 4.1 3.3 3.9 4.7 4.9 4.9 3.8 5.9 4.0 4.3 5.7 4.0 5.8 4.3
##   [55] 6.2 5.4 5.2 3.5 3.9 3.5 3.8 4.5 4.4 4.7 4.4 6.5 6.1 4.5 3.4 4.3 3.2 4.5
##   [73] 4.1 5.0 3.6 4.0 2.7 4.5 4.4 4.3 5.7 6.0 3.9 4.2 3.3 5.6 4.5 4.3 5.8 6.3
##   [91] 4.3 4.2 4.8 4.2 5.6 3.8 4.0 5.1 4.2 3.6 3.1 5.3 5.7 4.3 6.6 3.5 4.3 5.4
##  [109] 2.9 5.3 5.1 4.0 5.8 3.8 5.4 3.3 4.6 4.1 6.1 3.5 4.4 3.9 2.9 5.0 2.7 3.6
##  [127] 5.0 3.5 3.7 5.5 3.1 4.5 5.9 5.0 4.9 3.1 4.4 5.6 3.9 5.5 4.0 5.1 5.4 3.7
##  [145] 4.3 3.5 4.9 3.7 3.5 5.6 3.1 4.4 7.3 4.0 5.1 3.4 2.9 3.3 6.0 5.4 2.6 5.0
##  [163] 3.8 4.9 5.3 6.4 3.9 4.4 4.6 4.4 6.5 5.2 4.1 4.0 3.6 4.3 5.1 4.2 4.1 5.2
##  [181] 3.3 4.7 5.4 4.5 5.5 4.2 4.4 5.5 4.0 4.1 5.2 4.8 4.2 5.5 3.8 4.4 4.1 3.7
##  [199] 4.6 5.0 4.8 4.9 5.9 3.5 3.2 4.9 4.9 4.6 4.2 3.1 4.8 4.9 6.1 5.0 3.1 4.3
##  [217] 5.8 5.5 4.9 4.5 3.4 4.6 4.2 6.0 5.0 3.8 3.7 4.8 4.8 4.2 4.0 4.3 3.0 4.0
##  [235] 5.4 3.9 4.7 4.9 5.3 4.5 4.0 4.3 3.8 4.9 3.2 3.5 5.3 5.3 4.2 4.6 2.9 5.2
##  [253] 4.5 5.7 4.3 4.6 2.9 4.2 5.1 5.6 3.4 4.9 3.7 3.5 5.2 3.8 4.0 4.2 5.0 3.8
##  [271] 4.2 7.0 5.2 5.1 5.5 4.7 5.0 4.0 4.8 4.8 4.6 4.3 5.4 4.8 4.2 4.0 3.5 5.3
##  [289] 5.2 4.7 3.6 4.5 5.0 4.4 4.8 4.5 5.2 5.9 3.8 4.5 3.5 3.7 4.2 5.4 3.6 5.3
##  [307] 5.1 3.6 7.0 4.8 4.5 5.6 4.0 5.7 4.8 3.7 3.4 5.3 3.3 3.4 3.1 3.4 3.5 5.5
##  [325] 3.2 4.4 5.0 4.5 4.7 4.0 5.3 4.1 4.8 4.4 5.6 3.1 4.4 4.6 4.4 6.6 6.0 5.0
##  [343] 4.6 5.6 4.6 4.5 4.4 3.7 5.9 5.9 3.8 5.5 2.2 5.2 5.2 2.6 5.1 6.1 4.6 3.6
##  [361] 3.6 5.5 2.6 5.4 3.7 3.3 5.5 5.6 5.0 3.5 4.2 4.4 4.5 6.5 4.5 4.6 3.5 4.4
##  [379] 4.5 4.8 4.3 5.3 3.9 3.8 3.4 4.1 3.5 4.3 5.1 4.2 4.1 3.5 4.0 3.5 5.7 4.6
##  [397] 5.8 2.7 4.2 6.0 5.3 4.3 5.3 5.2 4.6 2.9 5.3 4.6 4.6 5.8 4.7 6.5 3.9 4.4
##  [415] 3.9 5.2 3.8 3.5 5.5 3.3 4.6 4.2 4.1 4.6 4.6 4.9 4.2 4.1 3.3 5.7 4.5 5.9
##  [433] 3.4 6.0 4.5 3.5 4.2 4.2 3.4 3.3 5.0 5.2 3.8 4.6 3.5 5.1 4.9 5.3 6.5 5.0
##  [451] 3.7 1.6 5.0 4.6 4.0 5.1 5.2 4.1 4.0 5.4 5.2 4.7 2.7 4.5 4.3 3.0 5.7 3.0
##  [469] 4.4 4.7 2.9 4.1 4.8 4.5 5.1 4.9 5.3 5.0 4.8 3.4 5.3 4.1 3.9 2.7 4.2 4.1
##  [487] 4.4 4.6 5.4 4.7 5.6 5.1 5.1 5.0 3.8 5.1 4.4 5.4 3.4 4.5 5.4 6.1 3.3 5.3
##  [505] 3.7 4.2 5.0 3.8 4.5 5.1 5.3 2.5 5.1 4.8 5.9 5.7 3.3 5.7 3.9 5.3 3.6 4.0
##  [523] 4.0 5.6 4.9 5.2 4.1 4.1 4.7 5.0 3.3 4.8 4.1 4.9 2.6 4.2 4.6 4.7 5.3 5.8
##  [541] 5.0 4.1 3.0 5.1 4.1 3.0 3.5 3.1 5.7 3.4 6.4 4.3 3.8 4.4 3.4 6.2 5.9 4.6
##  [559] 2.8 3.9 3.4 3.4 3.9 4.1 3.7 4.7 5.0 4.1 3.0 4.9 5.2 4.5 4.5 2.7 4.2 3.5
##  [577] 2.7 5.5 3.3 2.9 6.1 5.1 5.2 2.9 5.4 4.9 4.3 4.9 4.5 3.4 4.1 5.0 5.3 5.1
##  [595] 4.1 4.6 5.4 4.1 4.7 4.3 4.9 4.1 3.3 3.6 5.6 4.8 6.0 4.1 2.8 3.0 6.0 5.3
##  [613] 4.2 5.6 4.3 3.2 3.7 6.0 3.0 4.5 3.3 3.9 4.1 6.1 4.4 4.4 3.8 5.0 5.6 4.7
##  [631] 4.0 4.2 4.2 4.7 7.3 4.2 5.4 5.1 4.9 3.5 5.7 5.7 4.4 5.4 3.6 3.8 3.5 5.1
##  [649] 4.2 4.8 4.0 4.3 5.6 4.6 4.7 3.5 3.4 4.3 4.4 4.3 5.4 5.1 3.7 5.4 3.4 4.8
##  [667] 4.5 4.4 4.6 3.4 5.5 5.2 2.9 5.6 6.8 2.7 4.3 5.5 5.6 4.4 2.9 4.1 4.3 4.9
##  [685] 5.1 5.2 1.8 5.3 4.6 4.3 3.3 3.7 3.8 5.1 4.6 7.7 5.0 3.1 5.9 5.4 4.6 3.8
##  [703] 3.8 4.0 3.3 6.5 5.1 2.9 6.2 3.6 5.4 4.8 3.9 5.0 3.3 4.7 5.4 4.2 4.5 4.2
##  [721] 4.3 4.9 2.5 4.1 4.8 4.1 3.4 3.6 3.8 4.4 3.9 3.8 2.5 2.7 4.6 4.6 4.6 3.8
##  [739] 5.5 3.8 5.2 4.2 3.6 5.3 5.4 4.1 6.9 4.2 4.5 3.8 3.2 4.0 5.6 4.1 3.5 3.4
##  [757] 5.5 4.9 3.6 6.0 3.3 3.9 3.8 3.6 4.7 4.8 2.4 3.2 6.1 5.2 4.1 6.1 4.0 5.5
##  [775] 5.1 5.4 3.4 4.3 5.7 4.9 6.3 4.2 3.8 5.2 3.7 3.5 2.7 5.2 4.6 3.7 5.1 5.5
##  [793] 5.9 4.7 5.1 5.2 5.2 4.1 3.8 4.1 3.4 3.1 4.5 4.4 3.9 2.6 4.3 3.9 4.5 3.8
##  [811] 5.1 4.5 4.4 3.4 2.5 2.7 4.3 4.1 3.4 4.6 4.6 4.6 5.1 4.1 3.1 4.6 5.8 5.5
##  [829] 5.8 4.6 4.7 6.2 4.3 3.6 5.1 3.7 4.4 3.9 5.0 4.4 4.8 5.3 4.4 4.2 4.7 5.6
##  [847] 4.8 5.4 3.4 4.9 4.5 4.3 3.5 4.0 3.6 3.4 2.7 4.5 4.8 5.4 3.9 3.4 5.7 5.9
##  [865] 4.4 2.9 4.8 2.9 5.2 6.2 5.9 3.7 4.6 3.9 4.7 6.0 4.6 4.6 3.7 4.2 3.4 3.4
##  [883] 3.6 4.1 6.3 4.7 5.3 5.2 5.4 4.7 5.1 5.7 3.8 4.6 3.3 3.9 3.2 4.7 4.5 5.7
##  [901] 5.7 4.7 5.0 6.4 3.5 4.6 5.2 3.8 5.2 5.8 3.9 3.2 3.8 5.0 5.0 5.1 4.0 5.5
##  [919] 4.4 5.3 5.7 5.0 3.6 4.2 4.6 4.9 5.1 3.8 6.4 2.8 3.7 3.3 4.1 3.7 5.7 5.1
##  [937] 5.0 4.8 3.3 4.2 6.4 5.4 6.0 5.7 4.6 4.2 4.7 4.9 3.5 5.2 4.3 4.1 4.1 5.0
##  [955] 5.5 3.7 4.2 3.8 4.9 3.7 4.6 3.0 4.3 3.9 3.6 5.7 3.7 5.6 4.5 4.5 5.1 4.0
##  [973] 2.6 5.5 3.6 4.1 4.6 5.4 5.6 3.2 5.2 2.3 5.6 3.9 4.8 4.0 5.0 4.1 4.5 4.1
##  [991] 4.3 5.4 4.4 5.2 4.5 5.6 3.8 5.0 4.4 3.9
## 
## $func.thetastar
## [1] -0.0199
## 
## $jack.boot.val
##  [1]  0.49343284  0.32144847  0.30710059  0.22136223  0.01354467 -0.06454545
##  [7] -0.23286119 -0.23949580 -0.40058140 -0.52821918
## 
## $jack.boot.se
## [1] 0.9694903
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
##    [1] 4.7 4.6 4.1 4.4 4.5 4.0 4.9 4.5 3.1 4.2 4.9 4.8 4.0 3.8 3.6 3.8 4.2 5.6
##   [19] 5.2 3.3 6.1 5.1 3.1 5.6 5.1 3.9 4.7 5.9 5.1 2.7 5.2 4.3 5.0 6.4 4.3 4.9
##   [37] 4.5 4.2 3.7 4.6 3.4 5.2 5.7 5.7 5.3 4.0 6.1 4.7 4.7 4.3 4.8 4.6 5.9 4.0
##   [55] 4.5 4.4 4.0 2.8 4.8 3.8 5.4 3.8 5.6 5.0 3.8 5.9 4.1 4.4 6.0 2.9 4.0 2.9
##   [73] 5.2 5.3 5.7 5.1 3.6 3.3 6.0 5.1 4.0 2.7 4.8 4.2 5.3 3.7 3.6 3.9 5.3 3.7
##   [91] 3.1 5.7 6.2 4.0 4.7 3.9 4.6 5.1 5.4 3.9 4.0 4.8 4.8 5.5 4.1 2.7 6.3 4.3
##  [109] 5.4 4.1 4.4 6.3 4.9 5.9 2.9 2.9 3.0 4.0 4.7 4.8 3.4 6.3 5.8 4.2 4.5 3.3
##  [127] 4.3 4.2 4.7 1.8 4.5 2.2 4.2 3.5 5.9 4.5 6.4 5.8 5.0 5.3 5.3 6.0 4.5 5.3
##  [145] 3.2 4.7 4.7 4.6 5.0 3.4 4.3 3.3 5.6 4.1 4.0 4.5 4.6 5.3 2.8 4.7 5.4 5.9
##  [163] 4.2 4.9 3.2 4.4 4.0 3.7 3.6 4.1 2.9 3.4 4.4 4.9 4.3 4.8 4.2 3.0 3.7 2.8
##  [181] 4.4 3.9 4.0 4.3 2.4 4.1 4.5 4.7 6.0 3.3 3.6 5.4 4.5 2.4 6.6 4.8 4.7 3.2
##  [199] 2.7 4.6 5.2 4.9 4.0 5.5 5.5 3.9 3.7 4.5 4.1 6.5 4.7 3.4 4.6 2.7 4.6 4.2
##  [217] 3.5 5.1 5.2 5.4 3.6 4.9 5.0 5.0 4.1 4.2 3.8 4.2 3.0 4.4 4.5 5.3 5.3 3.4
##  [235] 5.0 5.2 5.4 6.3 2.9 3.7 6.1 3.8 3.6 5.6 4.8 5.5 2.9 3.3 2.9 4.8 4.7 2.8
##  [253] 4.5 4.6 2.9 5.5 5.7 5.9 4.8 4.6 3.8 4.9 5.2 4.8 4.1 3.7 5.2 4.7 4.7 4.0
##  [271] 5.2 3.5 4.6 5.4 3.9 4.9 4.4 6.1 5.0 4.7 3.4 4.6 5.7 5.0 2.3 4.1 3.7 4.5
##  [289] 2.7 4.7 6.5 6.1 5.3 4.5 4.8 5.6 3.7 4.8 4.1 4.5 3.9 3.7 4.5 6.3 4.4 4.6
##  [307] 4.1 4.6 4.7 4.6 3.3 5.9 3.9 2.8 5.3 2.8 4.3 3.8 5.1 3.4 5.3 3.8 4.6 5.3
##  [325] 4.7 5.9 3.4 4.6 6.5 4.4 4.7 5.5 4.0 4.1 3.7 2.7 3.5 2.8 4.7 3.6 5.7 3.7
##  [343] 5.5 5.0 4.4 4.5 3.5 4.5 4.8 3.5 3.6 4.6 3.4 4.2 4.8 2.8 5.9 4.6 5.3 1.5
##  [361] 4.1 4.9 3.6 5.5 4.3 4.8 5.0 4.4 4.4 3.3 4.0 3.3 3.5 3.3 5.4 4.9 4.3 5.4
##  [379] 4.2 5.9 5.0 3.3 4.2 5.1 4.9 5.6 4.9 4.2 5.3 4.1 2.0 4.9 4.8 5.6 4.4 4.1
##  [397] 5.5 5.4 3.8 3.7 4.4 4.4 4.8 4.5 5.1 5.9 6.1 3.8 3.5 2.4 4.9 3.3 6.2 3.0
##  [415] 3.5 4.0 5.5 2.8 5.9 3.2 4.9 4.7 5.8 4.9 5.3 4.5 5.2 6.1 5.6 3.5 3.5 3.6
##  [433] 5.3 4.3 5.2 3.7 4.6 5.6 5.4 4.1 4.2 5.2 4.9 4.4 5.3 3.7 4.9 5.3 4.7 5.6
##  [451] 4.3 4.6 5.7 5.8 4.2 3.5 4.5 5.1 4.9 4.4 4.3 4.3 3.6 3.8 3.2 2.2 4.6 4.5
##  [469] 3.7 5.1 5.8 5.4 4.2 5.1 3.8 5.2 2.9 3.7 4.7 4.8 4.0 4.3 4.5 4.3 4.5 4.7
##  [487] 4.0 3.5 3.1 4.4 3.8 3.3 3.4 5.2 5.0 4.0 4.3 4.6 4.0 5.1 4.2 4.3 4.9 5.4
##  [505] 5.4 4.6 4.6 4.4 4.0 5.5 5.0 5.3 4.2 6.1 5.8 3.6 5.2 4.9 5.6 2.7 5.2 6.4
##  [523] 5.2 3.5 3.6 3.7 5.2 4.7 4.7 5.0 5.4 4.7 4.2 5.3 4.0 5.4 6.3 4.7 4.3 4.1
##  [541] 6.8 6.5 4.9 4.7 3.8 3.7 5.3 5.6 4.5 4.3 4.7 5.6 4.9 4.9 3.5 6.5 4.3 4.4
##  [559] 3.8 3.5 6.3 4.1 5.5 5.1 5.3 4.9 4.0 4.9 4.6 4.8 3.5 5.9 5.2 4.9 4.5 3.5
##  [577] 2.3 3.5 5.2 3.2 5.2 3.8 5.7 5.8 4.9 6.3 3.5 3.6 4.8 4.0 4.9 4.1 4.3 4.5
##  [595] 4.6 6.0 4.7 5.4 3.6 4.5 4.0 6.1 4.5 4.3 4.9 4.5 4.2 4.8 5.3 3.9 4.8 4.5
##  [613] 5.4 6.2 3.5 4.4 3.7 4.6 5.9 4.9 5.4 5.9 2.3 4.2 5.2 3.7 4.4 5.6 3.2 5.9
##  [631] 3.8 4.9 4.2 5.3 5.5 4.8 3.3 4.4 5.7 3.6 5.0 5.2 4.7 4.8 4.3 4.4 5.4 5.2
##  [649] 5.0 5.9 4.5 4.8 5.8 5.0 4.1 3.9 5.1 5.6 5.5 3.9 5.1 5.7 6.3 4.5 5.1 4.6
##  [667] 4.7 4.3 4.2 4.8 4.6 5.8 4.5 6.0 5.7 3.0 3.6 4.1 4.1 4.7 5.6 4.8 5.4 3.3
##  [685] 3.4 3.4 4.4 3.7 4.3 3.8 2.8 4.1 4.7 5.9 5.1 3.8 3.0 6.2 3.8 4.0 3.9 5.2
##  [703] 4.2 3.8 4.4 3.0 2.0 5.7 4.9 5.0 2.7 5.8 3.9 3.5 3.8 4.6 4.2 5.1 3.7 3.7
##  [721] 5.9 3.2 3.4 5.2 3.9 5.1 4.3 6.3 4.5 3.1 4.4 4.7 3.4 5.5 2.7 4.7 4.0 5.0
##  [739] 3.1 5.4 4.5 4.4 2.6 4.6 3.9 3.2 2.9 5.7 5.5 3.8 3.8 3.7 3.1 6.2 4.8 4.9
##  [757] 5.0 3.8 4.9 4.1 4.5 4.6 4.5 4.4 5.6 4.7 5.3 4.3 4.5 4.6 3.3 4.4 4.6 5.1
##  [775] 4.5 4.9 3.1 3.2 3.9 4.5 5.4 5.7 4.2 3.3 5.2 3.8 3.6 5.1 3.0 4.5 4.9 3.6
##  [793] 4.7 5.4 4.7 4.2 3.7 5.2 5.2 4.0 2.7 4.7 4.5 4.4 5.2 5.3 4.3 4.8 4.1 4.0
##  [811] 3.7 5.4 4.6 4.0 5.1 5.6 5.0 6.5 3.8 4.9 5.3 3.5 3.1 4.7 2.8 4.0 3.4 4.4
##  [829] 3.8 6.8 5.0 6.2 3.3 4.4 4.5 5.9 6.2 5.0 5.2 4.3 3.7 3.3 3.9 5.1 3.9 2.7
##  [847] 4.0 5.0 6.4 5.7 6.2 3.3 4.6 4.2 4.4 4.1 4.9 4.4 4.2 3.6 4.2 4.0 3.5 5.3
##  [865] 4.0 3.5 3.9 5.4 3.0 4.8 6.1 4.5 4.1 3.8 4.0 3.9 5.2 4.8 4.1 4.1 4.9 3.1
##  [883] 4.8 3.2 2.8 4.0 4.6 3.8 4.6 5.2 4.5 3.8 5.2 5.0 2.6 4.9 4.6 5.4 3.8 5.2
##  [901] 4.8 6.4 5.9 5.9 3.3 2.8 3.6 6.0 4.6 4.8 4.7 2.7 5.6 4.9 4.1 5.0 3.7 2.2
##  [919] 5.1 4.7 5.2 4.3 4.4 4.6 5.7 5.0 6.8 3.8 3.2 6.5 2.8 3.7 4.8 3.8 5.5 3.7
##  [937] 3.1 5.7 4.5 4.0 6.4 5.0 4.0 6.6 3.0 3.6 3.6 4.3 4.6 4.3 4.8 2.6 5.8 3.5
##  [955] 2.0 4.7 4.4 5.8 4.1 3.5 5.0 4.7 4.4 4.8 3.4 5.2 4.8 4.7 4.4 4.4 2.9 3.3
##  [973] 3.1 5.3 4.1 5.3 3.7 4.8 3.2 3.5 5.6 4.8 4.4 2.8 3.8 4.9 4.9 4.8 4.9 3.7
##  [991] 6.2 2.7 3.9 6.0 5.3 4.8 5.8 4.7 4.4 4.9
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.400 5.400 5.316 5.200 5.100 4.928 4.900 4.700 4.636 4.500
## 
## $jack.boot.se
## [1] 0.9280121
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
## [1] 1.517419
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
##   3.477824   4.457942 
##  (1.486775) (2.050223)
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
## [1]  1.31875690  0.43071654  0.03871836  0.23895610  0.59214785 -0.68830081
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
##    [1]  1.168458536  1.577190915  1.033370723  0.268995453  1.864678095
##    [6]  1.367506230  1.026634345  1.479414136  2.016869165  1.487804697
##   [11] -0.062907302  1.799894225  2.119443065 -0.289437202  0.114788271
##   [16]  0.106876885 -0.290399368  0.678222924  2.326477224 -0.356078301
##   [21]  1.843956421 -0.168731738 -0.170426029  2.051110568  0.108037013
##   [26]  1.205920197 -0.029196984  1.994367972  0.990834307  1.299726476
##   [31]  1.870392664  1.965190924  0.162816439  0.047129378 -0.310668039
##   [36]  1.428378335  1.102555158 -0.689143234  1.100984036  0.017905399
##   [41]  1.477074496  1.848472455  1.385610195 -0.137485648  0.986739536
##   [46]  1.890847882 -0.348456020  1.455956493  1.112906274  1.627408972
##   [51]  1.195674062 -0.831861111  2.207232269  1.822810275  1.170312990
##   [56]  0.680459261  2.285719260  1.879281406  1.796047578  2.043575308
##   [61]  1.040321101  1.544000104  2.146662170  1.618030068  1.026577800
##   [66]  1.108823900  0.272700432  0.708498510 -0.915644656  2.087669777
##   [71]  0.331638124  0.928464627 -0.011387922  0.925849373  0.714279946
##   [76]  1.855892829  1.946375970  1.142573378  2.155717493  0.656460775
##   [81] -0.474138139  2.387428701  2.076906993  1.730940723 -0.058599180
##   [86]  0.299667602  1.075266387  0.673697819  1.654701229  0.505467795
##   [91] -0.148655969  0.268223679  1.441141268  0.686268661 -0.756680772
##   [96]  0.921697458  1.840435403 -0.216376870  1.012424305  0.889094124
##  [101]  1.981201063 -0.854831885  0.175524088 -0.497477513  1.735522993
##  [106]  1.467688501  1.616304791  1.306909921  0.265228366 -0.532371726
##  [111]  1.426560401  1.479144475 -0.217826151  0.275955427 -0.090001411
##  [116]  0.281286716  1.065164839  1.232802105  1.259339279  1.000553971
##  [121]  1.094099847  1.460405302  1.515466893  0.728334684 -0.984096422
##  [126]  1.524833638  1.256856672  1.265134128  1.878781580  1.204731515
##  [131]  1.527433282 -0.769574937  0.672737003 -0.146760125  1.164549851
##  [136]  1.855185676  0.574797131  1.378424238 -1.429699962  1.161080455
##  [141] -0.750096112  1.232503309  0.254518218  1.878262292 -0.107490884
##  [146]  0.531279353  0.855706701  1.257000755  1.777611252  1.897228852
##  [151]  1.781281202  2.152199363  1.605673623 -0.494461740 -0.115932801
##  [156]  0.642009731  0.713506088  1.435786072  0.547396289  1.784254309
##  [161]  1.068320547  1.597116484  1.617162298 -0.389225144  0.453536361
##  [166]  0.866724707  1.106330315 -0.072532675  1.894246514  0.153779260
##  [171] -0.167902508  0.988750375  1.612753340 -0.392350770  1.237029199
##  [176]  0.461690654  1.326124809  1.062003287  1.538246922  0.988991937
##  [181] -0.524712630  0.270251211  1.367743708  1.626183043  1.168353377
##  [186]  1.069113378  1.652368823 -0.111280413  1.977090271  1.456542210
##  [191]  0.627287711  0.519075351 -0.478265463  1.132673406  0.894727689
##  [196] -0.030362313 -0.285102766  0.319165607  1.468313470  0.177282133
##  [201]  0.503505335 -0.267151572 -0.120020055  1.365733416  1.216545686
##  [206] -0.070058991  1.028401054  1.074685549  1.421970848  1.516812483
##  [211]  0.944765035 -0.226642483 -0.022233339  1.229752125  0.341030176
##  [216]  0.791426065  1.106806373 -0.129989360  0.218359295  1.078812534
##  [221]  2.180430017 -1.371885275  1.084974926  0.191507638  0.570670197
##  [226]  1.755258460 -0.294266862 -0.108214890  2.132044106  0.579523459
##  [231] -0.862859244 -2.361909722  0.189093037  0.812343388 -0.404481184
##  [236]  1.589367095  0.761435745 -0.085027266  0.880218575  1.659838620
##  [241] -0.493787528  1.484386903 -0.090579077  1.048268506  0.776496628
##  [246]  0.950076564  2.022340549  0.924582906  0.960296359 -0.063607942
##  [251]  1.191400433  0.047790081  1.647849049  0.359796379  1.503191906
##  [256]  1.390521060  0.800194149  1.185259125  1.353021008  2.147309498
##  [261]  0.131028507 -0.601830197  0.487049852  0.074826351  0.726545530
##  [266] -0.522648525 -0.487364368  1.455910363  1.060254755  0.781014497
##  [271]  1.970890274 -0.239823791  1.636602077  0.843717062  0.647107741
##  [276]  1.439048293  1.733438861  0.756219706  1.189733045  1.039147409
##  [281]  0.841726217  2.153781054  2.123237963  0.331844337  1.958692942
##  [286]  0.086395321  1.381276396  1.072529669 -0.236463785 -0.264029937
##  [291] -0.125096163  0.945972050  1.457358539 -0.056261561  1.970517046
##  [296]  1.109598580 -0.326308863  0.945505511  1.469108719  1.865292935
##  [301]  2.107814806  1.046798717  0.063771540  1.310605399  0.416243570
##  [306]  0.226518408  1.449337354  1.122430649  0.595208913  1.357790721
##  [311]  0.320557660  0.921602816  1.146683468  2.212458400  1.708284746
##  [316] -0.439205676  0.034356671  0.857009111  1.513088153  1.029792842
##  [321]  0.697985833  1.866722049  2.330820905  0.023541892  0.182324945
##  [326]  0.610224694  2.183384549  0.465274659  0.723581943  1.778196520
##  [331]  2.048389419  2.335110867 -0.013977962  0.122511455  0.394077873
##  [336] -0.305138043  2.401159160  0.360590038  0.113030066  1.440830244
##  [341]  0.920803511  1.710358630 -0.190816456  1.847809524  1.624507017
##  [346]  0.617899109  0.196418070  1.368680850  1.221949447  1.703586918
##  [351] -0.413534129 -0.247536642  0.896735820  2.073336660  0.223544043
##  [356]  1.444324033  0.661596683  0.052583529  1.790817220  2.142830402
##  [361]  1.998884551  0.370902069 -0.562570295  0.700033307  1.812588018
##  [366]  0.244104808  1.421088957  0.486500297  1.478499361  1.949786940
##  [371]  1.105213737  0.070217094  2.182328539 -0.192986052  0.321659346
##  [376] -0.721431385  1.779299914  0.275322877  1.650974426 -0.192986052
##  [381]  1.689262918  1.397316956  0.601081052  1.163073997 -0.145026272
##  [386] -0.336782208  2.344965423  1.904660746  0.975779820  1.863788159
##  [391]  0.312978143  1.993066393  1.121450633  0.103371052 -0.435538409
##  [396]  0.316052236 -0.379265369 -0.740679458  1.843020556  1.951776378
##  [401]  0.333084740  0.663781358  0.272428660  1.485874981  1.067103554
##  [406] -0.657835842  1.154074067  0.138549774  0.786783011  1.024318913
##  [411]  0.159697110  1.052202330 -0.568896235  0.778136602  1.114971420
##  [416] -0.255950327  1.291661920  0.186098181 -0.543171408 -0.090089073
##  [421]  0.344054873  1.216384081  1.747022770  1.826642022 -0.089957764
##  [426]  0.789574833 -0.764889319  1.724510959 -0.288519031  1.826542441
##  [431]  1.012246476  2.140223971  1.483222501  0.182110574  0.988987931
##  [436]  0.134727096 -0.352492118 -0.003034545  1.604715384 -0.229376568
##  [441] -0.982807994  0.793861646  0.254585259 -0.032548326  0.582758467
##  [446]  1.669499687  1.075807119  2.072944276  1.600182410  0.556918254
##  [451] -0.048011068 -0.210896563  1.616751682  1.617032760  2.114204043
##  [456] -0.359433673  1.371237229 -0.161752705 -0.952852090 -0.314578337
##  [461]  0.345868545 -0.061311001 -0.173307747  2.048459129  1.849115363
##  [466]  1.842646274  0.886603168  1.190628220 -0.080360872  1.927717624
##  [471]  0.102281355  0.050800000  0.025930367  0.170768102  1.181832134
##  [476]  0.857081599 -0.584684154  0.156397298  0.395438540 -0.065459656
##  [481]  1.085302008 -0.391226707  1.697647840  1.388384413  1.755460723
##  [486]  1.404494292  0.890626767  1.899072009  1.539600300  0.863949540
##  [491]  0.963213984  0.486031024  1.274309226 -0.188177216  0.599266996
##  [496]  1.105517326  1.132756621  0.121112213  1.124504146 -0.649692329
##  [501]  1.145577038  0.423260546  0.138549774  0.210720203  1.520482544
##  [506]  0.088567152  0.779408056  1.712048766  0.080703591  2.052162719
##  [511] -0.110735286  0.178229301  2.120653625  1.934037414  1.025500008
##  [516]  0.226518408  1.211768866  0.780045320  1.163521296  1.216895640
##  [521]  1.124092865  0.639832814  0.508200265 -0.657835842  1.441660491
##  [526]  1.532777071  0.532845791  0.874471501 -0.588837065  1.425997097
##  [531]  1.967914646  1.826082702  0.316162881  1.120020679 -0.063873902
##  [536]  1.256320142 -0.126351536  0.064614235  1.681139377  0.030068116
##  [541]  1.778196520  2.390351657  2.122374240 -0.019690622 -0.420524436
##  [546]  1.826139481  1.529994608 -0.293542187  1.426566802 -0.063530920
##  [551]  1.706113923  2.124780928  1.076228540 -0.231919405  2.117601125
##  [556] -0.437936187 -0.167514073  0.409563098  1.850338242  1.480258884
##  [561] -0.030329684  1.780574167 -0.497437271  0.155105036 -0.128983272
##  [566]  2.073212763  1.497162450 -0.366109241  2.005822168  1.579716644
##  [571]  1.346900933 -0.831794821  1.947604268  1.548572845  1.623420496
##  [576]  1.476141343  0.654218854 -0.356877751  1.923879573  1.476329280
##  [581] -0.357318417  0.206712088  0.913087381  1.901023284  0.794230158
##  [586] -0.318244451  0.871187680  0.520404242  1.281970779  1.344462994
##  [591]  1.807212025 -0.696403061  1.285618052  1.137769516  1.368685633
##  [596]  1.608249670  0.532352822  1.563202007  1.652900111 -0.992640365
##  [601]  0.045492910 -0.196303593  1.232418962  0.010713086 -0.105035092
##  [606]  0.981430874  0.955982419  0.482180227  0.774723059 -0.206920738
##  [611]  1.061436739 -0.114188521  1.303610132 -0.450318637  0.732061538
##  [616]  1.136585800  0.415486459  1.817705521 -0.864175731  1.341311780
##  [621]  1.383755728  1.350341921  1.602219207  2.153222409  0.826432489
##  [626]  0.187972572  1.560842361  0.132260235  0.176997281 -0.336007658
##  [631]  0.695195025  1.100549194  1.065710383  0.153702315  1.863419095
##  [636]  0.188949475  1.958702583  0.075908190  2.143737220  0.872605831
##  [641]  0.945972050  1.591505551 -1.391726466 -0.161569973  0.274721087
##  [646] -0.292404999  1.250841021 -0.063487998  0.800111963  0.599266996
##  [651]  0.565492695  0.927397000 -1.468052491  0.998131265  1.201432704
##  [656]  0.121112213  1.079096791  1.204132105  1.256610855  1.226384736
##  [661]  0.776841598  0.751616459  1.701391283  1.329641120 -0.132249724
##  [666] -2.009398567  1.590821822  1.484386903  1.120830146  1.656856281
##  [671]  1.250257175  1.434712725  1.813408798  1.553899201  1.868504462
##  [676]  0.194879952  1.763044346  2.126557724  1.258799291  1.039147409
##  [681]  1.177349292 -1.582100225  1.484325189  1.631910023  1.260820707
##  [686]  1.563476593  1.363473350 -0.436851194  1.676801259 -0.248995790
##  [691]  0.223544043  0.664428627  1.736443508  0.972052184  0.137343666
##  [696] -0.131656535  0.200936714  0.081313742 -0.087317779  1.445791790
##  [701]  0.281866501  1.360679527  0.292388749  1.412188629  0.236626378
##  [706]  1.356900964  1.040777852 -0.372073610 -0.354239160  1.411069227
##  [711]  1.789161375  1.771184244  0.031649632  0.397819689  1.308082821
##  [716]  1.403472204  1.652344639  0.056204038  0.531741880  0.453184550
##  [721]  0.655641245  0.084813319 -0.073926115  1.613741356  0.908519537
##  [726]  1.717448687  1.386318222  1.357988095  1.557649063  1.936624460
##  [731] -0.256947793  1.254524216  0.491457879  1.678894014  1.107094276
##  [736]  1.114569158 -0.048826223  1.752532566  1.862425557  0.552395518
##  [741] -0.542653221  1.677511529  1.516812483 -0.029185481  1.650655658
##  [746]  1.991576221  1.005896219  1.854748450 -0.072584627  1.341139864
##  [751]  1.085301993 -0.408162362 -0.028171096  0.005811869  2.216399854
##  [756]  1.460338500  0.784320342 -0.293746598  0.624659237  0.958829572
##  [761]  0.961093072  2.101640841  1.265106187  0.449158594  2.035640873
##  [766]  1.884529574  0.879777966 -0.509196516  1.293484341  0.965876547
##  [771]  1.366306542  0.672328541  1.449530068  0.612469294  1.613741356
##  [776] -0.236463785 -0.473784811 -0.396605306  0.059196633  0.096632640
##  [781]  2.027312663  1.100324952  1.223498209 -0.422955004  1.547510142
##  [786]  1.049048752  0.879997136  0.472513466  1.097327614 -0.251979452
##  [791]  0.318667664 -0.119021449  1.239153524 -0.121722408  1.369430940
##  [796]  1.485911637 -0.551379786  1.858301453  2.081783817  0.160652179
##  [801]  1.586663977  0.734019068  2.343040380  1.248747119  1.564500631
##  [806] -0.296358818  1.433086652  1.749429559 -0.157920044 -0.861338937
##  [811]  0.784611233  2.120035535  0.024309196  1.897960061  1.691638451
##  [816]  0.588952683  1.414986861  1.385107136  0.618580209 -0.289345401
##  [821]  1.551088295  1.024046633  0.706204042  0.664828212  0.223313026
##  [826] -1.885533059  1.332085745  0.197835027  0.724327925  1.901496699
##  [831] -0.154242526  1.861039709  1.020667456  1.463069284  1.383217937
##  [836]  1.844156316  1.232503309 -0.290801548 -0.492544969  1.755070062
##  [841]  0.105835991 -1.611721477  1.990590781 -0.510572843 -0.512649497
##  [846]  1.581120772  1.038208014  1.483898980  2.100763932  1.098643727
##  [851]  1.203176199 -0.057133409  1.653391517  0.167867572  2.223297369
##  [856]  1.094144685  1.523855251 -0.264134199  0.544197214  0.191588380
##  [861]  1.169969472 -0.063922257  0.175215815  0.002036838  1.929016532
##  [866]  1.527756200  1.283351304  0.918251919  0.054525885  0.771860852
##  [871] -0.244509417  0.953350734  1.823052978  1.930707011  0.300888211
##  [876]  1.494457660 -0.714786597  2.094540170 -0.522310912  0.020497693
##  [881]  1.024593323  0.553610592  1.135130404  0.170453406  0.491522112
##  [886]  1.918989352 -0.026079321  1.517419147  1.640038547  0.646975254
##  [891]  1.876203651  1.040967732  0.293777411  1.126031315  1.461667245
##  [896]  1.547510142  0.078214323  1.221423492  1.836155020 -0.397845995
##  [901] -0.008262830  1.130449342 -1.486054656 -0.101149068  0.593058926
##  [906]  1.827983768  1.078812534  0.963281350  0.187635045  1.733680575
##  [911]  1.054227191 -0.212634610 -0.381742820  1.472734238  1.649196606
##  [916]  1.152629636  1.028179294  1.655341054 -0.352492118  2.134450728
##  [921]  1.209153896  0.281869698 -0.464808394  0.887357922  1.374005570
##  [926]  0.131198231  1.486764764  1.168507593 -0.200302241  1.266611807
##  [931]  1.477721334  0.569707592  1.453726230  0.111493929  1.331854490
##  [936]  0.165408901  1.453913556  1.253427632  0.325960913  0.156812348
##  [941]  1.061995377 -0.663424711  1.871787132  1.427253860  1.469253559
##  [946]  1.975637779 -0.058410913  0.904936705  1.306166918  1.179361752
##  [951]  1.556858426  0.970450544  0.508200265  0.503196101  0.251862239
##  [956]  0.295751113 -0.360356782  1.389367955 -0.124405018  0.656422716
##  [961]  0.805634237 -0.176771179  0.394316003  1.202196521  1.591220076
##  [966] -0.259245041  1.927717624  1.730662092 -0.264039583  1.081288414
##  [971]  1.172365301  2.125142911  0.360201943  1.501521678  0.859747773
##  [976]  1.510112249  2.078566168  1.752111649  1.264188073  0.960261593
##  [981]  1.344182574  0.908317585  1.440389537  1.194545271 -0.786866420
##  [986]  1.220334201  1.273216475  2.008414278  0.872869582 -0.545396195
##  [991]  1.590507049  1.814439802  1.608114632  2.025528913  2.042724382
##  [996]  0.863949540 -0.037281314  1.010210861  1.403983560  0.557233005
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
##      mean         sd    
##   0.7801412   0.4554517 
##  (0.1440265) (0.1018401)
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
## [1] -0.30985389  0.18488487  0.75788345 -0.18523678 -0.87185532  0.03344878
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
## [1] -0.0374
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9208307
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
## t1*      4.5 0.02352352   0.8915981
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 2 3 4 5 7 8 9 
## 1 2 1 1 1 2 1 1
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
## [1] -0.0127
```

```r
se.boot
```

```
## [1] 0.9027423
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

