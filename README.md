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


Tools used

Python, pandas, Lightkurve, NumPy, Matplotlib

Data

HJD_bjd_tdb.csv: magnitude measurements for the target, comparison, and check stars, data extracted using ASTAP.

<img width="2545" height="1200" alt="BJD_TDB" src="https://github.com/user-attachments/assets/33ebec40-5d38-4a1d-8c76-89ace30706c8" />
