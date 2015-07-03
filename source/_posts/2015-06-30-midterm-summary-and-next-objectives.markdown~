---
layout: post
title: "Midterm summary and next objectives"
date: 2015-06-30 12:02:55 +0200
comments: true
categories: 
---

As we have reached the midterm of the project, I am writing this post as a summary of what we have achieved and which are our next objectives. Our overall evaluation of our work is positive and we are excited about keep the coding and learning.

#### **Theory and discussion**

In one hand, we have been weekly discussing about the measures we had to implement, having debates about the information we could get from them and feasibility and practicality of these measures integrated in CPAC. Also, it has been a hard-work of researching and learning about new techniques and tools used in the field linked not only to measures related to non-linear analysis of the series, but also to a wide variety of preprocessing techniques, optimization and python language learning.

#### **Coding**

In the other hand, as a consequence of the previous point, we have accomplished the goal of implementing several measures, helper functions and tools building partially a toolbox for the analysis of non-linear information of series that will need triming and revision, on the principles of utility and complementarity. Organisation of these measures and outputs are also to come.

I have developed 4 working workflows of measures based on correlation and Information Theory between pairs of variables (Ivan has developed others complementing my work):

- Correlation WF
- Partial Correlation WF
- Mutual Information WF
- Transfer Entropy WF


Additionally, the toolbox has several measures and helper functions that allow us to extract information or perform treatments over series that still we have not translate them into workflows:

- Other measures:
  - Shannon entropy
  - ApEn
  - Conditional entropy
  - Entropy Correlation Coefficient
  - Phase synchronization
  - Phase Locking Values
  - Espectrum (Phase) Entropy
  - TE based in covariances for gaussian variables (no need of discretization).
  - Entropy Correlation Coefficient 
- Helper functions:
  - Voxel-wise timeseries generator from fMRI data
  - ROI timeseries generator from fMRI data
  - Transform (BIC discretization of signals based on Equiquantization criteria)
  - Frequency filters
  - Phase extractors
  

And also a Granger Causality (GC) module (Implementing Pairwise Conditional GC and MultiVariate GC using a VAR model) that still is under testing and bug-correcting phase with the following functions that can be found in [GitHub](https://github.com/erramuzpe/C-PAC/blob/series_mod/CPAC/series_mod/mvgc.py):

- Granger Causality functions based on VAR model:
  * tsdata_to_autocov: Timeseries to autocovariance matrix calculation
  * autocov_to_var: Calculation of the coefficients and the residuals using the VAR model.
  * autocov_to_pwcgc: Calculation of the Pairwise Granger Causality using the residuals previously calculated. 
  * autocov_to_mvgc: Calculation of the Multivariate Granger Causality using the residuals previously calculated. 



#### **Testing over real data**

Finally, there has been an application of the developed measures to real fMRI data, having reported some preliminary results. These results can be found in previous entries of this blog:

[Correlation, Entropies and Mutual Information](http://erramuzpe.github.io/C-PAC/blog/2015/06/03/advances-of-the-last-week/), [Transfer Entropy](http://erramuzpe.github.io/C-PAC/blog/2015/06/17/new-measures-transfer-entropy/) and [Phase Synchrony](http://erramuzpe.github.io/C-PAC/blog/2015/06/18/some-examples-with-phase-synchrony-measures/).


We are towards starting using extensively the Ncorr database for real data usage with the measures and stablish the use of new atlases to extend the 90 ROIs that we have been working with to 512 or/and 1024 regions. Also, our intention is to find new ways of representing our results. An idea of how we could represent our matrices in a space that we already know is explained [here](http://wiki.biac.duke.edu/biac:analysis:resting_pipeline), by taking correlation matrixes (could be any other measure's bi-directional results matrix) and generating figures like these:

{% img left http://wiki.biac.duke.edu/_media/biac:analysis:3d_functionalconnectome.png?cache=&w=900&h=738&tok=7a2a44 350 350 'image' 'image' %}


{% img center http://wiki.biac.duke.edu/_media/biac:analysis:2d_functionalconnectome.png?w=400&h=442&tok=5ee755 300 350 'image' 'image' %}




***



## **Next objectives**

Even if we are successfully completing our initial plan, we have still work to do: 

{% img center /images/calendar_midterm.png 500 500 'image' 'image' %}

After having summarized what we have done so far until the present moment, I would like to point some TO-DO's and objectives towards the second (and last) part of the project. 

- Merging of both students repositories and having a copy of it in a cluster, allowing running studies on it. 
- Solve code problems and developer strategies alike to CPAC already existing code (efficiency and some errors in measures).
- Work on the integration with CPAC (GUI building). 
- Keep following the initial plan: Cross-entropies, Fingerprints and Avalanche-point process measures. Discuss extensively about features to add, most relevant measures and future aplications over real data.
- Implement measures of multiscale dynamics .
- We have now a primitive and easy version of calculating the numbers of bins via Equantization. We are discussing more advanced methods to do it and it is likely to develop an alternative. 
- Related to frecuencies (develop using what we already have as a base):
  - Implement Phase Entropy and Sample Entropies.
  - Fingerprints -> Determine important frequencies. Use of the power spectrum. 



