---
layout: post
title: "Bonding period discussion: Measures to work with"
date: 2015-05-22 10:15:01 +0200
comments: true
categories: 
---



During the Bonding Period, we have been discussiing some measures more thoroughly, thinking in the possible benefits that C-PAC could obtain of implementing and integrating them. Finally, we decided to focus on three main blocks: 

__1 - Granger Causality:__
  
 GC  is the term used to measure the ability of predicting the future values of a time series using past values of another time series. Unlike in Information Theory measures, it can be applied to the timeseries directly. We have in mind of developing two methods:

 - Granger Causality Test: This test is a statistical hypothesis test for determining whether one time series is useful in forecasting another, using GC terms.

 - Pairwise GC: This measure excludes the effect of other series in the GC calculation in the multivariate approach. It has a high computational demand. 


__2 - Complex measures:__ 
 
 Some measures to be effective finding connectivity patterns that could be implemented:

 - Avalanches: The importance of this measure relies on the observation that large-scale brain dynamics can be traced as discrete scale-free avalanches. The algorithm has some sub-phases that can be useful too:
	- Point process analysis: Is the first step to avalanche calculation and also could be a good dimensionality reductor for other problems. 
	- Clustering and avalanche detection: There are already some clustering algorithms in CPAC that could be reused for this purpose.

 - Multi-fractal analysis, DFA (based on Hurst exponents):  Fractal processes, like trees or coastlines, are defined by self-similarity or power law scaling controlled by a single exponent, simply related to the fractal dimension or Hurst exponent of the process. Multifractal processes, like turbulence, have more complex behaviours defined by a spectrum of possible local scaling behaviours or singularity exponents.
 

__3 - Other measures:__
  
 - Cross correlation: Pairwise analysis of series looking for a direct correlation between them. The results obtained could be useful to analyze with graph-based methods.

 -  Autocorrelation: Is the same approach as Granger Causality, but looking to lagged correlation values instead of coefficients. It gives a fast and reliable information about the series, being analyzed with itself.

 - Fingerprints:  A fingerprinting algorithm is a procedure that maps an arbitrarily large data item to a much shorter bit string. We are looking for fMRI suitable fingerprints such IC-fingerprints based on frequency analysis or structural-fingerprints.

 - Surrogates: Understood as a tool for testing non-linearity in the data.
  

These measures will be furtherly discussed and how are they implemented will depend largely in the emerging ideas. Anyway, we have a timing (plan) for the project. We will work each month in one of the blocks mentioned, having the fourth week reserved for testing over large datasets and code refining (efficiency improvements). 

Moreover, our intention is to keep developing methods for C-PAC after the project period expires, since we are noticing that this can be a good opportunity of both learning and developing new methods. 
