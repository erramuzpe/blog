---
layout: post
title: "Clustering and Avalanche detection algorithms [POSTING IN PROGRESS]"
date: 2015-08-14 13:10:51 +0200
comments: true
categories: 
---

This week, as planned from the previous one, the integration has been extensively worked: the _cpac_pipeline.py_ has now our workflow fully integrated and I have change the workflows into a single one, making it easier the call to the methods. Also, the treatment of the inputs has changed, since we are not making the extraction of the series anymore (another module of C-PAC is taking the responsibility). But more interestingly, we have enforced the Criticality package with the correction of the Point Process and Conditional Rate Mapping from the previous week and the developing of 2 new functions: Clustering and Avalanche detection algorithms.

For this algorithms, we have also followed the headlines explained in the paper of [Tagliazucchi et al](http://journal.frontiersin.org/article/10.3389/fphys.2012.00015/abstract) ("_Criticality in large-scale brain fMRI dynamics unveiled by a novel point process analysis_"), we have been able to extract Avalanches in the brain, wich opens a new perspective in the analysis of brain dynamics in C-PAC.


## ** Clustering Detection  **

The technique is described, extracted from the section  _Methods_ of the paper, as:

_To detect contiguous clusters of activated voxels (defined as those crossing the threshold), for each time step, the problem was reduced to the detection of connected components in a suitably defined graph or network. More precisely, for each volume, a graph was constructed having each voxel as a node, and two nodes connected with a link if they were both activated (BOLD signal above 1 SD) and also first neighbors in the spatial sense. The connected components of this graph correspond to clusters of contiguous activated voxels isolated from other similarly defined clusters._

The implementation of this approach was straightforward, but it has many checks to do, so it is an algorithm to code very carefully. It is a first draft of the algorithm and the purpose was to have a functional procedure asap. In future revisions, I will try to rewrite it in a more “pythonic way”. 

{% codeblock lang:py %}
# Detects clusters after Point Processing a Brain 
def cluster_detection(in_file):

    import numpy as np
    import os
    import nibabel as nb
    from CPAC.criticallity import point_process

    # Treat fMRI image
    img = nb.load(in_file)
    data = img.get_data() 
    (n_x, n_y, n_z, n_t) = data.shape
    
    # Get the PP data
    pp_data = np.zeros((n_x, n_y, n_z, n_t))
    for i_ in range(n_x):
        for j_ in range(n_y):
            for k_ in range(n_z):
                voxel_data = data[i_,j_,k_,:] 
                pp_data[i_,j_,k_,:] = point_process(voxel_data)
    
    cluster_graph_data_total = np.zeros((n_x, n_y, n_z, n_t))
    for t_ in range(n_t):
        time_slice = pp_data[:,:,:,t_]
        cluster_graph_data = np.zeros((n_x, n_y, n_z))  
        cluster_number = 1
        
        for i_ in range(n_x):
            for j_ in range(n_y):
                for k_ in range(n_z):
                    
                    if time_slice[i_,j_,k_] == 1: # is active, check if it has active neighboours
                        if time_slice[i_-1,j_,k_] or time_slice[i_+1,j_,k_] \
                        or time_slice[i_,j_-1,k_] or time_slice[i_,j_+1,k_] \
                        or time_slice[i_,j_,k_-1] or time_slice[i_,j_,k_+1]:
                            
                            if cluster_graph_data[i_,j_,k_] == 0: # if is not in any previous cluster
                                this_cluster = (cluster_graph_data[i_-1,j_,k_] or cluster_graph_data[i_+1,j_,k_] \
                                or cluster_graph_data[i_,j_-1,k_] or cluster_graph_data[i_,j_+1,k_] \
                                or cluster_graph_data[i_,j_,k_-1] or cluster_graph_data[i_,j_,k_+1])
                                
                                if this_cluster == 0: #no neighbours in any previous cluster neither
                                    this_cluster = cluster_number
                                    cluster_graph_data[i_,j_,k_] = this_cluster
                                    cluster_number = cluster_number + 1
                                else: 
                                    #check cluster union
                                    merge_clusters = np.unique([cluster_graph_data[i_-1,j_,k_], cluster_graph_data[i_+1,j_,k_] \
                                , cluster_graph_data[i_,j_-1,k_], cluster_graph_data[i_,j_+1,k_] \
                                , cluster_graph_data[i_,j_,k_-1], cluster_graph_data[i_,j_,k_+1]])
                                    merge_clusters = merge_clusters[1:] #quit first value = 0
                                    
                                    this_cluster = merge_clusters[0]
                                    cluster_graph_data[i_,j_,k_] = this_cluster
                                    for cluster_to_merge in merge_clusters[1:]:
                                        cluster_graph_data[cluster_graph_data == cluster_to_merge] = this_cluster
                                    
                                    
                            else:
                                this_cluster = cluster_graph_data[i_,j_,k_]
                                
                            #find neighbours and give cluster_number
                            if time_slice[i_-1,j_,k_] == 1:
                                cluster_graph_data[i_-1,j_,k_] = this_cluster
                            elif time_slice[i_+1,j_,k_] == 1:
                                cluster_graph_data[i_+1,j_,k_] = this_cluster
                            elif time_slice[i_,j_-1,k_] == 1:
                                cluster_graph_data[i_,j_-1,k_] = this_cluster
                            elif time_slice[i_,j_+1,k_] == 1:
                                cluster_graph_data[i_,j_+1,k_] = this_cluster
                            elif time_slice[i_,j_,k_-1] == 1:
                                cluster_graph_data[i_,j_,k_-1] = this_cluster
                            elif time_slice[i_,j_,k_+1] == 1:
                                
                    # if not == 1¡, keep the search 
                        # if not neighbours, keep the search
                       
        cluster_graph_data_total[:,:,:,t_] = cluster_graph_data 
        
    return cluster_graph_data_total 
{% endcodeblock %}



## ** Avalanche Detection **

The technique can be easily described, extracted from the section  _Methods_ of the paper:

_The point process is defined by the sequences of time points at which the BOLD signal crosses a given threshold from below. (...) To construct the conditional rates the point process is defined at a seed location and at the targets throughout the entire brain. The BOLD signal is extracted from a seed region (top trace) and the points (arrows and vertical dashed lines) are defined by the crossings at 1 SD (horizontal dashed lines). Every time the signal at a target region crosses the threshold (asterisks) up to 2 time steps later than in the seed, the rate at the target is increased in one unit. This rate is normalized by the number of points in the seed. The top panel shows the location of the seed and of the two example targets, as well as the resulting average conditional rates maps (left) and DMN obtained from PICA (right). Medium panels show the BOLD signal at the seed and at the two example target regions._


{% codeblock lang:py %}
def avalanche_detec(cluster_file):
    
    import numpy as np
    import nibabel as nb
    import os

    # Treat fMRI image
    img = nb.load(cluster_file)
    cluster_data = img.get_data()
    (n_x, n_y, n_z, n_t) = cluster_data.shape
    
    avalanche_id_total = np.zeros((n_x, n_y, n_z, n_t))
    avalanche_id_num = 1
            
    for t_ in range(n_t):
     
        if t_ == 0: #if first timestep, all are candidates
            time_slice = cluster_data[:,:,:,t_]          
            time_slice_fut = cluster_data[:,:,:,t_+1]
            
            avalanche_id_now = avalanche_id_total[:,:,:,t_]   
            avalanche_id_fut = avalanche_id_total[:,:,:,t_+1]   

            for cluster in np.unique(cluster_data[:,:,:,t_])[1:]: #iterate over clusters
                # NEW AVALANCHE CASE
                if np.count_nonzero(time_slice_fut[(time_slice==cluster)]) >= 1 :                
                    avalanche_id_now[(time_slice==cluster)] = avalanche_id_num 
                    
                    for value in np.unique(time_slice_fut[(time_slice==cluster)])[1:]:
                        avalanche_id_fut[(time_slice_fut==value)] = avalanche_id_num
                    
                    avalanche_id_num = avalanche_id_num +1
                    
                    avalanche_id_total[:,:,:,t_] = avalanche_id_now
                    avalanche_id_total[:,:,:,t_+1] = avalanche_id_fut
                    
                    
        elif t_ < (n_t-1):  #if not first timestep, check previous
            print t_
            #time_slice_past = cluster_data[:,:,:,t_-1]
            time_slice = cluster_data[:,:,:,t_]          
            time_slice_fut = cluster_data[:,:,:,t_+1]
            
            avalanche_id_now = avalanche_id_total[:,:,:,t_]   
            avalanche_id_fut = avalanche_id_total[:,:,:,t_+1] 
            
            for cluster in np.unique(cluster_data[:,:,:,t_])[1:]:
                # PREVIOUS AVALANCHE CASE
                if np.count_nonzero(avalanche_id_now[(time_slice==cluster)]) != 0: 
                    if np.count_nonzero(time_slice_fut[(time_slice==cluster)]) >= 1 :

                        this_avalanche = avalanche_id_now[(time_slice==cluster)][0]
                        
                        for value in np.unique(time_slice_fut[(time_slice==cluster)])[1:]: 
                            avalanche_id_fut[(time_slice_fut==value)] = this_avalanche
                        
                        avalanche_id_total[:,:,:,t_+1] = avalanche_id_fut 
                
                # NEW AVALANCHE CASE
                elif np.count_nonzero(avalanche_id_now[(time_slice==cluster)]) == 0: #and np.count_nonzero(time_slice_past[(time_slice==cluster)]) == 0:
                    if np.count_nonzero(time_slice_fut[(time_slice==cluster)]) >= 1 :
                        
                        avalanche_id_now[(time_slice==cluster)] = avalanche_id_num
                        
                        for value in np.unique(time_slice_fut[(time_slice==cluster)])[1:]:
                            avalanche_id_fut[(time_slice_fut==value)] = avalanche_id_num
                            
                        avalanche_id_num = avalanche_id_num + 1
                   
                        avalanche_id_total[:,:,:,t_] = avalanche_id_now
                        avalanche_id_total[:,:,:,t_+1] = avalanche_id_fut    
    
    
    img_new = nb.Nifti1Image(avalanche_id_total, header=img.get_header(), affine=img.get_affine())
    # Reconstruct the 4D volume
    cond_rm_file = os.path.join(os.getcwd(), 'avalanche.nii.gz')
    img_new.to_filename(cond_rm_file)
    
    return avalanche_id_total 
{% endcodeblock %}


## ** Discussions on Clustering and Avalanche detection **

#### ** Clustering Detection **

After coding the algorithms as explained in the paper, we extended it to some discussion, we found further questions of interest with regard to the algorithm. Questions that have emerged after initial implementation are the following:

I was wondering about the root aspects of defining 1 single neighbour and not more in the clustering detection and also about the decision of determining a cluster just from one neighbouring. Which are the implications of these assumptions? I actually tried the implementation extending the neighbouring adding more neighbours (2 and 3).

I came across with less and bigger clusters, which we discussed for its possible interest specifically with brain images. See images below for clusters with the proposed 1 distance for neighbours and with a distance of 2, at a given time:



{% img center /images/cluster.png 800 800 'image' 'image' %}

From these approaches we can have some statistics, showing that the choice of the number of neighbours to consider is not a trivial decision: 

{% img center /images/cluster_stats.png 800 800 'image' 'image' %}


#### ** Avalanche Detection **

We collected all the avalanches for a given subject in a 4D Nifti file. Avalanches were significantly different depending of the choice of the neighbouring size (due to the change of the size of the mean cluster). Here we show a first approximation, we would have liked to make a movie, but we ran out of time for this week. 


{% img center /images/avalanche.gif 800 800 'image' 'image' %}

Here I have made a gif to represent how an avalanche is. This is an avalanche for the N2 cluster, take into account just the voxels in red/orange. It can be showed how the avalanche borns, grows and spreads, and finally dies. 

## ** Heading last week of coding **

Our objectives for next week are:

- Perform extensive testing over the measures and chose which ones are going to be in the package.

- Make code corrections and comment all the measures. This will head us into the documentation we have to make.






