---
layout: post
title: "Information Theory based measures and some advances"
date: 2015-05-21 17:55:05 +0200
comments: true
categories: 
---

During the bonding period, very interesting discussions came across related to the measures we could develop during the project. As an initial exercise, I thought it could be interesting to test some basic [Information Theory](https://en.wikipedia.org/wiki/Information_theory) based measures. Basically, I wanted to reproduce the calculation of [Entropy](https://en.wikipedia.org/wiki/Entropy_%28information_theory%29) and the related measures shown in this image:

{% img left /images/entropy.png 350 350 'image' 'image' %}

The calculation of the Entropy is quite straightforward, so I decided to test different implementations of some people and test them with the [timeit](https://docs.python.org/2/library/timeit.html) module. 

It turned that among 4-5 different functions I found [this one](http://blog.biolab.si/2012/06/15/computing-joint-entropy-in-python/) coded using numpy and allowing also the calculation of the  [Joint Entropy](https://en.wikipedia.org/wiki/Joint_entropy) using itertools was the fastest by far. 

{% codeblock lang:py %}
def entropy(*X):
    n_insctances = len(X[0])
    H = 0
    for classes in itertools.product(*[set(x) for x in X]):
        v = np.array([True] * n_insctances)
        for predictions, c in zip(X, classes):
            v = np.logical_and(v, predictions == c)
        p = np.mean(v)
        H += -p * np.log2(p) if p > 0 else 0
    return H
{% endcodeblock %}

Having the basic blocks, I built the Conditional Entropy and Mutual Information easily: 

{% codeblock lang:py %}
def mutual_information(X,Y):

    Hx = entropy(X)
    Hy = entropy(Y)
    Hxy = entropy(X,Y)
    MI = Hx + Hy - Hxy
    
    return MI

def cond_entropy(X,Y):
    
    Hy = entropy(Y)
    Hyx = entropy(Y,X)
    CE = Hyx - Hy 
    
    return CE
{% endcodeblock %}

I proved the functions over small vectors and all the properties seemed to work well. In the other hand, I sorted out the loading of the fMRI files (that I was loading with an external function) and the calculation of the TR using just the NIFTI header using  [nibabel](http://nipy.org/nibabel/).

The first time trying to calculate the Entropy voxel-wise (it took like 5 mins and used only 1 core, so this must be solved in [some way](https://github.com/FCP-INDI/C-PAC/blob/master/CPAC/pipeline/cpac_pipeline.py#L5880) or [another](https://docs.python.org/2/library/multiprocessing.html)) and over 90 timeseries extracted from a ROI-mask, I failed. As these measures are calculated over probability density functions, the data must be converted into 'states', binarized for example. This is another problem I have to solve: Dealing with fMRI datatypes and FSL functions. I still don't know how to make use directly of FSL utils (and neither about the different FSL options I have. This is a TO DO for my list). 


To solve the problem, I've created my own timeseries extractor (generates a Numpy array, not a CSV file as in _gen_roi_timeseries_._py_):

{% codeblock lang:py %}
    unit_data = nb.load(mask_file).get_data()
    datafile = nb.load(in_file)
    img_data = datafile.get_data()
    n_samples = img_data.shape[3]

    if unit_data.shape != img_data.shape[:3]:
        raise Exception('FAIL')

    nodes = np.unique(unit_data).tolist()
    roi_data = np.zeros((len(nodes)-1,n_samples))

    # Be carefull with number of ROIs and np-arrays
    nodes.sort()
    for n in nodes:
        if n > 0:
            node_array = img_data[unit_data == n]
            avg = np.mean(node_array, axis=0)
            roi_data[n-1] = np.round(avg, 6)
{% endcodeblock %}

This structure can be used to extract all voxels timeseries as well. As work for the next week, this data has to be binarized to two (or more) 'states' and then passed to the IT functions.

