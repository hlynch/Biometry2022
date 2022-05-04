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
## 2 3 4 8 
## 1 4 2 3
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
## [1] -0.0298
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
## [1] 2.668986
```

```r
UL.boot
```

```
## [1] 6.271414
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
##    [1] 3.9 3.8 4.0 4.9 3.6 5.3 3.4 3.1 3.9 3.2 5.1 4.2 5.1 4.7 5.6 4.1 4.8 3.9
##   [19] 3.4 2.9 3.9 5.5 4.1 4.6 5.7 5.8 3.1 4.8 3.7 4.7 4.6 3.7 5.2 3.8 3.1 4.9
##   [37] 4.3 4.0 5.5 4.0 5.6 4.0 5.6 6.0 5.0 3.8 4.0 4.2 3.0 5.3 4.8 3.5 4.2 4.3
##   [55] 3.5 3.0 4.6 5.3 3.1 4.6 5.9 3.3 3.5 2.8 5.9 4.7 4.4 4.2 2.4 4.1 5.2 4.4
##   [73] 4.9 5.2 3.8 3.8 4.0 6.5 4.3 4.4 4.4 4.9 5.0 3.8 4.8 4.8 5.1 3.0 3.2 5.6
##   [91] 4.9 4.0 3.2 3.8 5.1 3.9 2.7 5.6 4.7 4.1 5.3 3.6 3.8 5.8 5.3 4.3 4.7 6.5
##  [109] 4.0 5.2 5.1 4.8 5.2 5.7 4.1 3.9 3.6 4.1 3.9 3.6 3.6 5.9 4.1 5.1 5.6 3.6
##  [127] 5.6 6.5 4.2 4.7 6.4 4.4 4.0 4.2 4.6 5.2 3.3 3.9 5.6 5.0 5.2 4.1 3.1 3.7
##  [145] 4.7 2.8 4.4 4.2 3.9 4.2 5.4 3.4 3.8 3.9 3.1 3.6 4.7 3.3 4.5 3.5 5.1 4.3
##  [163] 4.1 6.2 2.7 3.2 4.3 3.9 3.9 5.3 3.6 4.3 5.3 4.7 6.2 2.4 5.6 3.4 4.9 5.2
##  [181] 5.3 6.0 4.5 5.6 4.1 2.8 5.1 4.1 5.6 4.6 4.7 4.0 3.6 4.4 5.1 5.1 4.3 5.5
##  [199] 5.1 5.3 4.6 5.7 6.5 4.0 3.8 3.8 3.0 3.6 4.7 5.0 5.6 5.6 5.9 5.4 4.5 3.9
##  [217] 3.8 3.8 3.2 3.8 2.6 5.0 4.4 5.5 5.1 4.6 5.5 4.8 4.2 4.3 4.9 5.7 4.5 5.0
##  [235] 5.6 4.1 5.0 4.5 5.2 3.7 3.5 6.4 4.7 5.5 5.0 4.2 4.5 3.7 3.7 5.1 4.6 5.1
##  [253] 4.5 5.3 4.9 3.9 4.4 3.1 5.1 5.4 4.4 4.5 2.8 4.2 5.8 4.3 4.1 7.1 5.4 3.4
##  [271] 3.9 5.3 3.2 3.6 5.3 5.3 5.8 5.6 2.6 4.7 4.6 5.3 5.5 3.9 5.4 4.1 3.3 4.9
##  [289] 4.4 3.9 3.8 4.2 3.1 4.8 4.2 5.0 2.9 3.7 3.4 4.9 5.4 5.6 3.6 3.1 5.6 5.4
##  [307] 5.6 3.5 4.1 5.1 4.8 4.2 4.4 4.7 4.3 3.8 3.2 4.3 4.1 4.2 5.1 5.7 4.4 4.6
##  [325] 2.5 3.7 4.9 3.6 4.5 6.2 4.2 2.4 3.9 4.5 5.5 4.5 2.7 3.4 4.9 3.3 4.9 3.1
##  [343] 5.7 5.5 4.9 2.6 3.6 3.4 5.2 3.3 4.2 4.6 3.5 4.1 3.1 2.7 5.2 5.2 4.9 4.5
##  [361] 5.4 5.8 5.0 4.7 5.0 4.6 3.9 4.7 3.4 3.8 5.6 4.3 5.4 3.3 4.2 4.7 4.0 5.1
##  [379] 3.6 4.8 5.9 4.5 4.1 5.6 5.5 4.2 5.4 5.3 4.2 4.8 4.2 5.5 4.5 3.5 6.1 4.4
##  [397] 4.3 5.2 4.2 4.3 4.7 3.9 4.3 2.9 5.9 4.6 5.9 4.5 5.9 5.9 4.9 4.8 2.9 3.6
##  [415] 4.2 5.4 3.2 5.3 3.5 3.6 4.1 4.6 3.6 3.7 4.9 3.5 4.4 4.1 4.6 4.9 4.9 4.9
##  [433] 4.6 4.6 4.1 5.4 4.4 3.0 7.1 6.1 4.6 5.2 4.4 3.8 3.9 2.9 3.6 5.0 2.3 6.0
##  [451] 4.7 6.5 6.0 4.9 4.6 5.7 5.7 4.3 5.4 6.2 2.7 4.0 3.9 5.0 4.2 4.4 4.0 5.1
##  [469] 2.9 4.9 4.6 3.5 6.5 5.9 6.2 5.2 4.4 5.2 3.4 5.7 5.3 4.3 4.0 4.9 5.0 4.4
##  [487] 3.2 3.6 5.3 3.5 5.7 5.2 3.4 5.2 5.6 5.9 2.3 5.2 4.1 6.4 5.0 5.4 5.8 6.1
##  [505] 4.0 4.4 5.0 3.2 5.7 3.8 5.5 5.6 3.2 3.9 4.4 4.4 4.0 3.0 2.6 3.3 5.3 5.7
##  [523] 2.9 4.1 4.8 5.8 4.3 4.5 3.9 4.3 3.8 4.2 5.4 4.8 6.3 5.0 3.8 5.6 4.1 3.4
##  [541] 3.7 3.6 4.7 4.6 4.9 5.3 5.3 3.8 4.6 4.7 6.4 3.7 2.9 6.2 4.2 2.9 4.2 4.1
##  [559] 3.9 3.8 4.3 3.1 4.7 4.2 5.3 2.7 6.4 4.4 4.0 5.7 3.4 4.0 3.0 6.4 5.7 3.8
##  [577] 5.2 3.5 3.5 4.7 2.6 5.3 5.5 4.0 3.9 3.8 6.8 3.6 4.5 5.4 4.4 5.3 4.4 4.0
##  [595] 3.6 4.3 6.5 4.4 3.8 4.3 3.3 4.3 5.5 2.6 5.0 4.2 2.2 5.0 4.7 4.6 5.0 5.8
##  [613] 3.9 3.2 4.8 4.8 5.1 3.7 5.0 4.6 5.2 4.1 4.6 3.2 3.3 3.8 5.5 4.2 3.8 5.1
##  [631] 4.7 4.3 5.1 5.0 2.7 3.1 4.9 5.0 4.4 3.8 3.4 4.6 4.6 5.7 4.0 4.9 5.6 4.9
##  [649] 4.2 4.3 4.9 4.4 3.9 4.5 4.6 4.5 5.9 5.0 4.2 5.4 4.0 4.4 5.0 5.2 5.0 2.0
##  [667] 5.1 4.5 5.6 3.2 4.6 5.6 4.8 4.3 5.9 3.2 2.9 4.4 3.5 4.6 5.5 6.0 4.5 2.8
##  [685] 4.5 6.4 4.4 4.0 4.2 4.1 3.4 5.8 5.0 4.6 3.2 4.8 4.5 4.8 4.9 5.4 4.3 4.1
##  [703] 3.9 3.2 3.2 4.2 6.4 4.2 5.4 3.2 1.9 4.2 4.0 4.1 4.1 4.3 5.0 4.5 4.9 5.4
##  [721] 4.5 4.9 4.0 4.0 4.5 6.2 4.8 3.0 5.0 5.2 4.3 4.2 3.4 5.3 4.1 4.0 5.7 5.2
##  [739] 4.0 5.5 3.5 4.0 3.6 4.2 4.0 3.9 2.6 6.2 3.8 5.4 3.8 4.4 3.9 4.7 4.6 4.3
##  [757] 5.9 5.4 3.0 4.4 4.3 5.3 4.6 4.2 2.2 4.6 5.0 4.2 3.2 3.6 5.1 4.8 3.8 4.0
##  [775] 6.3 4.6 5.0 5.6 4.7 4.8 4.0 3.6 4.2 5.1 4.4 4.3 4.5 3.6 3.4 3.7 4.1 4.9
##  [793] 4.9 5.3 3.8 4.7 5.1 5.1 6.2 3.6 3.1 5.1 4.7 5.5 5.8 4.3 4.3 3.3 5.1 4.0
##  [811] 3.8 4.8 6.6 5.9 5.0 2.3 6.5 3.7 4.4 5.8 3.8 2.7 3.9 4.4 4.5 4.1 5.3 4.9
##  [829] 4.7 4.3 4.0 3.3 4.3 4.4 5.4 6.0 3.8 3.3 5.1 4.0 4.9 3.3 3.9 3.5 5.5 4.8
##  [847] 4.4 3.9 3.2 4.1 4.0 4.1 5.0 6.3 3.3 5.7 2.8 5.5 4.4 4.6 2.3 5.2 3.6 4.7
##  [865] 4.2 5.5 6.0 4.5 5.1 3.2 5.1 4.3 4.0 6.0 4.7 3.5 5.4 3.2 6.3 4.6 4.5 4.9
##  [883] 4.7 5.1 4.5 3.8 3.6 3.7 6.4 5.3 4.2 4.8 3.8 3.4 2.7 5.1 4.9 4.4 4.1 4.1
##  [901] 2.9 4.8 6.2 4.0 3.9 4.4 3.8 5.6 4.2 4.3 3.6 4.3 3.8 5.3 4.5 3.8 3.3 5.7
##  [919] 3.7 4.2 4.0 3.9 4.8 5.3 4.2 4.3 4.4 6.0 5.1 5.3 6.1 4.7 3.7 2.5 3.7 3.7
##  [937] 5.9 3.4 4.7 3.3 3.8 3.7 4.0 4.2 4.7 3.5 3.1 4.1 3.8 5.0 4.1 3.8 2.8 4.2
##  [955] 5.9 5.8 4.3 5.2 4.5 5.8 4.5 5.2 4.0 4.6 5.3 3.9 4.4 4.8 3.9 4.6 4.2 6.6
##  [973] 3.1 5.6 4.5 3.0 5.0 4.6 3.6 5.1 4.8 5.9 4.0 5.2 4.4 4.6 5.5 2.9 5.3 4.8
##  [991] 6.2 2.5 4.3 4.0 4.2 4.1 4.3 4.5 3.7 4.6
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
##    [1] 3.4 3.7 4.6 5.2 4.9 3.1 4.2 5.2 3.9 3.9 4.2 4.2 4.3 5.6 5.5 5.0 4.1 4.9
##   [19] 4.1 3.3 5.5 3.7 4.2 2.9 4.7 3.8 4.3 3.1 3.4 5.7 3.1 4.2 4.8 4.5 5.1 3.7
##   [37] 5.0 4.9 4.5 5.2 4.1 4.7 5.0 5.3 3.9 3.6 4.1 6.1 4.3 6.6 3.3 4.9 3.2 5.2
##   [55] 3.3 5.3 5.3 3.6 4.6 3.5 2.9 6.4 2.9 3.6 6.0 5.9 4.0 4.8 5.0 4.4 4.7 4.4
##   [73] 4.5 7.0 6.1 3.8 4.9 5.2 5.6 3.8 4.6 3.5 3.0 3.7 4.3 4.3 4.5 3.9 3.3 4.6
##   [91] 3.9 4.9 3.3 6.5 5.1 4.1 2.6 5.4 3.8 6.6 4.9 5.8 4.9 4.4 5.9 5.9 3.7 4.0
##  [109] 4.0 6.3 4.5 6.3 5.4 5.7 4.6 3.3 4.0 4.8 4.9 3.6 3.1 3.9 6.4 5.2 4.1 4.0
##  [127] 6.1 4.5 5.4 5.3 5.7 3.2 4.8 4.5 2.3 5.1 4.4 4.7 4.4 4.5 4.6 5.0 4.2 4.1
##  [145] 4.6 3.2 5.4 4.2 3.0 5.0 4.8 5.3 5.0 4.0 3.8 3.6 2.9 4.1 4.7 3.3 4.8 4.9
##  [163] 4.0 4.8 5.4 4.1 4.7 3.3 3.8 5.3 3.8 2.7 4.4 4.9 4.2 4.2 4.6 4.1 3.3 4.1
##  [181] 4.0 4.2 5.9 4.0 3.8 3.2 3.3 3.5 3.2 4.1 3.6 5.2 4.7 5.1 4.4 5.0 4.3 4.9
##  [199] 4.0 3.7 2.0 4.7 4.8 4.5 3.7 4.3 4.9 5.2 5.3 3.6 5.3 3.3 4.5 4.3 3.4 3.1
##  [217] 4.1 5.6 4.6 4.8 2.7 4.0 3.1 4.0 3.3 4.5 4.7 5.5 4.6 2.9 6.0 4.3 4.6 4.8
##  [235] 4.9 3.9 5.4 5.7 4.3 4.6 5.0 4.5 4.2 4.8 4.9 3.7 4.3 4.5 3.0 4.3 5.2 5.2
##  [253] 4.4 3.9 6.1 3.8 5.2 5.4 4.3 5.3 5.7 4.9 5.1 5.2 4.3 4.9 5.0 4.9 4.0 4.1
##  [271] 5.9 3.9 4.1 2.2 5.1 1.9 3.5 3.6 6.6 5.4 4.8 5.1 3.7 3.8 3.4 4.0 4.6 6.2
##  [289] 6.4 4.1 5.4 3.6 6.1 4.0 5.5 3.2 5.4 3.3 4.5 5.7 4.5 4.8 3.6 3.3 2.7 4.6
##  [307] 4.3 5.8 4.1 4.2 4.9 5.2 5.7 3.5 4.1 4.3 6.4 5.1 3.9 4.2 3.6 5.0 5.0 3.9
##  [325] 4.5 5.2 3.9 4.2 4.6 6.0 4.9 3.8 3.7 4.2 5.0 5.1 4.8 4.0 4.0 3.9 4.7 4.1
##  [343] 3.2 3.4 6.0 3.4 5.2 4.5 3.8 4.7 3.6 5.2 3.5 4.9 4.3 3.7 4.8 4.8 4.9 4.6
##  [361] 3.9 2.6 4.7 5.0 3.2 3.7 3.9 4.4 4.0 2.9 4.6 5.6 4.5 4.8 4.2 2.8 4.6 4.6
##  [379] 5.3 4.2 5.0 4.5 5.6 6.1 7.0 4.7 4.3 3.4 4.3 4.0 3.1 4.8 3.4 4.1 3.9 3.7
##  [397] 3.6 3.9 3.5 4.6 6.1 3.8 5.1 3.6 5.3 3.8 5.2 4.8 5.8 4.8 5.8 5.7 3.7 5.0
##  [415] 5.5 4.8 5.0 4.1 3.2 4.5 4.6 3.3 5.4 4.2 4.2 3.2 3.8 1.9 4.3 3.5 6.0 4.8
##  [433] 3.4 5.0 3.8 4.1 3.8 5.8 4.3 6.3 3.2 4.5 4.7 4.6 3.8 2.7 4.2 4.8 4.7 3.6
##  [451] 4.4 6.1 4.9 5.0 4.6 4.1 3.3 5.9 4.9 4.4 4.9 4.5 4.6 5.0 6.0 4.0 4.3 3.3
##  [469] 2.6 4.4 4.6 5.8 4.6 4.5 5.2 4.3 6.9 5.1 5.3 3.9 4.4 3.9 4.9 3.8 5.8 3.9
##  [487] 5.3 4.8 4.5 4.7 3.8 4.8 5.8 4.1 4.2 5.8 4.7 4.1 3.1 4.6 5.0 3.3 4.9 3.2
##  [505] 4.2 4.2 4.3 5.0 6.1 3.5 6.7 4.1 3.5 4.0 5.5 4.3 4.9 4.8 5.7 3.9 4.6 3.9
##  [523] 5.2 3.1 5.3 3.5 5.4 3.1 3.9 5.2 4.3 4.5 3.4 3.0 5.6 3.2 5.6 4.4 3.1 5.1
##  [541] 4.8 3.8 5.5 4.0 4.3 3.5 5.5 5.3 5.5 2.3 3.6 4.6 4.2 2.3 5.4 4.0 4.7 4.3
##  [559] 4.8 3.5 4.0 4.9 4.6 4.2 5.3 3.8 5.4 4.6 6.0 3.8 3.7 5.8 5.8 4.1 3.5 5.7
##  [577] 4.3 3.1 5.7 4.8 5.9 3.5 3.1 5.7 4.2 2.7 6.1 4.5 4.2 5.5 3.7 4.9 4.7 4.4
##  [595] 5.7 5.5 2.1 5.0 5.8 4.7 3.8 3.6 4.5 4.2 4.3 4.2 5.5 2.7 4.5 3.4 6.6 5.0
##  [613] 3.2 5.7 6.0 5.1 4.8 6.3 4.4 4.4 5.0 5.0 6.2 2.6 4.6 4.9 5.8 4.3 5.2 2.2
##  [631] 4.2 3.5 4.0 3.4 5.9 4.5 3.0 4.1 4.1 5.7 5.2 4.0 3.8 4.1 3.9 3.5 5.1 2.9
##  [649] 5.7 4.5 4.1 4.7 4.5 5.5 4.0 5.3 6.2 4.3 3.4 2.9 4.1 4.7 3.5 5.1 6.4 6.0
##  [667] 4.1 4.6 2.7 4.9 5.0 4.1 4.8 4.2 4.6 4.8 4.3 4.0 5.6 3.7 4.9 3.9 4.8 4.5
##  [685] 4.7 2.8 5.2 3.0 4.6 4.5 3.0 5.7 5.0 5.0 5.4 4.7 5.4 4.3 4.7 4.7 6.0 4.5
##  [703] 4.6 4.8 3.9 4.2 4.5 4.1 4.8 4.6 6.6 5.5 3.8 2.8 4.0 4.3 4.1 5.9 4.9 4.0
##  [721] 4.0 4.9 2.3 4.5 5.9 5.2 4.2 4.9 3.6 4.6 6.1 4.8 1.5 3.9 4.9 5.9 4.1 2.8
##  [739] 4.2 3.9 5.5 4.5 3.3 3.1 6.5 4.8 2.7 3.9 4.1 5.9 4.8 4.1 6.6 4.9 4.6 4.9
##  [757] 3.9 3.8 4.1 5.8 4.1 3.4 4.9 4.8 5.3 4.5 3.8 5.6 4.9 4.5 6.7 4.6 3.5 4.4
##  [775] 4.6 3.8 4.9 4.6 3.2 3.1 5.4 2.8 3.2 5.5 4.4 2.6 7.0 6.1 3.4 4.5 4.3 3.6
##  [793] 4.9 5.2 4.7 2.9 4.4 4.9 4.3 5.1 2.5 5.2 5.0 4.5 5.2 5.3 3.7 4.6 4.5 4.4
##  [811] 5.6 3.7 3.9 5.2 4.2 6.2 4.2 4.7 4.0 5.4 4.4 3.6 4.7 3.0 5.8 5.6 6.3 3.7
##  [829] 2.8 3.5 4.3 4.0 3.7 5.3 3.7 4.0 4.5 5.0 6.3 4.8 5.5 4.1 5.4 5.2 5.1 5.0
##  [847] 4.7 4.1 5.9 4.6 3.7 5.5 3.8 3.6 4.1 4.6 3.6 5.4 6.1 4.9 5.0 4.8 3.6 5.4
##  [865] 5.3 4.9 4.7 5.3 3.6 3.2 2.9 3.3 5.4 4.2 2.6 2.8 4.1 6.7 5.4 5.7 3.8 4.2
##  [883] 4.0 3.1 4.0 4.4 5.1 3.4 4.7 5.1 4.6 5.1 4.6 4.0 4.4 4.5 3.9 3.2 4.4 2.7
##  [901] 3.2 3.1 3.0 4.0 5.3 4.5 4.0 5.3 3.8 2.8 4.0 4.8 5.2 4.7 4.7 5.9 4.4 4.9
##  [919] 5.6 5.5 5.1 5.7 4.9 5.0 3.8 4.0 4.3 4.8 5.6 5.2 3.6 4.7 5.4 3.8 5.0 5.0
##  [937] 5.1 4.7 5.6 2.8 5.7 4.8 4.1 5.3 4.6 5.1 4.2 4.7 7.4 4.5 5.5 3.9 4.8 4.3
##  [955] 6.5 6.6 4.6 5.4 5.4 5.9 6.1 5.5 4.1 5.3 3.8 5.3 4.6 3.8 5.9 3.9 3.6 4.4
##  [973] 6.5 4.1 5.0 4.2 4.8 6.1 3.9 3.7 5.0 4.0 5.6 4.3 4.3 4.1 5.6 4.8 3.8 4.4
##  [991] 4.4 4.2 3.4 5.3 4.4 5.9 4.3 4.6 5.1 6.8
## 
## $func.thetastar
## [1] -0.003
## 
## $jack.boot.val
##  [1]  0.52735294  0.36951872  0.28813056  0.18035191 -0.02683616 -0.04955752
##  [7] -0.17344633 -0.30862069 -0.41277778 -0.50000000
## 
## $jack.boot.se
## [1] 0.98497
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
##    [1] 4.0 6.2 5.9 4.1 3.5 2.6 6.1 5.8 5.5 5.2 3.9 1.7 5.2 5.4 6.2 4.0 4.7 5.0
##   [19] 4.4 4.9 4.8 5.3 4.6 3.5 4.3 4.0 2.5 3.9 4.4 2.9 6.9 6.9 3.6 3.6 4.8 4.8
##   [37] 4.5 3.7 4.5 4.1 5.7 3.2 4.7 5.3 6.0 4.4 3.7 1.9 5.1 4.1 5.2 5.5 3.9 5.4
##   [55] 5.5 3.9 6.2 5.1 5.3 4.3 4.7 4.9 5.0 4.0 3.9 4.7 4.3 3.8 5.4 5.0 6.9 5.0
##   [73] 6.6 4.3 3.7 4.4 4.2 4.6 5.7 5.5 5.1 4.2 3.8 4.5 3.9 3.4 2.7 5.3 4.7 3.1
##   [91] 5.1 5.4 5.1 5.3 3.8 4.1 4.0 5.1 4.3 4.6 3.1 4.5 3.8 3.9 4.6 5.5 3.5 3.2
##  [109] 5.3 2.9 5.2 5.2 5.5 3.1 4.8 5.6 4.1 5.6 4.4 5.4 4.4 5.7 4.9 4.3 4.8 5.0
##  [127] 3.8 4.2 4.7 4.6 4.0 4.0 4.5 4.5 4.3 4.6 5.0 6.5 4.3 4.5 3.5 4.3 4.3 4.3
##  [145] 4.7 4.6 5.1 4.6 3.4 4.3 4.6 5.6 5.9 4.0 6.6 3.7 3.7 4.4 3.5 3.9 4.7 2.9
##  [163] 4.3 4.8 3.6 4.4 4.1 4.7 6.3 4.2 4.7 4.4 3.7 4.8 2.9 4.4 4.0 4.1 3.4 3.4
##  [181] 5.5 5.9 4.1 4.8 4.5 5.3 3.5 3.6 4.2 4.8 4.0 4.5 2.8 3.1 5.5 5.4 3.1 5.1
##  [199] 4.5 4.3 4.7 4.8 4.1 4.5 5.0 4.6 4.6 4.9 4.2 3.5 3.5 3.3 5.4 4.8 5.3 3.3
##  [217] 3.6 5.6 4.5 5.8 3.1 6.0 5.2 3.2 5.2 5.0 5.4 5.0 4.9 3.4 5.4 4.5 4.7 5.0
##  [235] 4.0 2.7 6.6 4.5 4.7 4.9 4.7 5.3 4.0 3.6 2.8 4.0 5.1 4.4 5.0 5.3 4.8 3.1
##  [253] 5.6 4.1 4.9 3.0 3.9 2.4 4.1 5.5 3.7 4.9 3.7 4.5 3.6 4.0 5.7 3.2 4.2 5.0
##  [271] 3.0 4.9 4.4 5.3 4.4 5.0 4.5 4.8 4.0 3.8 6.0 4.0 4.0 6.0 3.5 5.2 5.6 2.7
##  [289] 3.8 4.2 4.5 3.8 3.9 4.5 4.5 4.4 4.5 3.7 4.4 4.7 3.6 5.1 4.8 5.9 4.9 4.6
##  [307] 3.6 4.3 4.4 4.8 4.4 5.1 5.0 4.8 4.4 6.0 4.8 4.9 2.0 2.3 5.6 2.9 5.4 4.6
##  [325] 5.7 4.4 5.0 5.7 5.6 4.5 5.5 3.8 3.3 2.9 4.1 4.6 4.2 5.2 4.6 5.3 3.9 5.5
##  [343] 5.2 3.7 4.1 4.7 5.3 5.1 4.1 3.4 4.8 5.5 3.6 3.2 4.0 4.9 3.3 4.3 3.9 4.8
##  [361] 4.6 4.4 4.2 5.5 4.0 5.2 5.1 2.8 3.8 3.5 4.5 4.8 3.7 2.8 2.7 5.4 4.9 2.7
##  [379] 4.6 5.8 5.0 4.3 5.0 4.3 6.1 4.6 5.0 5.7 4.5 5.5 5.4 3.4 2.8 5.0 3.4 4.4
##  [397] 4.7 4.8 3.9 3.2 5.9 5.3 4.1 3.4 5.2 4.9 5.8 4.4 5.6 6.2 4.0 5.0 7.2 5.4
##  [415] 3.4 5.2 4.8 4.0 5.0 4.7 5.6 4.9 4.6 3.8 5.7 5.0 4.6 6.0 3.4 3.9 5.8 4.0
##  [433] 5.6 2.5 4.8 3.7 6.4 3.6 3.8 5.6 4.5 5.4 3.2 3.9 2.7 4.1 4.8 4.0 4.3 2.9
##  [451] 4.8 5.4 4.3 6.2 4.2 4.3 5.0 3.9 5.0 5.5 2.4 3.7 4.0 4.0 4.3 4.6 5.1 3.9
##  [469] 2.4 4.7 4.2 4.9 3.3 5.0 5.7 6.0 3.4 4.1 4.6 5.0 4.3 3.0 3.7 4.1 4.4 4.2
##  [487] 4.7 4.9 4.8 5.8 3.7 4.0 5.3 4.1 5.8 5.4 5.1 5.6 5.6 4.0 4.7 3.2 5.9 4.7
##  [505] 3.5 5.3 4.3 4.7 5.2 4.8 3.9 4.1 3.1 4.6 4.7 5.2 2.5 4.4 3.7 5.9 4.0 4.6
##  [523] 3.8 4.7 3.4 2.3 5.2 3.7 4.4 3.4 5.3 4.6 3.7 2.7 4.6 4.4 5.6 3.8 5.2 4.2
##  [541] 3.8 4.0 5.3 5.1 5.5 5.6 5.5 4.3 4.3 4.7 4.8 4.6 4.1 5.9 3.7 4.7 4.0 4.9
##  [559] 4.3 3.8 4.2 4.5 4.5 4.4 6.1 6.4 4.8 4.8 6.0 4.0 3.0 4.0 3.7 4.4 4.4 5.6
##  [577] 5.9 4.1 3.2 3.5 4.3 4.5 5.2 5.2 3.9 5.0 3.9 5.2 3.8 5.3 4.0 5.4 2.9 3.9
##  [595] 4.7 3.7 4.4 3.2 3.8 4.2 5.2 5.1 4.2 3.6 3.8 4.1 3.6 3.9 3.5 4.9 4.7 3.8
##  [613] 4.7 4.6 3.3 5.1 4.4 5.7 6.1 5.4 4.9 4.7 4.9 4.1 3.7 4.3 4.2 4.7 5.3 4.8
##  [631] 3.2 3.9 4.5 4.8 4.0 4.4 3.0 5.4 4.6 3.7 4.0 4.2 2.7 2.5 4.4 5.3 4.6 6.0
##  [649] 4.5 2.6 5.8 4.9 5.0 5.2 3.0 5.3 3.3 6.6 4.2 6.0 5.9 4.8 4.0 5.7 4.2 2.9
##  [667] 3.9 3.7 5.6 3.3 3.4 6.0 4.1 4.2 4.9 6.1 4.6 4.6 4.9 5.4 4.5 4.8 6.3 3.7
##  [685] 4.9 4.1 5.3 5.8 5.1 4.3 4.6 4.5 3.9 4.2 4.6 5.6 4.7 3.8 5.4 4.7 3.2 5.3
##  [703] 5.3 5.0 4.2 4.9 3.1 4.0 4.5 4.1 4.6 5.2 4.8 3.3 4.5 3.1 2.9 3.4 4.7 3.4
##  [721] 5.1 2.8 5.1 5.0 4.9 3.5 6.3 5.9 3.7 4.6 4.6 4.7 5.0 2.7 3.5 6.9 4.3 3.5
##  [739] 5.4 3.9 5.0 5.2 4.6 5.4 3.3 5.3 5.2 5.4 4.4 4.3 5.4 4.9 6.1 4.6 4.0 5.0
##  [757] 5.3 6.5 4.1 4.1 5.6 4.1 4.6 4.1 5.6 4.2 4.8 3.9 5.1 5.1 5.1 2.3 4.2 5.4
##  [775] 4.4 4.9 5.7 4.1 4.4 2.8 2.6 3.4 4.9 3.8 4.7 4.1 6.1 4.8 4.8 4.1 4.5 3.6
##  [793] 4.9 5.8 4.4 4.8 3.8 4.0 5.0 5.3 5.4 4.6 4.5 3.3 3.6 3.0 5.2 5.2 5.1 6.2
##  [811] 3.6 4.0 4.9 4.7 5.3 3.6 4.4 5.5 4.6 4.5 2.6 3.7 4.5 5.0 4.5 3.9 5.4 3.5
##  [829] 4.5 3.1 3.9 4.1 4.8 3.5 4.8 4.5 4.0 4.6 4.2 3.4 5.6 5.6 4.4 5.5 4.2 3.2
##  [847] 4.8 3.5 5.5 6.2 3.5 4.8 4.6 3.2 3.9 4.0 4.2 3.1 4.5 3.7 5.1 4.7 3.3 3.9
##  [865] 5.6 4.2 3.7 4.5 4.4 3.4 3.2 5.0 6.2 4.3 5.1 3.1 3.2 3.7 5.4 2.6 5.1 3.6
##  [883] 2.9 4.3 6.1 3.0 5.5 5.4 3.6 5.3 5.0 4.8 5.8 3.9 5.1 5.6 4.0 5.2 4.3 4.8
##  [901] 2.9 4.8 4.7 5.7 3.3 2.3 4.1 4.5 5.4 5.1 4.4 4.0 3.9 3.7 5.1 5.5 3.4 3.8
##  [919] 3.5 6.0 6.0 4.6 5.2 3.7 3.5 3.6 3.3 4.6 3.4 3.4 5.7 4.2 5.1 3.6 4.2 5.6
##  [937] 5.3 5.0 5.1 4.1 6.2 6.4 3.0 2.2 4.9 3.5 4.5 4.7 4.3 4.4 4.3 4.2 4.1 3.8
##  [955] 4.5 3.3 5.3 2.9 4.4 3.7 4.7 4.3 5.0 5.6 6.4 3.6 4.6 3.5 5.1 3.8 6.0 4.9
##  [973] 4.8 4.4 4.0 5.0 5.9 5.4 5.3 5.6 4.3 3.5 5.9 5.0 4.2 4.2 3.7 3.9 2.7 4.2
##  [991] 6.2 4.5 4.4 3.4 6.0 5.6 5.6 2.6 4.8 5.2
## 
## $func.thetastar
## 72% 
##   5 
## 
## $jack.boot.val
##  [1] 5.400 5.300 5.300 5.300 5.000 5.000 5.000 4.708 4.700 4.500
## 
## $jack.boot.se
## [1] 0.875125
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
## [1] 1.121822
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
##      shape       rate   
##   2.2233869   4.0854240 
##  (0.9294184) (1.9149984)
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
## [1] 0.5746130 0.2654530 1.4335035 0.3049508 1.5449138 1.1767897
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
##    [1]  0.6146353361  1.3436461849 -0.5372288005  1.0119377560  1.9749042614
##    [6]  1.9700599146 -0.5433762803  1.0813833014  1.2139740777  1.2631945767
##   [11]  0.2523782146  1.1619995299  2.1934047011  1.1879920962  1.9356324019
##   [16]  0.7678389421  0.7061492500  0.6547798444  1.7883041190  0.2937665275
##   [21]  1.6497713453  0.1356683195  1.7560750217  1.6617236992  0.0707788556
##   [26]  1.2503351284 -0.5953726842  0.3311223645  0.1257567624  1.1483009879
##   [31]  0.3035078763 -0.4367981727  1.6351738131  0.8905770881  0.6873863330
##   [36]  1.1068761805  0.6605275711  0.9917126324  0.7117137593 -0.0575523849
##   [41]  0.8737685328  1.1923879535 -0.2226111957 -0.9454599484  0.7725998777
##   [46]  1.2016916198  1.1700739292  1.4982506708  0.5516672474  0.6272088852
##   [51]  1.4118646596  0.4875376262 -0.3824149949  1.0954369873  1.1799930541
##   [56]  1.0142217967  2.1387035038  1.2158902177  1.1778315885  0.1050386930
##   [61]  0.0842117721  1.7247597833  1.0544741526  0.6934710750  0.6246634214
##   [66]  1.4705818566  1.0786962767  1.9884529201  0.5489915494  0.7196729804
##   [71]  1.7503603460  0.8287628710 -0.3102562852  0.7134296725  0.6868637971
##   [76]  1.0887730841  0.5718653719  1.7798881614  1.3230593888  0.2458768659
##   [81]  1.2299158416  0.9305560668  0.9319919338  1.0789376023  1.1647438404
##   [86]  2.0894070389  1.4374425946  1.9058008427  1.3368126730  2.1064878528
##   [91]  0.3234144427 -0.0043044438  0.6232921666 -0.2095141017  0.6942109980
##   [96]  0.9863429507  0.9084727315  1.1586688832 -0.5107275423  0.7837254777
##  [101]  1.7432085214  0.5036757694 -0.0835032760  1.0890514897  1.1416378745
##  [106]  1.0792017960  0.3300123020  1.8489690625 -0.2868032307  0.7379222935
##  [111]  0.6100556168  2.1428026619  1.2741578476  0.6112668611 -0.0745543683
##  [116] -0.1330295093 -0.0379924438 -0.1058337994  0.4877319643 -0.1832066695
##  [121]  0.3925941192  0.6255626634  0.0508308148  0.7608216454  1.1779066501
##  [126]  1.4672167782  1.4742155352  0.3660097599  0.6884547381  0.3461694969
##  [131]  1.7040803821  1.2380742468  1.2406058541 -0.6390347171  1.1117842039
##  [136]  1.2389834415  1.1185336602  1.7824794474  1.2942996707  0.6922517041
##  [141]  0.8821039292 -0.0150366143 -0.5755635254  1.6224177321  0.8147131305
##  [146]  1.2478964469  1.6769774897  0.9993515331  0.7097985962  1.8477022057
##  [151]  0.6398028170  0.7554063693  1.0852530146  0.7749212898  1.1151321018
##  [156]  0.7253124069 -0.2308795071  0.9864890545  0.7956562013  1.6407260840
##  [161] -0.4982176715 -0.5824670043  0.8370354703  2.1425173950  1.8031829040
##  [166]  0.9581163574  0.8242882186  1.4167876095  1.2741134255  1.6207814641
##  [171]  1.0320970682  1.4209171779  1.5060853479  0.7809880773  2.0496923144
##  [176]  0.5246507951  1.3255296292  1.4039922793  2.2006002350  0.6547856514
##  [181]  1.2805960606  0.7860544396  1.3370989211  1.7397767530  0.9959170605
##  [186]  0.8156844681  1.7775082954  0.8683971196  0.2104920718  1.8824828695
##  [191]  0.4437259873  1.1853491036  0.8463771097  0.8067825571  2.0887296947
##  [196] -0.7819876381  1.1580250190  0.2880156661  1.9676090617  1.3395778258
##  [201]  1.1525508432  0.3098290861  1.3213485245  1.2224088334  1.2761525039
##  [206]  1.0611578936  1.9127530462 -0.7756603694  1.6385982653  0.6604942923
##  [211]  2.2567738845  1.0887238807  0.0195173463  1.3622502532  1.4090391016
##  [216]  0.7332276952  1.9278970415  1.3120836890  1.0585508297 -0.3021091183
##  [221]  0.4739203438  0.7785937049  0.6935991306  1.1710172731  0.7453046998
##  [226]  2.1443003435  0.7140979228  0.0864022095  1.4861751464  0.9073496811
##  [231]  1.2179110695  0.8555614184  1.6505300751  1.2520772074  0.4385539695
##  [236]  1.8978273854  0.2646543649  0.7840117513  1.5649116269  0.3648667123
##  [241]  1.5128147329  0.3441497694 -1.3300747377  0.7162566937  1.3029168353
##  [246] -0.2487424288  1.2528603734  1.1284932783  1.5882335195  1.2149291646
##  [251] -0.3297734044  1.9110593551  1.2173774801  0.4426798567  1.1623937934
##  [256]  0.8339539250  1.1559976421  0.9305985803  1.2198357244  0.4937899740
##  [261]  1.8179033971  1.2993572071  1.3115026794  1.7281878904  1.7366651220
##  [266]  0.8801784945  0.3408936751  1.2671155565  1.1579257206  1.4748041475
##  [271] -0.0015022471  0.7501740642  1.8429538179  0.9288186072  1.0712012039
##  [276] -0.5994870319  1.2105727718  1.6739143336 -0.1890978891  1.7268195298
##  [281]  1.8683660786  0.6754509134  1.1610168900 -0.5423235856  0.5061243660
##  [286]  0.3447222049  0.5060142248 -0.2579017576  0.8021398544 -0.3160546659
##  [291]  0.0907163945  1.2434014602  1.5070321711  1.0969935007  1.7316327078
##  [296]  0.3521490185  0.5502890175  1.0742952737  0.3412742250  0.7456121005
##  [301]  1.3441600371  1.7043167362  1.3174963051  0.6643816960  1.0281905977
##  [306]  1.1087025670  1.2523761880 -1.1256041629  1.7497998196  1.5884524597
##  [311]  1.2170641665  1.1623937934  1.6321410497  1.6195613601  0.2857350099
##  [316]  1.1712500172  1.0206240889  1.6381328511  0.8849640252  0.7192453355
##  [321]  1.0756723226 -0.2038031808  1.3406897164  1.0430507299  1.2904217229
##  [326] -0.0736135664  0.3031126174  1.3646174863  1.4146999732  0.5796609707
##  [331]  1.1918287077  1.1064506599  1.0234189300  0.7485999200  0.8424896960
##  [336]  1.2740052450  0.9111689493  1.9136296196  1.3608085948  0.3760176001
##  [341]  1.2103255054  0.7509520485  1.9184606376  1.9283228709  1.1888147591
##  [346]  0.7396016281  1.3690934635  1.3907626865  1.4399249917  0.4632027216
##  [351]  0.2732233413 -0.2538397914 -0.4144964182  1.5793624235  1.2604736852
##  [356]  0.7015928649  1.0019733447  0.8022971358  0.6218447403  0.7755336697
##  [361]  1.0890514897  1.0071711553  0.6825313860  1.0722825948  1.1003938747
##  [366] -0.3164737565  0.8841433605  1.2806851905  0.7889340173  1.1702993515
##  [371] -0.1796809766  0.7233374829 -1.6543753460  1.0785474626  2.1730048249
##  [376]  1.3414238425  1.5095652862  0.7454788493  1.1063197412  0.2752420403
##  [381]  1.2861784151  0.0817271882  0.6256193693  1.2106674066  1.0282764382
##  [386]  0.3144567204  0.3733130137  1.2962090404  1.3017154401  0.9150509411
##  [391]  1.2168313313  0.7413749706  0.6396408976  1.3783933276  0.9777924346
##  [396]  0.3977532089  1.1754302293 -0.0438330095  1.6911930866  0.9575115258
##  [401]  0.5918405444  1.5324742557  1.9104237828  1.6879266939  1.3737284169
##  [406] -0.1035497876  1.2445115231  2.0113130574  0.7217731882  0.8568387887
##  [411]  1.2876927908  0.9688486462  1.3402062684  1.2422280935  1.5473858980
##  [416]  1.0632003522  0.9562190705  1.0103353543 -0.4242119762  1.1079505729
##  [421]  1.4664279151  1.6985826315  1.6963783307  0.4886119523  1.1483979020
##  [426]  0.7645167220  0.3275599009  1.1010335441  0.4614610367  1.5127334996
##  [431] -0.7043135426  0.6551080138 -0.7067442102  0.9810479803  1.6994356602
##  [436]  1.1380205918  1.5785512528 -0.0119956200  0.2978002721  0.5679636551
##  [441]  1.3265610642 -0.9891348973  1.0066589475  0.7108840133  1.0755572570
##  [446]  0.6392965968  1.0508127433  1.2118925894  1.6830140699  0.9119599267
##  [451]  1.3034758316  0.6977568880  0.6690453293  1.9907807533  0.6183722685
##  [456]  0.9832049594  0.6547085469  1.8826070888 -0.3748022745  0.6387289985
##  [461] -0.3064639023  0.2357763011  0.2435575769 -0.3074676404  1.9358873971
##  [466] -0.2489239371 -0.0657281622  1.7392896714 -0.3893024576  1.0640392844
##  [471]  1.3834629218  1.2622312514  1.3053962529  0.1877358502  1.1292354304
##  [476]  0.9423009317  0.8425479968  0.3063417608  0.6521719744  1.1497346730
##  [481]  0.2763965134  1.4619700707  0.1203811405  1.1802618548  1.8836961819
##  [486]  0.7029912718  0.7150079859  2.2734863317 -0.0984759438  1.9307549764
##  [491]  0.2115907309  2.0797882543  0.9122877832  1.3023681160 -0.2193320720
##  [496]  0.1584557205  0.7421977487  1.1171684006  1.8847683268  0.6582488511
##  [501]  0.4616863409  1.3957093908  0.3733130137  0.6073737384  1.8771211617
##  [506]  0.2568628206  0.2960575421  0.9257360747  0.8164577880 -0.6913794763
##  [511]  0.3895906671 -1.0355615511  0.9655312811  1.1616795566  1.1805555118
##  [516] -0.7430971728  0.6772695394 -0.0527751574  0.6634085841  1.3129423217
##  [521]  0.3548754109  1.9975593342  0.5678830280  1.5646238352  1.0879068390
##  [526]  1.8048204561  1.8217366039  1.7068968292  1.0684046830  0.2937579230
##  [531]  1.7636327684  0.0030160497  1.4282803874  1.3702975654  0.7596607376
##  [536]  0.9617582817 -0.6980584692  0.7451442748  1.6293301821  1.2411035931
##  [541]  1.8137495502  1.9232411571  1.4165427249  1.0988535779  1.2533836278
##  [546] -0.1117826310  0.6827188856  1.4578810827  0.7234806016  0.6797623376
##  [551]  2.1153440264 -0.3960107263  0.7771557807  0.3680615323  0.8973269392
##  [556]  1.3075111082  0.9770282934  1.6498774794  0.3979231755  1.0853522978
##  [561]  0.1954271900  1.9996596221  1.8316378041  1.2588490679  0.7307101194
##  [566]  1.9058686686  1.4836631024  1.7170402840  2.0560758812  0.3301096846
##  [571]  1.1050121380  0.2839452501  1.2284371933  0.4065275915  1.8676802377
##  [576]  1.9035508661  1.1077977976  1.2001883809  0.8197779512  0.7284803827
##  [581]  1.8542271405 -0.9638973448  0.8237301905  0.8189607818  0.9938083778
##  [586]  0.6871051221 -0.7179952354  1.3086325479  0.9917336981 -0.8192583855
##  [591] -0.0511734445  1.8193639829  1.6764412726  0.6235678346  1.5727986722
##  [596]  1.2791162938  1.0610391647  1.1438302036  1.2279369291 -0.6357117163
##  [601]  2.2940762779  0.7692051069  0.7570705133  0.7453093838 -0.0066230519
##  [606]  0.7874644146  1.1218220279  0.8637757004  1.7979968060  0.4357101978
##  [611]  1.0611598223 -0.2417596199  0.3708187266  1.2440967730  1.8997040118
##  [616]  2.1254269951  0.9083612521  1.2475500244  0.3257253651  1.9419697860
##  [621]  1.1896925610 -0.2106840646 -0.0938374395  0.2540759389 -0.5364450151
##  [626]  0.6493294227  0.6316409442  1.1902859402 -0.1033094919  1.0955410550
##  [631] -0.1619177697  0.4590514839 -1.0369872260  1.3551676417 -0.0512071402
##  [636]  1.0403100649  0.7144523749  0.8798083661  1.2348481565  1.8760145013
##  [641]  0.8152309195  0.9809825243  0.6410820831  1.0654500762 -0.1345847680
##  [646]  1.6680197783  1.1483710666  0.2234366891  1.2369503536  0.7777073784
##  [651]  1.1739916146  1.6911601894 -0.2381271584  0.8017896563  1.2177143696
##  [656]  0.7052759058 -1.0886292678  0.2967006500  2.1158653505  0.2777446120
##  [661]  1.4555858784  1.9665562064  0.6540435139  0.0815633713  1.4833222135
##  [666]  1.1540942833  0.7306785414  0.8013815719 -0.4230769853  0.7106759303
##  [671]  0.5890692785  0.9178432993  1.2213640980  1.1878476958  1.4785341828
##  [676]  1.0692884655  0.2342467051  1.7373860044  0.8131180625  0.2193228304
##  [681]  0.1380141919 -0.7983247418  0.4090297988  0.9424285076  1.7170828453
##  [686]  0.3751266843  1.0722658665 -0.3307382793  1.1171705814  1.0866501711
##  [691]  0.9700526819  1.0719882352  0.6968102401  1.6717315241  1.9436069444
##  [696]  1.5715858850  1.0142677500  1.0250566777  0.1092414654  1.1101362942
##  [701]  1.8166047116  0.8308470088  2.0555451441  1.0888688099 -0.0006968002
##  [706] -0.5516693564  0.2711419498  1.3247851154  1.2850509098  0.4608025436
##  [711]  0.6163714896  1.2868861265  1.0681171033  0.1261815378 -0.0935590963
##  [716]  0.4688774152  1.2329873469  0.6222790158 -0.8595503008  0.5961610215
##  [721]  0.6898190159  1.2718199243  1.2812989747  1.7363731178  1.1358485832
##  [726]  1.4773944898  1.5500085092 -1.0033602191  1.8322369155  1.0785683907
##  [731]  1.7997644617  1.2598607162  0.9246301233  1.2471560446  0.3399422861
##  [736] -0.5261804109  1.2348629603  0.0472092795  0.6933986439  0.6206250817
##  [741]  1.2969729844  0.7409588937 -0.1238035337  1.0136084824  1.1450231148
##  [746]  0.8009502813  2.1089995080  0.5166050520  1.0738156861  1.2099874945
##  [751]  0.8746307634  0.2875279336  0.6741351641  0.3994310368  0.7336573102
##  [756]  1.8786459107  1.5256102792 -0.5019587172  1.6304419395  1.1032269406
##  [761]  0.9834342633  1.0177805418 -0.6106750493 -0.5159709200  0.8294715947
##  [766]  1.1125247379  1.1318306439  1.7675398211 -0.0862089477  1.5896299732
##  [771]  1.9753632313  1.2094524764  0.5713461866 -0.4248753575  1.7465618820
##  [776]  1.7766155324  1.2985530839 -0.9758905175  1.1541954914  1.6989713892
##  [781]  0.6037116786  0.6899137689  1.2491904469  1.6152530328  0.1856624804
##  [786]  0.8279689295 -0.4776766066  1.2244002374  1.5911926142  0.6758719345
##  [791]  0.2750987004 -0.1340211468  1.1799930541  1.2213824879  1.1888228530
##  [796]  1.4130613578  0.9875318302  0.5661304769  1.1536621386  0.7582936791
##  [801] -0.0002857802  0.6288189018  0.4350245065  0.8270612554  1.8205113065
##  [806]  0.1820249269  1.5395170934  1.0746125317  1.1459729693  1.0108390586
##  [811]  0.6782819761  1.0315930823  0.5978867768  1.2395298824  1.0341660472
##  [816]  0.7971407829  0.4328560717 -0.3622523417  0.7364751476  1.3733647125
##  [821]  0.1621871898  1.2061423982  1.2952981476  1.0839605769 -0.4861600226
##  [826]  2.0055729535  0.6496753742  0.2476593322  1.7127662028  1.2761109808
##  [831]  0.7389170297  0.9774701029  1.6728830474  1.0342465755  0.6139082366
##  [836]  1.2480358340  0.3625593027  1.2579711239  0.4218917653  1.1740500980
##  [841]  0.6147156946  1.0520374362  0.8240646158  0.6911164012  0.8440677470
##  [846]  1.1187120028  0.9852525586  1.2305301269  0.1631267505  1.9589145825
##  [851] -0.0767794409  0.6752375625  1.0388336301  1.1767944048  0.9652049604
##  [856]  1.1756845238  0.8122090623  0.3209244952  1.5087205308 -0.4953194566
##  [861]  1.4450293139  0.7832192968  1.2313077242  0.6604537958  0.4645076357
##  [866]  0.6765558973  0.9269961915  0.4854653572  1.0292230711  0.7441536970
##  [871]  1.8716917987  1.2896586971  1.8478872564  1.1381085235  1.3804781277
##  [876]  1.2262378790  1.6815452964  0.7561550661  1.8902802455 -0.1501207916
##  [881]  1.5044446624  1.8055165282  1.3035566226 -0.6934168017  1.1165888682
##  [886]  2.1023918730  0.6128020722  1.0335691072  1.6157163958  0.8725938486
##  [891] -0.6764432119  1.4251148284  1.1363386034  0.7794392236 -0.1857799160
##  [896]  0.9810479803  0.8478457864  0.4955996051 -0.1874037542 -0.4151685972
##  [901] -0.4510095105  1.2083736017  1.6116197978  2.2294010671  1.0091674494
##  [906]  1.0968881318  1.2010959796  1.6316879472  2.0104004405  0.5247338906
##  [911]  1.0841594030  1.4613063501  0.7366498197  0.8440166346  0.7092569011
##  [916]  0.7905122901  0.7893237833  0.5556778218  0.3945220988  1.2776214278
##  [921]  0.6805812877  0.4203249227 -0.7064892646  1.0968343353  1.1905880537
##  [926]  2.2671978448  0.1303345608  1.2038416969  0.6980663146  2.0993735353
##  [931]  1.1516198413  0.9504013622  1.8107869988  2.1627198083  0.9089179388
##  [936]  0.6949998733  1.4090224854  2.2567735872  0.2804382136  1.1138069465
##  [941]  2.2019707294  1.6246078018  1.0398214611  0.0372437565  1.9728196298
##  [946]  1.2982328336 -0.0108822411  1.0757813599  0.8522213209 -1.4255374627
##  [951]  0.8061270051  1.0471168702  0.0988976417 -0.4108022606  1.0387031842
##  [956]  1.4473812134  0.6515991497 -0.3758118675  1.2225562119  1.3479409207
##  [961]  0.3643404206 -0.2951515647  1.3913034303  1.7956917964  0.7216671112
##  [966]  0.8108879778  1.1941014946  0.8861693491  0.8477022931  1.1651839266
##  [971]  1.2209677576  1.0896536421  1.7844858530  0.5373579260  2.0707520978
##  [976]  0.5630293784  0.7220717189 -0.5852538948  1.6761731826  1.6561836798
##  [981]  1.1360303767  0.7422684487  1.7327902445  0.6174483278  0.7597799168
##  [986]  0.6855667399  1.7719328945  0.9966762942  1.8205113065  1.1725766155
##  [991]  1.8798855319  1.6141312717  0.6225885924  0.6332821284  1.5272006742
##  [996]  1.2449086903  1.3482481881  1.1133447900  2.3056435181  0.7837459709
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
##   0.54422446   0.38267124 
##  (0.12101127) (0.08556543)
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
## [1]  0.1151977 -0.1716668  0.1217162  0.6106963  0.6778687 -0.1547289
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
## [1] -0.0188
```

```r
sd(results2$thetastar)  #the standard deviation of the theta stars is the SE of the statistic (in this case, the mean)
```

```
## [1] 0.9126881
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
## t1*      4.5 -0.009009009   0.9181748
```

One of the main advantages to the 'boot' package over the 'bootstrap' package is the nicer formatting of the output.

Going back to our original code, lets see how we could reproduce all of these numbers:


```r
table(sample(x,size=length(x),replace=T))
```

```
## 
## 0 1 2 4 5 6 8 
## 1 1 2 1 3 1 1
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
## [1] 0.0085
```

```r
se.boot
```

```
## [1] 0.9145459
```

Why do our numbers not agree exactly with those of the boot package? This is because our estimates of bias and standard error are just estimates, and they carry with them their own uncertainties. That is one of the reasons we might bother doing jackknife-after-bootstrap.

The 'boot' package has a LOT of functionality. If we have time, we will come back to some of these more complex functions later in the semester as we cover topics like regression and glm.

