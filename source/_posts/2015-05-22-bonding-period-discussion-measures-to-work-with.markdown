---
layout: post
title: "Notes from the bonding period and Discussion: Initial plan of implementation"
date: 2015-05-22 10:15:01 +0200
comments: true
categories: 
---



__1 - Theoretical discussion:__
  

During the Bonding Period, we have been discussiing some measures more thoroughly, thinking in the possible benefits that C-PAC could obtain of implementing and integrating them into a general toolkit around the ideas of . As a test period we have been working on some approaches and analysis tools.




__2 - Collection of measures:__ 


Determination of non-linear components, transient and singularities:

-  Autocorrelation: Is the same approach as Granger Causality, but looking to lagged correlation values instead of coefficients. It gives a fast and reliable information about the series, being analyzed with itself.
 
 - Entropies and information probabilistic content, partial correlations and sampling formalism (binarizing...) 




- Cross correlation: Pairwise analysis of series looking for a direct correlation between them. The results obtained could be useful to analyze with graph-based methods.

Granger Causality  is the term used to measure the ability of predicting the future values of a time series using past values of another time series. Unlike in Information Theory measures, it can be applied to the timeseries directly. We have in mind of developing two methods:

 - Granger Causality Test: This test is a statistical hypothesis test for determining whether one time series is useful in forecasting another, using GC terms.

 - Pairwise GC: This measure excludes the effect of other series in the GC calculation in the multivariate approach. It has a high computational demand. 
 
Some measures to be effective finding connectivity patterns that could be implemented:

 - Avalanches: The importance of this measure relies on the observation that large-scale brain dynamics can be traced as discrete scale-free avalanches. The algorithm has some sub-phases that can be useful too:
	- Point process analysis: Is the first step to avalanche calculation and also could be a good dimensionality reductor for other problems. 
	- Clustering and avalanche detection: There are already some clustering algorithms in CPAC that could be reused for this purpose.

 - Multi-fractal analysis, time scaling formalisms...:  Fractal processes, like trees or coastlines, are defined by self-similarity or power law scaling controlled by a single exponent, simply related to the box-counting dimension or Hurst exponent of the process. Multifractal processes, like turbulence, have more complex behaviours defined by a spectrum of possible local scaling behaviours or singularity exponents.

 - Fingerprints:  A fingerprinting algorithm is a procedure that maps an arbitrarily large data item to a much shorter bit string. We are looking for fMRI suitable fingerprints such IC-fingerprints based on frequency analysis or structural-fingerprints.

__3 - Disscussion on implementation:__
  
These measures will be furtherly discussed and how are they implemented will depend largely in the emerging ideas. Anyway, we have an improved timing (plan) for the project which we are translating into the workflow. In term of metrics: We will work each month in some of the blocks mentioned, having the fourth week reserved for testing over large datasets and code refining (efficiency improvements).

{% img center /images/calendar.png 700 700 'image' 'image' %} 

Moreover, our intention is to keep developing methods for C-PAC after the project period expires, since we are noticing that this can be a good opportunity of both learning and developing new methods. 
