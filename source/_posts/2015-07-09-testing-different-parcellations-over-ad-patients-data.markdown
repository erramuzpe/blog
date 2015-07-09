---
layout: post
title: "Testing different parcellations over AD patients data"
date: 2015-07-09 11:23:22 +0200
comments: true
categories: 
---


This week we have been solving several problems we have had with regard to MVGC, after realizing that the model to calculate the residuals that we use to measure the causality could not be the best way of getting the information. We are working on it, making research about how this can be solved and if its neccesary to correct some features or the module needs a restyling. 

In parallel, we have been working with new data, specifically, the one located under the [ADHD200 initiative](http://neurobureau.projects.nitrc.org/ADHD200/Introduction.html). For this purpose, we selected the preprocessed NeuroImage dataset (4mm voxelsize). 

#### ** Data treatment and parcellation**

After having downloaded the data, we ruled out several subjects from the study (left handed...), resulting in 34 total subjects and then they were classified in 2 main groups: Controls and AD patients (20/14). 

Finally, we extracted the mean timeseries of this data using the cc400 and cc1000 masks and performed a test analysis. Corr, TE and MI were computed over the series and results were slightly disappointing.

{% img right /images/AD_400.png 400 400 'image' 'image' %}

After calculating the mean between each group not only there was signifficant difference between different groups, but the measures were close to 0. The results for the 1000 ROI parcellation was similar. This is a behaviour that it was also shown for the YALE dataset that we have been using in previous studies, being the calculation of the measures the same for 90, 400 and 1000 regions, we can just appreciate clear structures in the parcellation of 90 regions. It has not been tested the 90 region parcellation for this data because the voxel and mask size did not match with the present data. 

We are working on this, to prove if it is there any problem with the gereration of ROI timeseries or of it is a cause of the binarization of the data (the results have the same problem for correlation, so is not likely to be problem of this transformation). 

#### **Coding**

These week we also have been doing efforts to transcript Aproximate Entropies and Sampling Entropies into workflows with Ivan's helping, since these functions make use of recurrencies and advanced theory terms.
Additionally, I have added a slightly modificated function used in a previous work by Cameron, that saves the results into a Nifti file, making it easier to keep the results and export them. 


***

