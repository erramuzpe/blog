---
layout: post
title: "First scripts for C-PAC: this is just the beginning"
date: 2015-05-08 15:40:53 +0200
comments: true
categories: 
---

In this blog I will write about my experience with C-PAC development. Our intention here is to develop a series of scripts that allow us (C-PAC users) to calculate some measures over non-linear fMRI timeseries and integrate them in a workflow. Our aim is to extend and improve C-PAC's features. 

In this first post I will try to explain some of what I have done for my first two scripts for C-PAC as my first contact with C-PAC. I would like to learn a lot about C-PAC's structure, workflows etc. and give back too. The goal of this first scripts is to say 'hi', have some feedback from main developers and take a first hands-on with the code. 

In order to learn from [C-PAC's repo](https://github.com/FCP-INDI/C-PAC) on [on GitHub](https://github.com) and to C-PAC's forums and I saw a [question](https://groups.google.com/forum/#!topic/cpax_forum/GMnwQD7B8l8) on cross correlations and though it could be a good exercise to try possibilities (out of using other available utils like FSLcc) and afterwards, I also made the calculation for Transfer Entropy (TE) Granger Causality (GC)). As a short description, these 2 scripts calculate correlations and GC between ROI-time series extracted from an fMRI file (this is done using a ROI-mask with the _gen_roi_timeserier.py_ function which is already in C-PAC). 

My code is in the copy I [forked](https://github.com/erramuzpe/C-PAC), in a new branch called [series_mod](https://github.com/erramuzpe/C-PAC/tree/series_mod). I saved my scripts in a folder of the same name. 

We have got here 3 files, __init_ _ _.py_, _series_mod.py_ and _utils.py_ . 
I used these three files from a template of other toolbox in C-PAC. [_series_mod.py_](https://github.com/erramuzpe/C-PAC/blob/series_mod/CPAC/series_mod/series_mod.py) generates the workflows: 


{% codeblock lang:py %}
def create_corr():
    corr = pe.Workflow(name='corr')
    inputNode = pe.Node(util.IdentityInterface(fields=[
                                                'in_file',
                                                'mask',
                                                'TR'
                                                ]),
                        name='inputspec')

    outputNode = pe.Node(util.IdentityInterface(fields=[
                                                    'corr_mat']),
                        name='outputspec')

    corr_mat = pe.Node(util.Function(input_names=['in_file', 'mask','TR'],
                                   output_names=['out_file'],
                     function=compute_corr),
                     name='corr_mat')

    corr.connect(inputNode, 'in_file',
                    corr_mat, 'in_file')
    corr.connect(inputNode, 'mask',
                    corr_mat, 'mask')
    corr.connect(inputNode, 'TR',
                    corr_mat, 'TR')                   
    corr.connect(corr_mat, 'out_file',
                 outputNode, 'corr_mat')
    return corr
{% endcodeblock %}

There are 3 input parameters; fMRI series, the ROI-mask and TR of the fMRI. This function makes use of the functions in [_utils.py_](https://github.com/erramuzpe/C-PAC/blob/series_mod/CPAC/series_mod/utils.py). Let's see part of the code (present in my repo):
 
 

{% codeblock lang:py %}
def compute_corr(in_file, mask_file, TR):

    """
    Computes the Network Correlation Matrix for ROIs in the mask
    """

    import numpy as np
    from matplotlib.mlab import csv2rec
    from cpac.timeseries import gen_roi_timeseries
   
    #Import the time-series objects:
    from nitime.timeseries import TimeSeries
    #Import the analysis objects:
    from nitime.analysis import CorrelationAnalyzer
    #Import utility functions:
    from nitime.utils import percent_change
   
    output_type = [True,False] #list of boolean for csv and npz file formats
   
    data = gen_roi_timeseries(in_file, mask_file, output_type)
    #from this files gen_roi_timeseries
    #once we have the time series:
    data_rec = csv2rec(data[2])
   
    #Extract information:
    roi_names = np.array(data_rec.dtype.names)
    n_samples = data_rec.shape[0]
   
    #Make an empty container for the data
    data = np.zeros((len(roi_names), n_samples))
   
    for n_idx, roi in enumerate(roi_names):
        data[n_idx] = data_rec[roi]
   
    #Normalize the data:
    data = percent_change(data)
   
    T = TimeSeries(data, sampling_interval=TR)
    T.metadata['roi'] = roi_names
    #Initialize the correlation analyzer
    C = CorrelationAnalyzer(T)
   
    ## IN CASE WE WOULD LIKE TO ADD A THRESHOLD FEATURE (set inside the function
    # or from input)
    # C.corrcoef[C.corrcoef<0.7] = 0
    return  C.corrcoef
{% endcodeblock %}

It is a trivial computation after extracting the time-series. This data could have been treated or filtered, but this first scripts were just to train myself with the repo, python, nypipe workflows etc. . 

I started from my own implementations in python, but also took some of this code and ideas from these tutorials:

[1] http://nipy.org/nitime/examples/resting_state_fmri.html

[2] http://nipy.org/nitime/examples/granger_fmri.html 


The result for the correlation when making calculations on preprocessed data of 3mm voxel-size and a mask of 90 ROIs parcellation is 

{% img left /images/result.png 350 350 'image' 'image' %}

I will discuss about the measures we are going to work on in the next post. 




