---
layout: post
title: "Coding has begun!"
date: 2015-05-26 17:31:20 +0200
comments: true
categories: 
---

Yesterday was the first day of coding of the program, I hope this summer to be a great learning period!

We are now working in a first package of measures that allow us to do a preanalysis of the data and detect non-linear components. We are into measures like Autocorrelation, Entropies (lagged), frequency filtering and many more. 

Some problems with memory handling just arose, computing the voxel-wise correlation. We will discuss further how to solve this problem, but we found some solutions [\[1\]](http://stackoverflow.com/questions/13044880/memory-efficient-storage-of-many-large-scipy-sparse-matrices) [\[2\]](http://stackoverflow.com/questions/11002247/how-to-reduce-python-script-memory-usage) to start doing some research about this. 

I have added a new [partial correlation](https://gist.github.com/fabianp/9396204419c7b638d38f) function developed by Fabian Pedregosa (the author of [Scikit-learn](http://scikit-learn.org/stable/)), quite more demanding computationally than the simple correlation. 

I have improved the readability of the code and removed totally the calls to nitime  by using numpy.corrcoef to calculate the correlation over series. Also, I have cleaned the code and the user can choose between doing voxel-wise analysis or ROI-timeseries analysis with two new functions (probably I should name them differently, since CPACs already has functions named like that). 

{% codeblock lang:py %}
def gen_voxel_timeseries(in_file):
    """
    Extracts voxelwise timeseries and return a np array with them.   
    Parameters
    ----------
    in_file : nifti file
        4D EPI File 
    Returns
    -------
    data_array =  voxel(x,y,z) * timepoints
    """
    import numpy as np    
    import nibabel as nb
    
    img_data = nb.load(in_file).get_data()
    n_samples = img_data.shape[3]
    #print img_data.shape
    (n_x, n_y, n_z, n_t) = img_data.shape
    voxel_data_array = np.reshape(img_data, (n_x*n_y*n_z, n_t), order='F')
     
return  voxel_data_array
{% endcodeblock %}

{% codeblock lang:py %}
def gen_roi_timeseries(in_file, mask_file):
    """
    Extracts ROI timeseries and return a np array with them.    

    Parameters
    ----------
    in_file : nifti file
        4D EPI File       
    mask_file : nifti file
        Mask of the EPI File(Only Compute Correlation of voxels in the mask)
        Must be 3D    
    Returns
    -------
    data_array = ROI_number * timepoints
    """
    import numpy as np    
    import nibabel as nb
    
    img_data = nb.load(in_file).get_data()
    n_samples = img_data.shape[3]

    mask_data = nb.load(mask_file).get_data()
    # Cast as rounded-up integer
    mask_data = np.int64(np.ceil(mask_data)) 

    if mask_data.shape != img_data.shape[:3]:
        raise Exception('Invalid Shape Error.')

    nodes = np.unique(mask_data).tolist()
    nodes.remove(0) #quits the ROI number '0'
    roi_data_array = np.zeros((len(nodes),n_samples)) 
    nodes.sort()
    for n in nodes:
        node_array = img_data[mask_data == n]
        avg = np.mean(node_array, axis=0)
        roi_data_array[n-1] = np.round(avg, 6)
    
return  roi_data_array
{% endcodeblock %}

I have in mind the problem of how to save the data structure ("timeseries * timepoints" or "timepoints * timeseries"). I should ask this in the IRC, an agreement is necessary for building the functions that are going to use this data.  

I will finish this week with the Autocorrelation and Partial-Correlation implementation and testing. Coding these along with Transfer Entropy seem a good first week objective while putting into perspective of coding the points raised during discussion, such as:

- Type and properties of correlations and entropies to calculate (and how these describe the dimensionality of the data and complex patterns).

- Relation with frequency decomposition and pattern formation, we should extend discussion to Wavelets/Hurst spectrum.

- Identify useful elements for the fingerprint method (frequency bands).

To do:

- First objective: work on models of correlation, entropies and discretisation, as well as their statistical properties (Also as recurrence, etc.)
 
- Discuss further the papers from points Cameron made. ECC seems to be a good measure to incorporate to the package. 



