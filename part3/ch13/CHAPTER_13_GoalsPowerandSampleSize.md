

    options(jupyter.plot_mimetypes = 'image/png')

#CHAPTER 13 Goals, Power, and Sample Size
##13.1 The Will to Power
- 13.1.1 Goals and obstacles
- 13.1.2 Power
- 13.1.3 Sample size
- 13.1.4 Other expressions of goals

##13.2 Computing Power and Sample Size
- 13.2.1 When the goal is to exclude a null value
- 13.2.2 Formal solution and implementation in R
- 13.2.3 When the goal is precision
- 13.2.4 Monte Carlo approximation of power
- 13.2.5 Power from idealized or actual data

##13.3 Sequential Testing and the Goal of Precision
- 13.3.1 Examples of sequential tests
- 13.3.2 Average behavior of sequential tests

##13.4 Discussion
- 13.4.1 Power and multiple comparisons
- 13.4.2 Power: prospective, retrospective, and replication
- 13.4.3 Power analysis requires verisimilitude of simulated data
- 13.4.4 The importance of planning

##13.5 Exercises

## Power(검정력) : 2종 오류를 B라고 하면 1-B
### 2종 오류 : 귀무가설이 틀림에도 불구하고 채택해버리는 오류 
### 가설검정의 목적은 귀무가설이 틀렸다는 것을 보이고 싶은것이기 때문에 귀무가설이 틀렸다는 것을 잘 예측하는 것을 검정력이라고 부름
## ![Painting](http://www.ktword.co.kr/img_data/5094_1.JPG)

### 샘플 사이즈가 커지면 검정 통계량 값이 일반적으로 커지기 때문에 더욱 쉽게 귀무가설을 기각할 수 있음

### NHST(null hypothesis significance testing)
## ![Painting](http://4.bp.blogspot.com/-DzrRZxxGFgI/VVPphwHvJYI/AAAAAAAABYE/wka9i7-U9Ps/s640/TwoTrendsConverge.png)

### 이 장에서는 검정력의 정의와 계산방법에 대하여 논의함!!

## 13.1. The Will to Power
### 13.1.1 Goals and obstacles
+ drug? placebo? the rate of recovery for patients who tack a drug is higher than the rate of recovery for patients who take a placebo.
    - Goal : null value(zero difference) is not tenable
+ Specific effect size (quantitative theory), the curvature(곡률) of light around a massive object
    - Goal : specific value is tenable.

### **Formalized In terms of the Highest density interval**
+ Goal : Reject a null value of a parameter.
    - Formal expression : Show that a region of practical equivalence (ROPE) around the null value excludes the posterior 95% HDI
+ Goal : Affirm a predicted value of a parameter.
    - Formal expression : Show that a ROPE around the predicted value includes the posterior 95% HDI.
+ Goal : Achieve precision in the estimate of a parameter.
    - Formal expression : Show that the posterior 95% HDI has width less than a specified maximum.

+ HDI : natural interpretation for purposes of parameter esitmation and measurement of precision
+ Crucial obstacle to the goals of research : random sample

## ![Painting](http://image.slidesharecdn.com/ch13-130831070746-phpapp02/95/chapter13-goals-power-and-sample-size-doing-bayesian-data-analysis-a-tutorial-with-r-and-bugs-5-1024.jpg?cb=1377932940)

### 13.1.2 Power
+ Power : (In traditional) Rejecting the null hyothesis [one conventional sampling plan)
     - Reduce measurement noise (other influences, 약 투여시간, 식사의 변화 등)
     - Increase the chance of detecting an effect (large a dose, 약을 가능한 투여)
     - Increase the sample size 

+ The flow of information in a power analysis
    - 1. From the Hypothetical distribution of parameter values, randomly generate representative values. (데이터 생성기로 무작위 샘플 데이터 생성)
    - 2. From the representative parameter values, generate a random sample of data, using the planned sampling method. 
    - 3. From the simulated of data, compute the posterior estimate, using Bayesian analysis with audience-appropriate priors.
    - 4. From the posterior estimate, tally whether or not the goals were attained.
    - 5. Repeat the above steps many times, to approximate the power.
    - 데이터 생성기로 무작위 샘플 데이터 생성-> 사전 분포에 기초 베이지안에 의한 사후 확률을 계산 -> 사후 추정에서 목표 달성 여부를 집계. 95% HDI이 귀무가설 값 부근의 ROPE 설정폭보다 좁은지... -> 위를 반복하여 검출력은 목적이 달성된 횟수에 비례

![Painting](bayesian1.jpg)

### 13.1.3 Sample Size
+ Minimal Sample size : A large sample -> likelihood function get narrower -> posterior distribution narrower 
+ The best that a large sample can do is exactly reflect the data generating distribution
+ Bayesian approach to the goal of precision = Frequentist. But Bayesian HDIs do not suffer from the instability of frequentist confidence intervals when testing or sampling intentions change
+ 샘플 크기를 올리면 검출력이 상승하지만 비용이 듬. 검출력을 충족하는 최소 샘플 크기를 알고 늘어나면 우도 함수가 좁아질 수 있기 때문에 사후분포가 좁아짐. 통계라는 것은 한정된 샘플 데이터에서 아직 보지 못한 전체를 보는 것 검출력이 증가할수록 샘플 크기는크게 증가. 

### 13.1.4 Ohter expressions of goals
+ Average length criterion 
+ Entroy of a distribution (Uniform distribution : Has maximal entropy)

## 13.2. Computing Power and Sample size
### 13.2.1 When the goal is to exclude a null value
+ Our goal : Coin is unfair? (95% HDI excluds a ROPE around theta = 0.5)
    - HDI : 믿음의 분포를 요약하는 방법, 가장 신뢰하는 값의 구간 (믿음의 분포 대부분 95%를 차지하는 구간)
    - ROPE : Region Of Practical Equivalence 모수치가 영가설이 진술하는 값과 현실적으로 구분하기 힘든 특정 구간 안에 있다. 모수치가 -0.01~0.01 사이의 값이다. 
+ 동전 페어임을 기대하는 확률이 0.5지만 사실 0.49~0.51이라고 하는 것도 문제가 없다. (ROPE) 95% HDI 특정 폭에 들면 된다. 아무리 정밀하게 만들어진 동전도 50%라고 단언할 수 없고 왜곡된 동전도 10번 던져서 5번 나올 수 있음
+ equivalent prior sample method
+ Table 13.1 (the minimal sample size needed for the 95% HDI to exclude theta = 0.5 when flipping a single coin) 0.65%의 확률로 왜곡되어 있는 믿을 수 있는 동전. 동전의 표가 나올 확률 분포의 95% HDI가 0.5의 외부에 있는 것을 나타나기 위해 필요한 샘플 크기의 수!!
+ data-generating hypothesis (coin has a bias very near theta = 0.65)
+ beta distribution (shape parameters : 1300, 700)
+ ## ![Painting](http://image.slidesharecdn.com/ch13-130831070746-phpapp02/95/chapter13-goals-power-and-sample-size-doing-bayesian-data-analysis-a-tutorial-with-r-and-bugs-13-1024.jpg?cb=1377932940)


### 13.2.2 Formal solution and implementation in R


    minNforHDIpower = function( genPriorMode , genPriorN ,
                                HDImaxwid=NULL , nullVal=NULL , 
                                ROPE=c(max(0,nullVal-.02),min(1,nullVal+.02)) ,
                                desiredPower=0.8 , audPriorMode=0.5 , audPriorN=2 ,
                                HDImass=0.95 , initSampSize=20 , verbose=TRUE ) {
      # Check for argument consistency:
      if ( !xor( is.null(HDImaxwid) , is.null(nullVal) ) ) {
        stop("One and only one of HDImaxwid and nullVal must be specified.")
      }
      # Load HDIofICDF function if not already present:
      if ( !exists("HDIofICDF") ) source("DBDA2E-utilities.R")
      # Convert prior mode and N to a,b parameters of beta distribution:
      genPriorA = genPriorMode * (genPriorN-2) + 1
      genPriorB = ( 1.0 - genPriorMode ) * (genPriorN-2) + 1
      audPriorA = audPriorMode * (audPriorN-2) + 1
      audPriorB = ( 1.0 - audPriorMode ) * (audPriorN-2) + 1
      # Initialize loop for incrementing sampleSize:
      sampleSize = initSampSize
      notPowerfulEnough = TRUE
      # Increment sampleSize until desired power is achieved:
      while( notPowerfulEnough ) {
        zvec = 0:sampleSize # vector of all possible z values for N flips.
        # Compute probability of each z value for data-generating prior:
        pzvec = exp( lchoose( sampleSize , zvec )
                     + lbeta( zvec + genPriorA , sampleSize-zvec + genPriorB )
                     - lbeta( genPriorA , genPriorB ) )
        # For each z value, compute posterior HDI: 
        # hdiMat will hold HDI limits for each z:
        hdiMat = matrix( 0 , nrow=length(zvec) , ncol=2 )
        for ( zIdx in 1:length(zvec) ) {
          z = zvec[zIdx]
          hdiMat[zIdx,] = HDIofICDF( qbeta , 
                                     shape1 = z + audPriorA ,
                                     shape2 = sampleSize - z + audPriorB ,
                                     credMass = HDImass )
        }
        # Compute HDI widths:
        hdiWid = hdiMat[,2] - hdiMat[,1]
        # Sum the probabilities of outcomes with satisfactory HDI widths:
        if ( !is.null( HDImaxwid ) ) {
          powerHDI = sum( pzvec[ hdiWid < HDImaxwid ] )
        }
        # Sum the probabilities of outcomes with HDI excluding ROPE:
        if ( !is.null( nullVal ) ) {
          powerHDI = sum( pzvec[ hdiMat[,1] > ROPE[2] | hdiMat[,2] < ROPE[1] ] )
        }
        if ( verbose ) {
          cat( " For sample size = ", sampleSize , ", power = " , powerHDI ,
               "\n" , sep="" ) ; flush.console()
        }
        if ( powerHDI > desiredPower ) {  # If desired power is attained,
          notPowerfulEnough = FALSE       # set flag to stop,
        } else {                          # otherwise
          sampleSize = sampleSize + 1     # increment the sample size.
        }
      } # End while( notPowerfulEnough ).
      # Return the sample size that achieved the desired power:
      return( sampleSize )
    } # End of function.


    #http://sourceforge.net/projects/mcmc-jags/ jags 설치


    #install.packages(c("rjags", "runjags"), repos="http://cran.rstudio.com/ ")


    library(rjags)
    library(runjags)

    Loading required package: coda
    Linked to JAGS 3.4.0
    Loaded modules: basemod,bugs
    


    sameSize = minNforHDIpower(genPriorMode = 0.75, genPriorN = 2000, HDImaxwid=NULL, nullVal=0.5, 
                               ROPE=c(0.48,0.52), desiredPower=0.8, audPriorMode=0.5, audPriorN=2, 
                               HDImass=0.95, initSampSize=5, verbose=TRUE)

     For sample size = 5, power = 0.2383007
     For sample size = 6, power = 0.1783183
     For sample size = 7, power = 0.4460128
     For sample size = 8, power = 0.36732
     For sample size = 9, power = 0.30045
     For sample size = 10, power = 0.2441963
     For sample size = 11, power = 0.4549477
     For sample size = 12, power = 0.3905216
     For sample size = 13, power = 0.3326128
     For sample size = 14, power = 0.5207247
     For sample size = 15, power = 0.4608513
     For sample size = 16, power = 0.6291068
     For sample size = 17, power = 0.5729475
     For sample size = 18, power = 0.5179317
     For sample size = 19, power = 0.6663597
     For sample size = 20, power = 0.6159196
     For sample size = 21, power = 0.5655352
     For sample size = 22, power = 0.6976802
     For sample size = 23, power = 0.6521637
     For sample size = 24, power = 0.606033
     For sample size = 25, power = 0.7245362
     For sample size = 26, power = 0.6832871
     For sample size = 27, power = 0.7836981
     For sample size = 28, power = 0.7479021
     For sample size = 29, power = 0.7103786
     For sample size = 30, power = 0.8009259
    

### 13.2.3 When the goal in precision
+ You are interested in assessing the preferences of the general population regrading political cnadidates A and B


    sameSize = minNforHDIpower(genPriorMode = 0.75, genPriorN = 10, HDImaxwid=0.2, nullVal=NULL, 
                               ROPE=NULL, desiredPower=0.8, audPriorMode=0.5, audPriorN=2, 
                               HDImass=0.95, initSampSize=50, verbose=TRUE)

     For sample size = 50, power = 0.2144336
     For sample size = 51, power = 0.2063202
     For sample size = 52, power = 0.1985948
     For sample size = 53, power = 0.234573
     For sample size = 54, power = 0.2263021
     For sample size = 55, power = 0.2618712
     For sample size = 56, power = 0.2531572
     For sample size = 57, power = 0.2448051
     For sample size = 58, power = 0.279018
     For sample size = 59, power = 0.2703056
     For sample size = 60, power = 0.3038038
     For sample size = 61, power = 0.2948081
     For sample size = 62, power = 0.3274775
     For sample size = 63, power = 0.3182657
     For sample size = 64, power = 0.3500334
     For sample size = 65, power = 0.3406634
     For sample size = 66, power = 0.3714873
     For sample size = 67, power = 0.4017013
     For sample size = 68, power = 0.3918694
     For sample size = 69, power = 0.4210687
     For sample size = 70, power = 0.4112197
     For sample size = 71, power = 0.4394204
     For sample size = 72, power = 0.4668602
     For sample size = 73, power = 0.45681
     For sample size = 74, power = 0.4832719
     For sample size = 75, power = 0.5089359
     For sample size = 76, power = 0.5337822
     For sample size = 77, power = 0.5235513
     For sample size = 78, power = 0.5474934
     For sample size = 79, power = 0.5706373
     For sample size = 80, power = 0.5929882
     For sample size = 81, power = 0.6145578
     For sample size = 82, power = 0.6353626
     For sample size = 83, power = 0.6554231
     For sample size = 84, power = 0.6747629
     For sample size = 85, power = 0.6934076
     For sample size = 86, power = 0.7113842
     For sample size = 87, power = 0.7287209
     For sample size = 88, power = 0.7716517
     For sample size = 89, power = 0.787177
     For sample size = 90, power = 0.8266938
    

### 13.2.4 Monte Carlo approximation of power


    source("Jags-Ydich-Xnom1subj-MbernBeta.R")

    
    *********************************************************************
    Kruschke, J. K. (2015). Doing Bayesian Data Analysis, Second Edition:
    A Tutorial with R, JAGS, and Stan. Academic Press / Elsevier.
    *********************************************************************
    
    


    goalAchievedForSample = function (data ) {
        #Generate the MCMC chain:
        mcmcCoda = genMCMC(data = data, numSavedSteps=10000, saveName=NULL)
        #Check goal achievement, First, Compute the HDI:
        thetaHDI = HDIofMCMC(as.matrix(mcmcCoda[,"theta"]))
        #Define list for recording results:
        goalAchieved = list()
        #Goal: Exclude ROPE around null value:
        thetaROPE = c(0.48, 0.52)
        goalAchieved = c(goalAchieved, "ExcludeROPE"=(thetaHDI[1] > thetaROPE[2] | thetaHDI[2] < thetaROPE[1]) )
        #Goal : HDI less than max width:
        thetaHDImaxWid = 0.2
        goalAchieved = c(goalAchieved, "NarrowHDI"=(thetaHDI[2]-thetaHDI[1] < thetaHDImaxWid))
        #More goals can be inserted here if wanted...
        #Return list of goal results:
        return(goalAchieved)
    }


    #Specify mode and concentration of hypothetical parameter distribution:
    omega = 0.70
    kappa = 2000
    #Specify sample size for each simulated date set:
    sampleN = 74
    # Run a bunch of simulated experiments:
    nSimulatedDateSets = 1000 #An arbitrary large number.
    for (simIdx in 1:nSimulatedDateSets){
        #Generate random value from hypothesized parameter distribution
        genTheta = rbeta(1, omega*(kappa-2)+1, (1-omega)*(kappa-2)+1 )
        #Generate random data based on parameter value:
        sampleZ = rbinom(1, size=sampleN, prob=genTheta)
        #Convert to vector of 0's and 1's for delivery to JAGS function:
        simulatedData = c(rep(1,sampleZ), rep(0,sampleN-sampleZ))
        #Do Bayesian analysis on simulated data:
        goalAchieved = goalAchievedForSample(simulatedData)
        #Tally the results:
        if (!exists("goalTally")) { # if goalTally does not exist, create it
            goalTally = matrix(nrow=0, ncol=length(goalAchieved))
        }
        goalTally = rbind(goalTally, goalAchieved)
    }

    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 77
    
    Initializing model
    
    Burning in the MCMC chain...
    Sampling final MCMC chain...
    


    #For each goal...
    for (goalIdx in 1:NCOL(goalTally)){
        #Extract the goal name for subsequent display:
        goalName = colnames(goalTally)[goalIdx]
        #Compute number of successes:
        goalHits = sum(unlist(goalTally[,goalIdx]))
        #Compute number of attempts:
        goalAttempts = NROW(goalTally)
        #Compute proportion of successes:
        goalEst = goalHits/goalAttempts
        #Compute HDI around proportion:
        goalEstHDI = HDIofICDF(qbeta, shape=1+goalHits, shape2=1+goalAttempts-goalHits)
        #Display the result:
        show(paste0(goalName, ": Est.Power=", round(goalEst, 3), "; Low Bound=", round(goalEstHDI[1],3), "; High Bound=", round(goalEstHDI[2],3)))
    }

    [1] "ExcludeROPE: Est.Power=0.906; Low Bound=0.887; High Bound=0.923"
    [1] "NarrowHDI: Est.Power=0.389; Low Bound=0.359; High Bound=0.419"
    

### 13.2.5 Power from idealized or actual data


    #source("Jags-Ydich-XnomSsubj-MbinomBetaOmegaKappa-Power.R")

    
    *********************************************************************
    Kruschke, J. K. (2015). Doing Bayesian Data Analysis, Second Edition:
    A Tutorial with R, JAGS, and Stan. Academic Press / Elsevier.
    *********************************************************************
    
    Calling 3 simulations using the parallel method...
    Following the progress of chain 1 (the program will wait for all chains
    to finish before continuing):
    Welcome to JAGS 3.4.0 on Wed Jul 22 14:24:54 2015
    JAGS is free software and comes with ABSOLUTELY NO WARRANTY
    Loading module: basemod: ok
    Loading module: bugs: ok
    . . Reading data file data.txt
    . Compiling model graph
       Resolving undeclared variables
       Allocating nodes
       Graph Size: 314
    . Reading parameter file inits1.txt
    . Initializing model
    . Adapting 500
    -------------------------------------------------| 500
    ++++++++++++++++++++++++++++++++++++++++++++++++++ 100%
    Adaptation successful
    . Updating 500
    -------------------------------------------------| 500
    ************************************************** 100%
    . . . . Updating 13340
    -------------------------------------------------| 13300
    ************************************************** 100%
    * 100%
    . . . . Updating 0
    . Deleting model
    . 
    All chains have finished
    Simulation complete.  Reading coda files...
    Coda files loaded successfully
    Finished running the simulation
    


    idealGroupMean = 0.65
    idealGroupSD = 0.07
    idealNsubj = 100
    idealNtrlPerSubj = 100


    #Generate random theta values for idealized subjects:
    betaAB = betaABfromMeanSD(idealGroupMean, idealGroupSD)
    theta = rbeta(idealNsubj, betaAB$a, betaAB$b)
    #Transform the theta values to exactly match idealized mean, SD:
    theta = ((theta-mean(theta))/sd(theta))*idealGroupSD + idealGroupMean
    theta[theta >= 0.999 ] = 0.999
    theta[theta <= 0.001 ] = 0.001
    #Generate Idealized data very close to theta's:
    z = round(theta*idealNtrlPerSubj)
    #Convert to data format needed by JAGS function:
    #Set up an empth matrix for holding the data:
    dataMat = matrix(0, ncol=2, nrow=0, dimnames=list(NULL, c("y", "s")))
    #For each simulated subject,
    for (sIdx in 1:idealNsubj){
        #Create vector 0f 0's and 1's matching the z values generated above:
        yVec = c(rep(1,z[sIdx]), rep(0,idealNtrlPerSubj-z[sIdx]))
        #Bind the subject data to the bottom of the matrix:
        dataMat = rbind(dataMat, cbind(yVec, rep(sIdx, idealNtrlPerSubj)))
    }
    # Make it a data frame:
    idealDatFrm = data.frame(dataMat)


    genMCMC




<pre class=language-r><code>function (data, numSavedSteps = 50000, saveName = NULL) 
{
    require(rjags)
    if (class(data) == "data.frame") {
        y = myData$y
    }
    else {
        y = data
    }
    if (any(y != 0 &amp; y != 1)) {
        stop("All y values must be 0 or 1.")
    }
    Ntotal = length(y)
    dataList = list(y = y, Ntotal = Ntotal)
    modelString = "\n  model {\n    for ( i in 1:Ntotal ) {\n      y[i] ~ dbern( theta )\n    }\n    theta ~ dbeta( 1 , 1 )\n  }\n  "
    writeLines(modelString, con = "TEMPmodel.txt")
    initsList = function() {
        resampledY = sample(y, replace = TRUE)
        thetaInit = sum(resampledY)/length(resampledY)
        thetaInit = 0.001 + 0.998 * thetaInit
        return(list(theta = thetaInit))
    }
    parameters = c("theta")
    adaptSteps = 500
    burnInSteps = 500
    nChains = 4
    thinSteps = 1
    nIter = ceiling((numSavedSteps * thinSteps)/nChains)
    jagsModel = jags.model("TEMPmodel.txt", data = dataList, 
        inits = initsList, n.chains = nChains, n.adapt = adaptSteps)
    cat("Burning in the MCMC chain...\n")
    update(jagsModel, n.iter = burnInSteps)
    cat("Sampling final MCMC chain...\n")
    codaSamples = coda.samples(jagsModel, variable.names = parameters, 
        n.iter = nIter, thin = thinSteps)
    if (!is.null(saveName)) {
        save(codaSamples, file = paste(saveName, "Mcmc.Rdata", 
            sep = ""))
    }
    return(codaSamples)
}</code></pre>




    geneMCMC <- function (data, numSavedSteps = 50000, saveName = NULL) 
    {
        require(rjags)
        if (class(data) == "data.frame") {
            y = myData$y
        }
        else {
            y = data
        }
        if (any(y != 0 & y != 1)) {
            stop("All y values must be 0 or 1.")
        }
        Ntotal = length(y)
        dataList = list(y = y, Ntotal = Ntotal)
        modelString = "\n  model {\n    for ( i in 1:Ntotal ) {\n      y[i] ~ dbern( theta )\n    }\n    theta ~ dbeta( 1 , 1 )\n  }\n  "
        writeLines(modelString, con = "TEMPmodel.txt")
        initsList = function() {
            resampledY = sample(y, replace = TRUE)
            thetaInit = sum(resampledY)/length(resampledY)
            thetaInit = 0.001 + 0.998 * thetaInit
            return(list(theta = thetaInit))
        }
        parameters = c("theta")
        adaptSteps = 500
        burnInSteps = 500
        nChains = 4
        thinSteps = 20
        nIter = ceiling((numSavedSteps * thinSteps)/nChains)
        jagsModel = jags.model("TEMPmodel.txt", data = dataList, 
            inits = initsList, n.chains = nChains, n.adapt = adaptSteps)
        cat("Burning in the MCMC chain...\n")
        update(jagsModel, n.iter = burnInSteps)
        cat("Sampling final MCMC chain...\n")
        codaSamples = coda.samples(jagsModel, variable.names = parameters, 
            n.iter = nIter, thin = thinSteps)
        if (!is.null(saveName)) {
            save(codaSamples, file = paste(saveName, "Mcmc.Rdata", 
                sep = ""))
        }
        return(codaSamples)
    }


    #Run Bayesian analysis on idealized data:
    mcmcCoda = genMCMC(data=idealDatFrm, saveName=NULL, numSavedSteps=2000)
    #Convert coda object to matrix for convenience:
    mcmcMat = as.matrix(mcmcCoda)


    Error in genMCMC(data = idealDatFrm, saveName = NULL, numSavedSteps = 2000): 객체 'myData'를 찾을 수 없습니다
    



    Error in as.matrix(mcmcCoda): 객체 'mcmcCoda'를 찾을 수 없습니다
    


### 13.4.2 Power:propective, retrospective, and replication
####Retrospective and Replication Prospective (a apiori) power analysis : 선행 연구 (그러나 동일하지 않다)를 바탕으로 데이터 분포 가설을 얻는다. 
####Retrospective power analysis : 실제로 동일한 조건으로 취득한 데이터를 바탕으로 매개 변수를 설정한다. 이미 사후 분포를 가지고있는 셈이다. fil-con 실험은 여기에 해당한다. 
####Replication power : 반복 실험을했을 때 얼마나 확률로 목적을 달성 할 수 있는지 알고 싶습니다.
####Retrospective power analysis와 마찬가지로 실제로 같은 데이터를 바탕으로 사후 분포로부터 데이터를 얻는다. 그러나 사전 분포뿐만 아니라 원래의 데이터를 얻는다.

