Astrophotometry Differential Light Curve
[View the notebook](Astrophotometry_Differential_Light_Curve%20(2).ipynb)

A Python pipeline that turns raw photometry data from my own astrophotography into a differential light curve, as a self-directed project to build skills toward exoplanet transit detection.


What it does
Takes magnitude measurements for a target (variable) star, a comparison star, and a check star, extracted from astrophotography data using ASTAP
Converts differential magnitudes into relative flux using Pogson's relation
Cleans and normalises the data (outlier removal, binning) using Lightkurve
Plots relative flux against time (BJD_TDB) for both the target and check star, to confirm the comparison star is non-variable

Tools used

Python, pandas, Lightkurve, NumPy, Matplotlib

Data

HJD_bjd_tdb.csv: magnitude measurements for the target, comparison, and check stars, data extracted using ASTAP.

<img width="2545" height="1200" alt="BJD_TDB" src="https://github.com/user-attachments/assets/33ebec40-5d38-4a1d-8c76-89ace30706c8" />
