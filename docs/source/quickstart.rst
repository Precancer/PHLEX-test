Quick start
+++++++++++++++
1. Install `Nextflow <https://www.nextflow.io/docs/latest/getstarted.html#installation>`_
2. Install `Singularity <https://www.sylabs.io/guides/3.0/user-guide/>`_ or `Docker <https://docs.docker.com/engine/installation/>`_
3. `Download <https://github.com/FrancisCrickInstitute/TRACERx-PHLEX/>`_ the pipeline and a minimal dataset
4. Run TRACERx-PHLEX
.. code-block:: console
   nextflow run phlex/main.nf

Tutorial
+++++++++++++++
Detailed description on parameters, input files and functionalities is described for each module
 :ref:`PHLEX:Deep-imcyto<Deep-imcyto>`

   A module devoted to performign accurate nuclear and cellular segmentation in multiplex images.

#. :ref:`PHLEX:TYPEx<TYPEx_anchor>`

   A module for cellular phenotyping from marker expression intensities derived from multiplex images.

#. :ref:`Spatial-PHLEX`

   A module for performing several types of automated spatial analysis.




