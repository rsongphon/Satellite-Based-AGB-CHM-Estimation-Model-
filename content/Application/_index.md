+++
archetype = "chapter"
title = "Application"
weight = 4
+++

As we mention before in [methodology](/model/methodology/) section. Output prediction of the model is prediction value along with uncertainty estimation.

![inferenceconcept](/inferenceconcept.png?height=400px)
> Figure : Output prediction of the model is prediction value along with uncertainty estimation.. 

In order to know what applcation it can be used for. We start by describe each output 

## Interpret Prediction  Output

- Output in each pixel is predicted aboveground biomass **density** (Mg/ha)

- Because the properites of GEDI lidar footprint that has limitation of spatial resolution at 25m. Level 2 Canopy height was calculate as average height in fooprint region and aboveground biomass that model from canopy height as input then calculate as density.

![gedivisual](/gedivisual.png?width=400px)

- Common unit for aboveground biomass density is **Mg/ha** . but you can calculate to SI unit kg/m^2 by multiply each pixel by 0.1

- To interpret the output, keep in mind that each pixel represent aboveground biomass density base on spatial resoluton that of the data we use to we train model.

- For example : If we have 50m spatial resolution predicted aboveground biomass density output, each pixel represent **average aboveground biomass** cover over 50m area of that pixel

![agbcal1](/agbcal1.png)
> Example : Output prediction of the model in kg/m^2, 50m spatial resolution. Top-left corner pixel has 14.06 biomass per 1 unit of area (m^2)

- So if we need to calculate total aboveground biomass we can proceed by **multiply each pixel aboveground biomass density by total area**
    - in this case of 50m spatial resolution (50mx50m) = 2500 m^2 

![agbcal2](/agbcal2.png)
> Example : aboveground biomass of each area in kg, 50m spatial resolution.

- Then we can proceed to sum up the aboveground biomass in area of interest to get total AGB.

{{% notice style="tip" title="Observation"%}}
Observe that spatial resolution has effect on aboveground biomass accuracy, Best we can do is 25m since GEDI is the most high resolution LiDAR satellite available. But 25m footprint product still have horizontal geolocation error when using with other earth observation data. So after considering trade-off 50m provide acceptable result. (Explaination in the paper)
{{% /notice %}}

## Interpret variance

- Another output of the model is uncertainty of the model.
- It tell us  **how trustworthy of each pixel prediction**
- Since GEDI data is sparse, some region of forest in Thailand may not be the part of training dataset.
- When using information of AGB/canopy height in any area. varince will tell that if our models are certian about prediction.
- Higher value mean that pixel(s) input data may not be part of the training data set (Cloud , urban area , non-vegetation) or OOD data
    - Or maybe noisy data.
- **Considering not to use the output if pixel have high variance**

- Low value mean models are certian about prediction, well calibrate and trustworthy.
    - Data similar distribition to training set will most likely have low variance.

![lowuncer](/lowuncer.png) 
>Figure : Example of low uncertainty area (dense forest). Left side is model uncertainty, Blue area indicate low varaince while yelow indicate high varaiance. Left side is ESA world cover classification raster, green indicate dense forest.

![highuncer](/highuncer.png)
 >Figure : Example of high uncertainty area (city). Left side is model uncertainty, Blue area indicate low varaince while yelow indicate high varaiance. Left side is ESA world cover classification raster, pink indicate sparse vegetation.

 ## Model Limitation : Spatial resolution

- As described earlier, product of the model is average/density over each pixel. Spatial resolution has crucial impact for application.

![small](/small.png)

- It is not recommend to use model with target area less than spatial resolution since some information is loss from resolution constraints.

![large](/large.png)

- On the other hand, larger project area along with variance consideration. Provide more accuracy and trustworthy result.

## Example Usage

- Estimate project area larger than spatial resolution is not a problem, you can calculate AGB in each pixel by multiiply with area in eac pixel as describe in provious section and use zonal stat function to calculate total AGB over your polygon target.

![gooduse](/gooduse.png?width=200px)
> Figure : Good practice to use project size larger than spatial resolution.

- On the other hand, small project area that is less than spatial resolution. You cannot calculate AGB since it accuracy will be drop.

![baduse](/baduse.png?width=200px)
> Figure : If the target are is too small, AGB estimation will not provide useful information.