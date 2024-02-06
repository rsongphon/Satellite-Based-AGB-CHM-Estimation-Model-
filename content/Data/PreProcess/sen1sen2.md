+++
title = 'Sentinel 1 & 2'
date = 2024-02-06T20:58:11+07:00
weight = 1
+++

## Sentinel 2 Preprocessing

The Sentinel-2 satellite from the European Spatial Agency's (ESA) Copernicus program for Earth Observation (EO) consists of Sentinel-2A and Sentinel-2B, providing multispectral images covering Thailand. which is the target area. From a large photographic line of more than 290 kilometers, the images are repeated every 10 days and images processed approximately every 5 days.

Using data from MultiSpectral Instrument (MSI) Level-2A contains data for 12 bands: B1, B2, B3, B4, B5, B6, B7, B8, B8A, B9, B11, and B12.

Six month temporal coverage from 2020-11-01 to 2021-04-30 are select so that the physical characteristics of the forest canopy layer are similar.

L2A (Top of the atmosphere reflectance) data is processed from L1C (Top of the atmosphere reflectance) level using ESA's Sen2Cor algorithm.

Because of the properties of the sensor recording system that cannot be captured through the clouds. Therefore, satellite images are preprocess by filtering out images with cloud coverage of more than 50% using QA60 data, which is a cloud mask image.

Then a median composite time series is applied to all images in the selected time period to eliminates variations in data during the day and night. Result is to get the most cloud-free images.

The resulting images are then normalization in the range 0-1 to speed up model training.

Finally , images in each band at different resolutions (10 m, 20 m, 60 m) are resampling to a spatial resolution of 50 m.

## Sentinel 1 Preprocessing

Sentinel-1 data is Synthetic Aperture Radar (SAR) data with a high resolution and a surface width of 250 kilometers, covering the area of Thailand. It is repeated every 6 days with two satellites, Sentinel-1A and Sentinel-1B. We utilizing data within the same temporal range as Sentinel-2 (November 1, 2020, to April 30, 2021), 

During this time, Sentinel-1B satellite ended its mission Sentinel-1A satellite in Ground Range Detected (GRD) Interferometric Wide Swath (IW) mode was choose instead. which has vertical polarization (VV) and horizontal polarization (VH).

By the cloud-penetrating properties of the sensor and its demonstrated ability to monitor forest bioclimatology. Therefore, it is used together with Sentinel-2 data as a predictor variable.

Because SAR images contain noise which is caused by radar wave signals that bounce back irregularly from objects on the earth's surface that interact with each other. This causes black dots that appear as noise on the image data. Pre-processing step is need.

The image pre-processing steps are done using [Sentinel-1-Toolbox](https://sentinels.copernicus.eu/web/sentinel/toolboxes/sentinel-1)
1. Thermal noise removal 
2. Radiometric calibration 
3. Terrain correction.

Then threshold the value within the range (-30 , 0) decibels (dB). follow by a median composite time series that applied to all images in the selected time period.

Finally, normalized to a range of 0-1 and resampling resolution of 50 meters, similar to the Sentinel-2 image.

{{% notice style="Info" title="Info"%}}
The result pre-processing image of Sentinel1 and 2 are then stack together to serve as predictor to the model{{% /notice %}}

A pre-processing pipeline are show in diagram as follow.

![sen1sen2pre](/sen1sen2pre.png)

## Earth Engine Implementation

Example code snippet for preprocessing sentintel 1 and 2
- **only requirement is to import target shapefile into Earth Engine environment and rename as**  *target_shapefile*

See [documentation](https://developers.google.com/earth-engine/guides/table_upload) for how to upload various of assets into Earth Engine.

If you want to learn more about Earth Engine, explore [End-to-End-Tutorial](https://courses.spatialthoughts.com/end-to-end-gee.html)

```
/////////////////// User defined variables ////////////////////////////

// Country boundary polygon
var target = target_shapefile



// Start and end date to filter collections

var startDate = '2018-11-01'; //YYY-MM-DD 
var endDate = '2019-03-30';   //YYY-MM-DD 


// Maximum cloud cover percentage
var cloudCoverPerc = 50;


///////////////////////////////////////////////////////////////////////


// ----------------- SENTINEL-2 COLLECTION ------------------------

// We will use the Sentinel-2 Surface Reflection product.
// This dataset has already been atmospherically corrected
var s2 = ee.ImageCollection("COPERNICUS/S2_SR");

// Function to mask clouds S2
function maskS2srClouds(image) {
  var qa = image.select('QA60');

  // Bits 10 and 11 are clouds and cirrus, respectively.
  var cloudBitMask = 1 << 10;
  var cirrusBitMask = 1 << 11;

  // Both flags should be set to zero, indicating clear conditions.
  var mask = qa.bitwiseAnd(cloudBitMask).eq(0)
      .and(qa.bitwiseAnd(cirrusBitMask).eq(0));

  return image.updateMask(mask).divide(10000);
}

// Filter Sentinel-2 collection
var s2Filt = s2.filterBounds(target)
                .filterDate(startDate,endDate)
                .filterMetadata('CLOUDY_PIXEL_PERCENTAGE','less_than',cloudCoverPerc)
                .map(maskS2srClouds);



// Composite images
var s2composite = s2Filt.median().clip(target); // can be changed to mean, min, etc


var exportImage = s2composite.select('B.*').toFloat();


///////////////////////////////////////////////////////////////////////

// ----------------- SENTINEL-1 COLLECTION ------------------------
// Sentinel 1 Descending Image Is Out of Operation since December 2021!!

/// VV
var imgVV = ee.ImageCollection('COPERNICUS/S1_GRD')
        .filterBounds(target)
        .filterDate(startDate,endDate)
        .filter(ee.Filter.listContains('transmitterReceiverPolarisation', 'VV'))
        .filter(ee.Filter.eq('instrumentMode', 'IW'))
        .select('VV')
        .map(function(image) {
          var edgeleft = image.lt(-30.0);
          var edgeright = image.gt(0.0);
          var maskedImage = image.mask().and(edgeleft.not()).and(edgeright.not());
          return image.updateMask(maskedImage);
        });
        

var desc = imgVV.filter(ee.Filter.eq('orbitProperties_pass', 'DESCENDING'));
var asc = imgVV.filter(ee.Filter.eq('orbitProperties_pass', 'ASCENDING'));
var descVVimg = desc.median().clip(target).add(30).divide(30).toFloat();
var ascVVimg = asc.median().clip(target).add(30).divide(30).toFloat();
var neighborhoodSize = 1; // Adjust this value according to your needs

// Select Ascending Image
var ascVVFillingimg = ascVVimg.focal_mean({radius: neighborhoodSize, units: 'pixels', kernelType: 'circle'});




/// VH
var imgVH = ee.ImageCollection('COPERNICUS/S1_GRD')
        .filterBounds(target)
        .filterDate(startDate,endDate)
        .filter(ee.Filter.listContains('transmitterReceiverPolarisation', 'VH'))
        .filter(ee.Filter.eq('instrumentMode', 'IW'))
        .select('VH')
        .map(function(image) {
          var edgeleft = image.lt(-30.0);
          var edgeright = image.gt(0.0);
          var maskedImage = image.mask().and(edgeleft.not()).and(edgeright.not());
          return image.updateMask(maskedImage);
        });


var desc = imgVH.filter(ee.Filter.eq('orbitProperties_pass', 'DESCENDING'));
var asc = imgVH.filter(ee.Filter.eq('orbitProperties_pass', 'ASCENDING'));
var descVHimg = desc.median().clip(target).add(30).divide(30).toFloat();
var ascVHimg = asc.median().clip(target).add(30).divide(30).toFloat();
var neighborhoodSize = 1; // Adjust this value according to your needs

// Select Ascending Image
var ascVHFillingimg = ascVHimg.focal_mean({radius: neighborhoodSize, units: 'pixels', kernelType: 'circle'});



///////////////////////////////////////////////////////////////////////

// ----------------- Export Product ------------------------

// Create a multiband composite image
var composite = ee.Image([exportImage, ascVVimg, ascVHimg])


    Export.image.toCloudStorage({
    image: composite,
    description: 'Sen1_Sen2_Thailand_Median_Composite_Raw_10m_32647_14layer_predict_2019',
    //description: 'Sen1_Sen2_Sak_Median_Composite_Raw_10m_32647_14layer_predict',
    bucket: 'varuna-data-nonprod-analytic',
    fileNamePrefix: 'biomass-estimation-project/vm-backup/AGB_model_data/raw_2019/Sen1_Sen2_Median_Composite_Raw_10m_32647_14layer_predcit_large_2019',
    //fileNamePrefix: 'biomass-estimation-project/vm-backup/Canopy_model_data/Compostie_image_raw_predict/Sen1_Sen2_Sak_Median_Composite_Raw_10m_32647_14layer_predcit',
    scale: 50,
    region: target,
    maxPixels: 1e13
  });

```