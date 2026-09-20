# Astrophotometry - Differential Light Curve
[View the notebook](Finalised_Pipeline.ipynb)

## Overview
The chosen object for observation was the variable star V1045 Ori in the constellation of Orion, it has a a period of variability of ~1.5 days.

The aim of this project was primarily to develop a python pipeline which could automatically plot a differential light curve and, using appropriate error and statistical analysis, determine the significance of an perceived stellar flux variation. 

The analysis makes use of differential photometry, a technique in which the flux of a target (variable) star is compared to that if a nearby (non-variable) comparison star. This acts to mitigate, as far as possible, external variables such as sky brightness, light cloud cover etc. The flux of a third (non-variable) check star is also measured over the same window of time and its flux compared to the comparison star. If both the check and comparison star are truly non-variable the differential light curve for the check star should be ~flat within calculated uncertainties.

## Data Aquisition
The raw data for this experiment were collected from my garden. The astronomical seeing conditions are generally quite poor. Bortle 6, 2-3" of seeing and frequent interruption to imaging from passing clouds.

**Equipment used:**\
**Camera:** ASI 294mc pro - dedicated cooled colour astrocamera\
**Telescope:** TS Optics Photoline 60mm F6 refractor telescope (with field flattener) - small doublet refractor\
**Mount:** SkyWatcher EQ5r pro - midsized equatorial GoTo mount\
**Guiding:** ZWO 30mm F4 guide scope + ZWO 120mm mini guide camera - small monochrome guiding setup\
**Control System:** ZWO asiair pro - dedicated astrophotography mini pc\
**Accessories:** USB dew heater, ZWO Electronic autofocuser\

The observing run spanned only ~1 hour of continuous data (60 x 60s exposures at gain 120, -10°C, with two additional gaps from passing clouds), while the target's suspected period is on the order of 1.5 days. This run spans only a few percent of one full cycle, so even if the star is truly varying, the amount of change visible within this short window could easily be smaller than our measurement uncertainties and therefore statistically indistinguishable from noise. Combined with sub-optimal seeing conditions and measurement uncertainties set by amateur equipment it is rather unlikely that a significant detection in stellar flux variation will be measured. **However**, that is merely a guess, not a calculated result. Until the data is analysed we can be optimistically, if not naively, hopeful.    

## Method

### 1. Photometry 
The 60 exposure were loaded into ASTAP where they were first calibrated using darks (to remove thermal noise/hot pixels) and flats (to correct for uneven sensor illumination and remove dusk motes). The green channel was then extracted from the Bayer-matrix colour data, as it carries the highest sensitivity and best approximates the standard V photometric band. ASTAP then platesolved each frame (matching the pattern of detected stars against the Gaia catalogue to determine each frames pixel to sky coordinate mapping), which allows a given star to be found across all 60 frames. ASTAP's photometry tool then converted the reference star's raw magnitude into standard Johnson-V magnitudes, using Gaia's colour based transformation. This calibrated value then served as a reference for converting the raw magnitudes for all three stars into standard V magnitudes.

A .csv was then exported containing each star's calibrated magnitude and signal-to-noise ratio (SNR) at each timestamp (HJD) across the observing window.

**NB:** HJD was converted to BJD_TDB using [this](https://arbiter.nextastro.org/toolkit/bjd-converter) online converter.

### 2. Differential Photometry 
**The differential magnitude of two stars is defined as follows:**

Pogson's relation states:

$$m_1 - m_2 = -2.5 \log_{10}\left(\frac{F_1}{F_2}\right)$$

where $m_1$ and $m_2$ are the calibrated magnitudes, and $F_1$ and $F_2$ are the fluxes, of stars 1 and 2 respectively.

Therefore:

$$\frac{F_1}{F_2} = 10^{-0.4(m_1 - m_2)}$$

The relations for flux of the variable (v) and check (k) stars relative to the comparison star (c) are therefore given by:

$$\frac{F_v}{F_c} = 10^{-0.4(m_v - m_c)}$$\
$$\frac{F_k}{F_c} = 10^{-0.4(m_k - m_c)}$$

### 3. Data Cleaning and Normalisation
Sigma Clipping was then applied using LightKurve's .remove_outliers() to exclude data points whose relative flux deviated more than $3\sigma$ from the median flux. This was applied to both the variable (v_mask) and check star (k_mask).

The relative fluxes of the variable and check star were then normalised using LightKurve's .normalize(). This was done primarily to make the plots of the variable and check star visually comparable on a common scale while leaving the relative signal and error unchanged.

A constant shift was then applied to the relative flux of the check star to allow it to be displayed on the same chart as the variable star without points overlapping.

### 4. Uncertainty Estimation
The uncertainty in the flux of the variable, comparison and check stars was calculated individually using the respective signal to noise ratio (SNR) values outputted by ASTAP, for each frame.

SNR is related to fractional flux error by the following relation:

$$\frac{1}{\mathrm{SNR}}=\frac{\sigma_F}{F}$$

The sigma clipping masks (v_mask and k_mask) were applied to the corresponding SNR values, with the comparison star's SNR masked using either v_mask or k_mask depending on whether it was being paired with the variable or check star. This ensured that SNR values from clipped data points were excluded from the error calculations.

The fractional errors in relative flux were than calculated by propagating the fractional error in the target and comparison stars, using the standard method for independent variables:

$$\frac{\sigma_{F_{rel}}}{F_{rel}} = \sqrt{\left(\frac{1}{\mathrm{SNR}_{tar}}\right)^2 + \left(\frac{1}{\mathrm{SNR}_{comp}}\right)^2}$$

This fractional error was then multiplied by the normalised relative flux to obtain the absolute uncertainty for each data point.

### 5. Time Binning and Binned Uncertainty Estimation
The data points were then split into bins of 0.004 days, containing ~5 points on average. The mean relative flux and time were then calculated for each bin.

The uncertainty on the binned mean flux was calculated using the equation for the error on an equally weighted mean of independent measurements:

$$\sigma_{\bar{F}} = \frac{\sqrt{\sum_{i=1}^{N} \sigma_{F_i}^2}}{N}$$

where $\sigma_{\bar{F}}$ is the error on the mean flux for a given bin, $\sigma_{F_i}$ is the individual flux error of each measurement, and $N$ is the number of measurements in that bin. Since all frames were taken with fixed 60s exposures under similar observing conditions, the mean individual flux errors are expected to be fairly consistent within a bin, therefore an unweighted mean is reasonable. 

**NB:** when errors are approximately equal, this simplifies to the familiar $$\sigma_{\bar{F}} \approx \frac{\sigma_F}{\sqrt{N}}$$

Any bins with less than 3 data points were then removed using a filter.

### 6. Statistical Analysis
**Reduced Chi-Squared and p Test**

To determine whether the variable star shows statistically significant variation, a reduced chi-squared
($\chi^2_\nu$) test was carried out on the variable star's binned light curve. The results were compared to that of the non-variable check star.

$$\chi^2_\nu = \frac{1}{N-1}\sum_{i=1}^{N}\frac{(F_i - \bar{F})^2}{\sigma_{F_i}^2}$$

where $F_i$ and $\sigma_{F_i}$ are the mean flux and error of each bin, $\bar{F}$ is the unweighted mean flux across all bins and $N$ is the number of bins. A value of $\chi^2_\nu \approx 1$ indicates that the scatter of binned points is consistent with the calculated error (noise floor). A $\chi^2_\nu \gg 1$ indicates variation which cannot be explained by noise alone.

The check star's $\chi^2_\nu$ result acts as a baseline. Since it is assumed to be non variable, its $\chi^2_\nu$ verifies the accuracy of the calculated errors. If its $\chi^2_\nu \approx 1$ then a larger $\chi^2_\nu$ of the variable star can be interpreted as truly significant stellar flux variation. 

If the check star $\chi^2_\nu$ is significantly larger than 1 this indicates that the errors have been underestimated, or that the check/comparison star is not truly non-variable. In this situation an elevated $\chi^2_\nu$ of the variable star cannot be definitively interpreted as significant stellar flux variation.  

The p-value was then calculated using scipy.stats to determine the probability of obtaining a chi-squared value at least as large as that observed for both the variable and check stars.

Since the check star's reduced chi-squared did in fact significantly exceeded 1 (see Results), and the check star is known to be non variable from the Gaia catalogue, the formal SNR-derived flux errors were judged to underestimate the true point-to-point noise. A possible physical explanation of this is that reported SNR accounts for photon, sky, and read noise, but not scintillation (rapid flux fluctuations from atmospheric turbulence) which are expected to be non-negligible here given the modest 60 mm aperture and the target's low maximum altitude. While differential photometry partially cancels scintillation (since target and comparison stars share the same frames and similar sky positions) this cancellation is unlikely to be complete, leaving an additional, unaccounted contribution to flux errors. As these systematics are common for a single frame they should impact every star on the given frame in comparably. The non-variable check star was hence used as an empirical calibrator: its excess scatter (above the calcualted errors) was translated into a single correction factor, which was then applied multiplicatively to the SNR based errors of both stars.

Since $\chi^2_\nu \propto 1/\sigma_F^2$, an error that has been underestimated by a factor of $k$ produces a reduced chi-squared inflated by a factor of $k^2$; the correction factor was therefore taken as the square root of the check star's reduced chi-squared:

$$\sigma_{F_i}^{\text{scaled}} = \sigma_{F_i} \times \sqrt{\chi^2_{\nu,\text{check}}}$$

By construction, this forces $\chi^2_{\nu,\text{check,scaled}} = 1$. The reduced chi-squared and p-value tests were then repeated for both stars using the rescaled errors.





## Results and Discussion
**Differential Light Curve with Scaled Error Bars**
<img width="2545" height="1200" alt="Normalised Relative Flux vs Time" src="https://github.com/user-attachments/assets/d02e8ae6-8310-46c5-a2da-151ebbda9786" />




| | Formal Errors: $\chi^2_\nu$ | Formal Errors: $p$-value | Rescaled Errors: $\chi^2_\nu$ | Rescaled Errors: $p$-value |
|---|---|---|---|---|
| Variable Star | 11.25 | $8.87\times10^{-18}$ | 3.52 | $2.26\times10^{-4}$ |
| Check Star | 3.20 | $4.07\times10^{-4}$ | 1.00 | 0.44 |

The formal errors alone suggest that both stars vary significantly, which is contradictory for the check star, as it should be constant. This contradiction (formal $\chi^2_\nu = 3.20$, $p = 4.07\times10^{-4}$, for a star assumed non-variable) indicates that the errors were underestimated, rather than the check star being truly variable. The rescaling, discussed in section 6 of methods, brings the check star to $\chi^2_\nu \approx 1$ ($p = 0.44$), confirming it is now consistent with the constant flux assumption. The variable star's significance drops substantially under this correction but remains well above the noise floor ($\chi^2_\nu = 3.52$, $p = 2.26\times10^{-4}$), indicating that the observed flux variation **IS** significant.

Despite the apparent underestimate of error, the SNR-based uncertainty propagation remains essential to this analysis, as it provides the baseline against which the check star's excess scatter, and therefore the rescaling factor, is calculated (under the assumption that the check star is non-variable).

An alternative approach would be to compare the variable star's raw scatter directly to the check star's, rather than rescaling formal errors. This was avoided as it assumes both stars share the same noise level, ignoring that their differing brightness's give them different photon noise floors i.e brighter stars have lower fraction uncertainties (as reflected in their individual SNR values). It also provides only a single, blended scatter value for the entire run, rather than an individual uncertainty for each point or bin, meaning it cannot supply the per-bin weighting the chi-squared test requires to compute a formal significance value.

**Outlier Removal**

A 3σ clip was applied independently to each star's light curve. The variable star had 2 points removed (indices `[0, 1]`, corresponding to the first two exposures of the run, this is likely due to telescope/guiding settling rather than genuine signal or noise. The check star had no points removed (`[]`).

| | Points Clipped | Indices |
|---|---|---|
| Variable Star | 2 / 60 | `[0, 1]` |
| Check Star | 0 / 60 | `[]` |

**Bin Filtering**

Bins containing fewer than 3 data points were discarded to avoid unreliable mean/error estimates from very small samples.

| | Bins Kept | Threshold |
|---|---|---|
| Variable Star | 10 / 12 | ≥3 pts/bin |
| Check Star | 11 / 13 | ≥3 pts/bin |








Tools used

Python, pandas, Lightkurve, NumPy, Matplotlib

Data

HJD_bjd_tdb.csv: magnitude measurements for the target, comparison, and check stars, data extracted using ASTAP.

