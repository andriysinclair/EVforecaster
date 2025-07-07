## 📚 Table of Contents

1. [Overview](#overview)  
2. [Features](#features)  
3. [Installation](#installation)  
4. [Usage](#usage)  
5. [Examples](#examples) 
6. [Assumptions & Configuration](#assumptions--configuration)    
7. [References](#references)
8. [Citation](#citation)  
9. [License](#license)

# Overview

`EVforecaster` uses the 2002 - 2023 UK National Travel Survey (UK-NTS) to generate domestic EV charging demand curves. The UK-NTS contains trip level data, such as: trip start time, trip end time, trip distance, trip location, etc. Although the majority of trips in the UK-NTS are likely to have been carried out by combustion engine vehicles (CEVs), this simulation-based approach assumes that electric vehicles (EVs) carried out the trips. Taking the trips as given, `EVforecaster` tries to model where, at what time and for how long EVs would charge to be able to successfully undertake the trips. Once this is modelled, the information is aggregated to plot weekly demand curves at 5-minute granularity. The simulation algorithms requires the following information:

* Each trip location is randomly allocated a charger and a charging rate (kW/hour) based on a distribution.
* Each individual is allocated a car, which is modelled as a battery size and efficiency due. Based on a distribution.
* An individual's charging decision is modelled using the probabilistic function developed in Pareschi et al. (2020).
* Most simulation parameters (such as those mentioned above) are centrally defined in `config.py`.

It is capable of creating annual weekly demand curves that aggregate all the weeks of the year, weekly demand curves for specific seasons (defined by week ranges) or weekly demand curves for each week of the year.

Additionally, it is able to compare weekly demand curves for the largest domestic UK-based EV charging pilot, the 2017 Electric Chargepoint Analysis (ECA) (DfT, 2018). It plots the mean simulated demand curve (and standard errors) alongside the ECA and a histrogram of $R^2$ values below.

Various experiments can be ran to test model performance, as compared to the ECA, for different parameter configurations. Some are available in `showcase.ipynb`

# Installation

* From GitHub

`git clone https://github.com/andriysinclair/EVforecaster.git`
`cd EVforecaster`
`pip install .`

* From PyPi

TBC

# Usage

## Main Class Documentation



## General Usage

1. Download UK-NTS data from [UK Data Service](https://doi.org/10.5255/UKDA-SN-5340-14)

2. Unzip the downloaded file and move the following files from the `tab` folder into the `root/data/` directory.
   - `trip_eul_2002-2023.tab`
   - `day_eul_2002-2023.tab`
   - `household_eul_2002-2023.tab`

2. CD into the root directory and Import `EVforecaster`.

```python 
from EVforecaster.EVforecasterUser import EVforecaster
```

3. Create instance of class

```python 
evf = EVforecaster(travel_years=[2017])
```

  - `travel_years` should be between 2012 and 2017, this defines the years of travel data that you will use to generate annual charging demand forecasts
  - Upon creating an instance a dataset corresponding to the travel data of `travel_years` will be generated and moved into `root/dataframes/`, this can take some time. If another instance is made with the same `travel_years` it will not generate the dataset again but rather load an existing one from `root/dataframes/`.

4. Generate demand curves

```python 
results_dict = evf.generate_forecasts(N_sims=100,
weeks=list(range(1,53)),
home_shift=0,
experiment_name="agg_vs_agg_homeshift0",
ECA_overlay=None)
```

  - This will create 100 annual-aggregate weekly demand curves. The mean (over 100 simulations) demand curve, along with 95% confidence intervals is plotted and saved into `root/plots/`. The file name will be `agg_vs_agg_homeshift0.pdf`, or the parameter value of `experiment_name`.

  - Please refer to the class docstring above to understand the `resukts_dict` output.

  - As `ECA_overlay=None`, there will be no comparison with the ECA

5. Compare with ECA

  - If we set `ECA_overlay=list(range(39,53))`, then this will also plot weeks 39 to 52 (aggregated) of the ECA, calculate the $R^2$ and plot a distribution.
  - If a simulation, indexed by the `experiment_name` parameter, has been completed, then it can be loaded and used with various configurations of `ECA_overlay` for experimentation. 

6. Please see some examples of usage below. Available in `showcase.ipynb`.

# Examples




# References

- Pareschi, G., et al. (2020). *Are travel surveys a good basis for EV models?* Applied Energy, 275.
- Department for Transport (2018). Electric Chargepoint Analysis 2017: Domestics. Statistical release. UK Government. https://www.gov.uk/government/statistics/electric-vehicle-chargepoint-analysis-2017