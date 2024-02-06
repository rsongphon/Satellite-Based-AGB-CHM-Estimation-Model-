+++
title = 'Data Preprocess'
date = 2024-02-06T20:45:50+07:00
weight = 2
+++

This section explain preprocessing guideline in this project for each dataset.

The data used for development consists of image data from 3 satellites. Sentinel-1, Sentinel-2 from ESA and GEDI from NASA. Sentinel-1 and Sentinel-2 serve as predictor variables and GEDI serves as a ground truth reference,

## Sentinel 1 

- *Input of the model :* Model will inference AGB from pattern and relation in each band.​

- *Using raw Band*  : Relation of the band related to ​ AGB will extract from model in training process.​

- Use in training and inference process​

- Stack with sentinel 2​

- Temporal coverage for 6 month to keep seasonal information consistent.

- Interferometric Wide Swath ​

- Ascending : VV + VH total 2 layer​

- Median Composite​

- Thermal noise removal

## Sentinel 2


- *Input of the model* : Model will inference AGB from pattern and relation in each band.​

- *Using raw Band*  : Relation of the band related to ​ AGB will extract from model in training process.​

- Stack with sentinel 1​

- Use in training and inference process​

#### For Training ​

- Temporal coverage for 6 month to keep seasonal information consistent.

- Filter cloud < 50 %​

- Median Composite​

####  For Inference​

- Use sentinel2 image with minimum cloud coverage ​


## GEDI

- Target reference of the model. Use L4A layer to represent above ground biomass density .​
