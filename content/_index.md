+++
title = 'Satellite-Based AGB/CHM Estimation Model​'
date = 2024-02-02T13:53:11+07:00
+++

## Overview. 

![concept](/concept.png)


This project is an UNET probabilistic deep learning model for estimating forest structure data such as canopy height/above ground biomass from satellite-based data such as sentinel 1 , sentinel 2 and GEDI data. 

Here you can find all relevant information about this project in this [repository](https://gitlab.com/cloud_arv/agritech/varuna-ml/unet_canopyheight_estimation).

-----------------

## Repository

Source code available [here](https://gitlab.com/cloud_arv/agritech/varuna-ml/unet_canopyheight_estimation)

------------------

## AGB Output Result , Model Checkpoint , Data

- AGB baseline output mean , variance result from paper are locate at [Varuna GCS](https://console.cloud.google.com/storage/browser/varuna-data-nonprod-analytic/biomass-estimation-project/vm-backup/AGB_model_data/ensemble_final_output?pageState=(%22StorageObjectListTable%22:(%22f%22:%22%255B%255D%22))&project=varuna-th-dt-dp-nonprod&prefix=&forceOnObjectsSortingFiltering=false)

Path : varuna-data-nonprod-analytic/biomass-estimation-project/vm-backup/AGB_model_data/ensemble_final_output

- Ensemble model checkpoints are locate at [Varuna GCS](varuna-data-nonprod-analytic/biomass-estimation-project/vm-backup/AGB_model_data/best_checkpoint/ensem1)

Path : varuna-data-nonprod-analytic/biomass-estimation-project/vm-backup/AGB_model_data/best_checkpoint/ensem1

- Sampling training data (train/test/val) are locate at [Varuna GCS](varuna-data-nonprod-analytic/biomass-estimation-project/vm-backup/AGB_model_data/train_data) 

Path : varuna-data-nonprod-analytic/biomass-estimation-project/vm-backup/AGB_model_data/train_data

------------------





### Prerequisites

{{% notice style="warning" title="Attention"%}}
Some background in **geospatial data analysis** is require to work through the project. For new comer who does not have geospatial background, do not skip this step.
{{% /notice %}}



[PyGIS](https://pygis.io/docs/a_intro.html)  - Open Source Spatial Programming & Remote Sensing will introduce you to the methods required for spatial programming and make you getting familiar with concept like raster, vector, coordinate reference systems

------

### First-timer
If you don't have any clue about this project , go through  [Theorem](/Theorem/) section to look for brief overview of the project<br> 

It will guide you from background and details literature , methodology such as

- Problem assetment of the project
- Biomass / Aboveground Biomass definition
- Remote Sensing process
- Model Methodology


-----------------

## Dev/ML
 Explore [Data](/Data/)  and [Model](/Model/)  section for implementation detail of the project

-----------------

## User 
- [Prediction](/model/tutorials/prediction/)  Section will walk through step-by-step how to use model to produce outcome.
- [Application](/Application/)  Section descibe how to interpret the output prediction and get correct result.

-----------------