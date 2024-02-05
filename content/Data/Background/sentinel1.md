+++
title = 'Sentinel-1'
date = 2024-02-05T13:54:52+07:00
weight = 1
+++

#### Overview

The Sentinel - 1 radar imaging mission is composed of a constellation of **two polar-orbiting satellites** providing **continous all-weather, day and night imagery** for **Land** and **Maritime** Monitoring. 

**C-band synthentic aperture radar imaging has the advantage of operating at wavelenghts that are not obstructed by clouds or lack of illumination and therefore can acquire data during day or night under all weather conditions.** 

With 6 days repeat cycle on the entire world and daily acquistions of sea ice zones and Europe's major shipping routes, Sentinel-1 ensures reliable data availability to support emergency services and applications requiring time series observations. Sentinel-1 continues the retired ERS and ENVISAT missions. Level 1 GRD products are available since October 2014.

#### Application
- Monitoring sea ice, oil spills, marine winds, waves & currents, land-use change, land deformation among others, and to respond to emergencies such as floods and earthquakes.

#### Resolution
High - 10m, Medium - 40m

#### Temporal availability
October 2014- ongoing

{{% notice style="warning" title="Warining"%}}
On December 23, 2021, one of the two satellites - Sentinel-1B - encountered an anomaly of the power unit, causing SAR functionality to be lost, and the satellite will be intentionally deorbited in the future.ESA also plans to launch Sentinel-1C in the second quarter of 2023, a process typically followed by 3-6 months of the calibration process. Thus, between December 23, 2021 and the launch of a new satellite, only data from Sentinel-1A is available, which means some areas lost coverage completely, and many others have longer revisit times. {{% /notice %}}

#### Frequency
6 days repeat cycle 


#### Product
SENTINEL-1 data products distributed by ESA include

- Raw Level-0 data (for specific usage)
- Processed Level-1 Single Look Complex (SLC) data comprising complex imagery with amplitude and phase (systematic distribution limited to specific relevant areas)
- Ground Range Detected (GRD) Level-1 data with multi-looked intensity only (systematically distributed)
- Level-2 Ocean (OCN) data for retrieved geophysical parameters of the ocean (systematically distributed).

#### Mode

The SENTINEL-1 Synthetic Aperture Radar (SAR) instrument may acquire data in four exclusive modes:

![sen1mode](/sen1mode.jpeg)

- Stripmap (SM) - A standard SAR stripmap imaging mode where the ground swath is illuminated with a continuous sequence of pulses, while the antenna beam is pointing to a fixed azimuth and elevation angle.
- Interferometric Wide swath (IW) - Data is acquired in three swaths using the Terrain Observation with Progressive Scanning SAR (TOPSAR) imaging technique. In IW mode, bursts are synchronised from pass to pass to ensure the alignment of interferometric pairs. IW is SENTINEL-1's primary operational mode over land.
- Extra Wide swath (EW) - Data is acquired in five swaths using the TOPSAR imaging technique. EW mode provides very large swath coverage at the expense of spatial resolution.
- Wave (WV) - Data is acquired in small stripmap scenes called "vignettes", situated at regular intervals of 100 km along track. The vignettes are acquired by alternating, acquiring one vignette at a near range incidence angle while the next vignette is acquired at a far range incidence angle. WV is SENTINEL-1's operational mode over open ocean.


{{% notice style="Info" title="Info"%}}
The Sentinel-1 C-band SAR instruments supports operation in single polarisation (HH or VV) and dual polarisation (HH+HV or VV+VH), implemented through one transmit chain (switchable to H or V) and two parallel receive chains for H and V polarisation.

SM, IW and EW products are available in single (HH or VV) or dual polarisation (HH+HV or VV+VH). WV is single polarisation only (HH or VV). {{% /notice %}}

- **The primary conflict-free modes are IW, with VV+VH polarisation over land**
- **WV, with VV polarisation, over open ocean.**
- **EW mode is primarily used for wide area coastal monitoring including ship traffic, oil spill and sea-ice monitoring.** 
- **SM mode is only used for small islands and on request for extraordinary events such as emergency management.**

## Available data
- [Earth-Engine](https://developers.google.com/earth-engine/datasets/catalog/COPERNICUS_S1_GRD) Data Catalog
- [Alaska-Satellite-Facility](https://asf.alaska.edu/datasets/daac/sentinel-1/)
- [Sentinel-hub](https://www.sentinel-hub.com/)



