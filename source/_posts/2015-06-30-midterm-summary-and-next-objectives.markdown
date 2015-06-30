---
layout: post
title: "Midterm summary and next objectives"
date: 2015-06-30 12:02:55 +0200
comments: true
categories: 
---


As we have reached the midterm of the project, I am writing this post as a summary of what we have achieved and which are our next objectives. 

#### **Theory and discussion**

In one hand, we have been weekly discussing about the measures we had to implement, having debates about the information we could get from them and feasibility and practicality of these measures integrated in CPAC. Also, it has been a hard-work of researching and learning about new techniques and tools used in the field linked not only to measures related to non-linear analysis of the series, but also to a wide variety of preprocessing techniques, optimization and python language learning.

#### **Coding**

In the other hand, as a consequence of the previous point, we have accomplished the goal of implementing several measures, helper functions and tools building partially a toolbox for the analysis of non-linear information of series. 

We have developed 4 working workflows of measures based on correlation and Information Theory between pairs of variables.

corr
pcorr
MI
TE

Additionally, the toolbox has several measures and helper functions that allow us to extract information or perform treatments over series that still we have not translate them into workflows:


            gen_roi_timeseries
            gen_voxel_timeseries
            transform (discretization of signals based on Equiquantization criteria)
            entropy
            ApEn
            cond_entropy
            entropy_correlation coefficient
            frequency filters.
            phase_sync
            phase locking value

And also a Granger Causality (GC) module (Implementing Pairwise Conditional GC and MultiVariate GC using a VAR model) that still is under testing and bug-correcting phase with the following functions:

[explain]

tsdata_to_autocov
autocov_to_var 
autocov_to_pwcgc
autocov_to_mvgc

Even if we are successfully completing our initial plan, we have still work to do: 

[calendar image]

#### **Testing over real data**

Finally, there has been an application of the developed measures to real fMRI data, having reported some preliminary results. These results can be found in previous entries of this blog. [] [] []

#### **Next objectives and problem solving**

After having summarized what we have done so far until the present moment, I would like to point some TO-DO's and objectives towards the second (and last) part of the project. 

- Merging of both students repositories and having a copy of it in a cluster, allowing running studies on it. 
- Solve code problems (efficiency and some errors in measures).
- Work on the integration with CPAC (GUI building). 
- Keep following the initial plan: Cross-entropies, Fingerprints and Avalanches. 



 [\[1\]](http://www.futuremedicine.com/doi/abs/10.2217/14622416.7.3.407) 

 [\[2\]](http://www.sciencedirect.com/science/article/pii/S0165027013003701)

