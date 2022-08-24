.. _imcyto_anchor:

Segmentation with Deep-imcyto
=============================

Deep-imcyto is a deep learning based software pipeline for the identification of cells and nuclei in imaging  mass cytometry (IMC) data. 
It is based on the U-Net++ architecture and is trained on the TRACERx nuclear IMC dataset consisting of 40,000+ nuclei from IMC images of lung and other tissue types. 

Deep-imcyto is a Nextflow pipeline which started out as branch of the nfcore/imcyto IMC analysis pipeline from van Maldegem et al.


Example usage
=============

First, clone the TRACERx-PHLEX repository from github:

    .. prompt:: bash $

        git clone git@github.com:FrancisCrickInstitute/TRACERx-PHLEX.git

Download the pre-trained model weights for prediction of nuclei using the U-Net++ model:

    .. prompt:: bash $

        wget URLTOFILE

Unzip the files and move `boundaries.hdf5`, `edge_weighted_nuc.hdf5`, `COM.hdf5`, `AE_weights.hdf5` and `nuclear_morph_scaler.pkl` to a suitable directory.

Deep-imcyto can be run from a bash wrapper in the deep-imcyto root directory as follows:

    .. prompt:: bash $

        #!/bin/bash

        ## LOAD MODULES
        ml purge
        ml Nextflow/22.04.0
        ml Singularity/3.6.4

        # export cache directory for singularity
        export SINGULARITY_CACHEDIR='/camp/project/proj-tracerx-lung/tctProjects/rubicon/inputs/containers/deep-imcyto'
        export NXF_SINGULARITY_CACHEDIR='/camp/project/proj-tracerx-lung/tctProjects/rubicon/inputs/containers/deep-imcyto'

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

    The example below assumes that you have Nextflow (version 22.04.0) and Singularity (version 3.6.4) installed on your system. Your mileage may vary with earlier versions.

.. note::

    The Singularity containers required by deep-imcyto are fairly large (~6GB). They will be built automatically by Nextflow, but this may take some time.

.. tip::



Inputs and outputs
==================

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
