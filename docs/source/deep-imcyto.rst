.. _imcyto_anchor:

Segmentation with Deep-imcyto
=============================

Deep-imcyto is a deep learning based software pipeline for the identification of cells and nuclei in imaging  mass cytometry (IMC) data. 
It is based on the U-Net++ architecture and is trained on the TRACERx nuclear IMC dataset consisting of 40,000+ nuclei from IMC images of lung and other tissue types. 

Deep-imcyto is a Nextflow pipeline which started out as branch of the nfcore/imcyto IMC analysis pipeline from van Maldegem et al.


Example usage
=============
.. note::

    The example below assumes that you have Nextflow (version 22.04.0) and Singularity (version 3.6.4) installed on your system. Your mileage may vary with earlier versions.

First, clone the TRACERx-PHLEX repository from github:

.. code-block:: bash

    git clone git@github.com:FrancisCrickInstitute/TRACERx-PHLEX.git

Download the pre-trained model weights for prediction of nuclei using the U-Net++ model:

.. code-block:: bash

    wget URLTOFILE

Unzip the files and move ``boundaries.hdf5``, ``edge_weighted_nuc.hdf5``, ``COM.hdf5``, ``AE_weights.hdf5`` and ``nuclear_morph_scaler.pkl`` to a suitable directory.

Deep-imcyto can be run from a bash wrapper in the deep-imcyto root directory as follows:

.. code-block:: bash

    #!/bin/bash

    ## LOAD MODULES
    ml purge
    ml Nextflow/22.04.0
    ml Singularity/3.6.4

    # export cache directory for singularity
    export SINGULARITY_CACHEDIR='/path/to/cachedir/'
    export NXF_SINGULARITY_CACHEDIR='/path/to/cachedir/'

    ## RUN PIPELINE
    nextflow run ./main.nf\
        --input "/path/to/mcd.mcd"\
        --outdir '../results'\
        --metadata '/path/to/metadata.csv'\
        --full_stack_cppipe './assets/cppipes/full_stack_preprocessing.cppipe'\
        --segmentation_cppipe './assets/cppipes/segmentationP1.cppipe'\
        --ilastik_stack_cppipe './assets/cppipes/ilastik_stack_preprocessing.cppipe'\
        --compensation_tiff './assets/spillover/P1_imc*.tiff'\
        --plugins "/path/to/plugins"\
        --nuclear_weights_directory "/path/to/weights"\
        --segmentation_type 'dilation'\
        --nuclear_dilation_radius 5\
        -profile crick\
        -resume


.. note::

    The Singularity container required by deep-imcyto is fairly large (~6GB). It will be built automatically by Nextflow, but this may take some time.

.. tip::



Inputs and outputs
==================
- Required inputs:
    - `*.mcd` or `*.ome.tiff` images
        Input image files in `mcd`` or `ome.tiff` format.
    - `metadata.csv`
        A plaintext, delimited file containing isotope metadata for each image file.
- Workflow-dependent inputs:
    - `*.cppipe` files
        CPPipe files for performing CellProfiler-based pipelines.
    - Spillover --compensation_tiff
        A spillover tiff image file for compensation of isotope channel spillover, as described in REF.

Parameters
============

Segmentation options
====================

Deep-imcyto can perform nuclear and cellular segmentation in several modes:

+--------------------------------------+--------------------------------------------------------------------------------+
| Option                               | Description                                                                    |
+======================================+================================================================================+
| ``'consensus'``                      | Performs consensus cell segmentation as described in REF. Requires the user to |
|                                      | Have a custom cellprofiler .cppipe file specifying the consensus cell          |
|                                      | segmentation method for their IMC panel.                                       | 
|                                      |                                                                                |               
+--------------------------------------+--------------------------------------------------------------------------------+
| ``'dilation'``                       | Perform a simple whole cell segmentation by dilating nuclear predictions from  |
|                                      | Unet++ model                                                                   |
+--------------------------------------+--------------------------------------------------------------------------------+


    Consensus cell segmentation
    ---------------------------

    Nuclear dilation
    ----------------

Special cases
=============

Segmentation of non-nucleated cells
-----------------------------------


Troubleshooting
===============

Appendix: TRACERx Lung IMC nuclear training dataset
===================================================
