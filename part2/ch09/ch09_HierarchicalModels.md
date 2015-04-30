# Chapter 9. Hierarchical Models
Ka-Kyung Kim  
2015년 4월 29일  
* 베이지안 통계 R
* [Doing Bayesian Data Analysis, Second Edition: A Tutorial with R, JAGS, and Stan](https://sites.google.com/site/doingbayesiandataanalysis/)
* [Source Code](https://github.com/boboppie/kruschke-doing_bayesian_data_analysis)
* [Exercises and solutions](https://sites.google.com/site/doingbayesiandataanalysis/exercises/Kruschke-DBDA2E-ExerciseSolutions.pdf?attredirects=0&d=1)
* [Latex](http://www.codecogs.com/latex/eqneditor.php)

# Contents

* 9.1. A Single Coin from a Single Mint
* 9.2. Multiple Coins from a Single Mint
* 9.3. Shrinkage in Hierarchical Models
* 9.4. Speeding up JAGS
* 9.5. Extending the Hierarchy: Subjects Within Categories

Hierarchical model?

 Mathematical descriptions involving multiple parameters such that the credible values of some parameters meaningfully depend on the values of other parameters.
 
 example) coins minted from the same factory. $\omega$ - bias of the factory, $\theta_{s}$ - bias of the coins (where the subscript indexes different coins). </br>Credible values of $\theta_{s}$, depend on the values of $\omega$.
 
 examples) batting ability ofbaseball players who have different primary fielding positions, probability that a child bought lunch from the school cafeteria, in different schools in different districts, probability that patients recover from heart surgery performed by different surgical teams within different hospitals in different cities, etc.



The parameters at different levels in a hierarchical model coexist in a joint parameter space. 

$$ p(\theta,w|D) \propto (D|\theta,w)p(\theta,w) = p(D|\theta)p(\theta | w)p(w) \quad (9.1) $$

The data depend only on the value of $\theta$ in the sense that when the value $\theta$ set then the data are independent of all other parameter values. </br>
$\theta$ depends on the value of w and the value of $\theta$ is conditionally independent of all other parameters.

The dependencies among parameters become useful. 
1. For the given application.
2. All the data can jointly inform all the parameter estimates.
3. The dependencies can facilitate efficient Monte Carlo sampling from the posterior distribution, because clever algorithms can take advantage of conditional distributions (ex: Gibbs sampling uses conditional distributions).

Real-world data involving dichotomous outcomes

- Coin flip
- Recovery or nonrecovery from a disease after treatment
- Recalling or forgetting a studied item
- Choosing candidate A or candidate B in an election

- Therapeutic touch
- Extrasensory perception
- Baseball batting ability

## 9.1. A Single Coin from a Single Mint

The likelihood function is the Bernoulli distribution.</br>
$$ \gamma_{i} \sim dbern(\theta)\quad (9.2) $$

The prior distribution is a beta density.</br>
$$ \theta\sim dbeta(a, b) \quad (9.3) $$

**Reference in Chapter 6 (p129)**
Our goal is to convert a prior belief expressed in terms of central tendency and sample size into equivalent values of a and b in the beta distribution. </br>
Toward this goal, it is useful to know the *central tendency* and *spread of the beta distribution* expressed in terms of *a* and *b*. </br>
It turns out that the mean of the beta($\theta|a$, b) distribution is $\mu = a/(a+b)$ and the mode is $\omega=(a-1)/(a+b-2)$ for a > 1 and b > 1. </br>
Thus, when a = b, the mean and mode are 0.5. When a > b, the mean and mode are greater than 0.5, and when a < b, the mean and mode are less than 0.5. </br>
The spread ofthe beta distribution is related to the "concentration" K = a+ b. You can see from Figure 6.1 that as K = a + b gets larger, the beta distribution gets narrower or more concentrated.

The shape parameters of the beta density, a and b, can be re-expressed in terms of the mode $\omega$ and concentration K of the beta distribution: $a = \omega(K-2) + 1$ and $b = (1-\omega)(K-2) +  1$, where $\omega = (a-1)/(a+b-2)\ and\ K = a + b$</br>
$$ \theta \sim dbeta (\omega(K-2)+1, (1-\omega)(K-2)+1) \quad (9.4) $$

$\theta$ depends on the value of $\omega$.
K governs how near $\theta$ is to W, with larger values of K generating values of $\theta$ more concentrated near $\omega$.
Thus, the magnitude of K is an expression of our prior certainty regarding the dependence of $\theta$ on $\omega$. 

The larger K is, the more consistently the mint makes coins with $\theta$ close to $\omega$. 

$$ p(\omega) = beta(\omega | A_{w}, B_{w}) \quad (9.5) $$

$\omega$ is typically near $(A_{w} - 1)$/$(A_{w} + B_{w} - 2)$ (mode of the beta distribution)

![Figure 9.1 A model of hierarchical dependencies for data from a single coin](img/fig9.1.png)


$$ p(\theta,\omega|\gamma)= \frac{p(\gamma|\theta,\omega)p(\theta,\omega)}{p(\gamma)} $$
$$=\frac{p(\gamma|\theta)p(\omega|\theta)p(\omega)}{p(\gamma)}
\quad(9.6) $$

The three terms of the numerator in second line
1. The likelihood function is a Bernoulli distribution in Equation 9.2
2. The dependence of $\theta$ on $\omega$ is specified to be a beta density in Equation 9.4.
3. The prior distribution on $\omega$ is a beta density in Equation 9.5. 

The likelihood and prior can be re-expressed as a hierarchical chain of dependencies among parameters

 1. Important in
  - Human interpretation
  - Hierarchical models can be parsed for MCMC samplers very effectively
 2. Applications
  - Meaningful chain of dependencies is primary in the conceptual genesis of the model
  - Thinking of the model as a joint distribution on a joint parameter space

### 9.1.1. Posterior via grid approximation
When the parameters extend over a finite domain, and there are not too many of them, then we can approximate the posterior via grid approximation.

The prior distribution has $A_{\omega}$ = 2, $B_{\omega}$ = 2, and K = 100 in Figure 9.2. </br>
$p(\theta,\omega) = p(\theta|\omega)p(\omega) = beta(\theta|\omega(100- 2) +1, (1- \omega)(100- 2) + 1) beta(\omega|2, 2)$.

As this is a grid approximation,

1. The joint prior **p($\theta$, $\omega$) = p($\theta$, $\omega$) * p($\omega$)** at every grid point
2. Dividing by their sum across the entire grid.
3. The normalized probability masses -> (converted to) -> estimates of <u>probability density</u> at each grid point

by dividing each probability mass by the area of a grid cell.

![Figure 9.2](img/fig9.2.png)

prior p($\omega$), posterior p($\omega|{\gamma_{i}}$) => different </br>
Impact on beliefs about how $\omega$ is distributed </br>
$\because$ prior: uncertain => easily influenced by the data.

</br></br>
High prior certainty regarding $\omega$, low prior certainty regarding the dependence of $\theta$ on $\omega$. </br>
Figure 9.3 illustrates such a case, where $A_{\omega}$ = 20, $B_{\omega}$ = 20, and $K$ = 6. 

The likelihood graphs look the same in the two figures. </br>
The contour lines are parallel to the $\omega$ axis => $\omega$ has no influence on the likelihood.


![Figure 9.3](img/fig9.3.png)



**Comparison**

In Figure 9.2

* Low prior certainty on $\omega$ (small $A\omega$,$B\omega$)
* High prior certainty about the dependence of $\theta$ on $\omega$ (large $K$).
* The posterior distribution showed a big change in beliefs about $\omega$, but not much change in beliefs about the dependence of $\theta$ on $\omega$.

In Figure 9.3

* Complementary situation
* High prior certainty on $\omega$ and low prior certainty about the dependence of $\theta$ on $\omega$.


## 9.2. Multiple Coins from a Single Mint

$\theta_{s}$ : probability of remembering by subjects </br>
$\omega$ : random variation of subjects around the drug-induced tendency </br>
$\theta_{s} \sim dbeta(\omega(K-2)+1, (1 - \omega)(K - 2) + 1)$: subjects' variation around $\omega$,  K: fixed constant </br>
$\gamma_{i|s} \sim dbern(\theta_{s})$, where the subscript, i|s, indicates the ith observation within subjects

![Figure 9.4](img/fig9.4.png)



### 9.2.1 Posterior via grid approximation

![Figure 9.5](img/fig9.5.png)


![Figure 9.6](img/fig9.6.png)


The grid approximation displayed in Figures 9.5 and 9.6 used combs of only 50 points on each parameter ($\omega$, $\theta_{1}$, and $\theta_{2}$). 

In Figure 9.6, the prior on $\omega$ is the same gentle peak, </br> but the prior dependency of $\theta_{s}$ on $\omega$ is much stronger (K = 75 instead of K = 5).

This shift away from the data proportion of subject 2 is caused by the fact that the other coin had a larger sample size </br> => $\therefore$  more influence on the estimate of $\omega_{2}$
=> influence the estimate of $\theta_{2}$.

The values of the prior and likelihood at a large number of grid points and sums over them to determine the denominator of Bayes' rule.

Grid approximation is not a viable approach to even modestly large problems.

### 9.2.2 A realistic model with MCMC

When K of the concentration or consistency parameter was fixed at a large value, the individual $\theta_{s}$ values stayed close to $\omega$ </br>
When K was fixed at a small value, $\theta_{s}$ values could roam quite far from $\omega$.

=> In real situations, we do not know the value of K in advance!

![Figure 9.7](img/fig9.7.png)



The *gamma(K|s,r)* distribution is a probability density for k ≥ 0, with two parameters that determine its exact form, called its *shape s* and *rate r* parameters.

![Figure 9.8](img/fig9.8.png)

Prior beliefs are most intuitively expressed in terms of the *central tendency* and a *width of the distribution*.</br>
$\therefore$ useful to start with values for central tendency and width, and convert them into corresponding shape and rate values.

Mean of the gamma distribution: $\mu = s/\gamma$ </br>
Mode of $\omega=(s-1)/\gamma , s>1$ </br>
Standard deviation $\sigma = \sqrt{s}/\gamma$ </br> 

$$s=\frac{\mu^{2}}{\sigma^{2}}, \gamma=\frac{\mu}{\sigma^{2}},for\quad\mu>0\ (9.7)$$
$$s=1+\omega\gamma\ where\ \gamma=\frac{\omega+\sqrt{\omega^{2}+4{\sigma}^{2}}}{2{\sigma}^{2}}\ for\ mode\ omega>0\ (9.8)$$ 



```r
#install.packages("knitr")
library(knitr)
```

```
## Warning: package 'knitr' was built under R version 3.1.3
```

```r
#install.packages('rjags')
require(rjags)
```

```
## Loading required package: rjags
```

```
## Warning: package 'rjags' was built under R version 3.1.3
```

```
## Loading required package: coda
```

```
## Warning: package 'coda' was built under R version 3.1.3
```

```
## Linked to JAGS 3.4.0
## Loaded modules: basemod,bugs
```

```r
#setwd("D:/Study/Bayesian/bayesianR/ch09")
source("DBDA2E-utilities.R")
```

```
## 
## *********************************************************************
## Kruschke, J. K. (2015). Doing Bayesian Data Analysis, Second Edition:
## A Tutorial with R, JAGS, and Stan. Academic Press / Elsevier.
## *********************************************************************
```

```
## Warning: package 'runjags' was built under R version 3.1.3
```

```r
gammaShRaFromMeanSD(mean=10, sd=100)
```

```
## $shape
## [1] 0.01
## 
## $rate
## [1] 0.001
```

```r
gammaShRaFromModeSD(mode=10, sd=100)
```

```
## $shape
## [1] 1.105125
## 
## $rate
## [1] 0.01051249
```

```r
gammaParam = gammaShRaFromModeSD(mode=10, sd=100)
gammaParam$shape
```

```
## [1] 1.105125
```

```r
gammaParam$rate
```

```
## [1] 0.01051249
```


### 9.2.3. Doing it with JAGS
The arrows in the hierarchical diagram indicate the dependencies between the variables. Every arrow in the diagram has a corresponding expression in the JAGS model specification. 


```r
# Define the model:
#modelString = "
#model {
#  for ( i in 1:Ntotal ) {
#    y[i] ~ dbern( theta )
#  }
#  theta ~ dbeta( 1 , 1 )
#}

#model {
#  for ( i 1n l:Ntotal ) {
#    y[i] ~ dbern( theta[s[i]])
#  }
#  for ( s in 1:Nsubj ) {
#    theta[s] - dbeta( omega*(kappa-2)+1,  (1-omega)*(kappa-2)+1 )
#  }
#  omega ~ dbeta( l , l )
#  kappa <- kappaMinusTwo + 2
#  kappaMinusTwo - dgamma( 0.01 , 0.01 ) # mean=1 , sd=10 (generic vague)
#}
```

### 9.2.4 Example: Therapeutic touch
Therapeutic touch is a nursing technique in which the practitioner manually manipulates the "energy field" of a patient who is suffering from a disease. 
(Rosa, Rosa, Sarner, and Barrett (1998))
Each trial by flip of the coin -> The practitioner guess -> Each trial was scored as correct or wrong.

- Each 21 practitioner for 10 trials
- 7 practitioner tested twice approximately a year apart
- 30 paramters

=> How much the group as a whole or any individuals differed from chance performance(=0.50)?

![Figure 9.9](img/fig9.9.png)

Contents of 'TherapeuticTouchData.csv' </br>
y: outcome of the trials (0 or 1) </br>
s: identifier of the subject who generated each outcome </br>
s Name, y Name in the functions genMCMC and plotMCMC </br>



```r
#Read the data file:
myData = read.csv("TherapeuticTouchData.csv")
# Load the relevant model functions into R's working memory:
source("Jags-Ydich-XnomSsubj-MbernBetaOmegaKappa.R")
```

```
## 
## *********************************************************************
## Kruschke, J. K. (2015). Doing Bayesian Data Analysis, Second Edition:
## A Tutorial with R, JAGS, and Stan. Academic Press / Elsevier.
## *********************************************************************
```

```r
# Generate the MCMC chain: 
mcmcCoda = genMCMC(data=myData, sName="s", yName="y",
numSavedSteps=20000 , thinSteps=10)
```

```
## Compiling model graph
##    Resolving undeclared variables
##    Allocating nodes
##    Graph Size: 602
## 
## Initializing model
## 
## Burning in the MCMC chain...
## Sampling final MCMC chain...
```


```r
#Display diagnostics of chain, for specified parameters:
diagMCMC (codaObject=mcmcCoda, parName="omega")
```
![output9.1](outputs/output9.1.png)

**Output 9.1**



```r
diagMCMC (codaObject=mcmcCoda, parName="kappa")
```


```r
diagMCMC (codaObject=mcmcCoda, parName="theta[1]")
```


```r
# Get summary statistics of chain:
# diffIdVec: vector of subject indices that should have their posterior differences summarized.
smryMCMC (mcmcCoda, compVal=0.5, diffIdVec=c(1,14,28), compValDiff=0.0)
```

```
##                            Mean      Median        Mode     ESS HDImass
## omega                0.43556782  0.43595107  0.43863404 12485.1    0.95
## kappa               54.88247592 36.71597983 19.38930965  3868.5    0.95
## theta[1]             0.35947618  0.36775328  0.39106132 12252.8    0.95
## theta[2]             0.38290978  0.38819523  0.40824207 13782.1    0.95
## theta[3]             0.40808607  0.41142332  0.42087586 16618.8    0.95
## theta[4]             0.40774125  0.41085717  0.41902959 16480.7    0.95
## theta[5]             0.40670969  0.40986798  0.41229704 15256.3    0.95
## theta[6]             0.40741710  0.41077965  0.42691357 15724.1    0.95
## theta[7]             0.40688779  0.40954804  0.41525995 14850.7    0.95
## theta[8]             0.40661922  0.40981455  0.41493553 16329.5    0.95
## theta[9]             0.40667966  0.40945389  0.41238421 16775.5    0.95
## theta[10]            0.40755803  0.41099475  0.42203032 16917.2    0.95
## theta[11]            0.43075949  0.43110783  0.44034987 18747.5    0.95
## theta[12]            0.43120807  0.43061108  0.42368678 18512.4    0.95
## theta[13]            0.43037570  0.42997653  0.42718119 19091.3    0.95
## theta[14]            0.43090708  0.43054336  0.42104599 19081.7    0.95
## theta[15]            0.43026789  0.43057605  0.43433190 17819.6    0.95
## theta[16]            0.45485626  0.45209924  0.44310134 17927.9    0.95
## theta[17]            0.45472889  0.45209440  0.45222730 18562.5    0.95
## theta[18]            0.45427452  0.45114123  0.44118825 19350.0    0.95
## theta[19]            0.45423319  0.45164476  0.44096249 18160.8    0.95
## theta[20]            0.45436521  0.45191410  0.44888229 18472.4    0.95
## theta[21]            0.45311467  0.45081189  0.44967627 19010.9    0.95
## theta[22]            0.45373573  0.45097217  0.44760526 18534.4    0.95
## theta[23]            0.47787249  0.47144717  0.45940378 16984.5    0.95
## theta[24]            0.47769739  0.47211016  0.46420290 16168.7    0.95
## theta[25]            0.50144704  0.49274640  0.47937726 14133.5    0.95
## theta[26]            0.50194704  0.49364271  0.48792257 14131.7    0.95
## theta[27]            0.50167759  0.49274863  0.47907924 14922.5    0.95
## theta[28]            0.52522486  0.51437938  0.48887439 12408.0    0.95
## theta[1]-theta[14]  -0.07143090 -0.06095796 -0.04593949 16398.4    0.95
## theta[1]-theta[28]  -0.16574868 -0.14454902 -0.09532450 10409.5    0.95
## theta[14]-theta[28] -0.09431777 -0.07938548 -0.05196311 14225.1    0.95
##                         HDIlow      HDIhigh CompVal PcntGtCompVal ROPElow
## omega                0.3634888   0.50896450     0.5         3.855      NA
## kappa                4.2500889 162.71733768      NA            NA      NA
## theta[1]             0.1758237   0.51950356     0.5         3.770      NA
## theta[2]             0.2128656   0.53807904     0.5         6.560      NA
## theta[3]             0.2391448   0.56240266     0.5        11.510      NA
## theta[4]             0.2480878   0.56450251     0.5        11.345      NA
## theta[5]             0.2430916   0.56377919     0.5        11.400      NA
## theta[6]             0.2424339   0.56265368     0.5        11.470      NA
## theta[7]             0.2438985   0.56587793     0.5        11.325      NA
## theta[8]             0.2423211   0.56076931     0.5        11.130      NA
## theta[9]             0.2410394   0.56178020     0.5        11.275      NA
## theta[10]            0.2418789   0.56123887     0.5        11.505      NA
## theta[11]            0.2666391   0.58226506     0.5        17.975      NA
## theta[12]            0.2706876   0.59073060     0.5        18.270      NA
## theta[13]            0.2715880   0.59036785     0.5        18.115      NA
## theta[14]            0.2748292   0.59448117     0.5        17.880      NA
## theta[15]            0.2639835   0.58132072     0.5        17.475      NA
## theta[16]            0.2931170   0.60937511     0.5        26.465      NA
## theta[17]            0.3011809   0.62293185     0.5        26.770      NA
## theta[18]            0.2986064   0.61824352     0.5        26.450      NA
## theta[19]            0.2962029   0.61351935     0.5        26.285      NA
## theta[20]            0.2955589   0.61288103     0.5        26.475      NA
## theta[21]            0.2957731   0.61433654     0.5        26.010      NA
## theta[22]            0.2991205   0.61567599     0.5        26.360      NA
## theta[23]            0.3237553   0.65502282     0.5        36.440      NA
## theta[24]            0.3191052   0.64899318     0.5        36.140      NA
## theta[25]            0.3428332   0.68543635     0.5        46.515      NA
## theta[26]            0.3464274   0.68474784     0.5        46.715      NA
## theta[27]            0.3444499   0.68642292     0.5        46.510      NA
## theta[28]            0.3546349   0.71122678     0.5        56.580      NA
## theta[1]-theta[14]  -0.2986243   0.13362958     0.0        25.970      NA
## theta[1]-theta[28]  -0.4564162   0.06170424     0.0         8.410      NA
## theta[14]-theta[28] -0.3569836   0.11044988     0.0        20.420      NA
##                     ROPEhigh PcntLtROPE PcntInROPE PcntGtROPE
## omega                     NA         NA         NA         NA
## kappa                     NA         NA         NA         NA
## theta[1]                  NA         NA         NA         NA
## theta[2]                  NA         NA         NA         NA
## theta[3]                  NA         NA         NA         NA
## theta[4]                  NA         NA         NA         NA
## theta[5]                  NA         NA         NA         NA
## theta[6]                  NA         NA         NA         NA
## theta[7]                  NA         NA         NA         NA
## theta[8]                  NA         NA         NA         NA
## theta[9]                  NA         NA         NA         NA
## theta[10]                 NA         NA         NA         NA
## theta[11]                 NA         NA         NA         NA
## theta[12]                 NA         NA         NA         NA
## theta[13]                 NA         NA         NA         NA
## theta[14]                 NA         NA         NA         NA
## theta[15]                 NA         NA         NA         NA
## theta[16]                 NA         NA         NA         NA
## theta[17]                 NA         NA         NA         NA
## theta[18]                 NA         NA         NA         NA
## theta[19]                 NA         NA         NA         NA
## theta[20]                 NA         NA         NA         NA
## theta[21]                 NA         NA         NA         NA
## theta[22]                 NA         NA         NA         NA
## theta[23]                 NA         NA         NA         NA
## theta[24]                 NA         NA         NA         NA
## theta[25]                 NA         NA         NA         NA
## theta[26]                 NA         NA         NA         NA
## theta[27]                 NA         NA         NA         NA
## theta[28]                 NA         NA         NA         NA
## theta[1]-theta[14]        NA         NA         NA         NA
## theta[1]-theta[28]        NA         NA         NA         NA
## theta[14]-theta[28]       NA         NA         NA         NA
```

```
##                            Mean      Median        Mode     ESS HDImass
## omega                0.43556782  0.43595107  0.43863404 12485.1    0.95
## kappa               54.88247592 36.71597983 19.38930965  3868.5    0.95
## theta[1]             0.35947618  0.36775328  0.39106132 12252.8    0.95
## theta[2]             0.38290978  0.38819523  0.40824207 13782.1    0.95
## theta[3]             0.40808607  0.41142332  0.42087586 16618.8    0.95
## theta[4]             0.40774125  0.41085717  0.41902959 16480.7    0.95
## theta[5]             0.40670969  0.40986798  0.41229704 15256.3    0.95
## theta[6]             0.40741710  0.41077965  0.42691357 15724.1    0.95
## theta[7]             0.40688779  0.40954804  0.41525995 14850.7    0.95
## theta[8]             0.40661922  0.40981455  0.41493553 16329.5    0.95
## theta[9]             0.40667966  0.40945389  0.41238421 16775.5    0.95
## theta[10]            0.40755803  0.41099475  0.42203032 16917.2    0.95
## theta[11]            0.43075949  0.43110783  0.44034987 18747.5    0.95
## theta[12]            0.43120807  0.43061108  0.42368678 18512.4    0.95
## theta[13]            0.43037570  0.42997653  0.42718119 19091.3    0.95
## theta[14]            0.43090708  0.43054336  0.42104599 19081.7    0.95
## theta[15]            0.43026789  0.43057605  0.43433190 17819.6    0.95
## theta[16]            0.45485626  0.45209924  0.44310134 17927.9    0.95
## theta[17]            0.45472889  0.45209440  0.45222730 18562.5    0.95
## theta[18]            0.45427452  0.45114123  0.44118825 19350.0    0.95
## theta[19]            0.45423319  0.45164476  0.44096249 18160.8    0.95
## theta[20]            0.45436521  0.45191410  0.44888229 18472.4    0.95
## theta[21]            0.45311467  0.45081189  0.44967627 19010.9    0.95
## theta[22]            0.45373573  0.45097217  0.44760526 18534.4    0.95
## theta[23]            0.47787249  0.47144717  0.45940378 16984.5    0.95
## theta[24]            0.47769739  0.47211016  0.46420290 16168.7    0.95
## theta[25]            0.50144704  0.49274640  0.47937726 14133.5    0.95
## theta[26]            0.50194704  0.49364271  0.48792257 14131.7    0.95
## theta[27]            0.50167759  0.49274863  0.47907924 14922.5    0.95
## theta[28]            0.52522486  0.51437938  0.48887439 12408.0    0.95
## theta[1]-theta[14]  -0.07143090 -0.06095796 -0.04593949 16398.4    0.95
## theta[1]-theta[28]  -0.16574868 -0.14454902 -0.09532450 10409.5    0.95
## theta[14]-theta[28] -0.09431777 -0.07938548 -0.05196311 14225.1    0.95
##                         HDIlow      HDIhigh CompVal PcntGtCompVal ROPElow
## omega                0.3634888   0.50896450     0.5         3.855      NA
## kappa                4.2500889 162.71733768      NA            NA      NA
## theta[1]             0.1758237   0.51950356     0.5         3.770      NA
## theta[2]             0.2128656   0.53807904     0.5         6.560      NA
## theta[3]             0.2391448   0.56240266     0.5        11.510      NA
## theta[4]             0.2480878   0.56450251     0.5        11.345      NA
## theta[5]             0.2430916   0.56377919     0.5        11.400      NA
## theta[6]             0.2424339   0.56265368     0.5        11.470      NA
## theta[7]             0.2438985   0.56587793     0.5        11.325      NA
## theta[8]             0.2423211   0.56076931     0.5        11.130      NA
## theta[9]             0.2410394   0.56178020     0.5        11.275      NA
## theta[10]            0.2418789   0.56123887     0.5        11.505      NA
## theta[11]            0.2666391   0.58226506     0.5        17.975      NA
## theta[12]            0.2706876   0.59073060     0.5        18.270      NA
## theta[13]            0.2715880   0.59036785     0.5        18.115      NA
## theta[14]            0.2748292   0.59448117     0.5        17.880      NA
## theta[15]            0.2639835   0.58132072     0.5        17.475      NA
## theta[16]            0.2931170   0.60937511     0.5        26.465      NA
## theta[17]            0.3011809   0.62293185     0.5        26.770      NA
## theta[18]            0.2986064   0.61824352     0.5        26.450      NA
## theta[19]            0.2962029   0.61351935     0.5        26.285      NA
## theta[20]            0.2955589   0.61288103     0.5        26.475      NA
## theta[21]            0.2957731   0.61433654     0.5        26.010      NA
## theta[22]            0.2991205   0.61567599     0.5        26.360      NA
## theta[23]            0.3237553   0.65502282     0.5        36.440      NA
## theta[24]            0.3191052   0.64899318     0.5        36.140      NA
## theta[25]            0.3428332   0.68543635     0.5        46.515      NA
## theta[26]            0.3464274   0.68474784     0.5        46.715      NA
## theta[27]            0.3444499   0.68642292     0.5        46.510      NA
## theta[28]            0.3546349   0.71122678     0.5        56.580      NA
## theta[1]-theta[14]  -0.2986243   0.13362958     0.0        25.970      NA
## theta[1]-theta[28]  -0.4564162   0.06170424     0.0         8.410      NA
## theta[14]-theta[28] -0.3569836   0.11044988     0.0        20.420      NA
##                     ROPEhigh PcntLtROPE PcntInROPE PcntGtROPE
## omega                     NA         NA         NA         NA
## kappa                     NA         NA         NA         NA
## theta[1]                  NA         NA         NA         NA
## theta[2]                  NA         NA         NA         NA
## theta[3]                  NA         NA         NA         NA
## theta[4]                  NA         NA         NA         NA
## theta[5]                  NA         NA         NA         NA
## theta[6]                  NA         NA         NA         NA
## theta[7]                  NA         NA         NA         NA
## theta[8]                  NA         NA         NA         NA
## theta[9]                  NA         NA         NA         NA
## theta[10]                 NA         NA         NA         NA
## theta[11]                 NA         NA         NA         NA
## theta[12]                 NA         NA         NA         NA
## theta[13]                 NA         NA         NA         NA
## theta[14]                 NA         NA         NA         NA
## theta[15]                 NA         NA         NA         NA
## theta[16]                 NA         NA         NA         NA
## theta[17]                 NA         NA         NA         NA
## theta[18]                 NA         NA         NA         NA
## theta[19]                 NA         NA         NA         NA
## theta[20]                 NA         NA         NA         NA
## theta[21]                 NA         NA         NA         NA
## theta[22]                 NA         NA         NA         NA
## theta[23]                 NA         NA         NA         NA
## theta[24]                 NA         NA         NA         NA
## theta[25]                 NA         NA         NA         NA
## theta[26]                 NA         NA         NA         NA
## theta[27]                 NA         NA         NA         NA
## theta[28]                 NA         NA         NA         NA
## theta[1]-theta[14]        NA         NA         NA         NA
## theta[1]-theta[28]        NA         NA         NA         NA
## theta[14]-theta[28]       NA         NA         NA         NA
```

```r
# Display posterior information:
plotMCMC (mcmcCoda, data=myData, sName="s", yName="y",
compVal=0.5, diffIdVec=c(1,14,28), compValDiff=0.0)
```
![output9.2](outputs/output9.2.png)

**Output 9.2: same as Figure 9.10 upper pannel**

![output9.3](outputs/output9.3.png)

**Output 9.3: same as Figure 9.10 lower pannel**


Only the kappa parameter showed high autocorrelation => typical of higher-level parameters that control the variance of lower-level parameters. 

The chains for K show good overlap. 
The marginal posterior on K spans a wide range of values, and the estimates of the other parameters are not hugely affected by small changes in K.

![Figure 9.10](img/fig9.10.png)

![Figure 9.11](img/fig9.11.png)


It can be seen that the prior for the therapeutic touch data is uniform on the group-level mode, $\omega$, and uniform on each of the individual level biases, $\theta_{s}$. </br>
=> equal prior credibility to any possible bias.

The priors on the differences of individual biases: triangular distributions, peaked at a difference of zero.


## 9.3. Shrinkage in Hierarchical Models

Shrinkage of the estimates in hierarchical models: the estimates of low-level parameters are pulled closer together if there were not a higher-level distribution. </br>

In Figure 9.10, the most credible values of individual-level biases, $\theta_{s}$, were closer to the group-level mode, $\omega$, than the individual proportions correct, $z_{s}/N_{s}$. </br>
*$\therefore$* The variance between the estimated values $\theta_{s}$ is less than the variance between the data values $z_{s}/N_{s}$.
Shrinkage leads to reduced variance only for unimodal distributions of parameters. </br>
Shrinkage causes low-level parameters to shift toward the modes of the higher-level distribution. </br> 

Concept at the book's [blog](http://doingbayesiandataanalysis.blogspot.kr/2012/11/shrinkage-in-multi-level-hierarchical.html)
![BaseballDiagram](http://3.bp.blogspot.com/-_4ysn9BiU_4/ULUxhzGVnFI/AAAAAAAAAZ0/4xEKyrk5bBU/s640/BaseballDiagram.jpg)

Examples at the book's [blog](http://doingbayesiandataanalysis.blogspot.com/2013/03/shrinkage-in-bimodal-hierarchical.html) and in Kruschke and Vanpaemel (in press).

Shrunken parameter estimates are less affected by random sampling noise than estimates derived without hierarchical structure.</br> Shrinkage occurs because the estimate ofeach low-level parameter is influenced from two sources: </br>
(1) the subset ofdata that are directly dependent on the low-level parameter </br>
(2) the higher-level parameters on which the low-level parameter depends. </br>

It is important to understand that shrinkage is a consequence of hierarchical model structure, not Bayesian estimation.

Maximum likelihood estimate (MLE) for the hierarchical model of Figure 9.7

$$ p(\gamma_{i|s} | \theta_{s}, \omega, K) $$
$$ = bern(\gamma_{i|s}) · beta(\theta_{s}|\omega(K-2)+1, (1-\omega)(K-2)+1)\ (9.9) $$

For the whole set of data, $\gamma_{i|s}$, because we assume independence across data values, we take the product of that probability across all the data:
$$p({\gamma_{i|s}}|{\theta_{s},w,k})$$
$$=\prod_{s}\prod_{i|s}p({\gamma_{i|s}}|\theta_{s},w,k)$$
$$=\prod_{s}\prod_{i|s}bern({\gamma_{i|s}}|\theta_{s})\cdot beta(\theta_{s}|w(k-2)+1,(1-w)(k-2)+1)\ (9.10)$$



![Figure 9.12](img/fig9.12.png)



## 9.4. Speeding up JAGS

Methods to speed up processing ofJAGS. 
- changes the likelihood function in the JAGS model specification. - runjags package to run chains in parallel on multicore computers. 

Use the binomial likelihood function instead of the Bernoulli likelihood. </br>
$$p(Z_{s}|\theta_{s},N_{s}) = \binom{N_{s}}{Z_{s}}\theta_{s}^{Z_{s}}(1-\theta_{s})^{(N_{s}-Z_{s})}$$

![Eqn01](Eqn01.gif)
=> remove constant called binomial codefficient, $\binom{N_{s}}{Z_{s}}$ for JAGS by using its built-in binomial likelihood



```r
#z = aggregate( y , by=list(s) , FUN=sum )$x
#N = aggregate( rep(1,length(y)) , by=list(s) , FUN=sum )$x
#Nsubj = length(unique(s))
# Specify the data in a list, for later shipment to JAGS:
#dataList = list(
#  z = z ,
#  N = N ,
#  Nsubj = Nsubj
#)



#model {
#  for ( s in 1:Nsubj ) {
#    z[s] ~ dbin( theta[s] , N[s] )
#    theta[s] ~ dbeta( omega*(kappa-2)+1 , (1-omega)*(kappa-2)+1 ) 
#  }
#  omega ~ dbeta( 1 , 1 )
#  kappa <- kappaMinusTwo + 2
  #kappaMinusTwo ~ dgamma( 0.01 , 0.01 )  # mean=1 , sd=10 (generic vague)
#  kappaMinusTwo ~ dgamma( 1.105125 , 0.1051249 )  # mode=1 , sd=10 
#}

#source("Jags-Ydich-XnomSsubj-MbinomBetaOmegaKappa.R")
```


## 9.5. Extending the Hierarchy: Subjects Within Categories

Data with many dichonomous value, different categries


```r
# Read the data
myData = read.csv("BattingAverage.csv")
# Load the relevant model into R's working memory:
source("Jags-Ybinom-XnomSsubjCcat-MbinomBetaOmegaKappa.R")
```

```
## 
## *********************************************************************
## Kruschke, J. K. (2015). Doing Bayesian Data Analysis, Second Edition:
## A Tutorial with R, JAGS, and Stan. Academic Press / Elsevier.
## *********************************************************************
```

```r
# Generate the MCMC chain => Takes few minutes
mcmcCoda = genMCMC(data=myData,
                   zName="Hits", NName="AtBats", sName="Player",
                   cName="PriPos", numSavedSteps=11000, thinSteps=20 )
```

```
## Calling 3 simulations using the parallel method...
## Following the progress of chain 1 (the program will wait for all
## chains to finish before continuing):
## Welcome to JAGS 3.4.0 on Thu Apr 30 16:59:26 2015
## JAGS is free software and comes with ABSOLUTELY NO WARRANTY
## Loading module: basemod: ok
## Loading module: bugs: ok
## . . Reading data file data.txt
## . Compiling model graph
##    Resolving undeclared variables
##    Allocating nodes
##    Graph Size: 3887
## . Reading parameter file inits1.txt
## . Initializing model
## . Adapting 500
## -------------------------------------------------| 500
## ++++++++++++++++++++++++++++++++++++++++++++++++++ 100%
## Adaptation successful
## . Updating 500
## -------------------------------------------------| 500
## ************************************************** 100%
## . . . . . . Updating 73340
## -------------------------------------------------| 73300
## ************************************************** 100%
## * 100%
## . . . . Updating 0
## . Deleting model
## . 
## All chains have finished
## Simulation complete.  Reading coda files...
## Coda files loaded successfully
## Finished running the simulation
```

```r
# Display diagnostics of chain, for specified parameters:
for ( parName in c("omega[1]","omegaO","kappa[1]","kappaO", "theta[1]")) {
  diagMCMC( codaObject=mcmcCoda, parName=parName,
            #saveName=fileNameRoot, saveType=graphFileType #Error in diagMCMC(codaObject = mcmcCoda, parName = parName, saveName = fileNameRoot,  :   object 'fileNameRoot' not found
            #saveName=".", saveType=graphFileType #Error in paste0(file, ".", type) : object 'graphFileType' not found
          )
}
```

![output9.4](outputs/output9.4.png)</br>
**Output 9.4**

![output9.5](outputs/output9.5.png)</br>
**Output 9.5**

![output9.6](outputs/output9.6.png)</br>
**Output 9.6**

![output9.7](outputs/output9.7.png)</br>
**Output 9.7**

![output9.8](outputs/output9.8.png)</br>
**Output 9.8**



```r
# Get summary statistics of chain:
summaryInfo = smryMCMC(mcmcCoda, compVal=NULL)
```

```
##                    Mean      Median         Mode     ESS HDImass
## omega[1]     0.25159326   0.2516630   0.25138561 11001.0    0.95
## omega[2]     0.24850260   0.2486090   0.24826605 11001.0    0.95
## omega[3]     0.25507674   0.2551890   0.25483945 11001.0    0.95
## omega[4]     0.23704318   0.2371150   0.23720053 10661.0    0.95
## omega[5]     0.25652220   0.2565810   0.25637517 11001.0    0.95
## omega[6]     0.24865038   0.2487270   0.24800075 11001.0    0.95
## omega[7]     0.12213498   0.1222280   0.12187346  4671.5    0.95
## omega[8]     0.25893812   0.2590540   0.25939081 11001.0    0.95
## omega[9]     0.24679522   0.2469180   0.24727568 11001.0    0.95
## omegaO       0.22677181   0.2276580   0.22823408 11001.0    0.95
## kappa[1]   239.60694337 229.6020000 209.76149000 10551.7    0.95
## kappa[2]   258.36181192 245.7180000 235.34277275 11001.0    0.95
## kappa[3]   212.11760569 201.1700000 189.59474997 10662.2    0.95
## kappa[4]   171.83327603 165.7040000 156.63959371 11001.0    0.95
## kappa[5]   240.54239324 228.9570000 209.88914877 11001.0    0.95
## kappa[6]   216.58705853 206.9070000 202.35099549 10579.9    0.95
## kappa[7]   108.07017949  96.6090000  80.51339792  2286.8    0.95
## kappa[8]   403.99603245 381.8180000 351.07285839 11001.0    0.95
## kappa[9]   244.23345480 230.4370000 204.33784384 10240.0    0.95
## kappaO      61.72947343  56.4081000  45.63762885 10259.7    0.95
## theta[1]     0.13129015   0.1288460   0.12389872 11001.0    0.95
## theta[2]     0.24659862   0.2461820   0.24436321 10675.5    0.95
## theta[3]     0.25243199   0.2520200   0.25228599 11749.1    0.95
## theta[4]     0.23325839   0.2330580   0.23170779 10996.3    0.95
## theta[5]     0.25087375   0.2504760   0.25161985 11001.0    0.95
## theta[6]     0.12885334   0.1261530   0.12444015 11001.0    0.95
## theta[7]     0.12908216   0.1265310   0.11948342 11855.8    0.95
## theta[8]     0.24130004   0.2413320   0.24450839 10469.5    0.95
## theta[9]     0.26739032   0.2671580   0.26578383 10805.0    0.95
## theta[10]    0.27791961   0.2777170   0.27788814 11001.0    0.95
## theta[11]    0.12871685   0.1258990   0.12311658 11001.0    0.95
## theta[12]    0.24781849   0.2475180   0.24708975 11001.0    0.95
## theta[13]    0.24497144   0.2445350   0.24440618 11349.6    0.95
## theta[14]    0.24101133   0.2398870   0.23764437 10691.3    0.95
## theta[15]    0.24625511   0.2459880   0.24694220 11667.6    0.95
## theta[16]    0.22657484   0.2264040   0.22526061 11001.0    0.95
## theta[17]    0.27595090   0.2755900   0.27393410 11001.0    0.95
## theta[18]    0.24605900   0.2458150   0.24400501 11001.0    0.95
## theta[19]    0.27653617   0.2763050   0.27744571 11001.0    0.95
## theta[20]    0.12606114   0.1240760   0.11953142 11001.0    0.95
## theta[21]    0.23548199   0.2349900   0.23383802 11001.0    0.95
## theta[22]    0.26518314   0.2651480   0.26527256 11001.0    0.95
## theta[23]    0.12693923   0.1246520   0.11955343 11001.0    0.95
## theta[24]    0.13575087   0.1341040   0.12770993 11001.0    0.95
## theta[25]    0.24210000   0.2416310   0.23994907 11001.0    0.95
## theta[26]    0.24210875   0.2418210   0.24194923 12395.4    0.95
## theta[27]    0.13693193   0.1344050   0.13246828 11001.0    0.95
## theta[28]    0.24943127   0.2492550   0.24771304 11001.0    0.95
## theta[29]    0.12903246   0.1265500   0.12062228 10597.9    0.95
## theta[30]    0.27742498   0.2770670   0.27398078 12730.4    0.95
## theta[31]    0.12941909   0.1266910   0.12011078 11001.0    0.95
## theta[32]    0.12381296   0.1222740   0.11926135 11001.0    0.95
## theta[33]    0.25371029   0.2535880   0.25335699 10590.5    0.95
## theta[34]    0.23904523   0.2384470   0.23375204 11001.0    0.95
## theta[35]    0.12903533   0.1266160   0.12373580 11001.0    0.95
## theta[36]    0.21770369   0.2173700   0.21688516 12335.0    0.95
## theta[37]    0.12728353   0.1251440   0.12459961 11001.0    0.95
## theta[38]    0.23567130   0.2353380   0.23189619 11001.0    0.95
## theta[39]    0.24244320   0.2422700   0.24154385 11001.0    0.95
## theta[40]    0.25270884   0.2527250   0.25420829 11001.0    0.95
## theta[41]    0.21752699   0.2179570   0.21834522 11502.1    0.95
## theta[42]    0.23689247   0.2366850   0.23911775 11001.0    0.95
## theta[43]    0.12997293   0.1281610   0.11991255 11001.0    0.95
## theta[44]    0.12273738   0.1207560   0.12075418  8781.6    0.95
## theta[45]    0.12459292   0.1222070   0.11834389 11001.0    0.95
## theta[46]    0.25133067   0.2513580   0.25157682 10857.3    0.95
## theta[47]    0.26092224   0.2607230   0.26047840 11001.0    0.95
## theta[48]    0.20985404   0.2098380   0.21105976 11001.0    0.95
## theta[49]    0.14225170   0.1395710   0.13333966 10546.3    0.95
## theta[50]    0.12802766   0.1256160   0.12073400 10589.1    0.95
## theta[51]    0.11489591   0.1132490   0.11017330 10164.6    0.95
## theta[52]    0.23940052   0.2392690   0.23807618 11001.0    0.95
## theta[53]    0.12423599   0.1226390   0.11453549 11001.0    0.95
## theta[54]    0.12615119   0.1240560   0.12786913 11001.0    0.95
## theta[55]    0.24012928   0.2400920   0.24130100 10825.5    0.95
## theta[56]    0.12866151   0.1257060   0.12040268 11001.0    0.95
## theta[57]    0.26717828   0.2669250   0.26880963 11001.0    0.95
## theta[58]    0.26513819   0.2647770   0.26374671 11001.0    0.95
## theta[59]    0.30491638   0.3045990   0.30200403 11001.0    0.95
## theta[60]    0.12869461   0.1268010   0.12870721 11001.0    0.95
## theta[61]    0.12416296   0.1218530   0.11782028 10052.5    0.95
## theta[62]    0.25522342   0.2545860   0.25289639 11001.0    0.95
## theta[63]    0.27203869   0.2716360   0.26847780 11001.0    0.95
## theta[64]    0.25517482   0.2549250   0.25550791 11001.0    0.95
## theta[65]    0.24021211   0.2400890   0.23787580 11001.0    0.95
## theta[66]    0.25955443   0.2593050   0.25902566 11001.0    0.95
## theta[67]    0.23465850   0.2347200   0.23333505 11001.0    0.95
## theta[68]    0.14870000   0.1462520   0.14465268 10700.2    0.95
## theta[69]    0.24760012   0.2478080   0.24828020 12791.3    0.95
## theta[70]    0.12708964   0.1246580   0.12246230 11997.3    0.95
## theta[71]    0.12710169   0.1244750   0.11520167 11001.0    0.95
## theta[72]    0.26569077   0.2653760   0.26463868 11382.5    0.95
## theta[73]    0.25197708   0.2516320   0.25152876 11001.0    0.95
## theta[74]    0.22565844   0.2247530   0.21833935 11001.0    0.95
## theta[75]    0.25249391   0.2516100   0.25085847 11001.0    0.95
## theta[76]    0.10952058   0.1082060   0.10229689  9568.0    0.95
## theta[77]    0.28027496   0.2798630   0.28020380 11001.0    0.95
## theta[78]    0.24323747   0.2428460   0.24220928 11001.0    0.95
## theta[79]    0.24949079   0.2494640   0.25010180 10656.3    0.95
## theta[80]    0.23272971   0.2327880   0.23268216 11001.0    0.95
## theta[81]    0.25854101   0.2582640   0.25441851 10635.7    0.95
## theta[82]    0.23781766   0.2366930   0.23081534 11001.0    0.95
## theta[83]    0.25824882   0.2579830   0.26316232 11001.0    0.95
## theta[84]    0.24207379   0.2419920   0.24011677 11001.0    0.95
## theta[85]    0.26969738   0.2695860   0.27113199 11001.0    0.95
## theta[86]    0.12720997   0.1253200   0.11733171 10393.1    0.95
## theta[87]    0.12881839   0.1260050   0.11862584  9991.8    0.95
## theta[88]    0.27919363   0.2790210   0.27939712 11001.0    0.95
## theta[89]    0.25940649   0.2586950   0.26149140 11476.1    0.95
## theta[90]    0.30155400   0.3012080   0.29884968 11001.0    0.95
## theta[91]    0.12573311   0.1232110   0.11993857 11001.0    0.95
## theta[92]    0.23575488   0.2357410   0.22912327 11001.0    0.95
## theta[93]    0.12784476   0.1253270   0.11926034 11001.0    0.95
## theta[94]    0.25376370   0.2535240   0.24834289 11001.0    0.95
## theta[95]    0.24484656   0.2441630   0.24166567 11001.0    0.95
## theta[96]    0.25216416   0.2518700   0.24864307 11001.0    0.95
## theta[97]    0.12951898   0.1270150   0.12506404 11001.0    0.95
## theta[98]    0.25520461   0.2550780   0.25628499 11001.0    0.95
## theta[99]    0.12760118   0.1253600   0.12273710 11001.0    0.95
## theta[100]   0.20799087   0.2075380   0.20682075 11001.0    0.95
## theta[101]   0.24244318   0.2421090   0.24271207 11001.0    0.95
## theta[102]   0.09534844   0.0943767   0.09216475  8194.8    0.95
## theta[103]   0.14370372   0.1424350   0.13669018 11001.0    0.95
## theta[104]   0.10384066   0.1026480   0.09993205  9505.5    0.95
## theta[105]   0.12870244   0.1261430   0.11878623 11001.0    0.95
## theta[106]   0.23700406   0.2366100   0.23623549 10642.8    0.95
## theta[107]   0.24649646   0.2460530   0.24034019 11836.3    0.95
## theta[108]   0.22323678   0.2228460   0.22141687 11001.0    0.95
## theta[109]   0.29643397   0.2960790   0.29544760 11001.0    0.95
## theta[110]   0.23959965   0.2395600   0.24154780 11313.9    0.95
## theta[111]   0.26415143   0.2638570   0.26394430 10634.2    0.95
## theta[112]   0.12859876   0.1258230   0.12138346 11001.0    0.95
## theta[113]   0.24733823   0.2470690   0.24565573 11001.0    0.95
## theta[114]   0.31660029   0.3163570   0.31823272 10012.6    0.95
## theta[115]   0.31156635   0.3111870   0.30892972 11001.0    0.95
## theta[116]   0.12578248   0.1243660   0.11882931 11001.0    0.95
## theta[117]   0.26238284   0.2619280   0.26052179 11104.3    0.95
## theta[118]   0.14949631   0.1473320   0.14348156 11001.0    0.95
## theta[119]   0.22733396   0.2271700   0.22939647 11576.9    0.95
## theta[120]   0.25499048   0.2548390   0.25266605 11001.0    0.95
## theta[121]   0.25386110   0.2534100   0.25258561 11001.0    0.95
## theta[122]   0.26137654   0.2608490   0.25936412 11001.0    0.95
## theta[123]   0.29473574   0.2942820   0.29271451 11001.0    0.95
## theta[124]   0.25680722   0.2560320   0.25432195 11001.0    0.95
## theta[125]   0.11707489   0.1156170   0.11441745 10680.6    0.95
## theta[126]   0.23705003   0.2367780   0.23854925 11862.9    0.95
## theta[127]   0.23845662   0.2378130   0.23523680 11001.0    0.95
## theta[128]   0.23496859   0.2346390   0.23066793 11454.9    0.95
## theta[129]   0.13221528   0.1302410   0.12862414 11001.0    0.95
## theta[130]   0.13918563   0.1361350   0.13070549 10534.8    0.95
## theta[131]   0.27632195   0.2760360   0.27651037 10862.4    0.95
## theta[132]   0.12890677   0.1264270   0.12035412 10624.7    0.95
## theta[133]   0.26007444   0.2597620   0.26085455 11001.0    0.95
## theta[134]   0.25588143   0.2548740   0.25120005 10614.0    0.95
## theta[135]   0.26201497   0.2617120   0.26297415 10775.7    0.95
## theta[136]   0.24533223   0.2450220   0.24542335 10321.9    0.95
## theta[137]   0.24657734   0.2465200   0.24337956 11001.0    0.95
## theta[138]   0.13137186   0.1292040   0.12724102 11001.0    0.95
## theta[139]   0.24518924   0.2449340   0.24472093 11001.0    0.95
## theta[140]   0.13649586   0.1331250   0.12731788 11001.0    0.95
## theta[141]   0.24307333   0.2426280   0.24265185 11529.3    0.95
## theta[142]   0.25256895   0.2520010   0.25027600 11423.0    0.95
## theta[143]   0.25043592   0.2502660   0.25388130 11001.0    0.95
## theta[144]   0.27409754   0.2739260   0.27729552 11001.0    0.95
## theta[145]   0.12781301   0.1256530   0.12280810 11001.0    0.95
## theta[146]   0.25413532   0.2537280   0.25408428 11001.0    0.95
## theta[147]   0.12912204   0.1267790   0.12688222 11001.0    0.95
## theta[148]   0.23875071   0.2376280   0.23065028 11546.5    0.95
## theta[149]   0.27935499   0.2790320   0.27693813 11001.0    0.95
## theta[150]   0.16293044   0.1592490   0.15169599  9904.3    0.95
## theta[151]   0.25166373   0.2516380   0.25280442 11001.0    0.95
## theta[152]   0.14704092   0.1441790   0.13985289 10128.8    0.95
## theta[153]   0.23037171   0.2301400   0.23027562 11356.2    0.95
## theta[154]   0.27097015   0.2706180   0.27121995 11001.0    0.95
## theta[155]   0.12803825   0.1261370   0.12106783 11001.0    0.95
## theta[156]   0.13406798   0.1315370   0.12775261 11083.3    0.95
## theta[157]   0.12662386   0.1250320   0.12626792 10601.6    0.95
## theta[158]   0.26218211   0.2617720   0.26405575 12015.8    0.95
## theta[159]   0.27381066   0.2734510   0.27023736 11001.0    0.95
## theta[160]   0.22392776   0.2237990   0.22024294 11001.0    0.95
## theta[161]   0.12901676   0.1265890   0.11938034 11001.0    0.95
## theta[162]   0.27787141   0.2772890   0.27602186 10663.9    0.95
## theta[163]   0.12897020   0.1265990   0.12298680 11001.0    0.95
## theta[164]   0.24373408   0.2432050   0.24264854 11346.0    0.95
## theta[165]   0.12852712   0.1258990   0.12254929 11001.0    0.95
## theta[166]   0.21856327   0.2183580   0.21592110 11001.0    0.95
## theta[167]   0.12616331   0.1246620   0.12549981 11001.0    0.95
## theta[168]   0.21622513   0.2162860   0.21685058 11001.0    0.95
## theta[169]   0.13692652   0.1343620   0.13124339 11001.0    0.95
## theta[170]   0.12971784   0.1273170   0.12528277 10683.0    0.95
## theta[171]   0.12110878   0.1195020   0.11929805 11001.0    0.95
## theta[172]   0.12508973   0.1223490   0.11544275 11001.0    0.95
## theta[173]   0.12900438   0.1264520   0.12320117 11652.9    0.95
## theta[174]   0.27634092   0.2759680   0.27648355 11001.0    0.95
## theta[175]   0.23316823   0.2321960   0.22987346 11001.0    0.95
## theta[176]   0.21604905   0.2157720   0.21245351 11001.0    0.95
## theta[177]   0.25091952   0.2505290   0.25041168 10668.4    0.95
## theta[178]   0.27122811   0.2704770   0.26723489 11001.0    0.95
## theta[179]   0.12539389   0.1235650   0.11815638 11001.0    0.95
## theta[180]   0.12852735   0.1258670   0.12398880 11001.0    0.95
## theta[181]   0.24978972   0.2491410   0.25217912 11456.4    0.95
## theta[182]   0.12231352   0.1210920   0.11836662 10123.3    0.95
## theta[183]   0.23891313   0.2385410   0.23912209 11001.0    0.95
## theta[184]   0.23215470   0.2321170   0.22698100 11001.0    0.95
## theta[185]   0.25726490   0.2564780   0.25513892 11001.0    0.95
## theta[186]   0.24720273   0.2469390   0.24671037 11001.0    0.95
## theta[187]   0.28935120   0.2892670   0.29048640 11001.0    0.95
## theta[188]   0.24875580   0.2485440   0.24803105 11001.0    0.95
## theta[189]   0.26245876   0.2619150   0.26308785 11001.0    0.95
## theta[190]   0.25921864   0.2591080   0.26113382 10667.4    0.95
## theta[191]   0.28054687   0.2801490   0.27813284 11001.0    0.95
## theta[192]   0.25982532   0.2596630   0.26043195 11001.0    0.95
## theta[193]   0.12869759   0.1265460   0.12329487 10622.6    0.95
## theta[194]   0.24634513   0.2457920   0.24400888 11001.0    0.95
## theta[195]   0.25990679   0.2596100   0.25720369 11001.0    0.95
## theta[196]   0.11381102   0.1125520   0.11359979 10238.6    0.95
## theta[197]   0.24106620   0.2412470   0.24517273 11001.0    0.95
## theta[198]   0.22682356   0.2267790   0.22929209  9516.2    0.95
## theta[199]   0.24442279   0.2442550   0.24793735 10512.4    0.95
## theta[200]   0.23696160   0.2367700   0.23629304 11325.4    0.95
## theta[201]   0.12638707   0.1240300   0.11879836 11001.0    0.95
## theta[202]   0.24456756   0.2443880   0.24446475 11001.0    0.95
## theta[203]   0.24999852   0.2494460   0.24504442 11001.0    0.95
## theta[204]   0.13695840   0.1337630   0.13024647 11001.0    0.95
## theta[205]   0.26504656   0.2648340   0.26620937 11001.0    0.95
## theta[206]   0.23563367   0.2354220   0.23538700 11001.0    0.95
## theta[207]   0.25558680   0.2555040   0.25583432 11001.0    0.95
## theta[208]   0.13809988   0.1352320   0.12997416 10829.6    0.95
## theta[209]   0.27368954   0.2735070   0.27051558 11001.0    0.95
## theta[210]   0.24613783   0.2456920   0.24286122 11001.0    0.95
## theta[211]   0.14457995   0.1410710   0.13348785 11001.0    0.95
## theta[212]   0.26173202   0.2616000   0.25986337 11001.0    0.95
## theta[213]   0.15297532   0.1502110   0.14646460 11001.0    0.95
## theta[214]   0.12130023   0.1197610   0.11566042 11001.0    0.95
## theta[215]   0.27601624   0.2755970   0.27377280 11001.0    0.95
## theta[216]   0.23691386   0.2367520   0.23374470 11364.3    0.95
## theta[217]   0.23675648   0.2366240   0.23517350 11001.0    0.95
## theta[218]   0.27877071   0.2783070   0.27489321 11001.0    0.95
## theta[219]   0.10311301   0.1018120   0.09848354  8126.4    0.95
## theta[220]   0.23838378   0.2383180   0.23808631 11001.0    0.95
## theta[221]   0.13067342   0.1284620   0.12353745 11001.0    0.95
## theta[222]   0.25040183   0.2501010   0.24736973 10172.3    0.95
## theta[223]   0.24139297   0.2410490   0.24211128 11001.0    0.95
## theta[224]   0.25393772   0.2531660   0.25091307 11001.0    0.95
## theta[225]   0.14004447   0.1382720   0.13829969 11001.0    0.95
## theta[226]   0.13808354   0.1349650   0.13052830 11001.0    0.95
## theta[227]   0.29357469   0.2931740   0.29514673 11001.0    0.95
## theta[228]   0.27425730   0.2742320   0.27637815 11001.0    0.95
## theta[229]   0.26719018   0.2665300   0.26606037 11001.0    0.95
## theta[230]   0.23635764   0.2361470   0.23852093 11001.0    0.95
## theta[231]   0.24762067   0.2476260   0.24789757 11320.4    0.95
## theta[232]   0.12864559   0.1265030   0.12302049 11001.0    0.95
## theta[233]   0.26586698   0.2655730   0.26668070 11705.2    0.95
## theta[234]   0.23146208   0.2313300   0.23113826 11001.0    0.95
## theta[235]   0.24050165   0.2404580   0.24173599 10015.2    0.95
## theta[236]   0.12731539   0.1249390   0.12146515 11001.0    0.95
## theta[237]   0.23475524   0.2344280   0.23189357 11001.0    0.95
## theta[238]   0.24993555   0.2498860   0.25026757 10487.8    0.95
## theta[239]   0.12910997   0.1264260   0.11789345 10563.8    0.95
## theta[240]   0.12931517   0.1266260   0.11861022  9826.2    0.95
## theta[241]   0.22552497   0.2253110   0.22332337 11001.0    0.95
## theta[242]   0.21906863   0.2187680   0.21814619 11001.0    0.95
## theta[243]   0.12892078   0.1263480   0.12099702 11001.0    0.95
## theta[244]   0.25968486   0.2593930   0.25870279 10977.1    0.95
## theta[245]   0.25887186   0.2584240   0.25635937 11001.0    0.95
## theta[246]   0.12908717   0.1264170   0.11935601 11001.0    0.95
## theta[247]   0.26110999   0.2610130   0.26262320 11001.0    0.95
## theta[248]   0.25504052   0.2548440   0.25342359 11001.0    0.95
## theta[249]   0.26560724   0.2652400   0.26422680 11001.0    0.95
## theta[250]   0.23585355   0.2358140   0.23786077 11001.0    0.95
## theta[251]   0.27252475   0.2721670   0.27078952 11001.0    0.95
## theta[252]   0.12089414   0.1194600   0.12111545 10628.2    0.95
## theta[253]   0.28028053   0.2800240   0.27756945 10701.3    0.95
## theta[254]   0.24024423   0.2402740   0.24315032 10391.8    0.95
## theta[255]   0.25162689   0.2514040   0.24963723 11337.7    0.95
## theta[256]   0.24837597   0.2481570   0.24837602 11001.0    0.95
## theta[257]   0.12164914   0.1203240   0.11776555 11001.0    0.95
## theta[258]   0.27433250   0.2740120   0.27257680 11001.0    0.95
## theta[259]   0.22219316   0.2219720   0.22782063 11001.0    0.95
## theta[260]   0.27472845   0.2738320   0.27403862 11001.0    0.95
## theta[261]   0.12879428   0.1266570   0.12629328 11001.0    0.95
## theta[262]   0.24632195   0.2458880   0.24489783 11001.0    0.95
## theta[263]   0.24181820   0.2407250   0.23827088 11152.5    0.95
## theta[264]   0.13532296   0.1324000   0.12794900 11001.0    0.95
## theta[265]   0.12773890   0.1253560   0.12167407 12186.4    0.95
## theta[266]   0.24844057   0.2477880   0.24301258 11001.0    0.95
## theta[267]   0.29611645   0.2957020   0.29324181 11001.0    0.95
## theta[268]   0.12096352   0.1193580   0.11898204 11001.0    0.95
## theta[269]   0.14122543   0.1379380   0.12797846 10287.6    0.95
## theta[270]   0.21988360   0.2200280   0.22247913 11001.0    0.95
## theta[271]   0.12743022   0.1251190   0.11967155 10580.6    0.95
## theta[272]   0.23715126   0.2369240   0.23655478 10867.8    0.95
## theta[273]   0.22316684   0.2228220   0.22393853 10237.0    0.95
## theta[274]   0.23807047   0.2380200   0.24115245 11001.0    0.95
## theta[275]   0.22794492   0.2277380   0.22612645 11325.1    0.95
## theta[276]   0.12983483   0.1270340   0.12302740 11001.0    0.95
## theta[277]   0.26214190   0.2613860   0.25675309 11001.0    0.95
## theta[278]   0.23360468   0.2330940   0.23082174 11113.1    0.95
## theta[279]   0.26331768   0.2629530   0.25986998 11255.5    0.95
## theta[280]   0.28558279   0.2855180   0.28705933 11001.0    0.95
## theta[281]   0.11387909   0.1123230   0.10979735 10452.2    0.95
## theta[282]   0.25331540   0.2532110   0.25254250 11001.0    0.95
## theta[283]   0.24605717   0.2459530   0.24691485 11001.0    0.95
## theta[284]   0.24558941   0.2456470   0.24844323 11001.0    0.95
## theta[285]   0.29739651   0.2967490   0.29558414 11001.0    0.95
## theta[286]   0.26759973   0.2672800   0.26777647 11001.0    0.95
## theta[287]   0.25760514   0.2573180   0.25373963 11001.0    0.95
## theta[288]   0.28299761   0.2826640   0.28231196 11001.0    0.95
## theta[289]   0.11988683   0.1181660   0.11605119 11001.0    0.95
## theta[290]   0.25142192   0.2507570   0.25006533 11001.0    0.95
## theta[291]   0.23794911   0.2377220   0.23683818 11321.7    0.95
## theta[292]   0.25945654   0.2592090   0.25645240 11300.8    0.95
## theta[293]   0.25938558   0.2590810   0.25731043 11470.5    0.95
## theta[294]   0.12022965   0.1186260   0.11750855 11001.0    0.95
## theta[295]   0.14433887   0.1427330   0.14177019 10545.5    0.95
## theta[296]   0.23997877   0.2396250   0.23920652 11001.0    0.95
## theta[297]   0.25221863   0.2516270   0.25135920 11001.0    0.95
## theta[298]   0.26705194   0.2664890   0.26373458 11001.0    0.95
## theta[299]   0.12935779   0.1267180   0.12234656 11001.0    0.95
## theta[300]   0.16512114   0.1622680   0.15901828  9056.6    0.95
## theta[301]   0.26051232   0.2598900   0.26100972 10958.1    0.95
## theta[302]   0.11522106   0.1134870   0.11079059  9037.2    0.95
## theta[303]   0.12040458   0.1186040   0.11961096 11374.2    0.95
## theta[304]   0.13359707   0.1313070   0.12415411 11306.4    0.95
## theta[305]   0.28192588   0.2813210   0.27918934 11001.0    0.95
## theta[306]   0.11924841   0.1173360   0.10875345 10669.6    0.95
## theta[307]   0.26143647   0.2609370   0.26030884 11001.0    0.95
## theta[308]   0.24555005   0.2448120   0.24286775 11001.0    0.95
## theta[309]   0.24505811   0.2448030   0.24871229 11001.0    0.95
## theta[310]   0.24769070   0.2474430   0.24951646 11001.0    0.95
## theta[311]   0.24765574   0.2467460   0.24418428 10550.3    0.95
## theta[312]   0.25423601   0.2527640   0.25054516 11001.0    0.95
## theta[313]   0.27594074   0.2757620   0.27700410 11001.0    0.95
## theta[314]   0.12918337   0.1270060   0.12239179 10450.9    0.95
## theta[315]   0.25697995   0.2566200   0.25718331 10311.6    0.95
## theta[316]   0.23867365   0.2382980   0.23491387 11001.0    0.95
## theta[317]   0.25994125   0.2596690   0.25912174 11001.0    0.95
## theta[318]   0.13546636   0.1321220   0.12716752 11001.0    0.95
## theta[319]   0.26026493   0.2595720   0.25848682 11199.6    0.95
## theta[320]   0.28677018   0.2865910   0.29119511 10038.8    0.95
## theta[321]   0.24722265   0.2466010   0.24985898 11001.0    0.95
## theta[322]   0.25220196   0.2514310   0.24722354 11001.0    0.95
## theta[323]   0.28800175   0.2876740   0.28693002 11001.0    0.95
## theta[324]   0.12875140   0.1268330   0.12503544 10569.5    0.95
## theta[325]   0.11587267   0.1144480   0.11305161 11001.0    0.95
## theta[326]   0.24213180   0.2418980   0.24094793 11001.0    0.95
## theta[327]   0.28385045   0.2836740   0.28431516 11001.0    0.95
## theta[328]   0.23716695   0.2369420   0.23625147 11001.0    0.95
## theta[329]   0.14254920   0.1395670   0.13390773 10608.2    0.95
## theta[330]   0.24860678   0.2484000   0.25061344 11001.0    0.95
## theta[331]   0.25602079   0.2558430   0.25825207 11001.0    0.95
## theta[332]   0.27099780   0.2704100   0.27011321 11001.0    0.95
## theta[333]   0.23931637   0.2392490   0.23983464 11001.0    0.95
## theta[334]   0.24880066   0.2484150   0.24850993 11046.8    0.95
## theta[335]   0.23230520   0.2321280   0.23337966 11001.0    0.95
## theta[336]   0.23877329   0.2387880   0.23975940 11001.0    0.95
## theta[337]   0.12857581   0.1260470   0.11948409 10274.0    0.95
## theta[338]   0.25348358   0.2528210   0.25345205 11001.0    0.95
## theta[339]   0.15093056   0.1481370   0.14424850 11147.8    0.95
## theta[340]   0.11981980   0.1183110   0.11869835 10468.2    0.95
## theta[341]   0.25957913   0.2590660   0.25374748 11001.0    0.95
## theta[342]   0.24770629   0.2469640   0.24451084 11001.0    0.95
## theta[343]   0.24874044   0.2483690   0.24777569 11001.0    0.95
## theta[344]   0.24476363   0.2445970   0.24569756 11001.0    0.95
## theta[345]   0.12618936   0.1239880   0.12094730 11001.0    0.95
## theta[346]   0.24782524   0.2475270   0.24982570 11001.0    0.95
## theta[347]   0.26596826   0.2656050   0.26526729 11001.0    0.95
## theta[348]   0.25878199   0.2586810   0.25731399 11422.0    0.95
## theta[349]   0.25033060   0.2495110   0.24676927 10575.0    0.95
## theta[350]   0.14417562   0.1423690   0.13850608 10629.6    0.95
## theta[351]   0.16619578   0.1634740   0.15861003  8553.4    0.95
## theta[352]   0.27730036   0.2771830   0.27702738 11311.8    0.95
## theta[353]   0.12472472   0.1227240   0.12185520 10287.8    0.95
## theta[354]   0.12967484   0.1274820   0.12776971 11001.0    0.95
## theta[355]   0.12898296   0.1265810   0.12380041 11001.0    0.95
## theta[356]   0.26239515   0.2621360   0.26405547 10404.8    0.95
## theta[357]   0.24986982   0.2494980   0.24948216 10591.4    0.95
## theta[358]   0.09314817   0.0922478   0.09160304  7812.7    0.95
## theta[359]   0.12101620   0.1194880   0.11247118 11001.0    0.95
## theta[360]   0.10887463   0.1080820   0.11162718 10349.3    0.95
## theta[361]   0.24128165   0.2410240   0.23967601 11001.0    0.95
## theta[362]   0.12492373   0.1227280   0.11607190 11001.0    0.95
## theta[363]   0.26682488   0.2664560   0.26196457 11001.0    0.95
## theta[364]   0.13829431   0.1363610   0.13224750 11001.0    0.95
## theta[365]   0.22912669   0.2292370   0.23053362 11001.0    0.95
## theta[366]   0.24180821   0.2417540   0.24302659 11001.0    0.95
## theta[367]   0.12367774   0.1219090   0.12143054 11001.0    0.95
## theta[368]   0.26559988   0.2653780   0.26665496 11001.0    0.95
## theta[369]   0.16286324   0.1588280   0.15056968  9482.5    0.95
## theta[370]   0.12871940   0.1267260   0.13084908 10666.5    0.95
## theta[371]   0.22458424   0.2246330   0.22746563 11001.0    0.95
## theta[372]   0.27906101   0.2787360   0.27950136 11001.0    0.95
## theta[373]   0.25649104   0.2559700   0.25398149 11468.7    0.95
## theta[374]   0.12496535   0.1234280   0.12435209 10343.7    0.95
## theta[375]   0.25973744   0.2596080   0.26001969 11001.0    0.95
## theta[376]   0.12934760   0.1268380   0.12486938 10604.8    0.95
## theta[377]   0.24556612   0.2454450   0.24620395 11001.0    0.95
## theta[378]   0.12795746   0.1260150   0.12410085 11001.0    0.95
## theta[379]   0.12894899   0.1262330   0.11594821 11001.0    0.95
## theta[380]   0.25994283   0.2597430   0.25722054 11001.0    0.95
## theta[381]   0.13974912   0.1363850   0.13090863 11001.0    0.95
## theta[382]   0.13704311   0.1343880   0.13193991 10698.0    0.95
## theta[383]   0.23186142   0.2315880   0.23039344 11001.0    0.95
## theta[384]   0.12480551   0.1224350   0.11912365 11001.0    0.95
## theta[385]   0.25479856   0.2541930   0.25354811 11001.0    0.95
## theta[386]   0.22815638   0.2279180   0.22966115 11001.0    0.95
## theta[387]   0.12887860   0.1264560   0.12472477 11934.3    0.95
## theta[388]   0.25103012   0.2506820   0.25187164 11001.0    0.95
## theta[389]   0.25564968   0.2553150   0.25462714 11001.0    0.95
## theta[390]   0.22176678   0.2211510   0.21964519 10771.6    0.95
## theta[391]   0.23046566   0.2298080   0.22329098 11001.0    0.95
## theta[392]   0.26563793   0.2654250   0.26541663 10957.3    0.95
## theta[393]   0.23213228   0.2322900   0.23649035 11672.8    0.95
## theta[394]   0.28718845   0.2868650   0.28957712 11001.0    0.95
## theta[395]   0.22891458   0.2281040   0.22387315 10640.4    0.95
## theta[396]   0.25149107   0.2507780   0.25155428 11001.0    0.95
## theta[397]   0.12888024   0.1269730   0.12778123 11001.0    0.95
## theta[398]   0.23316540   0.2328460   0.23183546 11001.0    0.95
## theta[399]   0.13377609   0.1317530   0.13160379 11001.0    0.95
## theta[400]   0.25024371   0.2497200   0.24457413 11001.0    0.95
## theta[401]   0.24098930   0.2399810   0.24028853 10825.6    0.95
## theta[402]   0.12715374   0.1247010   0.11818112 11001.0    0.95
## theta[403]   0.28418443   0.2838590   0.28421682 11001.0    0.95
## theta[404]   0.25993582   0.2594740   0.26387858 11001.0    0.95
## theta[405]   0.23823557   0.2380810   0.23694709 11001.0    0.95
## theta[406]   0.23556102   0.2356200   0.23700875 11001.0    0.95
## theta[407]   0.13325587   0.1309000   0.12745753 11001.0    0.95
## theta[408]   0.22680459   0.2266470   0.22480956 11001.0    0.95
## theta[409]   0.16196121   0.1596590   0.15980894  9738.9    0.95
## theta[410]   0.23792484   0.2374890   0.23193700 11001.0    0.95
## theta[411]   0.12880140   0.1264110   0.12212334 10626.9    0.95
## theta[412]   0.24443041   0.2441400   0.24441828 11001.0    0.95
## theta[413]   0.12754452   0.1254470   0.12313901 11001.0    0.95
## theta[414]   0.13389058   0.1310850   0.12140736 11001.0    0.95
## theta[415]   0.19420086   0.1939830   0.19420939 10843.2    0.95
## theta[416]   0.12782933   0.1253830   0.12231143 10309.6    0.95
## theta[417]   0.29102594   0.2907250   0.29118346 11001.0    0.95
## theta[418]   0.12770135   0.1255360   0.12467956 11001.0    0.95
## theta[419]   0.24001212   0.2397170   0.23704611 10534.9    0.95
## theta[420]   0.24420574   0.2440010   0.24344089 11001.0    0.95
## theta[421]   0.21878380   0.2190230   0.21922867 11001.0    0.95
## theta[422]   0.26711149   0.2668410   0.26335489 11001.0    0.95
## theta[423]   0.23355845   0.2332460   0.23129156 11394.1    0.95
## theta[424]   0.25504481   0.2546540   0.25397691 11001.0    0.95
## theta[425]   0.24618109   0.2458280   0.24515124 11001.0    0.95
## theta[426]   0.25651586   0.2560490   0.25404139  9930.3    0.95
## theta[427]   0.28767401   0.2875190   0.28893732 11001.0    0.95
## theta[428]   0.22989375   0.2297630   0.22786240 11001.0    0.95
## theta[429]   0.16596171   0.1633330   0.15788686  9505.5    0.95
## theta[430]   0.24178698   0.2411950   0.23997986 10284.7    0.95
## theta[431]   0.25047252   0.2501260   0.24774954 11001.0    0.95
## theta[432]   0.22236474   0.2221200   0.22158235 11001.0    0.95
## theta[433]   0.26260787   0.2619720   0.26006038 11001.0    0.95
## theta[434]   0.28883039   0.2884240   0.28603338 10982.9    0.95
## theta[435]   0.12845018   0.1257690   0.12392276 11001.0    0.95
## theta[436]   0.24703935   0.2466980   0.24604930 11001.0    0.95
## theta[437]   0.29897446   0.2986890   0.29524411 11001.0    0.95
## theta[438]   0.12390306   0.1219920   0.12236688 11001.0    0.95
## theta[439]   0.27400300   0.2737250   0.27322964 11001.0    0.95
## theta[440]   0.26378471   0.2629040   0.25838237 11001.0    0.95
## theta[441]   0.24586239   0.2456100   0.24567128 11001.0    0.95
## theta[442]   0.11802608   0.1169820   0.11787078 10678.2    0.95
## theta[443]   0.23346643   0.2333370   0.23282635 11001.0    0.95
## theta[444]   0.24057211   0.2403800   0.23985161 11001.0    0.95
## theta[445]   0.27297680   0.2727480   0.27230388 11001.0    0.95
## theta[446]   0.24304341   0.2420260   0.24111476 11001.0    0.95
## theta[447]   0.27979331   0.2795390   0.27893933 11001.0    0.95
## theta[448]   0.22287777   0.2225680   0.22117045 11001.0    0.95
## theta[449]   0.27663260   0.2764350   0.27730081 11001.0    0.95
## theta[450]   0.26707722   0.2668640   0.26354775 11001.0    0.95
## theta[451]   0.25048862   0.2503700   0.24894678 11001.0    0.95
## theta[452]   0.12389514   0.1221920   0.12029336 11001.0    0.95
## theta[453]   0.24674099   0.2465810   0.24561792 11001.0    0.95
## theta[454]   0.24948235   0.2494440   0.25037429 11001.0    0.95
## theta[455]   0.12507574   0.1235250   0.12346319 11001.0    0.95
## theta[456]   0.23087226   0.2308070   0.22981800 11001.0    0.95
## theta[457]   0.24844200   0.2480220   0.24750137 11001.0    0.95
## theta[458]   0.13685114   0.1343440   0.12575045 11001.0    0.95
## theta[459]   0.24310680   0.2429720   0.24319995 11001.0    0.95
## theta[460]   0.13549898   0.1332690   0.12715634  9547.7    0.95
## theta[461]   0.28673164   0.2861760   0.28360583 11001.0    0.95
## theta[462]   0.27591788   0.2756240   0.27667823  9876.4    0.95
## theta[463]   0.13277991   0.1311690   0.12535385 11001.0    0.95
## theta[464]   0.25983876   0.2591990   0.25842295 11001.0    0.95
## theta[465]   0.11236285   0.1110470   0.11087766 10507.1    0.95
## theta[466]   0.30159197   0.3010650   0.29892367 10671.1    0.95
## theta[467]   0.15853621   0.1564610   0.15288828 10324.0    0.95
## theta[468]   0.12521271   0.1234430   0.12444890  9730.5    0.95
## theta[469]   0.25461262   0.2542590   0.25264526 11001.0    0.95
## theta[470]   0.25530504   0.2553620   0.25754887 10433.8    0.95
## theta[471]   0.12941540   0.1265580   0.12039809 11001.0    0.95
## theta[472]   0.25450188   0.2545260   0.25529332 10593.0    0.95
## theta[473]   0.28488154   0.2845300   0.28447839 11001.0    0.95
## theta[474]   0.12880522   0.1262990   0.12360711 10286.9    0.95
## theta[475]   0.23732470   0.2369750   0.23659451 11001.0    0.95
## theta[476]   0.25422569   0.2536400   0.25113835 11001.0    0.95
## theta[477]   0.22510876   0.2247580   0.22392749 11001.0    0.95
## theta[478]   0.26956091   0.2684350   0.26832833 11001.0    0.95
## theta[479]   0.24413555   0.2431880   0.24086390 11001.0    0.95
## theta[480]   0.25236524   0.2521100   0.25053810 11001.0    0.95
## theta[481]   0.12744837   0.1248820   0.11948250 11001.0    0.95
## theta[482]   0.12921572   0.1265660   0.12425464 11001.0    0.95
## theta[483]   0.25662359   0.2561680   0.25507054 11001.0    0.95
## theta[484]   0.25734051   0.2566770   0.25151502 11001.0    0.95
## theta[485]   0.26151401   0.2609160   0.26047303 11422.1    0.95
## theta[486]   0.23169418   0.2308130   0.22662243 11001.0    0.95
## theta[487]   0.24982111   0.2489250   0.24701827 11001.0    0.95
## theta[488]   0.12829082   0.1258990   0.12417868 11001.0    0.95
## theta[489]   0.25135223   0.2510300   0.25156911 11001.0    0.95
## theta[490]   0.26638290   0.2661640   0.26450787 11001.0    0.95
## theta[491]   0.15196834   0.1501310   0.15055301 11001.0    0.95
## theta[492]   0.20047450   0.1997630   0.19762045 11001.0    0.95
## theta[493]   0.26875936   0.2683050   0.26617348 11001.0    0.95
## theta[494]   0.19367260   0.1909760   0.17990782  7729.3    0.95
## theta[495]   0.12450712   0.1229210   0.12372708 10306.5    0.95
## theta[496]   0.12873560   0.1264550   0.12537048 11001.0    0.95
## theta[497]   0.26078183   0.2603990   0.25811088 10641.6    0.95
## theta[498]   0.14044720   0.1388410   0.13004382 11001.0    0.95
## theta[499]   0.27311721   0.2725310   0.26834606 11001.0    0.95
## theta[500]   0.24432614   0.2434750   0.24329108 11001.0    0.95
## theta[501]   0.12840541   0.1263340   0.12441448 11353.0    0.95
## theta[502]   0.23839281   0.2373790   0.23696978 11551.8    0.95
## theta[503]   0.12340367   0.1210920   0.11975390 11136.0    0.95
## theta[504]   0.14478855   0.1417010   0.14079707 11001.0    0.95
## theta[505]   0.24263350   0.2425500   0.24806148 12619.0    0.95
## theta[506]   0.24566580   0.2457200   0.24706686 11001.0    0.95
## theta[507]   0.22707816   0.2269330   0.22503546 11001.0    0.95
## theta[508]   0.12137803   0.1195890   0.11258548 11001.0    0.95
## theta[509]   0.26015703   0.2597760   0.25903263 11001.0    0.95
## theta[510]   0.11651307   0.1152900   0.10965208 11001.0    0.95
## theta[511]   0.13712472   0.1345270   0.13441798 11001.0    0.95
## theta[512]   0.25452259   0.2542070   0.25369794 11001.0    0.95
## theta[513]   0.12505347   0.1227890   0.12107211 11001.0    0.95
## theta[514]   0.23127980   0.2308830   0.22991541 11001.0    0.95
## theta[515]   0.12586799   0.1238420   0.12096335 11001.0    0.95
## theta[516]   0.11614143   0.1149630   0.11289941  9925.1    0.95
## theta[517]   0.26446003   0.2641710   0.26449323 11001.0    0.95
## theta[518]   0.25063556   0.2503530   0.25025046 11001.0    0.95
## theta[519]   0.27590184   0.2755070   0.27577806 11544.6    0.95
## theta[520]   0.25134539   0.2511350   0.24732770 11001.0    0.95
## theta[521]   0.25430220   0.2537740   0.25135231 11001.0    0.95
## theta[522]   0.12891403   0.1264150   0.12477013 10597.5    0.95
## theta[523]   0.12867418   0.1263470   0.11783274 11001.0    0.95
## theta[524]   0.14535869   0.1423960   0.13844468 10696.8    0.95
## theta[525]   0.24626856   0.2461170   0.24846508 11001.0    0.95
## theta[526]   0.29182511   0.2913320   0.29228904 11001.0    0.95
## theta[527]   0.26716085   0.2668600   0.26645731 11001.0    0.95
## theta[528]   0.11695081   0.1149640   0.11171326  9980.9    0.95
## theta[529]   0.24785856   0.2472730   0.24256244 10630.9    0.95
## theta[530]   0.24652891   0.2458890   0.24564300 10695.2    0.95
## theta[531]   0.13824075   0.1358600   0.12959510 11001.0    0.95
## theta[532]   0.10655466   0.1055350   0.10272536 10050.5    0.95
## theta[533]   0.26012905   0.2598230   0.25979476 11001.0    0.95
## theta[534]   0.10401735   0.1031790   0.10293531  9734.6    0.95
## theta[535]   0.24871695   0.2479780   0.24149312 11001.0    0.95
## theta[536]   0.23941943   0.2394390   0.23892827 11001.0    0.95
## theta[537]   0.12777373   0.1258000   0.12098179 11173.5    0.95
## theta[538]   0.22750523   0.2272850   0.22870077 11001.0    0.95
## theta[539]   0.25529845   0.2549410   0.25667745 11001.0    0.95
## theta[540]   0.12284364   0.1209610   0.11972993 11001.0    0.95
## theta[541]   0.27985610   0.2794420   0.27893696 11001.0    0.95
## theta[542]   0.16807126   0.1654260   0.16210389  8921.1    0.95
## theta[543]   0.23245210   0.2318650   0.23042029 11001.0    0.95
## theta[544]   0.12933131   0.1264530   0.12296077 10545.6    0.95
## theta[545]   0.25422320   0.2538980   0.25442405 11001.0    0.95
## theta[546]   0.24450471   0.2444630   0.24630712 11001.0    0.95
## theta[547]   0.21941140   0.2190610   0.21750764 11001.0    0.95
## theta[548]   0.12588867   0.1234340   0.11598049 10381.1    0.95
## theta[549]   0.24629267   0.2454000   0.24333076 11316.5    0.95
## theta[550]   0.24437641   0.2442920   0.24445787 11001.0    0.95
## theta[551]   0.22698871   0.2267630   0.22854568 11337.2    0.95
## theta[552]   0.22591142   0.2263150   0.22750044 11659.1    0.95
## theta[553]   0.12751034   0.1252550   0.12064057 11001.0    0.95
## theta[554]   0.25741520   0.2569870   0.25473954 11001.0    0.95
## theta[555]   0.23415231   0.2342130   0.23662428 10694.0    0.95
## theta[556]   0.22821839   0.2278690   0.22804391 10613.7    0.95
## theta[557]   0.21827929   0.2183360   0.22227170 11001.0    0.95
## theta[558]   0.12779851   0.1252650   0.11981785 10539.2    0.95
## theta[559]   0.12902437   0.1265500   0.12568928 11001.0    0.95
## theta[560]   0.24566154   0.2447320   0.24167682 11001.0    0.95
## theta[561]   0.13562022   0.1330050   0.12898840 10660.9    0.95
## theta[562]   0.30055488   0.3004190   0.29918727 11369.4    0.95
## theta[563]   0.24121570   0.2408490   0.23829228 11001.0    0.95
## theta[564]   0.24704769   0.2467650   0.24601312 10498.6    0.95
## theta[565]   0.24793035   0.2476830   0.24740600 11001.0    0.95
## theta[566]   0.24950346   0.2487680   0.24224872 11601.1    0.95
## theta[567]   0.14536507   0.1421450   0.13807109 10683.2    0.95
## theta[568]   0.25087471   0.2509260   0.25089691 11001.0    0.95
## theta[569]   0.23289324   0.2326970   0.23361283 11001.0    0.95
## theta[570]   0.12945806   0.1271080   0.12428884 11001.0    0.95
## theta[571]   0.12728226   0.1253270   0.12613425 10024.1    0.95
## theta[572]   0.23676728   0.2366730   0.23603580 11001.0    0.95
## theta[573]   0.30767105   0.3074330   0.30681371 11336.8    0.95
## theta[574]   0.23715753   0.2366160   0.23515146 10782.0    0.95
## theta[575]   0.13454718   0.1326150   0.13141139 11419.7    0.95
## theta[576]   0.24884188   0.2483150   0.24535270 11001.0    0.95
## theta[577]   0.23258856   0.2324980   0.23250085  9945.6    0.95
## theta[578]   0.12474376   0.1230380   0.12670572 11001.0    0.95
## theta[579]   0.23639187   0.2359710   0.23493709 11001.0    0.95
## theta[580]   0.24505714   0.2448630   0.24236100 11001.0    0.95
## theta[581]   0.12374848   0.1214890   0.11972845 10630.1    0.95
## theta[582]   0.12762287   0.1256770   0.12324184 13016.3    0.95
## theta[583]   0.12677116   0.1244840   0.12717077 11001.0    0.95
## theta[584]   0.12741766   0.1249150   0.12077045 11001.0    0.95
## theta[585]   0.24101924   0.2407600   0.23651432 10155.9    0.95
## theta[586]   0.26086331   0.2600320   0.25898528 11481.3    0.95
## theta[587]   0.22622503   0.2259520   0.22513366 11001.0    0.95
## theta[588]   0.27482508   0.2745250   0.27091911 11001.0    0.95
## theta[589]   0.12881940   0.1260210   0.12239602 11006.1    0.95
## theta[590]   0.12607273   0.1238190   0.11663882 11001.0    0.95
## theta[591]   0.14577345   0.1441060   0.14464366 10247.1    0.95
## theta[592]   0.14714922   0.1440790   0.14053842 10202.2    0.95
## theta[593]   0.12362731   0.1214770   0.12155950 11001.0    0.95
## theta[594]   0.13405231   0.1315220   0.12758167 10287.6    0.95
## theta[595]   0.10952417   0.1087180   0.10989023  8815.0    0.95
## theta[596]   0.23022597   0.2299300   0.22569729 11001.0    0.95
## theta[597]   0.29616188   0.2957910   0.29566346 11001.0    0.95
## theta[598]   0.25557937   0.2553520   0.25462531 11001.0    0.95
## theta[599]   0.27428009   0.2741130   0.27726513 11001.0    0.95
## theta[600]   0.23667260   0.2359590   0.23584534 11001.0    0.95
## theta[601]   0.12526616   0.1231430   0.11743698 11001.0    0.95
## theta[602]   0.25822792   0.2578900   0.25730804 11001.0    0.95
## theta[603]   0.25623932   0.2557410   0.25828469 11001.0    0.95
## theta[604]   0.13796134   0.1346240   0.12883676 11001.0    0.95
## theta[605]   0.26644708   0.2660410   0.26536470 11001.0    0.95
## theta[606]   0.22879579   0.2288700   0.22991860 11001.0    0.95
## theta[607]   0.26650796   0.2662140   0.26798638 12637.2    0.95
## theta[608]   0.24788234   0.2475780   0.24697080 10655.4    0.95
## theta[609]   0.26308770   0.2628460   0.26243645 11001.0    0.95
## theta[610]   0.23880247   0.2386500   0.23727020 11001.0    0.95
## theta[611]   0.27713654   0.2766810   0.27424101 11001.0    0.95
## theta[612]   0.12922227   0.1267450   0.12733710 11001.0    0.95
## theta[613]   0.11417190   0.1125540   0.10873948  8268.6    0.95
## theta[614]   0.13577236   0.1325900   0.12598846 10493.9    0.95
## theta[615]   0.12864097   0.1266890   0.12744112 11901.5    0.95
## theta[616]   0.27371674   0.2731270   0.27294258 11001.0    0.95
## theta[617]   0.24573158   0.2456060   0.24319467 11001.0    0.95
## theta[618]   0.13310113   0.1302850   0.12643885 11001.0    0.95
## theta[619]   0.27841755   0.2782420   0.27878071 11001.0    0.95
## theta[620]   0.28765184   0.2871490   0.28608577 11001.0    0.95
## theta[621]   0.24183951   0.2417060   0.24355689 11001.0    0.95
## theta[622]   0.22319924   0.2230380   0.22244268 11001.0    0.95
## theta[623]   0.23154112   0.2312570   0.22931567 11001.0    0.95
## theta[624]   0.12617622   0.1244200   0.12486576 11001.0    0.95
## theta[625]   0.24685377   0.2467470   0.24593075 11050.8    0.95
## theta[626]   0.25522393   0.2545460   0.25654243 11001.0    0.95
## theta[627]   0.23299263   0.2329680   0.23728312 11304.3    0.95
## theta[628]   0.24830847   0.2476030   0.24564863 10553.5    0.95
## theta[629]   0.25889907   0.2581450   0.25512605 11001.0    0.95
## theta[630]   0.28505311   0.2848850   0.28606050 10696.0    0.95
## theta[631]   0.12712788   0.1248100   0.12022146 11001.0    0.95
## theta[632]   0.21445633   0.2138970   0.21396240 11001.0    0.95
## theta[633]   0.16216208   0.1594200   0.15054913  9702.5    0.95
## theta[634]   0.25478100   0.2546160   0.25771044 11001.0    0.95
## theta[635]   0.26065022   0.2598590   0.25929902 11293.5    0.95
## theta[636]   0.23850240   0.2385240   0.23914855 11001.0    0.95
## theta[637]   0.25084798   0.2504710   0.24889168 11001.0    0.95
## theta[638]   0.24916786   0.2483440   0.24169693 11001.0    0.95
## theta[639]   0.14535932   0.1424110   0.13109556 11001.0    0.95
## theta[640]   0.14107155   0.1395110   0.13434747 11001.0    0.95
## theta[641]   0.11990809   0.1184880   0.11629503 11001.0    0.95
## theta[642]   0.21872619   0.2182460   0.21425301 10619.6    0.95
## theta[643]   0.13174681   0.1294190   0.12167235 11001.0    0.95
## theta[644]   0.26126633   0.2605480   0.25978309 11001.0    0.95
## theta[645]   0.13825288   0.1351470   0.13026321 11001.0    0.95
## theta[646]   0.12042751   0.1187660   0.11925772  9551.7    0.95
## theta[647]   0.22890904   0.2284670   0.22742547 11001.0    0.95
## theta[648]   0.25513105   0.2546260   0.25082931 11001.0    0.95
## theta[649]   0.24097633   0.2409580   0.24154428 11387.3    0.95
## theta[650]   0.26304744   0.2622860   0.26121838 11001.0    0.95
## theta[651]   0.26315035   0.2623040   0.26094426 10590.6    0.95
## theta[652]   0.29121889   0.2909740   0.29240522 11001.0    0.95
## theta[653]   0.12878497   0.1261450   0.12172046 11001.0    0.95
## theta[654]   0.12780980   0.1257830   0.12274257 11001.0    0.95
## theta[655]   0.12502726   0.1231180   0.12150719 11001.0    0.95
## theta[656]   0.25563700   0.2551080   0.25227075 11475.6    0.95
## theta[657]   0.12757918   0.1250920   0.12239041 10888.5    0.95
## theta[658]   0.29389122   0.2934830   0.29306868 11001.0    0.95
## theta[659]   0.12924697   0.1266340   0.12534176 11001.0    0.95
## theta[660]   0.27959033   0.2795070   0.27895764 11001.0    0.95
## theta[661]   0.24831399   0.2485500   0.25077737 11001.0    0.95
## theta[662]   0.12306237   0.1209970   0.11411082 11001.0    0.95
## theta[663]   0.24257846   0.2423460   0.24345985 11412.9    0.95
## theta[664]   0.26712665   0.2668930   0.26459887 11001.0    0.95
## theta[665]   0.12875029   0.1263650   0.12494658 11117.3    0.95
## theta[666]   0.23511885   0.2351800   0.23569354 11001.0    0.95
## theta[667]   0.24600253   0.2457280   0.24317000 11001.0    0.95
## theta[668]   0.26961933   0.2687140   0.26973006 11001.0    0.95
## theta[669]   0.24409956   0.2431670   0.23845939 11001.0    0.95
## theta[670]   0.12755439   0.1251510   0.12140232 10973.8    0.95
## theta[671]   0.24594116   0.2453230   0.24173711 11001.0    0.95
## theta[672]   0.12364646   0.1213510   0.11784962 10355.8    0.95
## theta[673]   0.27748508   0.2772740   0.27883408 11093.5    0.95
## theta[674]   0.24967125   0.2498740   0.25012446 11001.0    0.95
## theta[675]   0.24606684   0.2455410   0.23816630 11001.0    0.95
## theta[676]   0.13271591   0.1300680   0.12738884 11001.0    0.95
## theta[677]   0.23788644   0.2370560   0.23492620 11001.0    0.95
## theta[678]   0.21552250   0.2150350   0.21255532 11001.0    0.95
## theta[679]   0.24929062   0.2483980   0.24410517 11001.0    0.95
## theta[680]   0.25595036   0.2558470   0.25443718 11001.0    0.95
## theta[681]   0.22752530   0.2272400   0.22612668 10761.2    0.95
## theta[682]   0.12923074   0.1267290   0.12339499 11001.0    0.95
## theta[683]   0.24263442   0.2422020   0.23982733 10280.9    0.95
## theta[684]   0.12586998   0.1237420   0.12351133 11085.2    0.95
## theta[685]   0.25750660   0.2571440   0.25550791 11001.0    0.95
## theta[686]   0.25264544   0.2521720   0.25115585 11001.0    0.95
## theta[687]   0.12881355   0.1266460   0.12919213 10317.9    0.95
## theta[688]   0.27862622   0.2781690   0.27910449  9953.2    0.95
## theta[689]   0.22137140   0.2212910   0.22333230 11001.0    0.95
## theta[690]   0.12910973   0.1264320   0.12352605 11001.0    0.95
## theta[691]   0.13505575   0.1322640   0.12936356 11001.0    0.95
## theta[692]   0.24587952   0.2455970   0.24586368 11001.0    0.95
## theta[693]   0.27228324   0.2719420   0.27113678 10692.0    0.95
## theta[694]   0.26053318   0.2598090   0.25801045 11001.0    0.95
## theta[695]   0.28754273   0.2871380   0.28315311 11001.0    0.95
## theta[696]   0.26800044   0.2675230   0.26459691 11001.0    0.95
## theta[697]   0.24005646   0.2398220   0.24192662 11001.0    0.95
## theta[698]   0.23716132   0.2364110   0.23610029 11001.0    0.95
## theta[699]   0.24231795   0.2422570   0.24042550 11001.0    0.95
## theta[700]   0.27583454   0.2753460   0.27419015 11001.0    0.95
## theta[701]   0.25758772   0.2572770   0.25803735 11001.0    0.95
## theta[702]   0.25551614   0.2548480   0.25154106 11001.0    0.95
## theta[703]   0.15151650   0.1485420   0.14096248 10349.7    0.95
## theta[704]   0.12694116   0.1241370   0.11831025 11001.0    0.95
## theta[705]   0.31262791   0.3122580   0.31203638 11001.0    0.95
## theta[706]   0.28868543   0.2883940   0.28541788 11001.0    0.95
## theta[707]   0.24234027   0.2421610   0.24042753 11001.0    0.95
## theta[708]   0.12463249   0.1220790   0.11783829  9964.6    0.95
## theta[709]   0.26080708   0.2600700   0.26109455 10799.1    0.95
## theta[710]   0.24571174   0.2453440   0.24174342 11001.0    0.95
## theta[711]   0.27641275   0.2762790   0.27977205 10699.3    0.95
## theta[712]   0.24050654   0.2399350   0.23766141 11078.1    0.95
## theta[713]   0.25652251   0.2563550   0.25563048 10784.1    0.95
## theta[714]   0.12625510   0.1243720   0.12172242 10645.4    0.95
## theta[715]   0.24750278   0.2470740   0.24485472 11001.0    0.95
## theta[716]   0.23728766   0.2369550   0.24066755 11001.0    0.95
## theta[717]   0.23767850   0.2367010   0.23528669 11001.0    0.95
## theta[718]   0.21433493   0.2145270   0.21732768 11001.0    0.95
## theta[719]   0.13650749   0.1337180   0.12726123 10897.1    0.95
## theta[720]   0.26025678   0.2600500   0.25872999 11001.0    0.95
## theta[721]   0.28881973   0.2884380   0.28715717 11001.0    0.95
## theta[722]   0.12857550   0.1262400   0.12382507 10619.7    0.95
## theta[723]   0.12583966   0.1237150   0.12508065 11140.6    0.95
## theta[724]   0.25715653   0.2571100   0.25776260 11001.0    0.95
## theta[725]   0.12917703   0.1266080   0.11865364 11001.0    0.95
## theta[726]   0.24853121   0.2476310   0.24550283 11001.0    0.95
## theta[727]   0.23412399   0.2338760   0.23449211 11360.9    0.95
## theta[728]   0.23354753   0.2333790   0.23446848 11001.0    0.95
## theta[729]   0.21786779   0.2172960   0.21724352 11001.0    0.95
## theta[730]   0.24932359   0.2492720   0.25140483 11001.0    0.95
## theta[731]   0.12899650   0.1268600   0.11821488 11001.0    0.95
## theta[732]   0.26650177   0.2653600   0.26067262 11001.0    0.95
## theta[733]   0.25117221   0.2509750   0.25383588 11001.0    0.95
## theta[734]   0.12572998   0.1237540   0.12287491 11001.0    0.95
## theta[735]   0.27953396   0.2791970   0.27938530 11001.0    0.95
## theta[736]   0.27666359   0.2764830   0.27593867 11001.0    0.95
## theta[737]   0.23213347   0.2319820   0.22837268 11381.5    0.95
## theta[738]   0.12594513   0.1238800   0.12283026 11857.1    0.95
## theta[739]   0.24257312   0.2423870   0.23874327 11001.0    0.95
## theta[740]   0.11237230   0.1112910   0.11152352 11001.0    0.95
## theta[741]   0.12683288   0.1243830   0.12047923 10427.4    0.95
## theta[742]   0.28733874   0.2871800   0.28789713 11001.0    0.95
## theta[743]   0.24831884   0.2481260   0.24883448 11369.9    0.95
## theta[744]   0.27166047   0.2713590   0.27552175 11001.0    0.95
## theta[745]   0.23571601   0.2354540   0.23291000 11001.0    0.95
## theta[746]   0.24192830   0.2419290   0.24308186 11001.0    0.95
## theta[747]   0.25663764   0.2565650   0.25523522 11001.0    0.95
## theta[748]   0.23863737   0.2383270   0.23591550 10633.5    0.95
## theta[749]   0.26771891   0.2673190   0.26684920 11001.0    0.95
## theta[750]   0.13938416   0.1364610   0.12908527 11001.0    0.95
## theta[751]   0.23918568   0.2377880   0.23314123 11001.0    0.95
## theta[752]   0.12595531   0.1238170   0.11900687 11001.0    0.95
## theta[753]   0.23060002   0.2306060   0.22918125 10689.3    0.95
## theta[754]   0.10553660   0.1042710   0.09636354  9109.2    0.95
## theta[755]   0.12513792   0.1233000   0.11892890 11001.0    0.95
## theta[756]   0.13639616   0.1334100   0.12781991 11256.7    0.95
## theta[757]   0.14085851   0.1379920   0.13463268 11397.6    0.95
## theta[758]   0.25005768   0.2497650   0.24932474 11001.0    0.95
## theta[759]   0.24984521   0.2494930   0.24747204 11001.0    0.95
## theta[760]   0.13375016   0.1311940   0.13101442 10647.8    0.95
## theta[761]   0.26124781   0.2600200   0.25703511 10301.2    0.95
## theta[762]   0.24227409   0.2421220   0.24325679 11001.0    0.95
## theta[763]   0.26148920   0.2611620   0.26321671 12309.7    0.95
## theta[764]   0.13657759   0.1339110   0.12680634 11001.0    0.95
## theta[765]   0.26384253   0.2636710   0.26217081 11001.0    0.95
## theta[766]   0.24808371   0.2481420   0.25092659 11001.0    0.95
## theta[767]   0.12822855   0.1253130   0.12002607 11001.0    0.95
## theta[768]   0.12727553   0.1250050   0.12313902 10665.8    0.95
## theta[769]   0.22758608   0.2273150   0.22165130 10665.1    0.95
## theta[770]   0.26303018   0.2619460   0.25674802 11001.0    0.95
## theta[771]   0.28857694   0.2882550   0.28942876 11001.0    0.95
## theta[772]   0.29888410   0.2983410   0.29879673 10913.8    0.95
## theta[773]   0.13438127   0.1319580   0.12980440 11001.0    0.95
## theta[774]   0.12909861   0.1269100   0.12111457 11462.5    0.95
## theta[775]   0.26270774   0.2621780   0.26177013 11001.0    0.95
## theta[776]   0.21424670   0.2143230   0.21531678 11001.0    0.95
## theta[777]   0.12374642   0.1212870   0.11285152 11001.0    0.95
## theta[778]   0.12927022   0.1267310   0.11826716 11001.0    0.95
## theta[779]   0.12801976   0.1252050   0.12110698 11001.0    0.95
## theta[780]   0.22728064   0.2270610   0.22982464 11001.0    0.95
## theta[781]   0.11993282   0.1190780   0.11825655 11001.0    0.95
## theta[782]   0.12915516   0.1271670   0.12040381 11001.0    0.95
## theta[783]   0.12301572   0.1212550   0.12001132  9013.6    0.95
## theta[784]   0.23308247   0.2326320   0.23051997 11001.0    0.95
## theta[785]   0.26314835   0.2626120   0.26040163 11001.0    0.95
## theta[786]   0.12415539   0.1222590   0.12124212 11001.0    0.95
## theta[787]   0.27415451   0.2737170   0.27506506 11001.0    0.95
## theta[788]   0.24327184   0.2431210   0.24824620 11001.0    0.95
## theta[789]   0.24956379   0.2492750   0.24582851 11001.0    0.95
## theta[790]   0.12517097   0.1227790   0.11617848 11001.0    0.95
## theta[791]   0.11829962   0.1170610   0.11609647 11001.0    0.95
## theta[792]   0.22935638   0.2292970   0.23121816 11001.0    0.95
## theta[793]   0.23455534   0.2337690   0.23264936 11001.0    0.95
## theta[794]   0.26268826   0.2623100   0.26308586 11451.5    0.95
## theta[795]   0.11882504   0.1170550   0.11583428 11001.0    0.95
## theta[796]   0.25054584   0.2504150   0.25030586 11001.0    0.95
## theta[797]   0.12697658   0.1245780   0.11809332 11001.0    0.95
## theta[798]   0.12897167   0.1264980   0.12018212 11001.0    0.95
## theta[799]   0.23141196   0.2314290   0.23209770 10973.3    0.95
## theta[800]   0.26299655   0.2624930   0.26218712 11001.0    0.95
## theta[801]   0.12797959   0.1253670   0.12263000 11001.0    0.95
## theta[802]   0.25906251   0.2591140   0.25986647 11001.0    0.95
## theta[803]   0.23005211   0.2295450   0.23194852 11503.3    0.95
## theta[804]   0.23518992   0.2347830   0.23487674 11001.0    0.95
## theta[805]   0.26370366   0.2631740   0.26084431 11001.0    0.95
## theta[806]   0.13574989   0.1329780   0.12804039 11001.0    0.95
## theta[807]   0.23983713   0.2399530   0.24285456 10009.9    0.95
## theta[808]   0.29029236   0.2897990   0.28716433 11001.0    0.95
## theta[809]   0.25880639   0.2585780   0.25731931 10963.4    0.95
## theta[810]   0.25279484   0.2520790   0.25057241 11232.2    0.95
## theta[811]   0.24119211   0.2407190   0.23847148 11001.0    0.95
## theta[812]   0.11459069   0.1130690   0.11226277 10361.2    0.95
## theta[813]   0.13316596   0.1301870   0.12439575 11001.0    0.95
## theta[814]   0.23633095   0.2361290   0.23847262 11001.0    0.95
## theta[815]   0.25002959   0.2499280   0.24950871 10069.3    0.95
## theta[816]   0.26607892   0.2652170   0.26056110 11001.0    0.95
## theta[817]   0.12478203   0.1224200   0.11973525 11001.0    0.95
## theta[818]   0.12089797   0.1191100   0.11962851 11227.3    0.95
## theta[819]   0.24428532   0.2438580   0.23898388 10219.4    0.95
## theta[820]   0.22938988   0.2291490   0.22812608 10698.5    0.95
## theta[821]   0.12845383   0.1263660   0.12545054 11001.0    0.95
## theta[822]   0.25721030   0.2572300   0.25927449 11001.0    0.95
## theta[823]   0.25924294   0.2587440   0.25984540 11001.0    0.95
## theta[824]   0.20390627   0.2035460   0.20225257 11001.0    0.95
## theta[825]   0.22341360   0.2237100   0.22885785 11001.0    0.95
## theta[826]   0.27434160   0.2738320   0.27194612 11001.0    0.95
## theta[827]   0.25349485   0.2524530   0.25237574 11001.0    0.95
## theta[828]   0.23447769   0.2341920   0.23788358 11001.0    0.95
## theta[829]   0.25929018   0.2590890   0.25661047 11001.0    0.95
## theta[830]   0.21233082   0.2120120   0.21022150 11001.0    0.95
## theta[831]   0.27542130   0.2749420   0.27320084 11359.8    0.95
## theta[832]   0.24581906   0.2454620   0.25060457 11001.0    0.95
## theta[833]   0.12197304   0.1198610   0.11280610 11363.2    0.95
## theta[834]   0.27592185   0.2755830   0.27752338 11001.0    0.95
## theta[835]   0.12905605   0.1264610   0.12269423 12647.6    0.95
## theta[836]   0.24096971   0.2405600   0.24191273 11001.0    0.95
## theta[837]   0.23163268   0.2311970   0.22560265 11001.0    0.95
## theta[838]   0.12858118   0.1260240   0.12376364 11001.0    0.95
## theta[839]   0.17875310   0.1753800   0.16603003  8645.5    0.95
## theta[840]   0.22761676   0.2273660   0.22638068 11895.8    0.95
## theta[841]   0.15770212   0.1549080   0.14856433 10259.3    0.95
## theta[842]   0.12727946   0.1251140   0.12074554 11001.0    0.95
## theta[843]   0.25227674   0.2517400   0.25141684 11711.6    0.95
## theta[844]   0.27429846   0.2741870   0.27489252 11001.0    0.95
## theta[845]   0.23669665   0.2362840   0.23604881 10465.4    0.95
## theta[846]   0.12801454   0.1254420   0.12315032 11001.0    0.95
## theta[847]   0.26010803   0.2598840   0.25952029 11001.0    0.95
## theta[848]   0.26717333   0.2670660   0.26870244 11001.0    0.95
## theta[849]   0.25250867   0.2523650   0.25103720 11001.0    0.95
## theta[850]   0.25407153   0.2539720   0.25113249 11305.2    0.95
## theta[851]   0.21928443   0.2192180   0.22196376 10589.3    0.95
## theta[852]   0.12919662   0.1261690   0.11896699 10630.5    0.95
## theta[853]   0.25169628   0.2514640   0.25077277 11001.0    0.95
## theta[854]   0.27535892   0.2751950   0.27515441 11001.0    0.95
## theta[855]   0.24265484   0.2421530   0.24026106 10446.3    0.95
## theta[856]   0.24071663   0.2402670   0.23892657 10641.1    0.95
## theta[857]   0.26190630   0.2616780   0.26289019 11001.0    0.95
## theta[858]   0.23620064   0.2358720   0.23383178 11001.0    0.95
## theta[859]   0.25205807   0.2516890   0.24928790 11001.0    0.95
## theta[860]   0.25306220   0.2528140   0.25440360 11348.0    0.95
## theta[861]   0.13362565   0.1307870   0.12726865 11392.6    0.95
## theta[862]   0.23808669   0.2381930   0.23807447 11001.0    0.95
## theta[863]   0.14450100   0.1414040   0.13197567 10466.4    0.95
## theta[864]   0.23267125   0.2322060   0.23329907 11436.1    0.95
## theta[865]   0.24084119   0.2406900   0.24001628 11001.0    0.95
## theta[866]   0.13797957   0.1355860   0.13178811 11351.7    0.95
## theta[867]   0.25785260   0.2571980   0.25306169 11001.0    0.95
## theta[868]   0.21470234   0.2147060   0.21766386 10287.0    0.95
## theta[869]   0.24704957   0.2468220   0.24785022 11001.0    0.95
## theta[870]   0.30586881   0.3053970   0.30400555 11001.0    0.95
## theta[871]   0.26348025   0.2630100   0.26092210 11001.0    0.95
## theta[872]   0.26617823   0.2653890   0.25993582 11431.7    0.95
## theta[873]   0.11311568   0.1117900   0.11125175 10323.1    0.95
## theta[874]   0.25815576   0.2578650   0.25543008 11001.0    0.95
## theta[875]   0.22988243   0.2296980   0.23025919 11001.0    0.95
## theta[876]   0.24969368   0.2496200   0.24958947 10699.1    0.95
## theta[877]   0.27189404   0.2717260   0.27146209 11001.0    0.95
## theta[878]   0.22849189   0.2284450   0.22990938 11001.0    0.95
## theta[879]   0.25372221   0.2534250   0.25102528 11001.0    0.95
## theta[880]   0.23576497   0.2353820   0.23256731 10318.9    0.95
## theta[881]   0.24679256   0.2464690   0.24849390 11001.0    0.95
## theta[882]   0.22945511   0.2290420   0.22707414 11001.0    0.95
## theta[883]   0.22720066   0.2270480   0.22618999 10595.8    0.95
## theta[884]   0.24827480   0.2482950   0.24743262 11001.0    0.95
## theta[885]   0.14386676   0.1403630   0.13438312 11001.0    0.95
## theta[886]   0.25199601   0.2519320   0.25655251 11001.0    0.95
## theta[887]   0.26223460   0.2620480   0.26066558 11001.0    0.95
## theta[888]   0.12498158   0.1230650   0.12037654 10243.0    0.95
## theta[889]   0.25435744   0.2540690   0.25203166 10198.0    0.95
## theta[890]   0.25630987   0.2560990   0.25822685 10287.1    0.95
## theta[891]   0.13769432   0.1349070   0.13262945 11001.0    0.95
## theta[892]   0.12961103   0.1269250   0.12159709 10611.4    0.95
## theta[893]   0.21249489   0.2126640   0.21322887 11001.0    0.95
## theta[894]   0.24464435   0.2443630   0.24428349 11001.0    0.95
## theta[895]   0.11690438   0.1156230   0.11553798 11001.0    0.95
## theta[896]   0.20798556   0.2076350   0.20533788 11001.0    0.95
## theta[897]   0.10863643   0.1075260   0.10712780  9456.7    0.95
## theta[898]   0.14154181   0.1390320   0.13267091 11001.0    0.95
## theta[899]   0.30495537   0.3044520   0.30453243 11001.0    0.95
## theta[900]   0.12663634   0.1252360   0.12288584 11001.0    0.95
## theta[901]   0.26952588   0.2692530   0.26872667 11286.9    0.95
## theta[902]   0.25362353   0.2532850   0.25339186 11753.5    0.95
## theta[903]   0.13298285   0.1307600   0.12893659 11001.0    0.95
## theta[904]   0.12902849   0.1263440   0.12618088 11001.0    0.95
## theta[905]   0.13797572   0.1349760   0.13248668 11001.0    0.95
## theta[906]   0.12941238   0.1265920   0.12006721 11001.0    0.95
## theta[907]   0.23139010   0.2311890   0.22597563 11553.7    0.95
## theta[908]   0.23578609   0.2355870   0.23550514 11001.0    0.95
## theta[909]   0.12345098   0.1211900   0.11879950 11001.0    0.95
## theta[910]   0.23808164   0.2375760   0.23510165 11001.0    0.95
## theta[911]   0.12465114   0.1232220   0.12491596 12632.0    0.95
## theta[912]   0.13548242   0.1325020   0.12610717  9656.3    0.95
## theta[913]   0.24014164   0.2396500   0.23752859 10214.3    0.95
## theta[914]   0.12506457   0.1230910   0.12203932  9788.1    0.95
## theta[915]   0.27788264   0.2772340   0.27491487 11641.1    0.95
## theta[916]   0.12754664   0.1263930   0.12615095 10564.5    0.95
## theta[917]   0.25105315   0.2506230   0.24759694 11870.1    0.95
## theta[918]   0.14189288   0.1395270   0.13381819 11001.0    0.95
## theta[919]   0.23053657   0.2302870   0.23169767 11001.0    0.95
## theta[920]   0.13928556   0.1365160   0.13564390 11001.0    0.95
## theta[921]   0.24704294   0.2468910   0.24732871 11001.0    0.95
## theta[922]   0.24329779   0.2430380   0.24088634 11001.0    0.95
## theta[923]   0.13510064   0.1324020   0.12243578 11001.0    0.95
## theta[924]   0.25754718   0.2574100   0.25465465 11001.0    0.95
## theta[925]   0.22505094   0.2250090   0.22517310 11001.0    0.95
## theta[926]   0.12370455   0.1217240   0.11820357 10568.3    0.95
## theta[927]   0.22997958   0.2302230   0.23251974 10146.7    0.95
## theta[928]   0.25516589   0.2548010   0.25455149 11001.0    0.95
## theta[929]   0.13587478   0.1333390   0.12992637 10636.0    0.95
## theta[930]   0.12907236   0.1267920   0.13002211 10300.4    0.95
## theta[931]   0.15093778   0.1487320   0.14428738 10327.9    0.95
## theta[932]   0.11656853   0.1150940   0.11254639 10333.5    0.95
## theta[933]   0.24272058   0.2425300   0.24076468 11001.0    0.95
## theta[934]   0.29368126   0.2935430   0.29326500 11001.0    0.95
## theta[935]   0.12781495   0.1257490   0.12504650 11001.0    0.95
## theta[936]   0.12802068   0.1257640   0.12239298 11309.4    0.95
## theta[937]   0.24221738   0.2421150   0.24425441 11001.0    0.95
## theta[938]   0.24224326   0.2419580   0.23793723 11001.0    0.95
## theta[939]   0.26225113   0.2620370   0.26068792 11678.3    0.95
## theta[940]   0.28373479   0.2832750   0.28305920 10502.0    0.95
## theta[941]   0.24394953   0.2436120   0.24398754 11663.2    0.95
## theta[942]   0.27054541   0.2704760   0.27117618 11001.0    0.95
## theta[943]   0.14233874   0.1398500   0.13700886 11001.0    0.95
## theta[944]   0.13485981   0.1323610   0.12892028 11001.0    0.95
## theta[945]   0.27584416   0.2755080   0.27496154 11001.0    0.95
## theta[946]   0.15423757   0.1520430   0.14505084 10113.7    0.95
## theta[947]   0.11102572   0.1096300   0.10529834 10203.4    0.95
## theta[948]   0.26565552   0.2652500   0.26324140 11001.0    0.95
##                 HDIlow    HDIhigh CompVal PcntGtCompVal ROPElow ROPEhigh
## omega[1]     0.2423080   0.261246      NA            NA      NA       NA
## omega[2]     0.2377870   0.258159      NA            NA      NA       NA
## omega[3]     0.2443620   0.265510      NA            NA      NA       NA
## omega[4]     0.2268700   0.247580      NA            NA      NA       NA
## omega[5]     0.2453520   0.266944      NA            NA      NA       NA
## omega[6]     0.2388480   0.258123      NA            NA      NA       NA
## omega[7]     0.1098860   0.135106      NA            NA      NA       NA
## omega[8]     0.2496260   0.268180      NA            NA      NA       NA
## omega[9]     0.2357170   0.257678      NA            NA      NA       NA
## omegaO       0.1810690   0.271535      NA            NA      NA       NA
## kappa[1]   124.3330000 379.799000      NA            NA      NA       NA
## kappa[2]   119.9240000 421.676000      NA            NA      NA       NA
## kappa[3]   103.1610000 350.970000      NA            NA      NA       NA
## kappa[4]    89.7164000 264.895000      NA            NA      NA       NA
## kappa[5]   108.2750000 392.293000      NA            NA      NA       NA
## kappa[6]   108.6320000 343.097000      NA            NA      NA       NA
## kappa[7]    41.2895000 201.359000      NA            NA      NA       NA
## kappa[8]   186.3780000 685.258000      NA            NA      NA       NA
## kappa[9]   104.4390000 413.015000      NA            NA      NA       NA
## kappaO       8.8675200 123.316000      NA            NA      NA       NA
## theta[1]     0.0664946   0.197509      NA            NA      NA       NA
## theta[2]     0.2071220   0.288456      NA            NA      NA       NA
## theta[3]     0.2045370   0.302292      NA            NA      NA       NA
## theta[4]     0.2045760   0.261218      NA            NA      NA       NA
## theta[5]     0.2048040   0.301727      NA            NA      NA       NA
## theta[6]     0.0621954   0.198042      NA            NA      NA       NA
## theta[7]     0.0667393   0.202708      NA            NA      NA       NA
## theta[8]     0.1863930   0.297988      NA            NA      NA       NA
## theta[9]     0.2362390   0.298938      NA            NA      NA       NA
## theta[10]    0.2490290   0.310152      NA            NA      NA       NA
## theta[11]    0.0658133   0.201342      NA            NA      NA       NA
## theta[12]    0.2164020   0.279161      NA            NA      NA       NA
## theta[13]    0.2080700   0.282640      NA            NA      NA       NA
## theta[14]    0.1795810   0.308793      NA            NA      NA       NA
## theta[15]    0.1862140   0.304098      NA            NA      NA       NA
## theta[16]    0.1945960   0.260920      NA            NA      NA       NA
## theta[17]    0.2462010   0.306244      NA            NA      NA       NA
## theta[18]    0.2037650   0.291100      NA            NA      NA       NA
## theta[19]    0.2484230   0.307641      NA            NA      NA       NA
## theta[20]    0.0617183   0.194971      NA            NA      NA       NA
## theta[21]    0.1996560   0.273457      NA            NA      NA       NA
## theta[22]    0.2271720   0.303294      NA            NA      NA       NA
## theta[23]    0.0613392   0.194044      NA            NA      NA       NA
## theta[24]    0.0838580   0.188913      NA            NA      NA       NA
## theta[25]    0.1959790   0.292852      NA            NA      NA       NA
## theta[26]    0.2039080   0.276671      NA            NA      NA       NA
## theta[27]    0.0687292   0.207893      NA            NA      NA       NA
## theta[28]    0.2183560   0.280604      NA            NA      NA       NA
## theta[29]    0.0639993   0.198029      NA            NA      NA       NA
## theta[30]    0.2453940   0.309071      NA            NA      NA       NA
## theta[31]    0.0646467   0.202692      NA            NA      NA       NA
## theta[32]    0.0737257   0.173725      NA            NA      NA       NA
## theta[33]    0.2158310   0.289134      NA            NA      NA       NA
## theta[34]    0.1948290   0.283474      NA            NA      NA       NA
## theta[35]    0.0643456   0.199061      NA            NA      NA       NA
## theta[36]    0.1809070   0.254677      NA            NA      NA       NA
## theta[37]    0.0594750   0.194654      NA            NA      NA       NA
## theta[38]    0.2038990   0.267383      NA            NA      NA       NA
## theta[39]    0.1959140   0.291312      NA            NA      NA       NA
## theta[40]    0.2234180   0.283566      NA            NA      NA       NA
## theta[41]    0.1667510   0.265174      NA            NA      NA       NA
## theta[42]    0.1924620   0.284287      NA            NA      NA       NA
## theta[43]    0.0728837   0.189832      NA            NA      NA       NA
## theta[44]    0.0588296   0.187518      NA            NA      NA       NA
## theta[45]    0.0598170   0.190518      NA            NA      NA       NA
## theta[46]    0.2193690   0.283512      NA            NA      NA       NA
## theta[47]    0.2240700   0.297333      NA            NA      NA       NA
## theta[48]    0.1686130   0.251440      NA            NA      NA       NA
## theta[49]    0.0810906   0.207317      NA            NA      NA       NA
## theta[50]    0.0646926   0.199199      NA            NA      NA       NA
## theta[51]    0.0555429   0.172410      NA            NA      NA       NA
## theta[52]    0.2092820   0.270128      NA            NA      NA       NA
## theta[53]    0.0681314   0.180591      NA            NA      NA       NA
## theta[54]    0.0615621   0.192534      NA            NA      NA       NA
## theta[55]    0.1846710   0.294138      NA            NA      NA       NA
## theta[56]    0.0619912   0.198340      NA            NA      NA       NA
## theta[57]    0.2334540   0.302366      NA            NA      NA       NA
## theta[58]    0.2375150   0.294652      NA            NA      NA       NA
## theta[59]    0.2726770   0.337613      NA            NA      NA       NA
## theta[60]    0.0636570   0.198717      NA            NA      NA       NA
## theta[61]    0.0616596   0.193852      NA            NA      NA       NA
## theta[62]    0.2069680   0.304779      NA            NA      NA       NA
## theta[63]    0.2285560   0.313159      NA            NA      NA       NA
## theta[64]    0.2185620   0.293875      NA            NA      NA       NA
## theta[65]    0.2007670   0.279517      NA            NA      NA       NA
## theta[66]    0.2224950   0.295100      NA            NA      NA       NA
## theta[67]    0.1836590   0.283772      NA            NA      NA       NA
## theta[68]    0.0930996   0.210923      NA            NA      NA       NA
## theta[69]    0.2062350   0.288680      NA            NA      NA       NA
## theta[70]    0.0629595   0.196307      NA            NA      NA       NA
## theta[71]    0.0628746   0.198461      NA            NA      NA       NA
## theta[72]    0.2264440   0.306132      NA            NA      NA       NA
## theta[73]    0.2210440   0.282943      NA            NA      NA       NA
## theta[74]    0.1721920   0.283427      NA            NA      NA       NA
## theta[75]    0.1958640   0.311111      NA            NA      NA       NA
## theta[76]    0.0605240   0.161380      NA            NA      NA       NA
## theta[77]    0.2409330   0.317027      NA            NA      NA       NA
## theta[78]    0.1859830   0.300801      NA            NA      NA       NA
## theta[79]    0.2184330   0.277664      NA            NA      NA       NA
## theta[80]    0.2004580   0.264293      NA            NA      NA       NA
## theta[81]    0.2186930   0.299096      NA            NA      NA       NA
## theta[82]    0.1744010   0.303641      NA            NA      NA       NA
## theta[83]    0.2069260   0.309016      NA            NA      NA       NA
## theta[84]    0.1976500   0.284507      NA            NA      NA       NA
## theta[85]    0.2397960   0.298874      NA            NA      NA       NA
## theta[86]    0.0630182   0.196063      NA            NA      NA       NA
## theta[87]    0.0602336   0.197227      NA            NA      NA       NA
## theta[88]    0.2485480   0.311472      NA            NA      NA       NA
## theta[89]    0.2040630   0.309801      NA            NA      NA       NA
## theta[90]    0.2698200   0.334612      NA            NA      NA       NA
## theta[91]    0.0626989   0.194896      NA            NA      NA       NA
## theta[92]    0.1793950   0.289674      NA            NA      NA       NA
## theta[93]    0.0666891   0.199595      NA            NA      NA       NA
## theta[94]    0.2127070   0.292595      NA            NA      NA       NA
## theta[95]    0.1895150   0.302266      NA            NA      NA       NA
## theta[96]    0.2152720   0.287654      NA            NA      NA       NA
## theta[97]    0.0628564   0.197512      NA            NA      NA       NA
## theta[98]    0.2274200   0.283471      NA            NA      NA       NA
## theta[99]    0.0637550   0.195916      NA            NA      NA       NA
## theta[100]   0.1741930   0.244681      NA            NA      NA       NA
## theta[101]   0.1907430   0.294551      NA            NA      NA       NA
## theta[102]   0.0495437   0.141571      NA            NA      NA       NA
## theta[103]   0.0931067   0.199580      NA            NA      NA       NA
## theta[104]   0.0554726   0.151418      NA            NA      NA       NA
## theta[105]   0.0612269   0.197588      NA            NA      NA       NA
## theta[106]   0.1942150   0.279736      NA            NA      NA       NA
## theta[107]   0.1864060   0.306545      NA            NA      NA       NA
## theta[108]   0.1734660   0.273269      NA            NA      NA       NA
## theta[109]   0.2650210   0.327839      NA            NA      NA       NA
## theta[110]   0.1960800   0.284556      NA            NA      NA       NA
## theta[111]   0.2338390   0.295429      NA            NA      NA       NA
## theta[112]   0.0606496   0.195673      NA            NA      NA       NA
## theta[113]   0.2140450   0.281682      NA            NA      NA       NA
## theta[114]   0.2795580   0.353698      NA            NA      NA       NA
## theta[115]   0.2799470   0.344140      NA            NA      NA       NA
## theta[116]   0.0762739   0.175684      NA            NA      NA       NA
## theta[117]   0.2203680   0.303305      NA            NA      NA       NA
## theta[118]   0.0984535   0.207570      NA            NA      NA       NA
## theta[119]   0.1851140   0.270755      NA            NA      NA       NA
## theta[120]   0.2108610   0.300405      NA            NA      NA       NA
## theta[121]   0.2209180   0.287101      NA            NA      NA       NA
## theta[122]   0.2195200   0.306048      NA            NA      NA       NA
## theta[123]   0.2636710   0.326638      NA            NA      NA       NA
## theta[124]   0.2092840   0.310013      NA            NA      NA       NA
## theta[125]   0.0669438   0.169555      NA            NA      NA       NA
## theta[126]   0.1870230   0.285355      NA            NA      NA       NA
## theta[127]   0.1754390   0.301935      NA            NA      NA       NA
## theta[128]   0.1905680   0.279566      NA            NA      NA       NA
## theta[129]   0.0636815   0.197774      NA            NA      NA       NA
## theta[130]   0.0726216   0.210759      NA            NA      NA       NA
## theta[131]   0.2394950   0.316796      NA            NA      NA       NA
## theta[132]   0.0599298   0.194973      NA            NA      NA       NA
## theta[133]   0.2151560   0.306116      NA            NA      NA       NA
## theta[134]   0.1964180   0.312996      NA            NA      NA       NA
## theta[135]   0.2306010   0.294615      NA            NA      NA       NA
## theta[136]   0.1948400   0.297399      NA            NA      NA       NA
## theta[137]   0.2077770   0.287349      NA            NA      NA       NA
## theta[138]   0.0677526   0.198491      NA            NA      NA       NA
## theta[139]   0.2104140   0.283353      NA            NA      NA       NA
## theta[140]   0.0686551   0.204239      NA            NA      NA       NA
## theta[141]   0.1861810   0.300144      NA            NA      NA       NA
## theta[142]   0.2041640   0.295539      NA            NA      NA       NA
## theta[143]   0.2100710   0.292455      NA            NA      NA       NA
## theta[144]   0.2449950   0.304241      NA            NA      NA       NA
## theta[145]   0.0627273   0.196857      NA            NA      NA       NA
## theta[146]   0.2138730   0.297768      NA            NA      NA       NA
## theta[147]   0.0623588   0.198433      NA            NA      NA       NA
## theta[148]   0.1726020   0.306215      NA            NA      NA       NA
## theta[149]   0.2449510   0.311662      NA            NA      NA       NA
## theta[150]   0.0972565   0.236246      NA            NA      NA       NA
## theta[151]   0.1993680   0.302403      NA            NA      NA       NA
## theta[152]   0.0832074   0.217277      NA            NA      NA       NA
## theta[153]   0.1860010   0.274918      NA            NA      NA       NA
## theta[154]   0.2320610   0.312349      NA            NA      NA       NA
## theta[155]   0.0634682   0.197991      NA            NA      NA       NA
## theta[156]   0.0675129   0.202581      NA            NA      NA       NA
## theta[157]   0.0607178   0.194331      NA            NA      NA       NA
## theta[158]   0.2141940   0.307795      NA            NA      NA       NA
## theta[159]   0.2464890   0.302693      NA            NA      NA       NA
## theta[160]   0.1718490   0.277403      NA            NA      NA       NA
## theta[161]   0.0645219   0.199315      NA            NA      NA       NA
## theta[162]   0.2358500   0.318570      NA            NA      NA       NA
## theta[163]   0.0642462   0.199520      NA            NA      NA       NA
## theta[164]   0.1880200   0.298379      NA            NA      NA       NA
## theta[165]   0.0623692   0.196385      NA            NA      NA       NA
## theta[166]   0.1752990   0.262135      NA            NA      NA       NA
## theta[167]   0.0631220   0.189518      NA            NA      NA       NA
## theta[168]   0.1667310   0.264276      NA            NA      NA       NA
## theta[169]   0.0693087   0.210116      NA            NA      NA       NA
## theta[170]   0.0638931   0.196764      NA            NA      NA       NA
## theta[171]   0.0622887   0.185262      NA            NA      NA       NA
## theta[172]   0.0620199   0.192877      NA            NA      NA       NA
## theta[173]   0.0639807   0.199928      NA            NA      NA       NA
## theta[174]   0.2438040   0.307242      NA            NA      NA       NA
## theta[175]   0.1710160   0.296472      NA            NA      NA       NA
## theta[176]   0.1647460   0.264372      NA            NA      NA       NA
## theta[177]   0.1999060   0.303186      NA            NA      NA       NA
## theta[178]   0.2267100   0.318032      NA            NA      NA       NA
## theta[179]   0.0695728   0.183501      NA            NA      NA       NA
## theta[180]   0.0629977   0.199727      NA            NA      NA       NA
## theta[181]   0.1953780   0.305365      NA            NA      NA       NA
## theta[182]   0.0698209   0.176982      NA            NA      NA       NA
## theta[183]   0.1855780   0.294858      NA            NA      NA       NA
## theta[184]   0.1841680   0.282169      NA            NA      NA       NA
## theta[185]   0.2098620   0.308177      NA            NA      NA       NA
## theta[186]   0.2168430   0.276688      NA            NA      NA       NA
## theta[187]   0.2568800   0.324370      NA            NA      NA       NA
## theta[188]   0.2168050   0.281712      NA            NA      NA       NA
## theta[189]   0.2135420   0.311416      NA            NA      NA       NA
## theta[190]   0.2266770   0.291876      NA            NA      NA       NA
## theta[191]   0.2408140   0.321585      NA            NA      NA       NA
## theta[192]   0.2332140   0.288890      NA            NA      NA       NA
## theta[193]   0.0612566   0.197118      NA            NA      NA       NA
## theta[194]   0.1957860   0.295204      NA            NA      NA       NA
## theta[195]   0.2272860   0.291127      NA            NA      NA       NA
## theta[196]   0.0631026   0.162043      NA            NA      NA       NA
## theta[197]   0.1883340   0.295762      NA            NA      NA       NA
## theta[198]   0.1778930   0.274970      NA            NA      NA       NA
## theta[199]   0.1880480   0.299120      NA            NA      NA       NA
## theta[200]   0.1975050   0.279044      NA            NA      NA       NA
## theta[201]   0.0609241   0.192200      NA            NA      NA       NA
## theta[202]   0.1931120   0.296552      NA            NA      NA       NA
## theta[203]   0.1933430   0.309061      NA            NA      NA       NA
## theta[204]   0.0724498   0.209975      NA            NA      NA       NA
## theta[205]   0.2333520   0.297683      NA            NA      NA       NA
## theta[206]   0.2048480   0.266299      NA            NA      NA       NA
## theta[207]   0.2219540   0.289182      NA            NA      NA       NA
## theta[208]   0.0718651   0.210397      NA            NA      NA       NA
## theta[209]   0.2430720   0.307679      NA            NA      NA       NA
## theta[210]   0.1951130   0.297928      NA            NA      NA       NA
## theta[211]   0.0802069   0.220948      NA            NA      NA       NA
## theta[212]   0.2330240   0.290414      NA            NA      NA       NA
## theta[213]   0.0881738   0.218984      NA            NA      NA       NA
## theta[214]   0.0646113   0.177627      NA            NA      NA       NA
## theta[215]   0.2418730   0.307921      NA            NA      NA       NA
## theta[216]   0.1866560   0.287559      NA            NA      NA       NA
## theta[217]   0.2034060   0.269937      NA            NA      NA       NA
## theta[218]   0.2473840   0.312834      NA            NA      NA       NA
## theta[219]   0.0531636   0.155074      NA            NA      NA       NA
## theta[220]   0.1857450   0.289532      NA            NA      NA       NA
## theta[221]   0.0662273   0.195016      NA            NA      NA       NA
## theta[222]   0.1953310   0.306962      NA            NA      NA       NA
## theta[223]   0.1962020   0.290962      NA            NA      NA       NA
## theta[224]   0.1938070   0.313328      NA            NA      NA       NA
## theta[225]   0.0888253   0.192818      NA            NA      NA       NA
## theta[226]   0.0710666   0.211649      NA            NA      NA       NA
## theta[227]   0.2538590   0.333060      NA            NA      NA       NA
## theta[228]   0.2363870   0.312471      NA            NA      NA       NA
## theta[229]   0.2187920   0.317425      NA            NA      NA       NA
## theta[230]   0.1856940   0.280259      NA            NA      NA       NA
## theta[231]   0.2088410   0.285084      NA            NA      NA       NA
## theta[232]   0.0655371   0.201716      NA            NA      NA       NA
## theta[233]   0.2322270   0.300128      NA            NA      NA       NA
## theta[234]   0.1899950   0.272487      NA            NA      NA       NA
## theta[235]   0.2043030   0.276964      NA            NA      NA       NA
## theta[236]   0.0606637   0.194151      NA            NA      NA       NA
## theta[237]   0.1984940   0.271829      NA            NA      NA       NA
## theta[238]   0.2186470   0.280664      NA            NA      NA       NA
## theta[239]   0.0636598   0.199021      NA            NA      NA       NA
## theta[240]   0.0636246   0.198231      NA            NA      NA       NA
## theta[241]   0.1843560   0.263546      NA            NA      NA       NA
## theta[242]   0.1902390   0.250110      NA            NA      NA       NA
## theta[243]   0.0636781   0.199310      NA            NA      NA       NA
## theta[244]   0.2222240   0.298719      NA            NA      NA       NA
## theta[245]   0.2095130   0.307431      NA            NA      NA       NA
## theta[246]   0.0611470   0.197318      NA            NA      NA       NA
## theta[247]   0.2255910   0.296595      NA            NA      NA       NA
## theta[248]   0.2218530   0.289102      NA            NA      NA       NA
## theta[249]   0.2260780   0.302541      NA            NA      NA       NA
## theta[250]   0.1861280   0.285848      NA            NA      NA       NA
## theta[251]   0.2430620   0.305144      NA            NA      NA       NA
## theta[252]   0.0655220   0.178419      NA            NA      NA       NA
## theta[253]   0.2493980   0.311330      NA            NA      NA       NA
## theta[254]   0.1951850   0.288236      NA            NA      NA       NA
## theta[255]   0.2216450   0.282077      NA            NA      NA       NA
## theta[256]   0.2196800   0.277887      NA            NA      NA       NA
## theta[257]   0.0695064   0.177266      NA            NA      NA       NA
## theta[258]   0.2454070   0.303319      NA            NA      NA       NA
## theta[259]   0.1618260   0.288156      NA            NA      NA       NA
## theta[260]   0.2247660   0.324038      NA            NA      NA       NA
## theta[261]   0.0626547   0.198494      NA            NA      NA       NA
## theta[262]   0.1915160   0.301649      NA            NA      NA       NA
## theta[263]   0.1780650   0.309686      NA            NA      NA       NA
## theta[264]   0.0692714   0.205061      NA            NA      NA       NA
## theta[265]   0.0613122   0.194395      NA            NA      NA       NA
## theta[266]   0.1912850   0.303887      NA            NA      NA       NA
## theta[267]   0.2650450   0.329624      NA            NA      NA       NA
## theta[268]   0.0639734   0.177553      NA            NA      NA       NA
## theta[269]   0.0759322   0.212508      NA            NA      NA       NA
## theta[270]   0.1779480   0.264438      NA            NA      NA       NA
## theta[271]   0.0626117   0.195030      NA            NA      NA       NA
## theta[272]   0.1947790   0.279612      NA            NA      NA       NA
## theta[273]   0.1841490   0.261591      NA            NA      NA       NA
## theta[274]   0.1944270   0.282215      NA            NA      NA       NA
## theta[275]   0.1805090   0.275678      NA            NA      NA       NA
## theta[276]   0.0643991   0.201159      NA            NA      NA       NA
## theta[277]   0.2144920   0.309319      NA            NA      NA       NA
## theta[278]   0.1822730   0.284268      NA            NA      NA       NA
## theta[279]   0.2269850   0.300613      NA            NA      NA       NA
## theta[280]   0.2525400   0.320585      NA            NA      NA       NA
## theta[281]   0.0607058   0.171137      NA            NA      NA       NA
## theta[282]   0.2163480   0.289803      NA            NA      NA       NA
## theta[283]   0.2039840   0.291868      NA            NA      NA       NA
## theta[284]   0.2170600   0.272583      NA            NA      NA       NA
## theta[285]   0.2520400   0.343233      NA            NA      NA       NA
## theta[286]   0.2335460   0.303508      NA            NA      NA       NA
## theta[287]   0.2276890   0.288045      NA            NA      NA       NA
## theta[288]   0.2502620   0.316770      NA            NA      NA       NA
## theta[289]   0.0630420   0.178188      NA            NA      NA       NA
## theta[290]   0.1934080   0.312400      NA            NA      NA       NA
## theta[291]   0.1852940   0.293088      NA            NA      NA       NA
## theta[292]   0.2195960   0.301640      NA            NA      NA       NA
## theta[293]   0.2266570   0.290925      NA            NA      NA       NA
## theta[294]   0.0570731   0.183552      NA            NA      NA       NA
## theta[295]   0.0906660   0.198103      NA            NA      NA       NA
## theta[296]   0.1996990   0.281185      NA            NA      NA       NA
## theta[297]   0.2012200   0.302255      NA            NA      NA       NA
## theta[298]   0.2236010   0.310802      NA            NA      NA       NA
## theta[299]   0.0621924   0.198318      NA            NA      NA       NA
## theta[300]   0.1048510   0.232750      NA            NA      NA       NA
## theta[301]   0.2051450   0.319416      NA            NA      NA       NA
## theta[302]   0.0621646   0.172174      NA            NA      NA       NA
## theta[303]   0.0573627   0.184782      NA            NA      NA       NA
## theta[304]   0.0763031   0.195268      NA            NA      NA       NA
## theta[305]   0.2401570   0.322860      NA            NA      NA       NA
## theta[306]   0.0622108   0.183018      NA            NA      NA       NA
## theta[307]   0.2226510   0.305377      NA            NA      NA       NA
## theta[308]   0.1966120   0.292194      NA            NA      NA       NA
## theta[309]   0.2049240   0.287040      NA            NA      NA       NA
## theta[310]   0.1886280   0.307580      NA            NA      NA       NA
## theta[311]   0.1957790   0.301089      NA            NA      NA       NA
## theta[312]   0.1892880   0.320126      NA            NA      NA       NA
## theta[313]   0.2441410   0.309511      NA            NA      NA       NA
## theta[314]   0.0639988   0.198559      NA            NA      NA       NA
## theta[315]   0.2203580   0.296427      NA            NA      NA       NA
## theta[316]   0.1910640   0.283592      NA            NA      NA       NA
## theta[317]   0.2261840   0.294492      NA            NA      NA       NA
## theta[318]   0.0698448   0.207021      NA            NA      NA       NA
## theta[319]   0.2131790   0.307283      NA            NA      NA       NA
## theta[320]   0.2560320   0.317260      NA            NA      NA       NA
## theta[321]   0.1946870   0.300318      NA            NA      NA       NA
## theta[322]   0.2020080   0.302003      NA            NA      NA       NA
## theta[323]   0.2551470   0.321019      NA            NA      NA       NA
## theta[324]   0.0675101   0.197964      NA            NA      NA       NA
## theta[325]   0.0677426   0.167051      NA            NA      NA       NA
## theta[326]   0.2022550   0.283144      NA            NA      NA       NA
## theta[327]   0.2541120   0.314800      NA            NA      NA       NA
## theta[328]   0.2010250   0.272976      NA            NA      NA       NA
## theta[329]   0.0780650   0.215272      NA            NA      NA       NA
## theta[330]   0.2120250   0.286667      NA            NA      NA       NA
## theta[331]   0.2003110   0.317215      NA            NA      NA       NA
## theta[332]   0.2238490   0.317001      NA            NA      NA       NA
## theta[333]   0.2104550   0.268731      NA            NA      NA       NA
## theta[334]   0.1910950   0.309096      NA            NA      NA       NA
## theta[335]   0.1890910   0.280284      NA            NA      NA       NA
## theta[336]   0.2024050   0.274311      NA            NA      NA       NA
## theta[337]   0.0633659   0.197453      NA            NA      NA       NA
## theta[338]   0.1976520   0.311424      NA            NA      NA       NA
## theta[339]   0.0920399   0.217375      NA            NA      NA       NA
## theta[340]   0.0615151   0.177795      NA            NA      NA       NA
## theta[341]   0.2147390   0.302874      NA            NA      NA       NA
## theta[342]   0.1876100   0.307609      NA            NA      NA       NA
## theta[343]   0.2114800   0.286871      NA            NA      NA       NA
## theta[344]   0.2062110   0.282762      NA            NA      NA       NA
## theta[345]   0.0620617   0.195157      NA            NA      NA       NA
## theta[346]   0.1975930   0.298277      NA            NA      NA       NA
## theta[347]   0.2249870   0.307459      NA            NA      NA       NA
## theta[348]   0.2243680   0.294853      NA            NA      NA       NA
## theta[349]   0.1931080   0.309967      NA            NA      NA       NA
## theta[350]   0.0889839   0.201646      NA            NA      NA       NA
## theta[351]   0.1079110   0.224530      NA            NA      NA       NA
## theta[352]   0.2471850   0.309354      NA            NA      NA       NA
## theta[353]   0.0601557   0.191114      NA            NA      NA       NA
## theta[354]   0.0647975   0.201329      NA            NA      NA       NA
## theta[355]   0.0631146   0.197871      NA            NA      NA       NA
## theta[356]   0.2231900   0.301487      NA            NA      NA       NA
## theta[357]   0.2108780   0.287645      NA            NA      NA       NA
## theta[358]   0.0450315   0.143775      NA            NA      NA       NA
## theta[359]   0.0671005   0.179441      NA            NA      NA       NA
## theta[360]   0.0595533   0.159363      NA            NA      NA       NA
## theta[361]   0.2124720   0.268939      NA            NA      NA       NA
## theta[362]   0.0596548   0.192465      NA            NA      NA       NA
## theta[363]   0.2361970   0.298868      NA            NA      NA       NA
## theta[364]   0.0848122   0.192157      NA            NA      NA       NA
## theta[365]   0.1783980   0.279058      NA            NA      NA       NA
## theta[366]   0.2045360   0.280416      NA            NA      NA       NA
## theta[367]   0.0600048   0.189072      NA            NA      NA       NA
## theta[368]   0.2339850   0.295327      NA            NA      NA       NA
## theta[369]   0.0972538   0.237746      NA            NA      NA       NA
## theta[370]   0.0594042   0.195944      NA            NA      NA       NA
## theta[371]   0.1760470   0.275670      NA            NA      NA       NA
## theta[372]   0.2480890   0.309795      NA            NA      NA       NA
## theta[373]   0.2071020   0.302806      NA            NA      NA       NA
## theta[374]   0.0664105   0.185741      NA            NA      NA       NA
## theta[375]   0.2238840   0.297069      NA            NA      NA       NA
## theta[376]   0.0639731   0.200244      NA            NA      NA       NA
## theta[377]   0.2072650   0.286224      NA            NA      NA       NA
## theta[378]   0.0630352   0.198437      NA            NA      NA       NA
## theta[379]   0.0631609   0.198934      NA            NA      NA       NA
## theta[380]   0.2139680   0.304072      NA            NA      NA       NA
## theta[381]   0.0693638   0.211208      NA            NA      NA       NA
## theta[382]   0.0689040   0.208141      NA            NA      NA       NA
## theta[383]   0.1880060   0.275111      NA            NA      NA       NA
## theta[384]   0.0577733   0.191388      NA            NA      NA       NA
## theta[385]   0.1938790   0.317730      NA            NA      NA       NA
## theta[386]   0.1850130   0.273435      NA            NA      NA       NA
## theta[387]   0.0618143   0.195000      NA            NA      NA       NA
## theta[388]   0.2098120   0.296238      NA            NA      NA       NA
## theta[389]   0.2146760   0.295737      NA            NA      NA       NA
## theta[390]   0.1592250   0.283398      NA            NA      NA       NA
## theta[391]   0.1784620   0.283016      NA            NA      NA       NA
## theta[392]   0.2383240   0.294356      NA            NA      NA       NA
## theta[393]   0.1800690   0.281390      NA            NA      NA       NA
## theta[394]   0.2571810   0.318957      NA            NA      NA       NA
## theta[395]   0.1716630   0.288688      NA            NA      NA       NA
## theta[396]   0.1962100   0.308194      NA            NA      NA       NA
## theta[397]   0.0635818   0.200026      NA            NA      NA       NA
## theta[398]   0.1868090   0.275678      NA            NA      NA       NA
## theta[399]   0.0701910   0.203537      NA            NA      NA       NA
## theta[400]   0.1920700   0.313677      NA            NA      NA       NA
## theta[401]   0.1793660   0.307707      NA            NA      NA       NA
## theta[402]   0.0590382   0.192345      NA            NA      NA       NA
## theta[403]   0.2535770   0.316237      NA            NA      NA       NA
## theta[404]   0.2097520   0.309112      NA            NA      NA       NA
## theta[405]   0.2071970   0.268998      NA            NA      NA       NA
## theta[406]   0.1989850   0.275325      NA            NA      NA       NA
## theta[407]   0.0698261   0.198377      NA            NA      NA       NA
## theta[408]   0.1883480   0.262652      NA            NA      NA       NA
## theta[409]   0.1028960   0.224439      NA            NA      NA       NA
## theta[410]   0.1885130   0.287486      NA            NA      NA       NA
## theta[411]   0.0638260   0.200811      NA            NA      NA       NA
## theta[412]   0.1876760   0.304138      NA            NA      NA       NA
## theta[413]   0.0622289   0.196579      NA            NA      NA       NA
## theta[414]   0.0684016   0.203615      NA            NA      NA       NA
## theta[415]   0.1519860   0.235772      NA            NA      NA       NA
## theta[416]   0.0619585   0.195052      NA            NA      NA       NA
## theta[417]   0.2597210   0.320995      NA            NA      NA       NA
## theta[418]   0.0649896   0.197479      NA            NA      NA       NA
## theta[419]   0.1971680   0.282443      NA            NA      NA       NA
## theta[420]   0.2099710   0.279057      NA            NA      NA       NA
## theta[421]   0.1692190   0.269267      NA            NA      NA       NA
## theta[422]   0.2374060   0.299194      NA            NA      NA       NA
## theta[423]   0.1971240   0.271533      NA            NA      NA       NA
## theta[424]   0.2094350   0.298541      NA            NA      NA       NA
## theta[425]   0.2027490   0.287432      NA            NA      NA       NA
## theta[426]   0.2174800   0.294964      NA            NA      NA       NA
## theta[427]   0.2554960   0.320061      NA            NA      NA       NA
## theta[428]   0.1850160   0.277394      NA            NA      NA       NA
## theta[429]   0.1074760   0.228468      NA            NA      NA       NA
## theta[430]   0.1896210   0.297006      NA            NA      NA       NA
## theta[431]   0.1968890   0.308301      NA            NA      NA       NA
## theta[432]   0.1802660   0.264967      NA            NA      NA       NA
## theta[433]   0.2233120   0.303877      NA            NA      NA       NA
## theta[434]   0.2547120   0.323842      NA            NA      NA       NA
## theta[435]   0.0578671   0.195650      NA            NA      NA       NA
## theta[436]   0.2159100   0.278162      NA            NA      NA       NA
## theta[437]   0.2699050   0.331005      NA            NA      NA       NA
## theta[438]   0.0617909   0.191944      NA            NA      NA       NA
## theta[439]   0.2413170   0.307539      NA            NA      NA       NA
## theta[440]   0.2120000   0.322453      NA            NA      NA       NA
## theta[441]   0.2084080   0.282125      NA            NA      NA       NA
## theta[442]   0.0690704   0.172037      NA            NA      NA       NA
## theta[443]   0.2030020   0.264729      NA            NA      NA       NA
## theta[444]   0.1935620   0.289874      NA            NA      NA       NA
## theta[445]   0.2319520   0.312490      NA            NA      NA       NA
## theta[446]   0.1837520   0.298975      NA            NA      NA       NA
## theta[447]   0.2496590   0.309029      NA            NA      NA       NA
## theta[448]   0.1823190   0.262893      NA            NA      NA       NA
## theta[449]   0.2421270   0.315413      NA            NA      NA       NA
## theta[450]   0.2353500   0.299787      NA            NA      NA       NA
## theta[451]   0.2195650   0.281689      NA            NA      NA       NA
## theta[452]   0.0634886   0.187414      NA            NA      NA       NA
## theta[453]   0.2017200   0.292097      NA            NA      NA       NA
## theta[454]   0.2011530   0.296561      NA            NA      NA       NA
## theta[455]   0.0709574   0.188754      NA            NA      NA       NA
## theta[456]   0.1862790   0.278599      NA            NA      NA       NA
## theta[457]   0.2041640   0.293501      NA            NA      NA       NA
## theta[458]   0.0730599   0.203319      NA            NA      NA       NA
## theta[459]   0.2018320   0.281306      NA            NA      NA       NA
## theta[460]   0.0772291   0.195412      NA            NA      NA       NA
## theta[461]   0.2524010   0.323574      NA            NA      NA       NA
## theta[462]   0.2452190   0.306661      NA            NA      NA       NA
## theta[463]   0.0748095   0.190112      NA            NA      NA       NA
## theta[464]   0.2151550   0.306307      NA            NA      NA       NA
## theta[465]   0.0631842   0.165682      NA            NA      NA       NA
## theta[466]   0.2644820   0.340151      NA            NA      NA       NA
## theta[467]   0.1014820   0.218296      NA            NA      NA       NA
## theta[468]   0.0648292   0.185935      NA            NA      NA       NA
## theta[469]   0.2263390   0.282300      NA            NA      NA       NA
## theta[470]   0.2261460   0.284358      NA            NA      NA       NA
## theta[471]   0.0640911   0.199579      NA            NA      NA       NA
## theta[472]   0.2106500   0.296934      NA            NA      NA       NA
## theta[473]   0.2529090   0.317223      NA            NA      NA       NA
## theta[474]   0.0612809   0.195671      NA            NA      NA       NA
## theta[475]   0.2062730   0.269819      NA            NA      NA       NA
## theta[476]   0.2087340   0.300049      NA            NA      NA       NA
## theta[477]   0.1824660   0.270743      NA            NA      NA       NA
## theta[478]   0.2194750   0.324682      NA            NA      NA       NA
## theta[479]   0.1965570   0.292794      NA            NA      NA       NA
## theta[480]   0.2216080   0.285636      NA            NA      NA       NA
## theta[481]   0.0617853   0.195184      NA            NA      NA       NA
## theta[482]   0.0618921   0.197281      NA            NA      NA       NA
## theta[483]   0.2207020   0.292735      NA            NA      NA       NA
## theta[484]   0.1994490   0.313979      NA            NA      NA       NA
## theta[485]   0.2171230   0.309580      NA            NA      NA       NA
## theta[486]   0.1696930   0.296091      NA            NA      NA       NA
## theta[487]   0.1916370   0.311777      NA            NA      NA       NA
## theta[488]   0.0639582   0.193253      NA            NA      NA       NA
## theta[489]   0.2029140   0.305521      NA            NA      NA       NA
## theta[490]   0.2346420   0.295971      NA            NA      NA       NA
## theta[491]   0.0963322   0.208235      NA            NA      NA       NA
## theta[492]   0.1561830   0.248702      NA            NA      NA       NA
## theta[493]   0.2348340   0.300731      NA            NA      NA       NA
## theta[494]   0.1264520   0.260696      NA            NA      NA       NA
## theta[495]   0.0616689   0.186159      NA            NA      NA       NA
## theta[496]   0.0622756   0.195674      NA            NA      NA       NA
## theta[497]   0.2290660   0.290507      NA            NA      NA       NA
## theta[498]   0.0865983   0.192779      NA            NA      NA       NA
## theta[499]   0.2324330   0.313811      NA            NA      NA       NA
## theta[500]   0.1832990   0.303761      NA            NA      NA       NA
## theta[501]   0.0623736   0.196891      NA            NA      NA       NA
## theta[502]   0.1767320   0.305425      NA            NA      NA       NA
## theta[503]   0.0598640   0.189516      NA            NA      NA       NA
## theta[504]   0.0776712   0.216179      NA            NA      NA       NA
## theta[505]   0.1879890   0.301055      NA            NA      NA       NA
## theta[506]   0.1982100   0.293790      NA            NA      NA       NA
## theta[507]   0.1864340   0.268082      NA            NA      NA       NA
## theta[508]   0.0601451   0.182401      NA            NA      NA       NA
## theta[509]   0.2137090   0.304816      NA            NA      NA       NA
## theta[510]   0.0643262   0.171250      NA            NA      NA       NA
## theta[511]   0.0713985   0.204690      NA            NA      NA       NA
## theta[512]   0.2173440   0.290058      NA            NA      NA       NA
## theta[513]   0.0609935   0.195765      NA            NA      NA       NA
## theta[514]   0.1864520   0.276360      NA            NA      NA       NA
## theta[515]   0.0648890   0.190693      NA            NA      NA       NA
## theta[516]   0.0690472   0.167934      NA            NA      NA       NA
## theta[517]   0.2311540   0.300463      NA            NA      NA       NA
## theta[518]   0.2172020   0.284213      NA            NA      NA       NA
## theta[519]   0.2385820   0.319065      NA            NA      NA       NA
## theta[520]   0.2115850   0.293723      NA            NA      NA       NA
## theta[521]   0.2016920   0.305822      NA            NA      NA       NA
## theta[522]   0.0639200   0.198755      NA            NA      NA       NA
## theta[523]   0.0623141   0.199625      NA            NA      NA       NA
## theta[524]   0.0744691   0.216704      NA            NA      NA       NA
## theta[525]   0.2111160   0.281895      NA            NA      NA       NA
## theta[526]   0.2520400   0.334693      NA            NA      NA       NA
## theta[527]   0.2332110   0.301992      NA            NA      NA       NA
## theta[528]   0.0558061   0.179030      NA            NA      NA       NA
## theta[529]   0.1980610   0.298048      NA            NA      NA       NA
## theta[530]   0.1913710   0.304371      NA            NA      NA       NA
## theta[531]   0.0813440   0.197484      NA            NA      NA       NA
## theta[532]   0.0560500   0.156017      NA            NA      NA       NA
## theta[533]   0.2181360   0.305385      NA            NA      NA       NA
## theta[534]   0.0563538   0.152512      NA            NA      NA       NA
## theta[535]   0.1917340   0.306066      NA            NA      NA       NA
## theta[536]   0.1880000   0.289976      NA            NA      NA       NA
## theta[537]   0.0631459   0.197489      NA            NA      NA       NA
## theta[538]   0.1640840   0.292313      NA            NA      NA       NA
## theta[539]   0.2126590   0.297327      NA            NA      NA       NA
## theta[540]   0.0686690   0.180998      NA            NA      NA       NA
## theta[541]   0.2485870   0.312807      NA            NA      NA       NA
## theta[542]   0.1027310   0.236827      NA            NA      NA       NA
## theta[543]   0.1900030   0.276575      NA            NA      NA       NA
## theta[544]   0.0628717   0.200575      NA            NA      NA       NA
## theta[545]   0.2106780   0.298363      NA            NA      NA       NA
## theta[546]   0.1897830   0.296049      NA            NA      NA       NA
## theta[547]   0.1860750   0.253579      NA            NA      NA       NA
## theta[548]   0.0615885   0.193500      NA            NA      NA       NA
## theta[549]   0.1991970   0.294751      NA            NA      NA       NA
## theta[550]   0.2101090   0.279365      NA            NA      NA       NA
## theta[551]   0.1644160   0.289545      NA            NA      NA       NA
## theta[552]   0.1804120   0.269259      NA            NA      NA       NA
## theta[553]   0.0662063   0.199811      NA            NA      NA       NA
## theta[554]   0.2203010   0.296087      NA            NA      NA       NA
## theta[555]   0.1949780   0.274235      NA            NA      NA       NA
## theta[556]   0.1849420   0.270937      NA            NA      NA       NA
## theta[557]   0.1698610   0.265664      NA            NA      NA       NA
## theta[558]   0.0602549   0.196402      NA            NA      NA       NA
## theta[559]   0.0637797   0.199848      NA            NA      NA       NA
## theta[560]   0.1863420   0.305459      NA            NA      NA       NA
## theta[561]   0.0689357   0.205917      NA            NA      NA       NA
## theta[562]   0.2677520   0.336447      NA            NA      NA       NA
## theta[563]   0.2053530   0.277476      NA            NA      NA       NA
## theta[564]   0.2155300   0.282403      NA            NA      NA       NA
## theta[565]   0.2171660   0.278731      NA            NA      NA       NA
## theta[566]   0.1986180   0.300727      NA            NA      NA       NA
## theta[567]   0.0798864   0.221144      NA            NA      NA       NA
## theta[568]   0.2098640   0.291493      NA            NA      NA       NA
## theta[569]   0.1995110   0.266422      NA            NA      NA       NA
## theta[570]   0.0631423   0.199821      NA            NA      NA       NA
## theta[571]   0.0614936   0.194604      NA            NA      NA       NA
## theta[572]   0.1868930   0.287847      NA            NA      NA       NA
## theta[573]   0.2762190   0.341187      NA            NA      NA       NA
## theta[574]   0.1891290   0.288313      NA            NA      NA       NA
## theta[575]   0.0807795   0.191350      NA            NA      NA       NA
## theta[576]   0.2074790   0.289654      NA            NA      NA       NA
## theta[577]   0.1962800   0.268767      NA            NA      NA       NA
## theta[578]   0.0605325   0.192283      NA            NA      NA       NA
## theta[579]   0.1949950   0.277571      NA            NA      NA       NA
## theta[580]   0.2055730   0.284500      NA            NA      NA       NA
## theta[581]   0.0607274   0.190373      NA            NA      NA       NA
## theta[582]   0.0732449   0.188540      NA            NA      NA       NA
## theta[583]   0.0615426   0.193626      NA            NA      NA       NA
## theta[584]   0.0598164   0.195022      NA            NA      NA       NA
## theta[585]   0.1898510   0.290748      NA            NA      NA       NA
## theta[586]   0.2026140   0.319936      NA            NA      NA       NA
## theta[587]   0.1811930   0.271303      NA            NA      NA       NA
## theta[588]   0.2344410   0.314683      NA            NA      NA       NA
## theta[589]   0.0612691   0.196340      NA            NA      NA       NA
## theta[590]   0.0629880   0.195355      NA            NA      NA       NA
## theta[591]   0.0922297   0.201711      NA            NA      NA       NA
## theta[592]   0.0823988   0.225519      NA            NA      NA       NA
## theta[593]   0.0593400   0.188862      NA            NA      NA       NA
## theta[594]   0.0696804   0.203770      NA            NA      NA       NA
## theta[595]   0.0612789   0.159606      NA            NA      NA       NA
## theta[596]   0.1906510   0.271574      NA            NA      NA       NA
## theta[597]   0.2618540   0.331064      NA            NA      NA       NA
## theta[598]   0.2238230   0.288230      NA            NA      NA       NA
## theta[599]   0.2384680   0.307399      NA            NA      NA       NA
## theta[600]   0.1734580   0.301355      NA            NA      NA       NA
## theta[601]   0.0606022   0.189300      NA            NA      NA       NA
## theta[602]   0.2161670   0.301877      NA            NA      NA       NA
## theta[603]   0.2127290   0.303034      NA            NA      NA       NA
## theta[604]   0.0691302   0.208443      NA            NA      NA       NA
## theta[605]   0.2341670   0.298370      NA            NA      NA       NA
## theta[606]   0.1798210   0.276591      NA            NA      NA       NA
## theta[607]   0.2312970   0.305230      NA            NA      NA       NA
## theta[608]   0.2102100   0.285525      NA            NA      NA       NA
## theta[609]   0.2327550   0.296364      NA            NA      NA       NA
## theta[610]   0.2022940   0.276452      NA            NA      NA       NA
## theta[611]   0.2409430   0.312561      NA            NA      NA       NA
## theta[612]   0.0633462   0.199944      NA            NA      NA       NA
## theta[613]   0.0540330   0.176814      NA            NA      NA       NA
## theta[614]   0.0698596   0.202501      NA            NA      NA       NA
## theta[615]   0.0632300   0.197643      NA            NA      NA       NA
## theta[616]   0.2330670   0.313438      NA            NA      NA       NA
## theta[617]   0.2139180   0.275206      NA            NA      NA       NA
## theta[618]   0.0710194   0.198887      NA            NA      NA       NA
## theta[619]   0.2455330   0.308242      NA            NA      NA       NA
## theta[620]   0.2533980   0.323105      NA            NA      NA       NA
## theta[621]   0.1926760   0.288747      NA            NA      NA       NA
## theta[622]   0.1770300   0.265064      NA            NA      NA       NA
## theta[623]   0.1958230   0.268248      NA            NA      NA       NA
## theta[624]   0.0627064   0.194813      NA            NA      NA       NA
## theta[625]   0.2095890   0.288196      NA            NA      NA       NA
## theta[626]   0.1993900   0.312694      NA            NA      NA       NA
## theta[627]   0.1788270   0.285017      NA            NA      NA       NA
## theta[628]   0.1941040   0.308438      NA            NA      NA       NA
## theta[629]   0.2026450   0.319501      NA            NA      NA       NA
## theta[630]   0.2468600   0.323012      NA            NA      NA       NA
## theta[631]   0.0668929   0.188882      NA            NA      NA       NA
## theta[632]   0.1664020   0.265104      NA            NA      NA       NA
## theta[633]   0.1047000   0.223108      NA            NA      NA       NA
## theta[634]   0.2182560   0.294190      NA            NA      NA       NA
## theta[635]   0.2096700   0.319970      NA            NA      NA       NA
## theta[636]   0.1839510   0.291710      NA            NA      NA       NA
## theta[637]   0.2090380   0.295969      NA            NA      NA       NA
## theta[638]   0.2005710   0.295158      NA            NA      NA       NA
## theta[639]   0.0773796   0.219990      NA            NA      NA       NA
## theta[640]   0.0876643   0.198639      NA            NA      NA       NA
## theta[641]   0.0681263   0.174759      NA            NA      NA       NA
## theta[642]   0.1746900   0.259223      NA            NA      NA       NA
## theta[643]   0.0655258   0.197356      NA            NA      NA       NA
## theta[644]   0.2131510   0.311956      NA            NA      NA       NA
## theta[645]   0.0698286   0.207930      NA            NA      NA       NA
## theta[646]   0.0600009   0.182996      NA            NA      NA       NA
## theta[647]   0.1920160   0.267461      NA            NA      NA       NA
## theta[648]   0.2024850   0.313494      NA            NA      NA       NA
## theta[649]   0.1853610   0.293485      NA            NA      NA       NA
## theta[650]   0.2108810   0.313809      NA            NA      NA       NA
## theta[651]   0.2023100   0.321208      NA            NA      NA       NA
## theta[652]   0.2531230   0.331443      NA            NA      NA       NA
## theta[653]   0.0601753   0.195836      NA            NA      NA       NA
## theta[654]   0.0671572   0.196123      NA            NA      NA       NA
## theta[655]   0.0645925   0.187581      NA            NA      NA       NA
## theta[656]   0.2082740   0.299250      NA            NA      NA       NA
## theta[657]   0.0635823   0.198544      NA            NA      NA       NA
## theta[658]   0.2592990   0.328808      NA            NA      NA       NA
## theta[659]   0.0608872   0.197430      NA            NA      NA       NA
## theta[660]   0.2496060   0.311328      NA            NA      NA       NA
## theta[661]   0.2065460   0.289562      NA            NA      NA       NA
## theta[662]   0.0589176   0.186544      NA            NA      NA       NA
## theta[663]   0.2031240   0.285089      NA            NA      NA       NA
## theta[664]   0.2317920   0.302608      NA            NA      NA       NA
## theta[665]   0.0646286   0.199417      NA            NA      NA       NA
## theta[666]   0.1890140   0.279465      NA            NA      NA       NA
## theta[667]   0.2050840   0.289908      NA            NA      NA       NA
## theta[668]   0.2180650   0.321999      NA            NA      NA       NA
## theta[669]   0.1876240   0.299450      NA            NA      NA       NA
## theta[670]   0.0640953   0.197419      NA            NA      NA       NA
## theta[671]   0.2018550   0.290469      NA            NA      NA       NA
## theta[672]   0.0598408   0.189634      NA            NA      NA       NA
## theta[673]   0.2480740   0.310693      NA            NA      NA       NA
## theta[674]   0.2052150   0.290890      NA            NA      NA       NA
## theta[675]   0.1900020   0.306570      NA            NA      NA       NA
## theta[676]   0.0696015   0.201914      NA            NA      NA       NA
## theta[677]   0.1935530   0.278600      NA            NA      NA       NA
## theta[678]   0.1852110   0.246499      NA            NA      NA       NA
## theta[679]   0.1917580   0.307351      NA            NA      NA       NA
## theta[680]   0.2287250   0.282640      NA            NA      NA       NA
## theta[681]   0.1944460   0.259232      NA            NA      NA       NA
## theta[682]   0.0629590   0.197208      NA            NA      NA       NA
## theta[683]   0.2140370   0.273887      NA            NA      NA       NA
## theta[684]   0.0642938   0.191160      NA            NA      NA       NA
## theta[685]   0.1980790   0.316296      NA            NA      NA       NA
## theta[686]   0.1983580   0.310181      NA            NA      NA       NA
## theta[687]   0.0586300   0.197621      NA            NA      NA       NA
## theta[688]   0.2373430   0.319965      NA            NA      NA       NA
## theta[689]   0.1824290   0.261053      NA            NA      NA       NA
## theta[690]   0.0593266   0.196146      NA            NA      NA       NA
## theta[691]   0.0693751   0.206127      NA            NA      NA       NA
## theta[692]   0.1957320   0.299360      NA            NA      NA       NA
## theta[693]   0.2422340   0.303873      NA            NA      NA       NA
## theta[694]   0.2004210   0.316120      NA            NA      NA       NA
## theta[695]   0.2509080   0.324182      NA            NA      NA       NA
## theta[696]   0.2339660   0.302367      NA            NA      NA       NA
## theta[697]   0.1929650   0.285876      NA            NA      NA       NA
## theta[698]   0.1728210   0.304569      NA            NA      NA       NA
## theta[699]   0.2079190   0.275575      NA            NA      NA       NA
## theta[700]   0.2321210   0.320130      NA            NA      NA       NA
## theta[701]   0.2198950   0.295668      NA            NA      NA       NA
## theta[702]   0.2064990   0.305632      NA            NA      NA       NA
## theta[703]   0.0934386   0.221618      NA            NA      NA       NA
## theta[704]   0.0653666   0.198584      NA            NA      NA       NA
## theta[705]   0.2777540   0.347861      NA            NA      NA       NA
## theta[706]   0.2564770   0.319000      NA            NA      NA       NA
## theta[707]   0.2080990   0.279293      NA            NA      NA       NA
## theta[708]   0.0624697   0.193966      NA            NA      NA       NA
## theta[709]   0.2029290   0.318338      NA            NA      NA       NA
## theta[710]   0.1908900   0.298926      NA            NA      NA       NA
## theta[711]   0.2467140   0.306568      NA            NA      NA       NA
## theta[712]   0.1975400   0.285124      NA            NA      NA       NA
## theta[713]   0.2193420   0.296079      NA            NA      NA       NA
## theta[714]   0.0628661   0.193881      NA            NA      NA       NA
## theta[715]   0.2046470   0.288084      NA            NA      NA       NA
## theta[716]   0.1882600   0.284010      NA            NA      NA       NA
## theta[717]   0.1736080   0.304832      NA            NA      NA       NA
## theta[718]   0.1743680   0.253171      NA            NA      NA       NA
## theta[719]   0.0727153   0.204947      NA            NA      NA       NA
## theta[720]   0.2319000   0.291238      NA            NA      NA       NA
## theta[721]   0.2578270   0.321606      NA            NA      NA       NA
## theta[722]   0.0634076   0.198069      NA            NA      NA       NA
## theta[723]   0.0617657   0.193649      NA            NA      NA       NA
## theta[724]   0.2259850   0.286247      NA            NA      NA       NA
## theta[725]   0.0653198   0.200157      NA            NA      NA       NA
## theta[726]   0.1942890   0.303709      NA            NA      NA       NA
## theta[727]   0.1952590   0.271128      NA            NA      NA       NA
## theta[728]   0.2044560   0.263944      NA            NA      NA       NA
## theta[729]   0.1604260   0.272899      NA            NA      NA       NA
## theta[730]   0.2214210   0.275883      NA            NA      NA       NA
## theta[731]   0.0635977   0.197525      NA            NA      NA       NA
## theta[732]   0.2148290   0.319137      NA            NA      NA       NA
## theta[733]   0.1970440   0.311551      NA            NA      NA       NA
## theta[734]   0.0631085   0.191667      NA            NA      NA       NA
## theta[735]   0.2508750   0.311872      NA            NA      NA       NA
## theta[736]   0.2457540   0.306204      NA            NA      NA       NA
## theta[737]   0.2017000   0.265038      NA            NA      NA       NA
## theta[738]   0.0611726   0.192703      NA            NA      NA       NA
## theta[739]   0.1974840   0.286228      NA            NA      NA       NA
## theta[740]   0.0654775   0.161717      NA            NA      NA       NA
## theta[741]   0.0608657   0.194283      NA            NA      NA       NA
## theta[742]   0.2596610   0.317643      NA            NA      NA       NA
## theta[743]   0.2113080   0.284886      NA            NA      NA       NA
## theta[744]   0.2359440   0.309414      NA            NA      NA       NA
## theta[745]   0.1861480   0.283905      NA            NA      NA       NA
## theta[746]   0.2091560   0.275719      NA            NA      NA       NA
## theta[747]   0.2124110   0.299840      NA            NA      NA       NA
## theta[748]   0.1932410   0.283980      NA            NA      NA       NA
## theta[749]   0.2355900   0.302488      NA            NA      NA       NA
## theta[750]   0.0724246   0.212687      NA            NA      NA       NA
## theta[751]   0.1753060   0.308399      NA            NA      NA       NA
## theta[752]   0.0608369   0.193990      NA            NA      NA       NA
## theta[753]   0.1923360   0.266929      NA            NA      NA       NA
## theta[754]   0.0587702   0.155032      NA            NA      NA       NA
## theta[755]   0.0618494   0.189235      NA            NA      NA       NA
## theta[756]   0.0717384   0.208490      NA            NA      NA       NA
## theta[757]   0.0750403   0.207746      NA            NA      NA       NA
## theta[758]   0.2119700   0.286978      NA            NA      NA       NA
## theta[759]   0.2214080   0.279018      NA            NA      NA       NA
## theta[760]   0.0712411   0.203874      NA            NA      NA       NA
## theta[761]   0.2041940   0.317886      NA            NA      NA       NA
## theta[762]   0.1983580   0.290129      NA            NA      NA       NA
## theta[763]   0.2264400   0.297361      NA            NA      NA       NA
## theta[764]   0.0727641   0.211671      NA            NA      NA       NA
## theta[765]   0.2351470   0.294353      NA            NA      NA       NA
## theta[766]   0.2013710   0.293198      NA            NA      NA       NA
## theta[767]   0.0605839   0.196437      NA            NA      NA       NA
## theta[768]   0.0621407   0.195587      NA            NA      NA       NA
## theta[769]   0.1753540   0.280072      NA            NA      NA       NA
## theta[770]   0.2072150   0.320492      NA            NA      NA       NA
## theta[771]   0.2493020   0.328738      NA            NA      NA       NA
## theta[772]   0.2608490   0.339152      NA            NA      NA       NA
## theta[773]   0.0706398   0.200802      NA            NA      NA       NA
## theta[774]   0.0644173   0.200233      NA            NA      NA       NA
## theta[775]   0.2259300   0.301712      NA            NA      NA       NA
## theta[776]   0.1803600   0.247391      NA            NA      NA       NA
## theta[777]   0.0583629   0.187360      NA            NA      NA       NA
## theta[778]   0.0654282   0.198893      NA            NA      NA       NA
## theta[779]   0.0624777   0.196535      NA            NA      NA       NA
## theta[780]   0.1931310   0.261369      NA            NA      NA       NA
## theta[781]   0.0705334   0.175012      NA            NA      NA       NA
## theta[782]   0.0629827   0.197887      NA            NA      NA       NA
## theta[783]   0.0675914   0.182141      NA            NA      NA       NA
## theta[784]   0.1973820   0.270412      NA            NA      NA       NA
## theta[785]   0.2200550   0.308432      NA            NA      NA       NA
## theta[786]   0.0620746   0.193746      NA            NA      NA       NA
## theta[787]   0.2394680   0.310227      NA            NA      NA       NA
## theta[788]   0.1880750   0.302142      NA            NA      NA       NA
## theta[789]   0.2172560   0.281984      NA            NA      NA       NA
## theta[790]   0.0615953   0.193610      NA            NA      NA       NA
## theta[791]   0.0643518   0.175052      NA            NA      NA       NA
## theta[792]   0.1893000   0.267531      NA            NA      NA       NA
## theta[793]   0.1683610   0.297873      NA            NA      NA       NA
## theta[794]   0.2218640   0.306088      NA            NA      NA       NA
## theta[795]   0.0650323   0.177611      NA            NA      NA       NA
## theta[796]   0.2190360   0.280963      NA            NA      NA       NA
## theta[797]   0.0609014   0.195136      NA            NA      NA       NA
## theta[798]   0.0657335   0.202745      NA            NA      NA       NA
## theta[799]   0.1952320   0.267853      NA            NA      NA       NA
## theta[800]   0.2056190   0.317637      NA            NA      NA       NA
## theta[801]   0.0628489   0.200331      NA            NA      NA       NA
## theta[802]   0.2236190   0.292242      NA            NA      NA       NA
## theta[803]   0.1702700   0.297865      NA            NA      NA       NA
## theta[804]   0.1836860   0.288491      NA            NA      NA       NA
## theta[805]   0.2249080   0.301693      NA            NA      NA       NA
## theta[806]   0.0720411   0.206740      NA            NA      NA       NA
## theta[807]   0.2026090   0.275855      NA            NA      NA       NA
## theta[808]   0.2602020   0.320777      NA            NA      NA       NA
## theta[809]   0.2285400   0.288582      NA            NA      NA       NA
## theta[810]   0.2090590   0.297407      NA            NA      NA       NA
## theta[811]   0.1881970   0.293139      NA            NA      NA       NA
## theta[812]   0.0542645   0.175804      NA            NA      NA       NA
## theta[813]   0.0690106   0.203393      NA            NA      NA       NA
## theta[814]   0.1945320   0.280074      NA            NA      NA       NA
## theta[815]   0.2103300   0.286532      NA            NA      NA       NA
## theta[816]   0.2244090   0.312618      NA            NA      NA       NA
## theta[817]   0.0624337   0.194690      NA            NA      NA       NA
## theta[818]   0.0617605   0.187408      NA            NA      NA       NA
## theta[819]   0.2096040   0.279789      NA            NA      NA       NA
## theta[820]   0.1995390   0.261936      NA            NA      NA       NA
## theta[821]   0.0621170   0.196724      NA            NA      NA       NA
## theta[822]   0.2172450   0.292783      NA            NA      NA       NA
## theta[823]   0.2082420   0.310297      NA            NA      NA       NA
## theta[824]   0.1647080   0.245844      NA            NA      NA       NA
## theta[825]   0.1760140   0.270160      NA            NA      NA       NA
## theta[826]   0.2365100   0.314000      NA            NA      NA       NA
## theta[827]   0.1963640   0.317978      NA            NA      NA       NA
## theta[828]   0.1706660   0.299865      NA            NA      NA       NA
## theta[829]   0.2292000   0.290503      NA            NA      NA       NA
## theta[830]   0.1775230   0.251080      NA            NA      NA       NA
## theta[831]   0.2436240   0.308399      NA            NA      NA       NA
## theta[832]   0.1860380   0.306261      NA            NA      NA       NA
## theta[833]   0.0575050   0.187608      NA            NA      NA       NA
## theta[834]   0.2463760   0.307239      NA            NA      NA       NA
## theta[835]   0.0636351   0.199464      NA            NA      NA       NA
## theta[836]   0.1927940   0.289876      NA            NA      NA       NA
## theta[837]   0.1901170   0.277422      NA            NA      NA       NA
## theta[838]   0.0600103   0.195098      NA            NA      NA       NA
## theta[839]   0.1128450   0.246922      NA            NA      NA       NA
## theta[840]   0.1964340   0.258778      NA            NA      NA       NA
## theta[841]   0.0943128   0.224071      NA            NA      NA       NA
## theta[842]   0.0650607   0.194449      NA            NA      NA       NA
## theta[843]   0.2055770   0.300731      NA            NA      NA       NA
## theta[844]   0.2471870   0.302610      NA            NA      NA       NA
## theta[845]   0.2021710   0.272563      NA            NA      NA       NA
## theta[846]   0.0626156   0.197092      NA            NA      NA       NA
## theta[847]   0.2244590   0.296359      NA            NA      NA       NA
## theta[848]   0.2384050   0.295292      NA            NA      NA       NA
## theta[849]   0.2205060   0.284957      NA            NA      NA       NA
## theta[850]   0.2102390   0.298447      NA            NA      NA       NA
## theta[851]   0.1629050   0.273785      NA            NA      NA       NA
## theta[852]   0.0642951   0.198712      NA            NA      NA       NA
## theta[853]   0.2199010   0.283969      NA            NA      NA       NA
## theta[854]   0.2418990   0.308751      NA            NA      NA       NA
## theta[855]   0.1843510   0.300607      NA            NA      NA       NA
## theta[856]   0.2045990   0.280005      NA            NA      NA       NA
## theta[857]   0.2271920   0.297325      NA            NA      NA       NA
## theta[858]   0.1999270   0.275390      NA            NA      NA       NA
## theta[859]   0.2071630   0.295566      NA            NA      NA       NA
## theta[860]   0.2104940   0.296893      NA            NA      NA       NA
## theta[861]   0.0713607   0.204796      NA            NA      NA       NA
## theta[862]   0.1858220   0.291984      NA            NA      NA       NA
## theta[863]   0.0776537   0.218567      NA            NA      NA       NA
## theta[864]   0.1907000   0.276407      NA            NA      NA       NA
## theta[865]   0.2064520   0.276519      NA            NA      NA       NA
## theta[866]   0.0737770   0.206607      NA            NA      NA       NA
## theta[867]   0.2110490   0.306164      NA            NA      NA       NA
## theta[868]   0.1659370   0.266516      NA            NA      NA       NA
## theta[869]   0.1899990   0.301531      NA            NA      NA       NA
## theta[870]   0.2727350   0.338981      NA            NA      NA       NA
## theta[871]   0.2328220   0.296438      NA            NA      NA       NA
## theta[872]   0.2226800   0.309485      NA            NA      NA       NA
## theta[873]   0.0555649   0.175359      NA            NA      NA       NA
## theta[874]   0.2136640   0.297456      NA            NA      NA       NA
## theta[875]   0.1997270   0.259730      NA            NA      NA       NA
## theta[876]   0.2202010   0.281047      NA            NA      NA       NA
## theta[877]   0.2425660   0.299939      NA            NA      NA       NA
## theta[878]   0.1832740   0.272558      NA            NA      NA       NA
## theta[879]   0.2182320   0.291488      NA            NA      NA       NA
## theta[880]   0.1967920   0.275089      NA            NA      NA       NA
## theta[881]   0.2050610   0.290399      NA            NA      NA       NA
## theta[882]   0.1878950   0.269179      NA            NA      NA       NA
## theta[883]   0.1814750   0.270345      NA            NA      NA       NA
## theta[884]   0.2050760   0.291534      NA            NA      NA       NA
## theta[885]   0.0750333   0.215635      NA            NA      NA       NA
## theta[886]   0.1974390   0.304887      NA            NA      NA       NA
## theta[887]   0.2320180   0.293085      NA            NA      NA       NA
## theta[888]   0.0587783   0.189994      NA            NA      NA       NA
## theta[889]   0.2213440   0.285671      NA            NA      NA       NA
## theta[890]   0.2278780   0.287585      NA            NA      NA       NA
## theta[891]   0.0713309   0.210041      NA            NA      NA       NA
## theta[892]   0.0606023   0.198257      NA            NA      NA       NA
## theta[893]   0.1600640   0.260540      NA            NA      NA       NA
## theta[894]   0.2034900   0.289024      NA            NA      NA       NA
## theta[895]   0.0682865   0.170954      NA            NA      NA       NA
## theta[896]   0.1475690   0.268120      NA            NA      NA       NA
## theta[897]   0.0594308   0.158858      NA            NA      NA       NA
## theta[898]   0.0802389   0.204393      NA            NA      NA       NA
## theta[899]   0.2677690   0.343256      NA            NA      NA       NA
## theta[900]   0.0769056   0.178340      NA            NA      NA       NA
## theta[901]   0.2373650   0.302054      NA            NA      NA       NA
## theta[902]   0.2128060   0.291485      NA            NA      NA       NA
## theta[903]   0.0661798   0.200829      NA            NA      NA       NA
## theta[904]   0.0649638   0.199728      NA            NA      NA       NA
## theta[905]   0.0719047   0.210471      NA            NA      NA       NA
## theta[906]   0.0656138   0.201127      NA            NA      NA       NA
## theta[907]   0.1991770   0.262587      NA            NA      NA       NA
## theta[908]   0.2075890   0.265652      NA            NA      NA       NA
## theta[909]   0.0578712   0.188671      NA            NA      NA       NA
## theta[910]   0.1998710   0.275916      NA            NA      NA       NA
## theta[911]   0.0651854   0.190432      NA            NA      NA       NA
## theta[912]   0.0697802   0.206950      NA            NA      NA       NA
## theta[913]   0.2017480   0.280926      NA            NA      NA       NA
## theta[914]   0.0651210   0.191402      NA            NA      NA       NA
## theta[915]   0.2436720   0.312430      NA            NA      NA       NA
## theta[916]   0.0749319   0.182184      NA            NA      NA       NA
## theta[917]   0.2024220   0.300116      NA            NA      NA       NA
## theta[918]   0.0814437   0.205092      NA            NA      NA       NA
## theta[919]   0.1703580   0.296488      NA            NA      NA       NA
## theta[920]   0.0751733   0.210324      NA            NA      NA       NA
## theta[921]   0.2148000   0.278238      NA            NA      NA       NA
## theta[922]   0.2072220   0.279907      NA            NA      NA       NA
## theta[923]   0.0655562   0.205046      NA            NA      NA       NA
## theta[924]   0.2267650   0.290246      NA            NA      NA       NA
## theta[925]   0.1787760   0.267523      NA            NA      NA       NA
## theta[926]   0.0594062   0.188603      NA            NA      NA       NA
## theta[927]   0.1795580   0.278284      NA            NA      NA       NA
## theta[928]   0.2147610   0.297553      NA            NA      NA       NA
## theta[929]   0.0814731   0.197481      NA            NA      NA       NA
## theta[930]   0.0669352   0.201959      NA            NA      NA       NA
## theta[931]   0.0950204   0.210141      NA            NA      NA       NA
## theta[932]   0.0613635   0.170901      NA            NA      NA       NA
## theta[933]   0.1947150   0.291160      NA            NA      NA       NA
## theta[934]   0.2619800   0.326168      NA            NA      NA       NA
## theta[935]   0.0591143   0.193840      NA            NA      NA       NA
## theta[936]   0.0627301   0.196901      NA            NA      NA       NA
## theta[937]   0.2086540   0.276066      NA            NA      NA       NA
## theta[938]   0.2066480   0.279116      NA            NA      NA       NA
## theta[939]   0.2322840   0.293600      NA            NA      NA       NA
## theta[940]   0.2361550   0.327019      NA            NA      NA       NA
## theta[941]   0.1826710   0.301701      NA            NA      NA       NA
## theta[942]   0.2405720   0.300649      NA            NA      NA       NA
## theta[943]   0.0840591   0.205019      NA            NA      NA       NA
## theta[944]   0.0676633   0.204236      NA            NA      NA       NA
## theta[945]   0.2453250   0.307839      NA            NA      NA       NA
## theta[946]   0.0996233   0.214938      NA            NA      NA       NA
## theta[947]   0.0606587   0.160782      NA            NA      NA       NA
## theta[948]   0.2362520   0.293203      NA            NA      NA       NA
##            PcntLtROPE PcntInROPE PcntGtROPE
## omega[1]           NA         NA         NA
## omega[2]           NA         NA         NA
## omega[3]           NA         NA         NA
## omega[4]           NA         NA         NA
## omega[5]           NA         NA         NA
## omega[6]           NA         NA         NA
## omega[7]           NA         NA         NA
## omega[8]           NA         NA         NA
## omega[9]           NA         NA         NA
## omegaO             NA         NA         NA
## kappa[1]           NA         NA         NA
## kappa[2]           NA         NA         NA
## kappa[3]           NA         NA         NA
## kappa[4]           NA         NA         NA
## kappa[5]           NA         NA         NA
## kappa[6]           NA         NA         NA
## kappa[7]           NA         NA         NA
## kappa[8]           NA         NA         NA
## kappa[9]           NA         NA         NA
## kappaO             NA         NA         NA
## theta[1]           NA         NA         NA
## theta[2]           NA         NA         NA
## theta[3]           NA         NA         NA
## theta[4]           NA         NA         NA
## theta[5]           NA         NA         NA
## theta[6]           NA         NA         NA
## theta[7]           NA         NA         NA
## theta[8]           NA         NA         NA
## theta[9]           NA         NA         NA
## theta[10]          NA         NA         NA
## theta[11]          NA         NA         NA
## theta[12]          NA         NA         NA
## theta[13]          NA         NA         NA
## theta[14]          NA         NA         NA
## theta[15]          NA         NA         NA
## theta[16]          NA         NA         NA
## theta[17]          NA         NA         NA
## theta[18]          NA         NA         NA
## theta[19]          NA         NA         NA
## theta[20]          NA         NA         NA
## theta[21]          NA         NA         NA
## theta[22]          NA         NA         NA
## theta[23]          NA         NA         NA
## theta[24]          NA         NA         NA
## theta[25]          NA         NA         NA
## theta[26]          NA         NA         NA
## theta[27]          NA         NA         NA
## theta[28]          NA         NA         NA
## theta[29]          NA         NA         NA
## theta[30]          NA         NA         NA
## theta[31]          NA         NA         NA
## theta[32]          NA         NA         NA
## theta[33]          NA         NA         NA
## theta[34]          NA         NA         NA
## theta[35]          NA         NA         NA
## theta[36]          NA         NA         NA
## theta[37]          NA         NA         NA
## theta[38]          NA         NA         NA
## theta[39]          NA         NA         NA
## theta[40]          NA         NA         NA
## theta[41]          NA         NA         NA
## theta[42]          NA         NA         NA
## theta[43]          NA         NA         NA
## theta[44]          NA         NA         NA
## theta[45]          NA         NA         NA
## theta[46]          NA         NA         NA
## theta[47]          NA         NA         NA
## theta[48]          NA         NA         NA
## theta[49]          NA         NA         NA
## theta[50]          NA         NA         NA
## theta[51]          NA         NA         NA
## theta[52]          NA         NA         NA
## theta[53]          NA         NA         NA
## theta[54]          NA         NA         NA
## theta[55]          NA         NA         NA
## theta[56]          NA         NA         NA
## theta[57]          NA         NA         NA
## theta[58]          NA         NA         NA
## theta[59]          NA         NA         NA
## theta[60]          NA         NA         NA
## theta[61]          NA         NA         NA
## theta[62]          NA         NA         NA
## theta[63]          NA         NA         NA
## theta[64]          NA         NA         NA
## theta[65]          NA         NA         NA
## theta[66]          NA         NA         NA
## theta[67]          NA         NA         NA
## theta[68]          NA         NA         NA
## theta[69]          NA         NA         NA
## theta[70]          NA         NA         NA
## theta[71]          NA         NA         NA
## theta[72]          NA         NA         NA
## theta[73]          NA         NA         NA
## theta[74]          NA         NA         NA
## theta[75]          NA         NA         NA
## theta[76]          NA         NA         NA
## theta[77]          NA         NA         NA
## theta[78]          NA         NA         NA
## theta[79]          NA         NA         NA
## theta[80]          NA         NA         NA
## theta[81]          NA         NA         NA
## theta[82]          NA         NA         NA
## theta[83]          NA         NA         NA
## theta[84]          NA         NA         NA
## theta[85]          NA         NA         NA
## theta[86]          NA         NA         NA
## theta[87]          NA         NA         NA
## theta[88]          NA         NA         NA
## theta[89]          NA         NA         NA
## theta[90]          NA         NA         NA
## theta[91]          NA         NA         NA
## theta[92]          NA         NA         NA
## theta[93]          NA         NA         NA
## theta[94]          NA         NA         NA
## theta[95]          NA         NA         NA
## theta[96]          NA         NA         NA
## theta[97]          NA         NA         NA
## theta[98]          NA         NA         NA
## theta[99]          NA         NA         NA
## theta[100]         NA         NA         NA
## theta[101]         NA         NA         NA
## theta[102]         NA         NA         NA
## theta[103]         NA         NA         NA
## theta[104]         NA         NA         NA
## theta[105]         NA         NA         NA
## theta[106]         NA         NA         NA
## theta[107]         NA         NA         NA
## theta[108]         NA         NA         NA
## theta[109]         NA         NA         NA
## theta[110]         NA         NA         NA
## theta[111]         NA         NA         NA
## theta[112]         NA         NA         NA
## theta[113]         NA         NA         NA
## theta[114]         NA         NA         NA
## theta[115]         NA         NA         NA
## theta[116]         NA         NA         NA
## theta[117]         NA         NA         NA
## theta[118]         NA         NA         NA
## theta[119]         NA         NA         NA
## theta[120]         NA         NA         NA
## theta[121]         NA         NA         NA
## theta[122]         NA         NA         NA
## theta[123]         NA         NA         NA
## theta[124]         NA         NA         NA
## theta[125]         NA         NA         NA
## theta[126]         NA         NA         NA
## theta[127]         NA         NA         NA
## theta[128]         NA         NA         NA
## theta[129]         NA         NA         NA
## theta[130]         NA         NA         NA
## theta[131]         NA         NA         NA
## theta[132]         NA         NA         NA
## theta[133]         NA         NA         NA
## theta[134]         NA         NA         NA
## theta[135]         NA         NA         NA
## theta[136]         NA         NA         NA
## theta[137]         NA         NA         NA
## theta[138]         NA         NA         NA
## theta[139]         NA         NA         NA
## theta[140]         NA         NA         NA
## theta[141]         NA         NA         NA
## theta[142]         NA         NA         NA
## theta[143]         NA         NA         NA
## theta[144]         NA         NA         NA
## theta[145]         NA         NA         NA
## theta[146]         NA         NA         NA
## theta[147]         NA         NA         NA
## theta[148]         NA         NA         NA
## theta[149]         NA         NA         NA
## theta[150]         NA         NA         NA
## theta[151]         NA         NA         NA
## theta[152]         NA         NA         NA
## theta[153]         NA         NA         NA
## theta[154]         NA         NA         NA
## theta[155]         NA         NA         NA
## theta[156]         NA         NA         NA
## theta[157]         NA         NA         NA
## theta[158]         NA         NA         NA
## theta[159]         NA         NA         NA
## theta[160]         NA         NA         NA
## theta[161]         NA         NA         NA
## theta[162]         NA         NA         NA
## theta[163]         NA         NA         NA
## theta[164]         NA         NA         NA
## theta[165]         NA         NA         NA
## theta[166]         NA         NA         NA
## theta[167]         NA         NA         NA
## theta[168]         NA         NA         NA
## theta[169]         NA         NA         NA
## theta[170]         NA         NA         NA
## theta[171]         NA         NA         NA
## theta[172]         NA         NA         NA
## theta[173]         NA         NA         NA
## theta[174]         NA         NA         NA
## theta[175]         NA         NA         NA
## theta[176]         NA         NA         NA
## theta[177]         NA         NA         NA
## theta[178]         NA         NA         NA
## theta[179]         NA         NA         NA
## theta[180]         NA         NA         NA
## theta[181]         NA         NA         NA
## theta[182]         NA         NA         NA
## theta[183]         NA         NA         NA
## theta[184]         NA         NA         NA
## theta[185]         NA         NA         NA
## theta[186]         NA         NA         NA
## theta[187]         NA         NA         NA
## theta[188]         NA         NA         NA
## theta[189]         NA         NA         NA
## theta[190]         NA         NA         NA
## theta[191]         NA         NA         NA
## theta[192]         NA         NA         NA
## theta[193]         NA         NA         NA
## theta[194]         NA         NA         NA
## theta[195]         NA         NA         NA
## theta[196]         NA         NA         NA
## theta[197]         NA         NA         NA
## theta[198]         NA         NA         NA
## theta[199]         NA         NA         NA
## theta[200]         NA         NA         NA
## theta[201]         NA         NA         NA
## theta[202]         NA         NA         NA
## theta[203]         NA         NA         NA
## theta[204]         NA         NA         NA
## theta[205]         NA         NA         NA
## theta[206]         NA         NA         NA
## theta[207]         NA         NA         NA
## theta[208]         NA         NA         NA
## theta[209]         NA         NA         NA
## theta[210]         NA         NA         NA
## theta[211]         NA         NA         NA
## theta[212]         NA         NA         NA
## theta[213]         NA         NA         NA
## theta[214]         NA         NA         NA
## theta[215]         NA         NA         NA
## theta[216]         NA         NA         NA
## theta[217]         NA         NA         NA
## theta[218]         NA         NA         NA
## theta[219]         NA         NA         NA
## theta[220]         NA         NA         NA
## theta[221]         NA         NA         NA
## theta[222]         NA         NA         NA
## theta[223]         NA         NA         NA
## theta[224]         NA         NA         NA
## theta[225]         NA         NA         NA
## theta[226]         NA         NA         NA
## theta[227]         NA         NA         NA
## theta[228]         NA         NA         NA
## theta[229]         NA         NA         NA
## theta[230]         NA         NA         NA
## theta[231]         NA         NA         NA
## theta[232]         NA         NA         NA
## theta[233]         NA         NA         NA
## theta[234]         NA         NA         NA
## theta[235]         NA         NA         NA
## theta[236]         NA         NA         NA
## theta[237]         NA         NA         NA
## theta[238]         NA         NA         NA
## theta[239]         NA         NA         NA
## theta[240]         NA         NA         NA
## theta[241]         NA         NA         NA
## theta[242]         NA         NA         NA
## theta[243]         NA         NA         NA
## theta[244]         NA         NA         NA
## theta[245]         NA         NA         NA
## theta[246]         NA         NA         NA
## theta[247]         NA         NA         NA
## theta[248]         NA         NA         NA
## theta[249]         NA         NA         NA
## theta[250]         NA         NA         NA
## theta[251]         NA         NA         NA
## theta[252]         NA         NA         NA
## theta[253]         NA         NA         NA
## theta[254]         NA         NA         NA
## theta[255]         NA         NA         NA
## theta[256]         NA         NA         NA
## theta[257]         NA         NA         NA
## theta[258]         NA         NA         NA
## theta[259]         NA         NA         NA
## theta[260]         NA         NA         NA
## theta[261]         NA         NA         NA
## theta[262]         NA         NA         NA
## theta[263]         NA         NA         NA
## theta[264]         NA         NA         NA
## theta[265]         NA         NA         NA
## theta[266]         NA         NA         NA
## theta[267]         NA         NA         NA
## theta[268]         NA         NA         NA
## theta[269]         NA         NA         NA
## theta[270]         NA         NA         NA
## theta[271]         NA         NA         NA
## theta[272]         NA         NA         NA
## theta[273]         NA         NA         NA
## theta[274]         NA         NA         NA
## theta[275]         NA         NA         NA
## theta[276]         NA         NA         NA
## theta[277]         NA         NA         NA
## theta[278]         NA         NA         NA
## theta[279]         NA         NA         NA
## theta[280]         NA         NA         NA
## theta[281]         NA         NA         NA
## theta[282]         NA         NA         NA
## theta[283]         NA         NA         NA
## theta[284]         NA         NA         NA
## theta[285]         NA         NA         NA
## theta[286]         NA         NA         NA
## theta[287]         NA         NA         NA
## theta[288]         NA         NA         NA
## theta[289]         NA         NA         NA
## theta[290]         NA         NA         NA
## theta[291]         NA         NA         NA
## theta[292]         NA         NA         NA
## theta[293]         NA         NA         NA
## theta[294]         NA         NA         NA
## theta[295]         NA         NA         NA
## theta[296]         NA         NA         NA
## theta[297]         NA         NA         NA
## theta[298]         NA         NA         NA
## theta[299]         NA         NA         NA
## theta[300]         NA         NA         NA
## theta[301]         NA         NA         NA
## theta[302]         NA         NA         NA
## theta[303]         NA         NA         NA
## theta[304]         NA         NA         NA
## theta[305]         NA         NA         NA
## theta[306]         NA         NA         NA
## theta[307]         NA         NA         NA
## theta[308]         NA         NA         NA
## theta[309]         NA         NA         NA
## theta[310]         NA         NA         NA
## theta[311]         NA         NA         NA
## theta[312]         NA         NA         NA
## theta[313]         NA         NA         NA
## theta[314]         NA         NA         NA
## theta[315]         NA         NA         NA
## theta[316]         NA         NA         NA
## theta[317]         NA         NA         NA
## theta[318]         NA         NA         NA
## theta[319]         NA         NA         NA
## theta[320]         NA         NA         NA
## theta[321]         NA         NA         NA
## theta[322]         NA         NA         NA
## theta[323]         NA         NA         NA
## theta[324]         NA         NA         NA
## theta[325]         NA         NA         NA
## theta[326]         NA         NA         NA
## theta[327]         NA         NA         NA
## theta[328]         NA         NA         NA
## theta[329]         NA         NA         NA
## theta[330]         NA         NA         NA
## theta[331]         NA         NA         NA
## theta[332]         NA         NA         NA
## theta[333]         NA         NA         NA
## theta[334]         NA         NA         NA
## theta[335]         NA         NA         NA
## theta[336]         NA         NA         NA
## theta[337]         NA         NA         NA
## theta[338]         NA         NA         NA
## theta[339]         NA         NA         NA
## theta[340]         NA         NA         NA
## theta[341]         NA         NA         NA
## theta[342]         NA         NA         NA
## theta[343]         NA         NA         NA
## theta[344]         NA         NA         NA
## theta[345]         NA         NA         NA
## theta[346]         NA         NA         NA
## theta[347]         NA         NA         NA
## theta[348]         NA         NA         NA
## theta[349]         NA         NA         NA
## theta[350]         NA         NA         NA
## theta[351]         NA         NA         NA
## theta[352]         NA         NA         NA
## theta[353]         NA         NA         NA
## theta[354]         NA         NA         NA
## theta[355]         NA         NA         NA
## theta[356]         NA         NA         NA
## theta[357]         NA         NA         NA
## theta[358]         NA         NA         NA
## theta[359]         NA         NA         NA
## theta[360]         NA         NA         NA
## theta[361]         NA         NA         NA
## theta[362]         NA         NA         NA
## theta[363]         NA         NA         NA
## theta[364]         NA         NA         NA
## theta[365]         NA         NA         NA
## theta[366]         NA         NA         NA
## theta[367]         NA         NA         NA
## theta[368]         NA         NA         NA
## theta[369]         NA         NA         NA
## theta[370]         NA         NA         NA
## theta[371]         NA         NA         NA
## theta[372]         NA         NA         NA
## theta[373]         NA         NA         NA
## theta[374]         NA         NA         NA
## theta[375]         NA         NA         NA
## theta[376]         NA         NA         NA
## theta[377]         NA         NA         NA
## theta[378]         NA         NA         NA
## theta[379]         NA         NA         NA
## theta[380]         NA         NA         NA
## theta[381]         NA         NA         NA
## theta[382]         NA         NA         NA
## theta[383]         NA         NA         NA
## theta[384]         NA         NA         NA
## theta[385]         NA         NA         NA
## theta[386]         NA         NA         NA
## theta[387]         NA         NA         NA
## theta[388]         NA         NA         NA
## theta[389]         NA         NA         NA
## theta[390]         NA         NA         NA
## theta[391]         NA         NA         NA
## theta[392]         NA         NA         NA
## theta[393]         NA         NA         NA
## theta[394]         NA         NA         NA
## theta[395]         NA         NA         NA
## theta[396]         NA         NA         NA
## theta[397]         NA         NA         NA
## theta[398]         NA         NA         NA
## theta[399]         NA         NA         NA
## theta[400]         NA         NA         NA
## theta[401]         NA         NA         NA
## theta[402]         NA         NA         NA
## theta[403]         NA         NA         NA
## theta[404]         NA         NA         NA
## theta[405]         NA         NA         NA
## theta[406]         NA         NA         NA
## theta[407]         NA         NA         NA
## theta[408]         NA         NA         NA
## theta[409]         NA         NA         NA
## theta[410]         NA         NA         NA
## theta[411]         NA         NA         NA
## theta[412]         NA         NA         NA
## theta[413]         NA         NA         NA
## theta[414]         NA         NA         NA
## theta[415]         NA         NA         NA
## theta[416]         NA         NA         NA
## theta[417]         NA         NA         NA
## theta[418]         NA         NA         NA
## theta[419]         NA         NA         NA
## theta[420]         NA         NA         NA
## theta[421]         NA         NA         NA
## theta[422]         NA         NA         NA
## theta[423]         NA         NA         NA
## theta[424]         NA         NA         NA
## theta[425]         NA         NA         NA
## theta[426]         NA         NA         NA
## theta[427]         NA         NA         NA
## theta[428]         NA         NA         NA
## theta[429]         NA         NA         NA
## theta[430]         NA         NA         NA
## theta[431]         NA         NA         NA
## theta[432]         NA         NA         NA
## theta[433]         NA         NA         NA
## theta[434]         NA         NA         NA
## theta[435]         NA         NA         NA
## theta[436]         NA         NA         NA
## theta[437]         NA         NA         NA
## theta[438]         NA         NA         NA
## theta[439]         NA         NA         NA
## theta[440]         NA         NA         NA
## theta[441]         NA         NA         NA
## theta[442]         NA         NA         NA
## theta[443]         NA         NA         NA
## theta[444]         NA         NA         NA
## theta[445]         NA         NA         NA
## theta[446]         NA         NA         NA
## theta[447]         NA         NA         NA
## theta[448]         NA         NA         NA
## theta[449]         NA         NA         NA
## theta[450]         NA         NA         NA
## theta[451]         NA         NA         NA
## theta[452]         NA         NA         NA
## theta[453]         NA         NA         NA
## theta[454]         NA         NA         NA
## theta[455]         NA         NA         NA
## theta[456]         NA         NA         NA
## theta[457]         NA         NA         NA
## theta[458]         NA         NA         NA
## theta[459]         NA         NA         NA
## theta[460]         NA         NA         NA
## theta[461]         NA         NA         NA
## theta[462]         NA         NA         NA
## theta[463]         NA         NA         NA
## theta[464]         NA         NA         NA
## theta[465]         NA         NA         NA
## theta[466]         NA         NA         NA
## theta[467]         NA         NA         NA
## theta[468]         NA         NA         NA
## theta[469]         NA         NA         NA
## theta[470]         NA         NA         NA
## theta[471]         NA         NA         NA
## theta[472]         NA         NA         NA
## theta[473]         NA         NA         NA
## theta[474]         NA         NA         NA
## theta[475]         NA         NA         NA
## theta[476]         NA         NA         NA
## theta[477]         NA         NA         NA
## theta[478]         NA         NA         NA
## theta[479]         NA         NA         NA
## theta[480]         NA         NA         NA
## theta[481]         NA         NA         NA
## theta[482]         NA         NA         NA
## theta[483]         NA         NA         NA
## theta[484]         NA         NA         NA
## theta[485]         NA         NA         NA
## theta[486]         NA         NA         NA
## theta[487]         NA         NA         NA
## theta[488]         NA         NA         NA
## theta[489]         NA         NA         NA
## theta[490]         NA         NA         NA
## theta[491]         NA         NA         NA
## theta[492]         NA         NA         NA
## theta[493]         NA         NA         NA
## theta[494]         NA         NA         NA
## theta[495]         NA         NA         NA
## theta[496]         NA         NA         NA
## theta[497]         NA         NA         NA
## theta[498]         NA         NA         NA
## theta[499]         NA         NA         NA
## theta[500]         NA         NA         NA
## theta[501]         NA         NA         NA
## theta[502]         NA         NA         NA
## theta[503]         NA         NA         NA
## theta[504]         NA         NA         NA
## theta[505]         NA         NA         NA
## theta[506]         NA         NA         NA
## theta[507]         NA         NA         NA
## theta[508]         NA         NA         NA
## theta[509]         NA         NA         NA
## theta[510]         NA         NA         NA
## theta[511]         NA         NA         NA
## theta[512]         NA         NA         NA
## theta[513]         NA         NA         NA
## theta[514]         NA         NA         NA
## theta[515]         NA         NA         NA
## theta[516]         NA         NA         NA
## theta[517]         NA         NA         NA
## theta[518]         NA         NA         NA
## theta[519]         NA         NA         NA
## theta[520]         NA         NA         NA
## theta[521]         NA         NA         NA
## theta[522]         NA         NA         NA
## theta[523]         NA         NA         NA
## theta[524]         NA         NA         NA
## theta[525]         NA         NA         NA
## theta[526]         NA         NA         NA
## theta[527]         NA         NA         NA
## theta[528]         NA         NA         NA
## theta[529]         NA         NA         NA
## theta[530]         NA         NA         NA
## theta[531]         NA         NA         NA
## theta[532]         NA         NA         NA
## theta[533]         NA         NA         NA
## theta[534]         NA         NA         NA
## theta[535]         NA         NA         NA
## theta[536]         NA         NA         NA
## theta[537]         NA         NA         NA
## theta[538]         NA         NA         NA
## theta[539]         NA         NA         NA
## theta[540]         NA         NA         NA
## theta[541]         NA         NA         NA
## theta[542]         NA         NA         NA
## theta[543]         NA         NA         NA
## theta[544]         NA         NA         NA
## theta[545]         NA         NA         NA
## theta[546]         NA         NA         NA
## theta[547]         NA         NA         NA
## theta[548]         NA         NA         NA
## theta[549]         NA         NA         NA
## theta[550]         NA         NA         NA
## theta[551]         NA         NA         NA
## theta[552]         NA         NA         NA
## theta[553]         NA         NA         NA
## theta[554]         NA         NA         NA
## theta[555]         NA         NA         NA
## theta[556]         NA         NA         NA
## theta[557]         NA         NA         NA
## theta[558]         NA         NA         NA
## theta[559]         NA         NA         NA
## theta[560]         NA         NA         NA
## theta[561]         NA         NA         NA
## theta[562]         NA         NA         NA
## theta[563]         NA         NA         NA
## theta[564]         NA         NA         NA
## theta[565]         NA         NA         NA
## theta[566]         NA         NA         NA
## theta[567]         NA         NA         NA
## theta[568]         NA         NA         NA
## theta[569]         NA         NA         NA
## theta[570]         NA         NA         NA
## theta[571]         NA         NA         NA
## theta[572]         NA         NA         NA
## theta[573]         NA         NA         NA
## theta[574]         NA         NA         NA
## theta[575]         NA         NA         NA
## theta[576]         NA         NA         NA
## theta[577]         NA         NA         NA
## theta[578]         NA         NA         NA
## theta[579]         NA         NA         NA
## theta[580]         NA         NA         NA
## theta[581]         NA         NA         NA
## theta[582]         NA         NA         NA
## theta[583]         NA         NA         NA
## theta[584]         NA         NA         NA
## theta[585]         NA         NA         NA
## theta[586]         NA         NA         NA
## theta[587]         NA         NA         NA
## theta[588]         NA         NA         NA
## theta[589]         NA         NA         NA
## theta[590]         NA         NA         NA
## theta[591]         NA         NA         NA
## theta[592]         NA         NA         NA
## theta[593]         NA         NA         NA
## theta[594]         NA         NA         NA
## theta[595]         NA         NA         NA
## theta[596]         NA         NA         NA
## theta[597]         NA         NA         NA
## theta[598]         NA         NA         NA
## theta[599]         NA         NA         NA
## theta[600]         NA         NA         NA
## theta[601]         NA         NA         NA
## theta[602]         NA         NA         NA
## theta[603]         NA         NA         NA
## theta[604]         NA         NA         NA
## theta[605]         NA         NA         NA
## theta[606]         NA         NA         NA
## theta[607]         NA         NA         NA
## theta[608]         NA         NA         NA
## theta[609]         NA         NA         NA
## theta[610]         NA         NA         NA
## theta[611]         NA         NA         NA
## theta[612]         NA         NA         NA
## theta[613]         NA         NA         NA
## theta[614]         NA         NA         NA
## theta[615]         NA         NA         NA
## theta[616]         NA         NA         NA
## theta[617]         NA         NA         NA
## theta[618]         NA         NA         NA
## theta[619]         NA         NA         NA
## theta[620]         NA         NA         NA
## theta[621]         NA         NA         NA
## theta[622]         NA         NA         NA
## theta[623]         NA         NA         NA
## theta[624]         NA         NA         NA
## theta[625]         NA         NA         NA
## theta[626]         NA         NA         NA
## theta[627]         NA         NA         NA
## theta[628]         NA         NA         NA
## theta[629]         NA         NA         NA
## theta[630]         NA         NA         NA
## theta[631]         NA         NA         NA
## theta[632]         NA         NA         NA
## theta[633]         NA         NA         NA
## theta[634]         NA         NA         NA
## theta[635]         NA         NA         NA
## theta[636]         NA         NA         NA
## theta[637]         NA         NA         NA
## theta[638]         NA         NA         NA
## theta[639]         NA         NA         NA
## theta[640]         NA         NA         NA
## theta[641]         NA         NA         NA
## theta[642]         NA         NA         NA
## theta[643]         NA         NA         NA
## theta[644]         NA         NA         NA
## theta[645]         NA         NA         NA
## theta[646]         NA         NA         NA
## theta[647]         NA         NA         NA
## theta[648]         NA         NA         NA
## theta[649]         NA         NA         NA
## theta[650]         NA         NA         NA
## theta[651]         NA         NA         NA
## theta[652]         NA         NA         NA
## theta[653]         NA         NA         NA
## theta[654]         NA         NA         NA
## theta[655]         NA         NA         NA
## theta[656]         NA         NA         NA
## theta[657]         NA         NA         NA
## theta[658]         NA         NA         NA
## theta[659]         NA         NA         NA
## theta[660]         NA         NA         NA
## theta[661]         NA         NA         NA
## theta[662]         NA         NA         NA
## theta[663]         NA         NA         NA
## theta[664]         NA         NA         NA
## theta[665]         NA         NA         NA
## theta[666]         NA         NA         NA
## theta[667]         NA         NA         NA
## theta[668]         NA         NA         NA
## theta[669]         NA         NA         NA
## theta[670]         NA         NA         NA
## theta[671]         NA         NA         NA
## theta[672]         NA         NA         NA
## theta[673]         NA         NA         NA
## theta[674]         NA         NA         NA
## theta[675]         NA         NA         NA
## theta[676]         NA         NA         NA
## theta[677]         NA         NA         NA
## theta[678]         NA         NA         NA
## theta[679]         NA         NA         NA
## theta[680]         NA         NA         NA
## theta[681]         NA         NA         NA
## theta[682]         NA         NA         NA
## theta[683]         NA         NA         NA
## theta[684]         NA         NA         NA
## theta[685]         NA         NA         NA
## theta[686]         NA         NA         NA
## theta[687]         NA         NA         NA
## theta[688]         NA         NA         NA
## theta[689]         NA         NA         NA
## theta[690]         NA         NA         NA
## theta[691]         NA         NA         NA
## theta[692]         NA         NA         NA
## theta[693]         NA         NA         NA
## theta[694]         NA         NA         NA
## theta[695]         NA         NA         NA
## theta[696]         NA         NA         NA
## theta[697]         NA         NA         NA
## theta[698]         NA         NA         NA
## theta[699]         NA         NA         NA
## theta[700]         NA         NA         NA
## theta[701]         NA         NA         NA
## theta[702]         NA         NA         NA
## theta[703]         NA         NA         NA
## theta[704]         NA         NA         NA
## theta[705]         NA         NA         NA
## theta[706]         NA         NA         NA
## theta[707]         NA         NA         NA
## theta[708]         NA         NA         NA
## theta[709]         NA         NA         NA
## theta[710]         NA         NA         NA
## theta[711]         NA         NA         NA
## theta[712]         NA         NA         NA
## theta[713]         NA         NA         NA
## theta[714]         NA         NA         NA
## theta[715]         NA         NA         NA
## theta[716]         NA         NA         NA
## theta[717]         NA         NA         NA
## theta[718]         NA         NA         NA
## theta[719]         NA         NA         NA
## theta[720]         NA         NA         NA
## theta[721]         NA         NA         NA
## theta[722]         NA         NA         NA
## theta[723]         NA         NA         NA
## theta[724]         NA         NA         NA
## theta[725]         NA         NA         NA
## theta[726]         NA         NA         NA
## theta[727]         NA         NA         NA
## theta[728]         NA         NA         NA
## theta[729]         NA         NA         NA
## theta[730]         NA         NA         NA
## theta[731]         NA         NA         NA
## theta[732]         NA         NA         NA
## theta[733]         NA         NA         NA
## theta[734]         NA         NA         NA
## theta[735]         NA         NA         NA
## theta[736]         NA         NA         NA
## theta[737]         NA         NA         NA
## theta[738]         NA         NA         NA
## theta[739]         NA         NA         NA
## theta[740]         NA         NA         NA
## theta[741]         NA         NA         NA
## theta[742]         NA         NA         NA
## theta[743]         NA         NA         NA
## theta[744]         NA         NA         NA
## theta[745]         NA         NA         NA
## theta[746]         NA         NA         NA
## theta[747]         NA         NA         NA
## theta[748]         NA         NA         NA
## theta[749]         NA         NA         NA
## theta[750]         NA         NA         NA
## theta[751]         NA         NA         NA
## theta[752]         NA         NA         NA
## theta[753]         NA         NA         NA
## theta[754]         NA         NA         NA
## theta[755]         NA         NA         NA
## theta[756]         NA         NA         NA
## theta[757]         NA         NA         NA
## theta[758]         NA         NA         NA
## theta[759]         NA         NA         NA
## theta[760]         NA         NA         NA
## theta[761]         NA         NA         NA
## theta[762]         NA         NA         NA
## theta[763]         NA         NA         NA
## theta[764]         NA         NA         NA
## theta[765]         NA         NA         NA
## theta[766]         NA         NA         NA
## theta[767]         NA         NA         NA
## theta[768]         NA         NA         NA
## theta[769]         NA         NA         NA
## theta[770]         NA         NA         NA
## theta[771]         NA         NA         NA
## theta[772]         NA         NA         NA
## theta[773]         NA         NA         NA
## theta[774]         NA         NA         NA
## theta[775]         NA         NA         NA
## theta[776]         NA         NA         NA
## theta[777]         NA         NA         NA
## theta[778]         NA         NA         NA
## theta[779]         NA         NA         NA
## theta[780]         NA         NA         NA
## theta[781]         NA         NA         NA
## theta[782]         NA         NA         NA
## theta[783]         NA         NA         NA
## theta[784]         NA         NA         NA
## theta[785]         NA         NA         NA
## theta[786]         NA         NA         NA
## theta[787]         NA         NA         NA
## theta[788]         NA         NA         NA
## theta[789]         NA         NA         NA
## theta[790]         NA         NA         NA
## theta[791]         NA         NA         NA
## theta[792]         NA         NA         NA
## theta[793]         NA         NA         NA
## theta[794]         NA         NA         NA
## theta[795]         NA         NA         NA
## theta[796]         NA         NA         NA
## theta[797]         NA         NA         NA
## theta[798]         NA         NA         NA
## theta[799]         NA         NA         NA
## theta[800]         NA         NA         NA
## theta[801]         NA         NA         NA
## theta[802]         NA         NA         NA
## theta[803]         NA         NA         NA
## theta[804]         NA         NA         NA
## theta[805]         NA         NA         NA
## theta[806]         NA         NA         NA
## theta[807]         NA         NA         NA
## theta[808]         NA         NA         NA
## theta[809]         NA         NA         NA
## theta[810]         NA         NA         NA
## theta[811]         NA         NA         NA
## theta[812]         NA         NA         NA
## theta[813]         NA         NA         NA
## theta[814]         NA         NA         NA
## theta[815]         NA         NA         NA
## theta[816]         NA         NA         NA
## theta[817]         NA         NA         NA
## theta[818]         NA         NA         NA
## theta[819]         NA         NA         NA
## theta[820]         NA         NA         NA
## theta[821]         NA         NA         NA
## theta[822]         NA         NA         NA
## theta[823]         NA         NA         NA
## theta[824]         NA         NA         NA
## theta[825]         NA         NA         NA
## theta[826]         NA         NA         NA
## theta[827]         NA         NA         NA
## theta[828]         NA         NA         NA
## theta[829]         NA         NA         NA
## theta[830]         NA         NA         NA
## theta[831]         NA         NA         NA
## theta[832]         NA         NA         NA
## theta[833]         NA         NA         NA
## theta[834]         NA         NA         NA
## theta[835]         NA         NA         NA
## theta[836]         NA         NA         NA
## theta[837]         NA         NA         NA
## theta[838]         NA         NA         NA
## theta[839]         NA         NA         NA
## theta[840]         NA         NA         NA
## theta[841]         NA         NA         NA
## theta[842]         NA         NA         NA
## theta[843]         NA         NA         NA
## theta[844]         NA         NA         NA
## theta[845]         NA         NA         NA
## theta[846]         NA         NA         NA
## theta[847]         NA         NA         NA
## theta[848]         NA         NA         NA
## theta[849]         NA         NA         NA
## theta[850]         NA         NA         NA
## theta[851]         NA         NA         NA
## theta[852]         NA         NA         NA
## theta[853]         NA         NA         NA
## theta[854]         NA         NA         NA
## theta[855]         NA         NA         NA
## theta[856]         NA         NA         NA
## theta[857]         NA         NA         NA
## theta[858]         NA         NA         NA
## theta[859]         NA         NA         NA
## theta[860]         NA         NA         NA
## theta[861]         NA         NA         NA
## theta[862]         NA         NA         NA
## theta[863]         NA         NA         NA
## theta[864]         NA         NA         NA
## theta[865]         NA         NA         NA
## theta[866]         NA         NA         NA
## theta[867]         NA         NA         NA
## theta[868]         NA         NA         NA
## theta[869]         NA         NA         NA
## theta[870]         NA         NA         NA
## theta[871]         NA         NA         NA
## theta[872]         NA         NA         NA
## theta[873]         NA         NA         NA
## theta[874]         NA         NA         NA
## theta[875]         NA         NA         NA
## theta[876]         NA         NA         NA
## theta[877]         NA         NA         NA
## theta[878]         NA         NA         NA
## theta[879]         NA         NA         NA
## theta[880]         NA         NA         NA
## theta[881]         NA         NA         NA
## theta[882]         NA         NA         NA
## theta[883]         NA         NA         NA
## theta[884]         NA         NA         NA
## theta[885]         NA         NA         NA
## theta[886]         NA         NA         NA
## theta[887]         NA         NA         NA
## theta[888]         NA         NA         NA
## theta[889]         NA         NA         NA
## theta[890]         NA         NA         NA
## theta[891]         NA         NA         NA
## theta[892]         NA         NA         NA
## theta[893]         NA         NA         NA
## theta[894]         NA         NA         NA
## theta[895]         NA         NA         NA
## theta[896]         NA         NA         NA
## theta[897]         NA         NA         NA
## theta[898]         NA         NA         NA
## theta[899]         NA         NA         NA
## theta[900]         NA         NA         NA
## theta[901]         NA         NA         NA
## theta[902]         NA         NA         NA
## theta[903]         NA         NA         NA
## theta[904]         NA         NA         NA
## theta[905]         NA         NA         NA
## theta[906]         NA         NA         NA
## theta[907]         NA         NA         NA
## theta[908]         NA         NA         NA
## theta[909]         NA         NA         NA
## theta[910]         NA         NA         NA
## theta[911]         NA         NA         NA
## theta[912]         NA         NA         NA
## theta[913]         NA         NA         NA
## theta[914]         NA         NA         NA
## theta[915]         NA         NA         NA
## theta[916]         NA         NA         NA
## theta[917]         NA         NA         NA
## theta[918]         NA         NA         NA
## theta[919]         NA         NA         NA
## theta[920]         NA         NA         NA
## theta[921]         NA         NA         NA
## theta[922]         NA         NA         NA
## theta[923]         NA         NA         NA
## theta[924]         NA         NA         NA
## theta[925]         NA         NA         NA
## theta[926]         NA         NA         NA
## theta[927]         NA         NA         NA
## theta[928]         NA         NA         NA
## theta[929]         NA         NA         NA
## theta[930]         NA         NA         NA
## theta[931]         NA         NA         NA
## theta[932]         NA         NA         NA
## theta[933]         NA         NA         NA
## theta[934]         NA         NA         NA
## theta[935]         NA         NA         NA
## theta[936]         NA         NA         NA
## theta[937]         NA         NA         NA
## theta[938]         NA         NA         NA
## theta[939]         NA         NA         NA
## theta[940]         NA         NA         NA
## theta[941]         NA         NA         NA
## theta[942]         NA         NA         NA
## theta[943]         NA         NA         NA
## theta[944]         NA         NA         NA
## theta[945]         NA         NA         NA
## theta[946]         NA         NA         NA
## theta[947]         NA         NA         NA
## theta[948]         NA         NA         NA
```

```r
# Display posterior information:
plotMCMC(mcmcCoda, data=myData,
        zName="Hits", NName="AtBats", sName="Player", cName="PriPos",
        compVal=NULL ,
        diffCList=list( c("Pitcher", "Catcher"), c("Catcher","1st Base") ),
        diffSList=list( c("Kyle Blanks","Bruce Chen"),
                        c("Mike Leake","Wandy Rodriguez"),
                        c("Andrew McCutchen", "Brett Jackson"),
                        c("ShinSoo Choo","Ichiro Suzuki") ),
        compValDiff=0.0)
```

![output9.11](outputs/output9.11.png)

**Output 9.11: same as Figure 9.14 left panel**

![output9.1](outputs/output9.12.png)

**Output 9.12: same as Figure 9.14 right panel**

![output9.13](outputs/output9.13.png)

**Output 9.13: same as Figure 9.15 left panel**

![output9.14](outputs/output9.14.png)

**Output 9.14: same as Figure 9.16 left panel**

![output9.15](outputs/output9.15.png)

**Output 9.15: same as Figure 9.16 right panel**

![output9.16](outputs/output9.16.png)

**Output 9.16: same as Figure 9.15 right panel**


![Figure 9.13](img/fig9.13.png)


### 9.5.1 Example: Baseball batting abilities by position [book's blog](http://doingbayesiandataanalysis.blogspot.com/2012/11/shrinkage-in-multi-level-hierarchical.html)

![Figure 9.14](img/fig9.14.png)

=> Position-level batting abilities ($\omega$ in modeling)

![Figure 9.15](img/fig9.15.png)

=> Abilites of selected individuals with identical batting records

![Figure 9.16](img/fig9.16.png)

=> Same position with different batting records

**Summary of example**
shrinkage of individual-ability estimates based on category (fielding position) </br>
: Players with many at-bats (large $N_{s}$) had somewhat less shrinkage of their individual estimates </br>
than players with few at-bats (small $N_{s}$), who had estimates dominated by the position information.

If not categorize by position, </br>
the estimated abilities of any two players with identical batting records </br>
would also be identical regardless of their positions.

=> The parameter estimates are meaningful descriptions of the data only in the
context of the model structure.


![Figure 9.17](img/fig9.17.png)

9 position parameters ($\omega_{c}$) contributing to the overall mode ($\omega$), </br>
but dozens or hundreds of players ($\theta_{s}$) contributing to each position ($\omega_{c}$).

=> The certainty of estimate at the overall level is less than the certainty of estimate
within each position.

=> If there are only a few categories, the overall level typically is not estimated very precisely.

Test for all the 968 parameters: </br>
In traditional statistical testing based on p-values, we would pay a penalty for even intending to make more comparisons. </br>
In a Bayesian analysis, decisions are based on the posterior distribution, which is based only on the data </br>
(and the prior)

## 9.6. EXERCISES
Look for more exercises at https://sites.google.com/site/doingbayesiandataanalysis/



