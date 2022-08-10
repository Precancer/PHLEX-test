.. _Deep-imcyto:

Segmentation with Deep-imcyto
=============================

Deep-imcyto is a deep learning based software pipeline for the identification of cells and nuclei in imaging  mass cytometry (IMC) data. 
It is based on the U-Net++ architecture and is trained on the TRACERx nuclear IMC dataset consisting of 40,000+ nuclei from IMC images of lung and other tissue types. 

The tool is available as a docker image and can be run on any machine with a GPU. 

Segmentation modes
==================

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



TRACERx Lung IMC nuclear training dataset
=========================================