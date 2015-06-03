---
layout: post
title: "Advances of the last week"
date: 2015-06-03 16:41:23 +0200
comments: true
categories: 
---

Last week we finalized with "homework" and we have successfully solved them:

- Solve the problems with workflows. ✓ [nipype is not working as i was expecting, so if you want to use the data generated during the workflow, you have to specifically save it] 
{% codeblock lang:py %}
	np.savetxt(in_file+'_corr.txt', corr_mat)
{% endcodeblock %}
- Try real data over the workflows and see if we can get some interesting results. ✓ [it has been done using different type of data (filtered, non-filtered...), different measures (corr, pcorr and MI) and, in the case of MI, different bin(states) numbers (see below)] 
- Implement further synchronization analysis measures.  ✓ [partialcorr workflow was added to the package and Entropy Correlation Coefficient was implemented as in [\[1\]](http://www.futuremedicine.com/doi/abs/10.2217/14622416.7.3.407)
{% codeblock lang:py %}
	def entropy_cc(X,Y): 
    
    """
    Entropy correlation coefficient p(H) = sqrt(I(X,Y)/0.5*(H(X)+H(Y)))
    """
    import numpy as np
    from CPAC.series_mod import entropy
    from CPAC.series_mod import mutual_information
    
    Hx = entropy(X)
    Hy = entropy(Y)
    Ixy = mutual_information(Y,X)
    ECC = np.sqrt(Ixy/(0.5*(Hx+Hy)))
    
    return ECC   
{% endcodeblock %}

We also progressed in discussion, I've a clearer vision of how to implement Multivariate Conditional Granger Causality, based on the one in [\[2\]](http://www.sciencedirect.com/science/article/pii/S0165027013003701) , implementing "conditional" Granger Causality, understood as "one variable causing a second variable if the prediction error variance of the first is reduced after including the second variable in the model, with all other variables included in both cases" as in [\[3\]](http://www.sciencedirect.com/science/article/pii/S0165027008002379). For this purpose, we will program an algorithm that uses the autocovariance sequence and discover whether one of these sequences is reducing the uncertainty of the second (with the mean prediction error), having into account the rest of the series. The order of the model will be likely to be 1, since the TR has a low frequency and the dynamics between the source and the target for order higher would be too separated from each other.
The implementation is still in research phase, but I've done some experiments with randomly generated series:

{% codeblock lang:py %}
X = np.random.randn(1000)
Y = np.random.randn(1000)

Z = Y.copy();
Z[1:] = Z[1:] + X[0:-1] #so: X influences Z with timelag 1 (order)
{% endcodeblock %}

And then, as explained [here](http://www.nld.ds.mpg.de/~timeseries/presentations/granger.py).

With this, we are heading towards a transition from the first to the second box of measures that we were thinking of (the first one more related to preprocessing like filtering, synchronization, basic entropies... and the second one as an analysis module, where we are adding Conditional Granger Causality, Mutual Information, Entropy Correlation Coefficient and many more to come). Anyway, we are not closing the door of the first box, since we have in mind to implement surrogate analysis as in [\[4\]](http://www.sciencedirect.com/science/article/pii/S0167278900000439) to unveil non-linear phenomena occurring.

Moreover, we were thinking in implementing some kind of preprocessing scripts for frequency-filtering and computing the z-score of the series. Actually, an easy band_pass filter was added to [the repo](https://github.com/erramuzpe/C-PAC/blob/series_mod/CPAC/series_mod/utils.py#L409-L427) . But then we realised that CPAC already has this functionality implemented, it works fine and there is no point in duplicating it. We will save the bp filter in case we want to use it later on, as we have seen that could be interesting to save some specific bandwidth for some analyses [\[5\]](http://www.sciencedirect.com/science/article/pii/S1053811910011602). 

## **Testing workflows with real data**

here results coming...

- Explain problem with masks
- Explain diff outputs with diff bins
smth else?

