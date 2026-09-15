# Astrophotometry - Differential Light Curve
[View the notebook](Astrophotometry_Differential_Light_Curve%20(2).ipynb)

## Overview
The chosen object for observation was the variable star V1045 Ori in the constellation of Orion, it has a a period of variability of ~1.5 days.

The aim of this project was primarily to develop a python pipeline which could automatically plot a differential light curve and, using appropriate error and statistical analysis, determine the significance of an perceived stellar flux variation. 

The analysis makes use of differential photometry, a technique in which the flux of a target (variable) star is compared to that if a nearby (non-variable) comparison star. This acts to mitigate, as far as possible, external variables such as sky brightness, light cloud cover etc. The flux of a third (non-variable) check star is also measured over the same window of time and its flux compared to the comparison star. If both the check and comparison star are truly non-variable the differential light curve for the check star should be ~flat within calculated uncertainties.

## Data Aquisition
The raw data for this experiment were collected from my garden. The astronomical seeing conditions are generally quite poor. Bortle 6, 2-3" of seeing and frequent interruption to imaging from passing clouds.

**Equipment used:**\
Camera: ASI 294mc pro - dedicated cooled colour astrocamera\
Telescope: TS Optics Photoline 60mm F6 refractor telescope (with field flattener) - small doublet refractor\
Mount: SkyWatcher EQ5r pro - midsized equatorial GoTo mount\
Guiding: ZWO 30mm F4 guide scope + ZWO 120mm mini guide camera - small monochrome guiding setup\
Control System: ZWO asiair pro - dedicated astrophotography mini pc\
Accessories: USB dew heater, ZWO Electronic autofocuser\

The observing run spanned only ~1 hour of continuous data (60 x 60s exposures at gain 120, -10°C, with two additional gaps from passing clouds), while the target's suspected period is on the order of 1.5 days. This run spans only a few percent of one full cycle, so even if the star is truly varying, the amount of change visible within this short window could easily be smaller than our measurement uncertainties and therefore statistically indistinguishable from noise. Combined with sub-optimal seeing conditions and measurement uncertainties set by amateur equipment it is rather unlikely that a significant detection in stellar flux variation will be measured. **However**, that is merely a guess, not a calculated result. Until the data is analysed we can be optimistically, if not naively, hopeful.    

## Method

### 1. Photometry 
The 60 exposure were loaded into ASTAP where they were first calibrated using darks (to remove thermal noise/hot pixels) and flats (to correct for uneven sensor illumination and remove dusk motes). The green channel was then extracted from the Bayer-matrix colour data, as it carries the highest sensitivity and best approximates the standard V photometric band. ASTAP then platesolved each frame (matching the pattern of detected stars against the Gaia catalogue to determine each frames pixel to sky coordinate mapping), which allows a given star to be found across all 60 frames. ASTAP's photometry tool then converted the reference star's raw magnitude into standard Johnson-V magnitudes, using Gaia's colour based transformation. This calibrated value then served as a reference for converting the raw magnitudes for all three stars into standard V magnitudes.

A .csv was then exported containing each star's calibrated magnitude and signal-to-noise ratio (SNR) at each timestamp (HJD) across the observing window.

**NB:** HJD was converted to BJD_TDB using [this](https://arbiter.nextastro.org/toolkit/bjd-converter) online converter.

### Differential Photometry 
The differential magnitude of the variable and comparison star was defined as follows,

 $\Delta m_{v-c} = m_{v} - m_{c}$

 Pogson's relation states:
 $m_1 - m_2 = -2.5log_(10) * (F_1/F_2)$




Tools used

Python, pandas, Lightkurve, NumPy, Matplotlib

Data

HJD_bjd_tdb.csv: magnitude measurements for the target, comparison, and check stars, data extracted using ASTAP.

<img width="2545" height="1200" alt="BJD_TDB" src="https://github.com/user-attachments/assets/33ebec40-5d38-4a1d-8c76-89ace30706c8" />
