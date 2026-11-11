# storm_assess

A Python package for reading and manipulating cyclone track data.  
  
#### Introduction

`storm_assess` is a Python module to read ascii-format output from the *TRACK* and *TempestExtremes* cyclone-tracking algorithms, originally developed by Joanne Camp (Met Office & Bureau of Meteorology). This package has been used in recent publications, but the far more capable [HuracanPy](https://huracanpy.readthedocs.io/en/stable/) is now recommended.  

The package is available on JASMIN at: `/gws/nopw/j04/huracan/tools/python/storm_assess/`. A version of `storm_assess`, developed by Malcolm Roberts (Met Office), was developed for reading NetCDF-format track output data (from HighResMIP), which is available [here](https://github.com/PRIMAVERA-H2020/HighResMIP-storm_reader).

#### Publications

The following peer-reviewed publications use this repo's version of `storm_assess`:

Baker, A. J., Vannière, B., and Vidale, P. L. [On the realism of tropical cyclones simulated in global storm-resolving climate models](https://agupubs.onlinelibrary.wiley.com/doi/10.1029/2024GL109841). *Geophysical Research Letters* 51, e2024GL109841 (2024).

Baker, A. J., Roberts, M. J., Vidale, P. L., Hodges, K. I., J., Seddon, J., Vannière, B., Haarsma, R. J., Schiemann, R., Kapetanakis, D., Tourigny, E., Lohmann, K., Roberts, C. D., and Terray, L. [Extratropical transition of tropical cyclones in a multiresolution ensemble of atmosphere-only and fully coupled global climate models](https://journals.ametsoc.org/view/journals/clim/aop/JCLI-D-21-0801.1/JCLI-D-21-0801.1.xml). *Journal of Climate* 35, 5283–5306 (2022).

#### The `storm()` class

Each `storm` instance contains attributes that describe track location and storm-local fields (i.e., wind speed, central pressure etc) for each track.

For example:

```python
storm = storms[i]
storm.obs
storm.vmax
storm.mslp_min
storm.obs[t].date
storm.obs[t].lon
storm.obs[t].lat
storm.obs_at_vmax()
storm.obs_at_min_mslp().date
```

#### Example: load a *TRACK* output file and plot a pressure–wind relationship

```python
import numpy as np
import matplotlib.pyplot as plt
from storm_assess import track

# Load a list of storms and get the wind speed and central pressure data
track_fn = 'track_filename'
storms = list(track.load(track_fn))
mslp_minima = [storm.mslp_min for storm in storms]
vmax_maxima = [storm.vmax for storm in storms]

# Fit
coeff = np.polyfit(vmax_maxima, mslp_minima, 2.)
poly1d = np.poly1d(coeff)
fit_x = np.linspace(min(vmax_maxima), max(vmax_maxima), len(vmax_maxima))
fit_y = poly1d(fit_x)

# Plot
plt.scatter(vmax_maxima, mslp_minima, color='r', marker='.', alpha=0.5)
plt.plot(fit_x, fit_y, color='r', label='dataset name')
plt.ylabel(r'p$_{min}$')
plt.xlabel(r'v$_{max}$')
plt.legend()
plt.show()
```
