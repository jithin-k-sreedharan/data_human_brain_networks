# Human Brain Networks Dataset of 100 Subjects with Node Labels

This repository provides brain networks (in edge-list format) of 100 unrelated young adults formed from their resting state fMRI. Each network has nodes as 180 cortical regions, and each node is an identified brain region with area description (like visual, motor, auditory etc.) properly known. The labels of the nodes in the graph are available [here](https://media.nature.com/original/nature-assets/nature/journal/v536/n7615/extref/nature18933-s3.pdf) in Table 1, page 81-86 with parcel index same as the node ID in the network (Supplementary Neuroanatomical Results For "A Multi-modal Parcellation of Human Cerebral Cortex", Glasser et al.)

We found that node labels (brain region names) are not present or only partially available in most of the brain networks publicly available, and thus difficult to do targeted studies. We hope that our dataset will be helpful to many others.

**Contributors:** [Vikram Ravindra](https://github.com/vikramravindra) and [Jithin K. Sreedharan](https://github.com/jithin-k-sreedharan).

## Description

We start with the raw data of Human Connectome Project (HCP) [500-subject data release (2014)](https://www.humanconnectome.org/study/hcp-young-adult/document/500-subjects-data-release) that is originally in CIFTI file format. Documentation of the release is available [here](https://www.humanconnectome.org/storage/app/media/documentation/s500/hcps500meg2releasereferencemanual.pdf).
This release provides data files of resting state functional connectivity MRI of 100 unrelated healthy human adults.  There are 2 sessions per subject and 2 scans per subject, and thus in total 400 time series.

Procedure:
* We first use the [FieldTrip](http://www.fieldtriptoolbox.org) toolbox to load the file in to MATLAB.
* Each file has about 90k time series and some metadata. Nearly first 50k of them belong to the cortex, which can be extracted from the "brainstructure" field in the metadata.
* Then, we do a "global signal regression" proposed by [Murphy et al.](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2750906/) to remove global effects.
* We use the atlas generated by [Glasser et al. 2016.](https://www.nature.com/articles/nature18933) on the cleaned data. This has a membership (1-360) for each of the 50k time series. We take an average of the points, to be left with a (360 x time points) matrix.
* We do a correlation on the rows to get a (360 x 360) matrix, and finally restrict the matrices to 180 x 180 to focus on one hemisphere.
* In order to form unweighted networks, we employ one of following two strategies:
  - A spanning tree is created first from the complete weighted network of the correlation matrix, and later k-nearest neighbors (higher correlation values) of each node are added into this network, where k is chosen as 10 in our case.
  - To form a adjacency matrix with binary values, we apply a threshold on each element of the correlation matrix. The threshold is chosen as the minimum value needed to make the resulting graph connected.

The Python Jupyter notebook `forming_brain_nw_HCP.ipynb` contains code to form networks from the correlation matrices with these two ideas.

## An Application

This dataset has been used in the [TIMES](https://github.com/jithin-k-sreedharan/times) project that finds arrival order of nodes in dynamic network given only one snapshot. Please see the TIMES [repository](https://github.com/jithin-k-sreedharan/times) or the following paper for more information:

[Inferring Temporal Information from a Snapshot of a Dynamic Network](https://rdcu.be/boQ5z)\
Jithin K. Sreedharan, Abram Magner, Ananth Grama, and Wojciech Szpankowski.\
_Nature Scientific Reports 2019_. [Supplementary Material](https://static-content.springer.com/esm/art%3A10.1038%2Fs41598-019-38912-0/MediaObjects/41598_2019_38912_MOESM1_ESM.pdf) (details of analysis and implementation)

The correlation matrices are not uploaded due to their heavy size. If you need to work on them, please contact me.
