.. _imcyto_anchor:

PHLEX: deep-imcyto
=============================

**deep-imcyto** is a deep learning-based software pipeline for the identification of nuclei and cells in imaging mass cytometry (IMC) data. 
It is based on a U-Net++ architecture combined with a custom postprocessing procedure, and is trained on the :ref:`NISD-anchor`` consisting of 40,000+ nuclei from IMC images of lung and other tissue types. 

**deep-imcyto** began as branch of the **nfcore/imcyto** IMC analysis pipeline from van Maldegem et al. As such running deep-imcyto should be familiar to users of nfcore/imcyto.

**deep-imcyto** is built using `Nextflow <https://www.nextflow.io>`_, a workflow tool to run tasks across multiple compute infrastructures in a very portable manner. 
It comes with docker containers making installation trivial and results highly reproducible.

Workflow options
================
deep-imcyto provides three primary workflow options for raw IMC data:
    -  *QC:* quality control of raw IMC data.
    -  *simple segmentation*: segmentation of nuclei with the deep-imcyto nucleus segmentation model, followed by pixel expansion to approximate cellular boundaries.
    -  *MCCS / cellprofiler* segmentation: segmentation of nuclei with the deep-imcyto nucleus segmentation model, followed by execution of a custom CellProfiler pipeline designed to take nuclear predictions as input.

Workflow 1: QC
--------------

Workflow 2: Simple segmentation
-------------------------------

Workflow 3: Multiplexed consensus cell segmentation
---------------------------------------------------
Multiplexed consensus cell segmentation (MCCS) is a method developed in the Swanton lab for segmenting cells in IMC data from accurate nuclear predictions.

1. CellProfiler segmentation
-----------------------------

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

deep-imcyto can be run from a bash wrapper in the deep-imcyto root directory as follows:

.. code-block:: bash

    #!/bin/bash

    ## LOAD MODULES
    ml purge
    ml Nextflow/22.04.0
    ml Singularity/3.6.4

    # export cache directory for singularity
    export NXF_SINGULARITY_CACHEDIR='/path/to/cachedir/'

    ## RUN PIPELINE
    nextflow run ./main.nf\
        --input "/path/to/mcd.mcd"\
        --outdir '../results'\
        --metadata '/path/to/metadata.csv'\
        --full_stack_cppipe './assets/cppipes/full_stack_preprocessing.cppipe'\
        --segmentation_cppipe './assets/cppipes/segmentationP1.cppipe'\
        --compensation_tiff './assets/spillover/P1_imc*.tiff'\
        --plugins "/path/to/plugins"\
        --nuclear_weights_directory "/path/to/weights"\
        --segmentation_workflow 'consensus'\
        --nuclear_dilation_radius 5\
        -profile crick\
        -resume

.. code-block:: bash

    #!/bin/bash

    ## LOAD MODULES
    ml purge
    ml Nextflow/22.04.0
    ml Singularity/3.6.4

    # Define folder for deep-imcyto software containers to be stored:
    export NXF_SINGULARITY_CACHEDIR='/path/to/cachedir/'

    # RUN PIPELINE:
    nextflow run ./main.nf\
        --input "/camp/path/to/data/my_image.mcd"\
        --outdir '/camp/path/to/results'\
        --metadata '/camp/path/to/channel_metadata_deepimcyto.csv'\
        --nuclear_weights_directory "/camp/path/to/weights"\ # The path to the directory containing the neural network weights.
        --segmentation_workflow 'simple'\
        --nuclear_dilation_radius 5\
        --preprocess_method 'hotpixel'\
        --email username@crick.ac.uk\
        -profile crick\
        -w '/path/to/work/directory' # Path to a suitable directory where the nextflow will save working/interim files e.g. lab scratch directory.


.. note::

    The Singularity container required by deep-imcyto is fairly large (~6GB). It will be built automatically by Nextflow, but this may take some time.

.. tip::



Inputs and outputs
==================
Inputs
-------
- Required inputs:
    - `*.mcd`, `*.txt` or `*.ome.tiff` images
        Input image files in `mcd`` or `ome.tiff` format.
    - `metadata.csv`
        A plaintext, delimited file containing isotope metadata for each image file.
- Workflow-dependent inputs:
    - `*.cppipe` files
        CPPipe files for performing CellProfiler-based pipelines.
    - Spillover --compensation_tiff
        A spillover tiff image file for compensation of isotope channel spillover, as described in REF.

Expected Outputs
-------

Output from deep-imcyto has the following directory structure.

.. code-block:: bash

   results
   ├── channel_preprocess
   ├── cell_segmentation
   ├── imctools          
   ├── nuclear_preprocess        
   ├── nuclear_segmentation
   ├── pipeline_info
   └── pseudo_HandE

- Preprocessed channel images in `.tiff` format.
    Preprocessing depends on the type of preprocessing specified with the `--preprocessing` flag:
    - `--preprocessing 'cellprofiler'`
        CellProfiler-based preprocessing of channel images.
    - `--preprocessing 'hotpixel'`
        Remove hot pixels from channel images only.`
    - `--preprocessing 'none'`
        No additional channel preprocessing.`
- imctools
    Raw tiff channel images, split into substacks for each identifier in the metadata.
    
    .. code-block:: bash

        imctools
        ├── full_stack
        ├── ilastik_stack
        ├── nuclear          
        ├── spillover        
        ├── counterstain

.. note::

    The name of the `cell_segmentation` directory will vary depending on which `segmentation_type` is specified.

- Raw tiff channel images from the input image files
    - `*.tiff`



    
Parameters
============

.. note::

    Default parameters are specified in the `nextflow.config` file. Default parameters can be overridden by specifying the parameter in the command line. e.g. to change the default 
    value by which predicted nucleus masks are dilated by in the `simple` workflow of deep-imcyto to 10 pixels (from a default of 5), the following flag should be added to the run command:

        .. code-block:: bash

            nextflow run main.nf --nuclear_dilation_radius 10



Segmentation options
====================

deep-imcyto can perform nuclear and cellular segmentation in several modes:

+--------------------------------------+--------------------------------------------------------------------------------+
| Option                               | Description                                                                    |
+======================================+================================================================================+
| ``'consensus'``                      | Performs consensus cell segmentation as described in REF. Requires the user to |
|                                      | Have a custom cellprofiler .cppipe file specifying the consensus cell          |
|                                      | segmentation method for their IMC panel.                                       | 
|                                      |                                                                                |               
+--------------------------------------+--------------------------------------------------------------------------------+
| ``'cellprofiler``                    | Performs cell segmentation using a custom CellProfiler pipeline.               |
|                                      |                                                                                |
|                                      |                                                                                | 
|                                      |                                                                                |               
+--------------------------------------+--------------------------------------------------------------------------------+
| ``'dilation'``                       | Perform a simple whole cell segmentation by dilating nuclear predictions from  |
|                                      | Unet++ model                                                                   |
+--------------------------------------+--------------------------------------------------------------------------------+



Special cases
=============

Segmentation of non-nucleated cells
-----------------------------------


Troubleshooting
===============

.. _NISD-anchor:
Appendix: TRACERx Lung IMC nuclear training dataset
===================================================
