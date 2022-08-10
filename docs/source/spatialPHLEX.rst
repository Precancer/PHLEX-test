.. _Spatial-PHLEX:

Introduction: Spatial analysis with Spatial-PHLEX:
==================================================
The pipeline does all these excellent things.


Inputs
======
- `cell_objects.csv`
    - A plaintext, delimited file containing single cell-level coordinate data for a set of images, plus their phenotypic identities.
- `metadata.csv`
    - A plaintext, delimited file containing metadata information about the images in `cell_objects.csv`. To run the pipeline this file must contain, for each image, an image identifier (`'imagename'`), and the width and height in pixels for every image as columns with the header `'image_width'` and `'image_height'`.

Spatial-PHLEX parameters
==========
.. list-table:: User-defined parameters in Spatial-PHLEX
    :widths: 25 25 50
    :header-rows: 1

    * - Parameter
      - Description
    * - --objects
      - Path to the cell objects file. e.g. /path/to/cell_objects.csv
    * - --objects_delimiter
      - The delimiter used in the cell objects file. e.g. ',' or '\t'.


Cell type niche analysis via density-based spatial clustering
=============================================================
Some information.


Cellular barrier scoring
========================
Some more information.