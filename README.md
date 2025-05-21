    %let pgm=utl-propensity-score-matching-sas-psmatch-r-psm-package-matchit;

    %stop_submission;

    Propensity score matching sas psmatch and r psm package matchit

          Contents
             1 sas psmatch (solution uses base sas, stat and graph)
             2 r matchit
             3 options sas psmatch r matchit cobalt

    AI QUERIES
    ==========
    please provide a reproducible example of r matchit propensity score matching, psm, using a caliper
    please provide a reproducible example of sas psmatch propensity score matching with i bunary response variable

    github
    https://tinyurl.com/yc6u5eh5
    https://github.com/rogerjdeangelis/utl-propensity-score-matching-sas-psmatch-r-psm-package-matchit

    Available sas macros that do not require sas stat
    https://github.com/Jiangtang/Programming-SAS/blob/master/UserMacros/mayo/gmatch.sas
    https://people.duke.edu/~hammill/software/gmatch.sas

    communities.sas
    https://tinyurl.com/25uhcn36
    https://communities.sas.com/t5/New-SAS-User/propensity-score-matching-code/m-p/616983

    Balance covariates in two arms of a clincal trial
    Propensity scoring identifies treatment and control pairs with similar characteristics

        PROC PSMATCH does provide optimized matching:
    https://go.documentation.sas.com/doc/en/pgmsascdc/v_062/statug/statug_psmatch_details14.htm#statug_psmatch002514

    The underlying linear assignment solver is the same one that is used in PROC OPTNET, PROC OPTMODEL, PROC OPTGRAPH, and PROC OPTNETWORK.

    Comments from SAS-Lers
    ======================
    
    Richard Hockey
    What I would really like is for psmatch to have an option to enter your own matching
    criteria and do optimised matching. I have found that I have to use 3rd party macros
    which I can then edit to acheive this. The best one I've found is %vmatch from Mayo which is very fast as it uses proc optnet.


    Rob Pratt
    PROC PSMATCH does provide optimized matching:
    https://tinyurl.com/2mbnbmmc
    https://go.documentation.sas.com/doc/en/pgmsascdc/v_062/statug/statug_psmatch_details14.htm#statug_psmatch002514

    The underlying linear assignment solver is the same one that is used in
    PROC OPTNET, PROC OPTMODEL, PROC OPTGRAPH, and PROC OPTNETWORK.


    Problem create this plot

                          Standardized Mean Difference (SMD)
             -1.5      -1.0      -0.5       0.0       0.5       1.0
             --+---------+---------+---------+---------+---------+-----------------+
             | Sample Sizes:                 |              VAR     SMD   MARKER   |
             |                               |                                     |
    Covariate|           Control Treated     |              age    0.063* MATCHED  |Covariates
        sngl + All           185     185  all|*-* matched   age   -0.129  ALL      + single
             | Matched        78      78     |              deg    0.055  MATCHED  |
             | Unmatched     107     107     |              deg   -0.228  ALL      |
             |                               |              edu    0.207  MATCHED  |
             |                               |              edu    0.648  ALL      |
        race +  * all-----------------------*|matched       race  -0.046  MATCHED  + race
             |                               |              race  -1.475  ALL      |
             |                               |              sngl   0.157  MATCHED  |
             |                               |              sngl   0.067  ALL      |
             |                               |                                     |
        edu  +                           matched *--------* all  * Nice Reductions + educaton
             |Standardized Mean Difference   |                                     |
             |                               |                                     |
             | SMD =  MEAN1 - MEAN2          |                                     |
             |    +-------------------  all  |                                     |
        deg  +   /       2         2    *------* matched                           + deg
             |  / (StdDev1 + stdDev0)        |                                     |
             |\/  --------------------       |                                     |
             |            2                  |                                     |
             |                               |                                     |
        age  +                        all *---* matched                            + age
             |                               |                                     |
             |                               |                                     |
             |                               |                                     |
             --+---------+---------+---------+---------+---------+-----------------+
             -1.5      -1.0      -0.5       0.0       0.5       1.0
                        Standardized Mean Difference (SMD)

    SOAPBOX ON

    Be skeptical.

    AI did confim my analysis. Take it with a grain of salt?

    From AI
    Your understanding of propensity scoring is strong
    and well-aligned with standard definitions and applications.

    Propensity scoring can be thought of like randomization, Randomization
    does natural balancing of covariates.

    For example, if the underlying population ("universe" of patients) has a female-to-male ratio of 55/45,
     and your treatment arm matches this ratio (55/45), but your control arm is imbalanced (75/25), this
    introduces a potential confounder. In this case, sex is a confounding factor because differences in
    outcomes between groups could be due to the unequal sex distribution rather than the effect of the
    treatment itself.

    To address this, you should rebalance the control arm so that its sex
    ratio matches that of the treatment arm (and ideally, the underlying population). This can be done
    through matching, stratification, or statistical adjustment. This process helps ensure that any
    observed differences in outcomes are more likely attributable to the treatment rather than to
    differences in sex distribution[6].

    Sex is a well-known confounder in clinical research
    because it can influence disease risk, treatment response, and outcomes. If not properly
    balanced or adjusted for, it can bias the results and reduce the validity of your conclusions.

    Mathmatical explanation

    The objective function in propensity scoring is to maximize the likelihood
    of correctly predicting treatment assignment based on observed covariates.
    The resulting propensity scores are then used to balance the distribution of these covariates
    between treated and untreated groups, thereby reducing confounding and mimicking ran
    domization.

    In practice, for binary treatments, this propensity score
    is often estimated using logistic regression:


    logitPr(Z=1|X)=b  + b X  + b X  ...   b X
                    0    1 1    2 2        n n

    and the estimated propensity score is the expected value

                                        b0*b2 + b1*x1 + b1 + b2*x2
                                       e
     expected propensity score psm = -------------------------------
                                           b0*b2 + b1*x1 + b2*x2
                                     b1 + e                      + 1
     maximize this?

             /         \
             |  psm    |
       logit | -----   |
             | 1 - psm |
             \         /
    FYI

      CONTENTS

        1 better if you have more controls than treatments?
        2 how caliper is computed

      1 if you have more contols you are likly to lose
        fewer treatmest when matching?

      2 One of the key factors in propensity scoring is caliper.

        Here is how it is calculated.
        0.2 is an empirical finding, seems to work best?

        Run logistic model
        m_ps <- glm(trt ~ age + race + deg + sngl +  edu, data = lalonde, family = binomial)

        Vector of propensity scores
        df$ps <- predict(m_ps, type = "response")

        Vector of log of odds ratio
        df$logit_ps <- log(df$ps / (1 - df$ps))

        Caliper_with  = .2 * standard deviation of the log of the propensity(probability) odds ratioo
        caliper_width <- 0.2 * sd(df$logit_ps)

    SOAPBOX OFF


    /*                   _
    (_)_ __  _ __  _   _| |_
    | | `_ \| `_ \| | | | __|
    | | | | | |_) | |_| | |_
    |_|_| |_| .__/ \__,_|\__|
            |_|
    */

    options validvarname=v7;
    libname sd1 "d:/sd1";
    data sd1.have ;
      input trt age race deg edu sngl @@;
    cards4;
    1 37 1 0 11 0  1 28 1 0 08 1  1 24 1 0 10 1  1 20 1 0 10 1  0 29 2 0 06 0  0 19 3 1 13 1  0 29 3 1 12 0  0 55 1 0 04 1
    1 22 2 0 09 1  1 31 1 0 11 0  1 26 1 0 10 1  1 28 1 0 10 1  0 25 3 1 13 0  0 18 1 0 11 1  0 40 3 1 16 0  0 18 1 0 11 1
    1 30 1 1 12 1  1 18 1 0 11 1  1 25 1 0 11 1  1 24 1 1 12 1  0 39 3 0 10 0  0 21 3 0 10 1  0 19 3 0 10 1  0 16 2 0 09 0
    1 27 1 0 11 1  1 25 1 1 12 1  1 18 1 0 11 1  1 19 1 0 08 1  0 33 3 1 12 0  0 24 3 1 12 0  0 16 1 0 06 1  0 53 3 1 12 1
    1 33 1 0 08 1  1 30 1 0 11 0  1 19 1 0 11 1  1 23 1 1 12 1  0 31 3 1 12 0  0 18 3 0 11 1  0 22 3 0 08 0  0 17 1 0 08 1
    1 22 1 0 09 1  1 17 1 0 10 1  1 43 1 0 09 1  1 42 1 0 09 0  0 36 1 1 12 0  0 18 1 0 11 1  0 49 3 1 14 0  0 27 1 1 14 1
    1 23 1 1 12 1  1 37 1 0 09 1  1 27 1 1 13 1  1 25 1 1 13 1  0 35 3 0 07 0  0 26 1 1 12 1  0 47 3 0 09 0  0 37 1 0 08 1
    1 32 1 0 11 1  1 41 1 0 04 0  1 17 1 0 09 1  1 18 1 0 09 1  0 26 2 0 08 1  0 18 1 1 12 1  0 34 2 0 11 0  0 17 1 0 10 1
    1 22 1 1 16 1  1 42 1 1 14 0  1 30 1 0 11 1  1 21 1 1 12 1  0 53 3 1 12 1  0 32 3 0 04 0  0 30 3 0 10 0  0 48 3 1 12 0
    1 33 3 1 12 0  1 22 3 0 11 1  1 26 1 0 10 0  1 27 1 0 10 1  0 28 3 1 12 1  0 18 1 0 11 1  0 22 1 0 11 0  0 55 3 0 07 1
    1 19 1 0 09 1  1 17 1 0 08 1  1 20 1 0 09 1  1 21 1 0 08 1  0 26 2 1 12 1  0 40 3 0 10 0  0 55 3 0 07 0  0 21 3 1 15 1
    1 21 1 1 13 1  1 29 1 0 08 1  1 17 2 0 09 1  1 22 1 0 09 1  0 38 2 0 08 0  0 31 3 0 06 1  0 34 3 1 12 1  0 16 1 0 10 1
    1 18 1 0 08 1  1 35 1 0 10 1  1 20 1 1 12 1  1 31 1 0 04 1  0 23 3 0 08 0  0 46 3 0 07 0  0 22 3 0 11 1  0 17 3 0 10 1
    1 27 1 0 10 0  1 27 1 0 11 1  1 18 1 0 11 1  1 24 1 0 10 0  0 25 3 1 15 0  0 20 2 0 09 0  0 19 3 1 12 1  0 27 3 1 12 0
    1 17 1 0 07 1  1 29 1 0 04 1  1 27 1 1 12 0  1 29 1 0 10 1  0 37 2 0 11 1  0 16 3 0 10 1  0 24 3 0 10 0  0 51 1 0 04 1
    1 19 1 0 10 1  1 28 1 0 09 1  1 21 3 1 12 1  1 29 1 1 12 1  0 48 3 0 07 0  0 18 3 1 12 1  0 36 3 1 12 0  0 39 1 0 02 0
    1 27 1 1 13 1  1 27 1 0 11 1  1 27 1 1 12 1  1 19 3 0 10 1  0 16 3 0 09 1  0 40 2 1 12 1  0 35 3 0 11 0  0 25 3 1 14 1
    1 23 1 0 10 1  1 23 3 0 07 1  1 20 1 1 12 1  1 19 2 0 11 0  0 29 3 0 10 0  0 16 3 0 09 1  0 29 3 0 10 1  0 24 2 0 01 0
    1 40 1 1 12 1  1 45 1 0 05 0  1 19 1 0 10 1  1 31 1 0 09 1  0 22 3 0 11 0  0 16 3 0 10 1  0 24 3 0 09 0  0 21 3 1 18 1
    1 26 1 1 12 1  1 29 1 1 13 1  1 23 1 1 12 1  1 22 1 0 10 0  0 24 1 1 12 0  0 16 3 0 08 1  0 18 3 0 10 1  0 21 3 1 18 1
    1 23 1 0 11 1  1 27 1 0 09 1  1 29 1 1 14 1  1 21 1 0 09 1  0 47 3 0 11 1  0 20 3 0 11 0  0 21 2 1 13 1
    1 41 3 1 14 1  1 46 1 1 13 1  1 18 1 0 10 1  1 17 1 0 10 1  0 34 3 0 11 0  0 32 2 0 06 0  0 29 3 1 13 0
    1 38 3 0 09 1  1 18 1 0 06 1  1 19 1 0 09 1  1 26 1 1 12 0  0 19 3 1 12 0  0 32 1 1 16 1  0 21 3 1 15 1
    1 24 1 0 11 1  1 25 1 1 12 1  1 27 3 1 13 0  1 20 2 0 09 1  0 21 3 1 15 0  0 19 3 1 12 1  0 20 3 1 14 1
    1 18 1 0 10 1  1 28 1 1 15 1  1 18 3 0 11 1  1 19 1 0 10 1  0 28 3 1 13 1  0 17 1 0 05 1  0 19 1 0 06 1
    1 29 1 0 11 0  1 25 3 0 11 1  1 27 1 0 09 0  1 26 1 0 10 1  0 24 3 1 15 1  0 18 1 0 11 1  0 19 1 1 12 1
    1 25 1 0 11 1  1 22 1 1 12 1  1 22 1 1 12 1  1 28 1 0 11 1  0 28 2 0 08 0  0 20 3 1 12 0  0 20 3 1 13 1
    1 27 2 0 10 1  1 21 1 0 09 1  1 23 1 0 10 0  1 22 2 1 12 0  0 23 3 1 12 0  0 19 1 1 13 1  0 26 3 0 05 0
    1 17 1 0 10 1  1 40 1 0 11 1  1 23 2 1 12 1  1 33 1 0 11 1  0 42 3 0 07 0  0 24 3 0 08 0  0 26 2 0 09 1
    1 24 1 0 11 1  1 22 1 0 11 1  1 20 1 0 11 1  1 22 3 1 12 1  0 25 3 1 12 0  0 27 3 1 12 0  0 17 1 0 09 1
    1 17 1 0 10 1  1 25 1 1 12 1  1 17 1 0 09 1  1 29 2 0 10 1  0 27 3 1 12 0  0 19 3 0 09 1  0 28 3 0 08 0
    1 48 1 0 04 1  1 18 1 1 12 1  1 28 1 0 11 1  1 33 1 1 12 0  0 24 3 0 07 0  0 52 1 0 08 0  0 16 3 0 09 0
    1 25 1 0 11 0  1 38 3 1 12 1  1 26 1 0 11 0  1 25 1 1 14 0  0 23 3 1 12 0  0 18 2 1 12 1  0 50 1 0 05 0
    1 20 1 1 12 1  1 27 1 1 13 1  1 20 1 0 11 1  1 35 1 0 09 0  0 50 3 1 12 0  0 45 3 1 12 1  0 19 1 0 09 1
    1 25 1 1 12 1  1 27 1 0 08 1  1 24 1 0 11 0  1 35 1 0 08 0  0 19 3 1 12 1  0 36 3 0 08 0  0 18 3 0 11 1
    1 42 1 1 14 1  1 38 1 0 11 1  1 31 1 0 09 1  1 33 1 0 11 0  0 51 3 1 12 0  0 19 3 0 03 0  0 16 2 0 07 1
    1 25 1 0 05 1  1 23 2 0 08 1  1 23 3 0 08 0  0 37 1 0 09 0  0 20 3 0 10 0  0 32 3 1 13 0  0 21 3 0 11 0
    1 23 1 1 12 0  1 26 1 0 11 1  1 18 1 0 10 1  0 20 1 1 12 1  0 19 1 0 09 1  0 17 1 0 08 1  0 16 3 0 08 1
    1 46 1 0 08 0  1 21 3 1 12 1  1 29 1 1 12 1  0 34 3 0 08 0  0 20 3 0 07 1  0 21 3 0 08 0  0 16 2 0 09 1
    1 24 1 0 10 1  1 25 1 0 08 1  1 26 3 0 11 1  0 22 1 1 14 0  0 23 3 1 12 0  0 18 3 0 10 1  0 18 1 0 10 1
    1 21 1 1 12 1  1 31 1 0 11 0  1 24 1 0 09 1  0 42 2 0 00 0  0 37 3 1 14 0  0 24 1 1 12 0  0 16 1 0 09 1
    1 19 3 0 09 1  1 17 1 0 10 1  1 25 1 1 12 1  0 25 2 0 09 1  0 24 3 0 10 0  0 42 3 0 08 1  0 20 1 0 09 1
    1 17 1 0 08 1  1 25 1 0 11 1  1 24 1 0 10 1  0 40 3 1 13 0  0 21 3 1 12 1  0 19 2 0 08 1  0 18 1 0 11 1
    1 18 2 0 08 0  1 21 1 1 12 1  1 46 1 0 08 1  0 36 3 1 12 0  0 24 3 1 12 1  0 17 1 0 09 1  0 46 1 0 11 0
    1 20 1 0 11 1  1 44 1 0 11 1  1 31 3 1 12 1  0 39 2 0 06 0  0 20 3 1 13 1  0 23 1 1 13 1  0 17 1 0 08 1
    1 25 1 0 11 0  1 25 3 1 12 1  1 19 1 0 11 1  0 29 3 1 12 0  0 17 1 0 07 1  0 46 3 0 07 1  0 16 3 0 09 1
    1 17 1 0 08 1  1 18 1 0 09 1  1 19 1 0 08 1  0 22 2 1 13 1  0 18 1 0 10 1  0 18 3 0 10 0  0 33 2 1 12 0
    1 17 1 0 09 1  1 42 1 1 12 1  1 27 1 0 11 1  0 40 2 0 03 0  0 30 3 1 12 0  0 19 1 0 10 1  0 29 3 0 11 0
    1 25 1 0 05 1  1 25 1 0 10 1  1 26 1 0 11 0  0 25 3 1 12 0  0 30 3 0 07 0  0 26 3 0 07 0  0 19 3 1 12 1
    1 23 1 1 12 1  1 31 2 0 09 1  0 25 1 1 12 0  0 20 3 1 12 0  0 17 3 0 10 1  0 16 3 0 10 1  0 20 1 1 12 1
    ;;;;
    run;quit;

    /**************************************************************************************************************************/
    /* Obs    trt    age    race    deg    edu    sngl                                                                        */
    /*                                                                                                                        */
    /*   1     1      37      1      0      11      0                                                                         */
    /*   2     1      28      1      0       8      1                                                                         */
    /*   3     1      24      1      0      10      1                                                                         */
    /*   4     1      20      1      0      10      1                                                                         */
    /*   5     0      29      2      0       6      0                                                                         */
    /*                                                                                                                        */
    /* 366     0      25      1      1      12      0                                                                         */
    /* 367     0      20      3      1      12      0                                                                         */
    /* 368     0      17      3      0      10      1                                                                         */
    /* 369     0      16      3      0      10      1                                                                         */
    /* 370     0      20      1      1      12      1                                                                         */
    /**************************************************************************************************************************/

    /*                                             _       _
    / |  ___  __ _ ___   _ __  ___ _ __ ___   __ _| |_ ___| |__
    | | / __|/ _` / __| | `_ \/ __| `_ ` _ \ / _` | __/ __| `_ \
    | | \__ \ (_| \__ \ | |_) \__ \ | | | | | (_| | || (__| | | |
    |_| |___/\__,_|___/ | .__/|___/_| |_| |_|\__,_|\__\___|_| |_|
                        |_|
    */

    ods listing
       gpath="d:\png"
       style=analysis;

    ods graphics on /
      reset=index
      width=5in
      height=5in
      imagefmt=png
      imagename='imgBig';

    ods output psinfo=psinfo;

    proc psmatch data=sd1.have;
     class trt;
     psmodel trt(treated='1')
        = age race deg edu sngl;
     match
        method=greedy(k=1)
        caliper=0.2;
     assess
        lps
        var=(age race deg edu sngl);
     output
        out=matched_data
        matchid=_MatchID;
    run;quit;

    ods graphics off;

    proc print data=psinfo(
       drop=
           ControlMax
           ControlMin
           ControlWeight
           TreatedMax
           TreatedMin
           TreatedWeight
           controlfreq
       rename=( %utl_renamel(
           TreatedFreq
           ControlMean
           ControlStdDev
           TreatedMean
           TreatedStdDev
          ,Sample
           trtMean
           trtStdDev1
           ctlMean0
           ctlStdDev0))
           where=( obs in ('All','Matched')));
       format _numeric_ 5.3;
    run;quit;

    /**************************************************************************************************************************/
    /* WORK.PSINFO                          ctl    trt                                                                        */
    /*                       ctl     trt    Std    Std     Mean                                                               */
    /* Obs        Sample    Mean0   Mean   Dev0   Dev1     Diff                                                               */
    /*                                                                                                                        */
    /* All        185.0     0.694  0.306  0.211  0.274    0.388                                                               */
    /* Matched    78.00     0.578  0.550  0.275  0.260    0.028 ** much better                                                */
    /**************************************************************************************************************************/

    /**************************************************************************************************************************/
    /* WORK.MATCHED_DATA                                                            Match                                     */
    /* OBS    TRT    AGE    RACE    DEG    SNGL    EDU      _PS_     _MATCHWGT_      ID                                       */
    /*                                                                                                                        */
    /*   1     1      37      1      0      11      0     0.77092         1          33                                       */
    /*   2     1      28      1      0       8      1     0.77779         .           .                                       */
    /*   3     1      24      1      0      10      1     0.83041         1          16                                       */
    /*   4     1      20      1      0      10      1     0.81542         1          18                                       */
    /*   5     0      29      2      0       6      0     0.17204         .           .                                       */
    /* ...                                                                                                                    */
    /* 366     0      25      1      1      12      0     0.55899         1          44                                       */
    /* 367     0      20      3      1      12      0     0.05435         .           .                                       */
    /* 368     0      17      3      0      10      1     0.17416         .           .                                       */
    /* 369     0      16      3      0      10      1     0.17049         .           .                                       */
    /* 370     0      20      1      1      12      1     0.73831         1          26                                       */
    /**************************************************************************************************************************/

    proc means data=matched_data stackodsoutput n mean stddev missing;
    class trt _MATCHWGT_;
    types trt*_MATCHWGT_;
    types trt;
    var
      age
      race
      deg
      sngl
      edu;
    ods output summary=sasStats;
    run;quit;

    /**************************************************************************************************************************/
    /* WORK.SASSTATS;                                                                                                         */
    /* TRT    WEIGHTS    NOBS    _CONTROL_    VARIABLE     N       MEAN      STDDEV                                           */
    /*                                                                                                                        */
    /*  0        .        185                   age       185    26.9676    10.4105                                           */
    /*  0        .        185                   race      185     2.3622     0.8620                                           */
    /*  0        .        185                   deg       185     0.4000     0.4912                                           */
    /*  0        .        185                   sngl      185    10.1784     2.9165                                           */
    /*  0        .        185                   edu       185     0.5189     0.5010                                           */
    /*  1        .        185        1          age       185    25.8162     7.1550                                           */
    /*  1        .        185                   race      185     1.2541     0.6214                                           */
    /*  1        .        185                   deg       185     0.2919     0.4559                                           */
    /*  1        .        185                   sngl      185    10.3459     2.0107                                           */
    /*  1        .        185                   edu       185     0.8108     0.3927                                           */
    /*  0        0        107                   age       107    27.2430     9.5422                                           */
    /*  0        0        107                   race      107     2.9065     0.2924                                           */
    /*  0        0        107                   deg       107     0.4673     0.5013                                           */
    /*  0        0        107                   sngl      107    10.3178     3.1190                                           */
    /*  0        0        107                   edu       107     0.3832     0.4884                                           */
    /*  0        1         78        1          age        78    26.5897    11.5494                                           */
    /*  0        1         78                   race       78     1.6154     0.8254                                           */
    /*  0        1         78                   deg        78     0.3077     0.4645                                           */
    /*  0        1         78                   sngl       78     9.9872     2.6211                                           */
    /*  0        1         78                   edu        78     0.7051     0.4589                                           */
    /*  1        0        107        1          age       107    24.8037     6.7648                                           */
    /*  1        0        107                   race      107     1.0187     0.1361                                           */
    /*  1        0        107                   deg       107     0.2617     0.4416                                           */
    /*  1        0        107                   sngl      107    10.3271     1.8107                                           */
    /*  1        0        107                   edu       107     0.8224     0.3839                                           */
    /*  1        1         78        1          age        78    27.2051     7.4805                                           */
    /*  1        1         78                   race       78     1.5769     0.8454                                           */
    /*  1        1         78                   deg        78     0.3333     0.4745                                           */
    /*  1        1         78                   sngl       78    10.3718     2.2684                                           */
    /*  1        1         78                   edu        78     0.7949     0.4064                                           */
    /**************************************************************************************************************************/

    /*---- no sort needed ----*/
    %utl_transpose(
        data=sasStats(where=(n ne 107))
       ,sort=YES
       ,by=variable nobs
       ,id=trt
       ,out=saswant
       ,var=n mean stddev);

    /**************************************************************************************************************************/
    /* WORK.SASWANT               TREAT=0                     TREAT=1 (TREATED)                                               */
    /*                      ------------------------     ------------------------                                             */
    /* variable    nobs     N0     Mean0     StdDev0     N1     Mean1     StdDev1                                             */
    /*                                                                                                                        */
    /*   age matched 78     78    26.5897    11.5494     78    25.9231    7.36924 matched                                     */
    /*   age all    185    185    26.9676    10.4105    185    25.8162    7.15502 all                                         */
    /*                                                                                                                        */
    /*   deg         78     78     0.3077     0.4645     78     0.2692    0.44643 matched                                     */
    /*   deg        185    185     0.4000     0.4912    185     0.2919    0.45587 all                                         */
    /*                                                                                                                        */
    /*   edu         78     78     0.7051     0.4589     78     0.7821    0.41552 matched                                     */
    /*   edu        185    185     0.5189     0.5010    185     0.8108    0.39272 all                                         */
    /*                                                                                                                        */
    /*   race        78     78     1.6154     0.8254     78     1.5769    0.84545 matched                                     */
    /*   race       185    185     2.3622     0.8620    185     1.2541    0.62144 all                                         */
    /*                                                                                                                        */
    /*   sngl        78     78     9.9872     2.6211     78    10.0256    2.18599                                             */
    /*   sngl       185    185    10.1784     2.9165    185    10.3459    2.01065                                             */
    /**************************************************************************************************************************/

     /*---- Calculate Standardized Mean Difference (SMD) ----*/

     proc sql;
      create
         table smd as
      select
         variable
        ,nobs
        ,(mean1-mean0)/(sqrt((StdDev1**2+stddev0**2)/2)) as smd
        ,case nobs
           when 185 then "all"
           else "matched"
         end as ltr length=32
      from
         want
    ;quit;

    /*---- Note you annotate proc plot ----*/:
    data anno;
      set smd end=dne;
      output;
      if dne then do;
         variable='sngl';
         smd=-1.75;
         ltr='Standardized M ean Difference';
         output;
      end;
    run;quit;


    /**************************************************************************************************************************/
    /* WORK.SMD Calculate Standardized Mean Difference (SMD)                                                                  */
    /*   variable    nobs       smd      ltr                                                                                  */
    /*                                                                                                                        */
    /*     age         78     0.06325    matched                                                                              */
    /*     age        185    -0.12890    all                                                                                  */
    /*     deg         78     0.05461    matched                                                                              */
    /*     deg        185    -0.22814    all                                                                                  */
    /*     edu         78     0.20704    matched                                                                              */
    /*     edu        185     0.64847    all                                                                                  */
    /*     race        78    -0.04603    matched                                                                              */
    /*     race       185    -1.47465    all                                                                                  */
    /*     sngl        78     0.15692    matched                                                                              */
    /*     sngl       185     0.06690    all                                                                                  */
    /**************************************************************************************************************************/

    options ls=64 ps=32;
    proc plot data=anno;
     title "Calculate Standardized Mean Difference (SMD)";
     label smd="Standardized Mean Difference";
     label variable="Covariate";
     plot variable*smd='*' $ ltr /box href=0;
    run;quit;
    options ls=255 ps=66;


    /**************************************************************************************************************************/
    /* Calculate Standardized Mean Difference (SMD)                                                                           */
    /*                                                                                                                        */
    /*          Plot of variable*smd$ltr.  Symbol used is '*'.                                                                */
    /*                                                                                                                        */
    /*        -+--------+--------+--------+--------+--------+--------+-                                                       */
    /*   sngl +     * Standardized Mean Difference |* * matched       +                                                       */
    /*        |                                    all                |                                                       */
    /*        |                                    |                  |                                                       */
    /*        |                                    |                  |                                                       */
    /*        |                                    |                  |                                                       */
    /*   race +         * all                     *|matched           +                                                       */
    /* C      |                                    |                  |                                                       */
    /* o      |                                    |                  |                                                       */
    /* v      |                                    |                  |                                                       */
    /* a      |                                    |                  |                                                       */
    /* r edu  +                                matched *       * all  +                                                       */
    /* i      |                                    |                  |                                                       */
    /* a      |                                    |                  |                                                       */
    /* t      |                                    |                  |                                                       */
    /* e      |                                    |                  |                                                       */
    /*   deg  +                            all *   |* matched         +                                                       */
    /*        |                                    |                  |                                                       */
    /*        |                                    |                  |                                                       */
    /*        |                                    |                  |                                                       */
    /*        |                                    |                  |                                                       */
    /*   age  +                              all * |* matched         +                                                       */
    /*        -+--------+--------+--------+--------+--------+--------+-                                                       */
    /*       -2.0     -1.5     -1.0     -0.5      0.0      0.5      1.0                                                       */
    /*                                                                                                                        */
    /*                      Standardized Mean Difference                                                                      */
    /*                                                                                                                        */
    /**************************************************************************************************************************/

    /*___                           _       _     _ _
    |___ \   _ __   _ __ ___   __ _| |_ ___| |__ (_) |_
      __) | | `__| | `_ ` _ \ / _` | __/ __| `_ \| | __|
     / __/  | |    | | | | | | (_| | || (__| | | | | |_
    |_____| |_|    |_| |_| |_|\__,_|\__\___|_| |_|_|\__|

    */

    %utl_rbeginx;
    parmcards4;
    library(MatchIt)
    library(cobalt)
    library(haven)
    library(tableone)
    library(dplyr)
    library(sqldf)
    source("c:/oto/fn_tosas9x.R")
    options(sqldf.dll = "d:/dll/sqlean.dll")
    have<-read_sas("d:/sd1/have.sas7bdat")
    head(have)
    # Propensity Score Matching with a 0.2 standard deviation caliper
    set.seed(123)  # For reproducibility

    mtch <- matchit(
      trt ~ age + edu + race + sngl + deg,
      data = have,
      method = "nearest",
      distance = "glm",  # Logistic regression for PS estimation
      caliper = 0.2,     # Caliper width (in SD of logit(PS))
      std.caliper = TRUE # Use SD units for caliper
    )

    # Show matching results
    summary(mtch)
    sink("d:/txt/match_summary.txt")
    summary(mtch)
    sink()

    mtchdf <- match.data(mtch)
    mtchful <- match.data(mtch, drop.unmatched = FALSE)

    # Extract matched data

    png("d:/png/match_compare.png")
    # Visualize balance assessment
    love.plot(mtch,
              threshold = 0.1,   # Balance threshold
              stars = "std")     # Show standardized mean differences
    dev.off();

    fn_tosas9x(
          inp    = mtchful
         ,outlib ="d:/sd1/"
         ,outdsn ="mtchful"
         )
    ;;;;
    %utl_rendx;


    data _null_;
     infile "d:/txt/match_summary.txt";
     input;
     put _infile_;
    run;quit;

    /**************************************************************************************************************************/
    /* matchit(formula = trt ~ age + edu + race + sngl + deg, data = have,                                                    */
    /*     method = "nearest", distance = "glm", caliper = 0.2, std.caliper = TRUE)                                           */
    /*                                                                                                                        */
    /* Summary of Balance for All Data:                                                                                       */
    /*          Means Treated Means Control Std. Mean Diff. Var. Ratio eCDF Mean  eCDF Max                                    */
    /* distance        0.6939        0.3061          1.8367     0.5923    0.3567    0.5946                                    */
    /* age            25.8162       26.9676         -0.1609     0.4724    0.0707    0.1243                                    */
    /* edu            10.3459       10.1784          0.0833     0.4753    0.0411    0.1135                                    */
    /* race            1.2541        2.3622         -1.7831     0.5197    0.3694    0.5892                                    */
    /* sngl            0.8108        0.5189          0.7453          .    0.2919    0.2919                                    */
    /* deg             0.2919        0.4000         -0.2378          .    0.1081    0.1081                                    */
    /*                                                                                                                        */
    /* Summary of Balance for Matched Data:                                                                                   */
    /*          Means Treated Means Control Std. Mean Diff. Var. Ratio eCDF Mean  eCDF Max Std. Pair Dist.                    */
    /* distance        0.5870        0.5504          0.1731     1.1880    0.0588    0.2308          0.1761                    */
    /* age            27.2051       26.5897          0.0860     0.4195    0.1045    0.3333          1.4406                    */
    /* edu            10.3718        9.9872          0.1913     0.7490    0.0285    0.1282          1.3008                    */
    /* race            1.5769        1.6154         -0.0619     1.0491    0.0214    0.0513          0.1857                    */
    /* sngl            0.7949        0.7051          0.2291          .    0.0897    0.0897          0.7529                    */
    /* deg             0.3333        0.3077          0.0564          .    0.0256    0.0256          1.0152                    */
    /**************************************************************************************************************************/

    proc sort data=sd1.mtchful;
     by subclass trt;
    run;quit;

    /**************************************************************************************************************************/
    /* SD1.MTCHFUL                                                                                                            */
    /* Obs    rownames    trt    age    race    deg    edu    sngl    distance    weights    subclass                         */
    /*                                                                                                                        */
    /*   1         5       0      29      2      0       6      0      0.17204       0           . NOT MATCHED                */
    /*   2         6       0      19      3      1      13      1      0.15007       0           .                            */
    /*   3         7       0      29      3      1      12      0      0.06756       0           .                            */
    /*   4        13       0      25      3      1      13      0      0.07527       0           .                            */
    /*   5        24       0      16      2      0       9      0      0.22309       0           .                            */
    /*  ...                                                                                                                   */
    /*                                                                                                                        */
    /* 365       342       0      16      3      0       9      1      0.14168       1          76 MATCHED PAIR               */
    /* 366       337       1      25      3      1      12      1      0.14197       1          76                            */
    /*                                                                                                                        */
    /* 367       215       0      19      1      1      13      1      0.77395       1          77 MATCHED PAIR               */
    /* 368       358       1      25      1      0      10      1      0.83400       1          77                            */
    /*                                                                                                                        */
    /* 369       346       0      22      2      1      13      1      0.45649       1          78 MATCHED PAIR               */
    /* 370       365       1      31      2      0       9      1      0.51671       1          78                            */
    /**************************************************************************************************************************/  \

    proc means data=sd1.mtchful stackodsoutput n mean stddev;
    class trt weights;
    types trt trt*weights;
    var
      age
      race
      deg
      sngl
      edu;
    ods output summary=stats;
    run;quit;

    /**************************************************************************************************************************/
    /*  WORK.STATS                                                                                                            */
    /*   trt    weights    NObs    _control_    Variable     N       Mean      StdDev                                         */
    /*                                                                                                                        */
    /*    0        .        185                   age       185    26.9676    10.4105                                         */
    /*    0        .        185                   race      185     2.3622     0.8620                                         */
    /*    0        .        185                   deg       185     0.4000     0.4912                                         */
    /*    0        .        185                   sngl      185     0.5189     0.5010                                         */
    /*    0        .        185                   edu       185    10.1784     2.9165                                         */
    /*    1        .        185        1          age       185    25.8162     7.1550                                         */
    /*    1        .        185                   race      185     1.2541     0.6214                                         */
    /*    1        .        185                   deg       185     0.2919     0.4559                                         */
    /*    1        .        185                   sngl      185     0.8108     0.3927                                         */
    /*    1        .        185                   edu       185    10.3459     2.0107                                         */
    /*    0        0        107                   age       107    27.2430     9.5422                                         */
    /*    0        0        107                   race      107     2.9065     0.2924                                         */
    /*    0        0        107                   deg       107     0.4673     0.5013                                         */
    /*    0        0        107                   sngl      107     0.3832     0.4884                                         */
    /*    0        0        107                   edu       107    10.3178     3.1190                                         */
    /*    0        1         78        1          age        78    26.5897    11.5494                                         */
    /*    0        1         78                   race       78     1.6154     0.8254                                         */
    /*    0        1         78                   deg        78     0.3077     0.4645                                         */
    /*    0        1         78                   sngl       78     0.7051     0.4589                                         */
    /*    0        1         78                   edu        78     9.9872     2.6211                                         */
    /*    1        0        107        1          age       107    24.8037     6.7648                                         */
    /*    1        0        107                   race      107     1.0187     0.1361                                         */
    /*    1        0        107                   deg       107     0.2617     0.4416                                         */
    /*    1        0        107                   sngl      107     0.8224     0.3839                                         */
    /*    1        0        107                   edu       107    10.3271     1.8107                                         */
    /*    1        1         78        1          age        78    27.2051     7.4805                                         */
    /*    1        1         78                   race       78     1.5769     0.8454                                         */
    /*    1        1         78                   deg        78     0.3333     0.4745                                         */
    /*    1        1         78                   sngl       78     0.7949     0.4064                                         */
    /*    1        1         78                   edu        78    10.3718     2.2684                                         */
    /**************************************************************************************************************************/

    /*---- no sort needed ----*/
    %utl_transpose(
        data=stats(where=(weights ne 0))
        ,sort=YES
        ,by=variable nobs
        ,id=trt
        ,out=want
        ,var=n mean stddev);

    /**************************************************************************************************************************/
    /*  WORK.WANT                                                                                                             */
    /*   variable    nobs     N0     Mean0     StdDev0     N1     Mean1     StdDev1                                           */
    /*                                                                                                                        */
    /*     age         78     78    26.5897    11.5494     78    27.2051    7.48047                                           */
    /*     age        185    185    26.9676    10.4105    185    25.8162    7.15502                                           */
    /*     deg         78     78     0.3077     0.4645     78     0.3333    0.47446                                           */
    /*     deg        185    185     0.4000     0.4912    185     0.2919    0.45587                                           */
    /*     edu         78     78     9.9872     2.6211     78    10.3718    2.26841                                           */
    /*     edu        185    185    10.1784     2.9165    185    10.3459    2.01065                                           */
    /*     race        78     78     1.6154     0.8254     78     1.5769    0.84545                                           */
    /*     race       185    185     2.3622     0.8620    185     1.2541    0.62144                                           */
    /*     sngl        78     78     0.7051     0.4589     78     0.7949    0.40641                                           */
    /*     sngl       185    185     0.5189     0.5010    185     0.8108    0.39272                                           */
    /**************************************************************************************************************************/

    proc sql;
      create
         table smd as
      select
         variable
        ,nobs
        ,(mean1-mean0)/(sqrt((StdDev1**2+stddev0**2)/2)) as smd
        ,case nobs
           when 185 then "all"
           else "matched"
         end as ltr length 44
      from
         want
    ;quit;

    /*---- Note you annotate proc plot ----*/
    data anno;
      set smd end=dne;
      output;
      if dne then do;
         variable='sngl';
         smd=-1.75;
         ltr='Standardized M ean Difference';
         output;
      end;
    run;quit;

    options ls=64 ps=32;
    proc plot data=anno;
     title "Calculate Standardized Mean Difference (SMD)";
     label smd="Standardized Mean Difference";
     label variable="Covariate";
     plot variable*smd='*' $ ltr /box href=0;
    run;quit;
    options ls=255 ps=66;


    /**************************************************************************************************************************/
    /*  WORK.ANNO                                                                                                             */
    /*   variable    nobs       smd      ltr                                                                                  */
    /*                                                                                                                        */
    /*     age         78     0.06325    matched                                                                              */
    /*     age        185    -0.12890    all                                                                                  */
    /*     deg         78     0.05461    matched                                                                              */
    /*     deg        185    -0.22814    all                                                                                  */
    /*     edu         78     0.15692    matched                                                                              */
    /*     edu        185     0.06690    all                                                                                  */
    /*     race        78    -0.04603    matched                                                                              */
    /*     race       185    -1.47465    all                                                                                  */
    /*     sngl        78     0.20704    matched                                                                              */
    /*     sngl       185     0.64847    all                                                                                  */
    /*     sngl       185    -1.75000    Standardized M ean Difference                                                        */
    /*                                                                                                                        */
    /*  Calculate Standardized Mean Difference (SMD)                                                                          */
    /*                                                                                                                        */
    /*           Plot of variable*smd$ltr.  Symbol used is '*'.                                                               */
    /*                                                                                                                        */
    /*         -+--------+--------+--------+--------+--------+--------+-                                                      */
    /*    sngl +     * Standardized M ean Difference|   *       * all  +                                                      */
    /*         |                                    |matched           |                                                      */
    /*         |                                    |                  |                                                      */
    /*         |                                    |                  |                                                      */
    /*         |                                    |                  |                                                      */
    /*    race +         * all                     *|matched           +                                                      */
    /*  C      |                                    |                  |                                                      */
    /*  o      |                                    |                  |                                                      */
    /*  v      |                                    |                  |                                                      */
    /*  a      |                                    |                  |                                                      */
    /*  r edu  +                                 all|* * matched       +                                                      */
    /*  i      |                                    |                  |                                                      */
    /*  a      |                                    |                  |                                                      */
    /*  t      |                                    |                  |                                                      */
    /*  e      |                                    |                  |                                                      */
    /*    deg  +                            all *   |* matched         +                                                      */
    /*         |                                    |                  |                                                      */
    /*         |                                    |                  |                                                      */
    /*         |                                    |                  |                                                      */
    /*         |                                    |                  |                                                      */
    /*    age  +                              all * |* matched         +                                                      */
    /*         -+--------+--------+--------+--------+--------+--------+-                                                      */
    /*        -2.0     -1.5     -1.0     -0.5      0.0      0.5      1.0                                                      */
    /*                                                                                                                        */
    /*                       Standardized Mean Difference                                                                     */
    /**************************************************************************************************************************/

    /*____               _   _
    |___ /    ___  _ __ | |_(_) ___  _ __  ___   ___  __ _ ___   _ __
      |_ \   / _ \| `_ \| __| |/ _ \| `_ \/ __| / __|/ _` / __| | `__|
     ___) | | (_) | |_) | |_| | (_) | | | \__ \ \__ \ (_| \__ \ | |
    |____/   \___/| .__/ \__|_|\___/|_| |_|___/ |___/\__,_|___/ |_|
                  |_|
    */

    AI Query for pavkage options for r matchit and
    https://www.perplexity.ai/search/please-provide-all-the-options-i.WttSVOQsmHf6pA.6EazA
    please provide all the options for propensity scoring psm using the r packages
    please provide all the options for propensity scoring psm using sas psmath

    /*                      _       _     _ _
     _ __   _ __ ___   __ _| |_ ___| |__ (_) |_
    | `__| | `_ ` _ \ / _` | __/ __| `_ \| | __|
    | |    | | | | | | (_| | || (__| | | | | |_
    |_|    |_| |_| |_|\__,_|\__\___|_| |_|_|\__|

    */
    R package Matchit

     Parameter       Options/Description                                                         Example/Default

     method         "nearest" (default), "optimal", "full", "genetic", "subclass", "cem"         method   = "optimal"
     distance       "glm" (logistic regression), "gam", "gbm", "lasso", "ridge", "elasticnet"    distance = "gbm"
     exact          Variables for exact matching (e.g., exact = c("sex", "age"))                 exact = "married"
     ratio          Number of controls per treated unit (default: 1)                             ratio = 2
     replace        TRUE (reuse controls) or FALSE (no replacement)                              replace = FALSE
     caliper        Maximum allowable distance (e.g., caliper = 0.1)                             caliper   = 0.2
     std.caliper    TRUE (standardize caliper by SD) or FALSE                                    std.caliper = TRUE
     discard        Handling units outside common support: "none", "both", "control", "treated"  discard     = "both"
     reestimate     TRUE (re-estimate propensity scores after discarding) or FALSE               reestimate  = FALSE

    Advance features

    1 Machine learning models: Use distance = "gbm" (gradient boosting) or "lasso" for non-linear propensity scores.
    2 Exact matching: Force exact matches on critical covariates.
    3 Subclassification: Divide data into strata using method = "subclass".

    Citations:

    [1]  https://cran.r-project.org/web/packages/AER/AER.pdf
    [2]  https://pmc.ncbi.nlm.nih.gov/articles/PMC8246231/
    [3]  https://cran.r-project.org/web/packages/ivmte/vignettes/ivmte.html
    [4]  https://rpubs.com/mbounthavong/propensity_score_r
    [5]  https://www.r-causal.org/chapters/08-propensity-scores
    [6]  https://pmc.ncbi.nlm.nih.gov/articles/PMC11187614/
    [7]  https://bfi.uchicago.edu/wp-content/uploads/BFI_WP_202001.pdf
    [8]  https://dlab.berkeley.edu/news/introduction-propensity-score-matching-matchit

    /*                _           _ _
     _ __    ___ ___ | |__   __ _| | |_
    | `__|  / __/ _ \| `_ \ / _` | | __|
    | |    | (_| (_) | |_) | (_| | | |_
    |_|     \___\___/|_.__/ \__,_|_|\__|

    */
    R Package Cobalt (Balance Assessment with cobalt)

    Package      Focus      Key Features      Source

    twang          Weighting with GBM            Uses generalized boosted models for propensity scores
    CBPS           Covariate balancing scores    Optimizes balance during propensity score estimation
    rollmatch      Longitudinal data             Matches units entering treatment at different time points

    /*                                         _       _
     ___  __ _ ___   _ __  ___ _ __ ___   __ _| |_ ___| |__
    / __|/ _` / __| | `_ \/ __| `_ ` _ \ / _` | __/ __| `_ \
    \__ \ (_| \__ \ | |_) \__ \ | | | | | (_| | || (__| | | |
    |___/\__,_|___/ | .__/|___/_| |_| |_|\__,_|\__\___|_| |_|
                    |_|
    */

     https://chat.deepseek.com/a/chat/s/a93647ab-c59d-4541-8e2c-8fbb462ed5ae
     please provide all the options for propensity scoring psm using sas psmatch

     PROC PSMATCH Statement Options

     DATA=:                       Input dataset
     REGION=:                     Common support region (ALL, TREATED, CONTROL, OVERLAP)
     OBS=:                        Output dataset for matched observations
     OBSID=:                      Variable name for observation ID
     PS=:                         Output dataset with propensity scores
     SEED=:                       Random number seed
     CALIPER=:                    Caliper width for matching
     CALIPERMULT=:                Multiplier for caliper width
     REPLACE=:                    Whether to allow replacement (YES/NO)
     N=:                          Number of matches per unit (for k:1 matching)
     DISTANCE=:                   Distance metric (LOGIT, PROBIT, etc.)
     EXACT=:                      Variables for exact matching
     GREEDY=:                     Use greedy matching algorithm (YES/NO)
     METHOD=:                     Matching method (GREEDY, OPTIMAL, etc.)
     ORDER=:                      Order for matching (DATA, RANDOM, LARGEST, SMALLEST)

     PSMODEL Statement Options
     LINK=:                       Link function for propensity score model (LOGIT, PROBIT, CLOGLOG)
     INCLUDE=:                    Variables to always include in model
     SELECTION=:                  Variable selection method (BACKWARD, FORWARD, STEPWISE, NONE)
     STOP=:                       Stopping criterion for variable selection
     DETAILS=:                    Display model details (YES/NO)

     MATCH Statement Options
     METHOD=:                     Matching method (GREEDY, OPTIMAL, etc.)
     K=:                          Number of matches per unit
     CALIPER=:                    Matching caliper
     REPLACE=:                    Allow replacement (YES/NO)
     DISTANCE=:                   Distance metric (LOGIT, PROBIT, etc.)
     EXACT=:                      Variables for exact matching
     ORDER=:                      Order for matching (DATA, RANDOM, LARGEST, SMALLEST)

     ASSESS Statement Options

     PLOTS=:                      Types of diagnostic plots (ALL, LOVEPLOT, PSGRAPH, etc.)
     BINS=:                       Number of bins for balance assessment
     VAR=:                        Variables for balance assessment
     STANDARDIZE=:                Standardize differences (YES/NO)


     OUTPUT Statement Options

     OUT=:                        Output dataset
     OBS=:                        Output dataset for matched  observations
     PS=:                         Output dataset with propensity scores
     STATS=:                      Output dataset with  matching statistics

    Common Matching Methods Available

    Nearest neighbor matching (with or  without replacement)
     Optimal matching
     Greedy matching
     Caliper matching
     Mahalanobis distance matching
     Exact matching
     Stratification/Subclassification
     Radius matching
     Kernel matching
     Local linear matching

    /*              _
      ___ _ __   __| |
     / _ \ `_ \ / _` |
    |  __/ | | | (_| |
     \___|_| |_|\__,_|

    */
