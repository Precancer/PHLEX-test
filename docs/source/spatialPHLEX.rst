Introduction: Spatial analysis with Spatial-PHLEX:
==================================================
The pipeline does all these excellent things.


Cell type niche analysis via density-based spatial clustering
=============================================================
Some information.


Cellular barrier scoring
========================
Some more information.


Inputs
======
- `cell_objects.csv`
    - A plaintext, delimited file containing single cell-level coordinate data for a set of images, plus their phenotypic identities.
- `metadata.csv`
    - A plaintext, delimited file containing metadata information about the images in `cell_objects.csv`. To run the pipeline this file must contain, for each image, an image identifier (`'imagename'`), and the width and height in pixels for every image as columns with the header `'image_width'` and `'image_height'`.