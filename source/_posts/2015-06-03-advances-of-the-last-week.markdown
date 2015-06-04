---
layout: post
title: "Advances of this week"
date: 2015-06-03 16:41:23 +0200
comments: true
categories: 
---

Last week we finalized with "homework" and we have successfully solved them:

- Solve the problems with workflows. ✓ [Currently, to make transformations with  the data generated during the workflow, you have to specifically save it] 
{% codeblock lang:py %}
	np.savetxt(in_file+'_corr.txt', corr_mat)
{% endcodeblock %}
- Try (test and validate) real data over the workflows and see if we can get some interesting results. ✓ [it has been done using different type of data (filtered, non-filtered...), different measures (corr, pcorr and MI) and, in the case of MI, different bin(states) numbers (see 2nd part of the post)] 
- Implement further synchronization analysis measures.  ✓ [partialcorr workflow was studied and added  to the package and Entropy Correlation Coefficient was implemented as in [\[1\]](http://www.futuremedicine.com/doi/abs/10.2217/14622416.7.3.407). Also, it was studied the lossless dimensional discretization by the use of better optimal discretization, sampled  entropies and recurrence statistics.

We also progressed in discussion, I've a clearer vision of how to implement Multivariate Conditional Granger Causality, based on the one in [\[2\]](http://www.sciencedirect.com/science/article/pii/S0165027013003701) , implementing "conditional" Granger Causality in multivariate manner [\[3\]](http://www.sciencedirect.com/science/article/pii/S0165027008002379). For this purpose, we will program an algorithm that uses the autocovariance sequence and discover whether one of these sequences is reducing the uncertainty of the second (with the mean prediction error), having into account the rest of the series. The order of the model will be likely to be 1, since the TR has a low frequency and the dynamics between the source and the target for order higher would be too separated from each other.
The implementation is still in research phase, but I've done some experiments with randomly generated series:

{% codeblock lang:py %}
X = np.random.randn(1000)
Y = np.random.randn(1000)

Z = Y.copy();
Z[1:] = Z[1:] + X[0:-1] #so: X influences Z with timelag 1 (order)
{% endcodeblock %}

And then, as explained [here](http://www.nld.ds.mpg.de/~timeseries/presentations/granger.py).

With this, we are heading towards a transition from the first to the second box of measures that we were thinking of (the first one more related to preprocessing like filtering, synchronization, basic entropies... and the second one as an analysis module, where we are adding Conditional Granger Causality, Mutual Information, Entropy Correlation Coefficient and many more to come). Anyway, we are not closing the door of the first box, since we have in mind to implement surrogate analysis as in [\[4\]](http://www.sciencedirect.com/science/article/pii/S0167278900000439) to unveil non-linear phenomena occurring.

Moreover, we were thinking in implementing some kind of preprocessing scripts for frequency-filtering and computing the z-score of the series. Actually, an easy band_pass filter was added to [the repo](https://github.com/erramuzpe/C-PAC/blob/series_mod/CPAC/series_mod/utils.py#L409-L427) . But then we realised that CPAC already has this functionality implemented, it works fine and there is no point in duplicating it. We will save the bp filter in case we want to use it later on, as we have seen that could be interesting to save some specific bandwidth for some analyses [\[5\]](http://www.sciencedirect.com/science/article/pii/S1053811910011602). We will further discuss these options, as we are heading towards frequency and coherence analysis as we see that it will bring us interesting possibilities.   

## **Testing workflows with real data**

I've tested the Correlation (corr), Partial Correlation (pcorr) and Mutual Information (MI) (with 3 different bin_number: 20, 50 and 100) workflows over a dataset of 29 subjects (196 timepoints*voxel) and a 90 ROI parcellation mask (another mask with more parcellations was used, see below). The dataset was preprocessed and filtered previously. I've printed some graphs to comment on results:
 
a) Randomly selected one matrix (one subject) of each one of the measures: corr, pcorr and MI20 (MI50 and MI100 were too sparse, see below). Corr and MI captured some structures (most of them in common) and pcorr was not able to. We are going to try to figure out what happened with pcorr and if this is a suitable result for this measure. 
In the other hand, results were quite robust between subjects, unveiling similar relations between ROIs. See below the mean matrix over subjects. 

{% img center /images/rand_all.png 800 300 'image' 'image' %}


b) Mean matrix over subjects was computed for each one of the measures of the graphic a), see figure for the mean-corr and mean-pcorr ( means of MI20, MI50 and MI100 below). Then, a threshold of 0.7, 0.3 was applied for corr and pcorr consecutively (that is because pcorr had very small values) giving 1 to values passing it and 0 for the rest. MI20 was left without change. After that, a sum over rows was applied to the matrices (in green) and sorted (in blue), so corr and pcorr show amount of pairs of rows passing the threshold, while MI20 shows just a sum over values of different ROIs.

{% img right /images/mean_all.png 800 200 'image' 'image' %}

{% img right /images/all_dist.png 800 200 'image' 'image' %}


c) Mean values over subjects for MI20, MI50 and MI100. The principal diagonal was set to 0 for visualization purposes, as these values represent the Entropy and are higher that the rest. As can be seen, the reported structures change with the amount of bins chosen.  Having a very blurry effect in MI100 and structures better defined in MI20 (more small MI values, but structures better captured). This is mainly because having 100 states for a signal with 196 timepoints, reduces the possibility of repeating a state, thus, the maximum value of the MI increases (approximating the  maximum possible Entropy) as the number of bins increases. 

{% img right /images/MI_bins.png 800 200 'image' 'image' %}

In this last figure, it can be seen the repetition frequency for values that pass the threshold as calculated in b). The threshold is chosen as the 70% of the maxvalue of each mean-matrix. As seen in the previous figure, MI100 has a higher number of ROI pairs over the th value, but they are caused by the discretization effect and not unveiling interesting structures in the data. 

{% img right /images/freq_MI.png 800 200 'image' 'image' %}

 
### **Mask and parcellations issues**

We also came across with another problem that we would like to solve; when a user selects a mask that is not equally fitting the fMRI image i.e. the shappes are not the same, the computation can not be done. Our goal is that this gets solved, probably within the main workflow. 

Another issue is that we also performed a small test over Cameron's 400 region [parcellation mask](http://ccraddock.github.io/cluster_roi/atlases.html), but the resulting matrixes were quite sparse comparing to the one with 90 ROIs. We will keep researching why this happened. 



