<p align="center"> Geomapping Pipeline </p>
<p align="center"> <img src="./docs/Figure1_Pipeline.png" width="100%" /> </p>
<!--asldfkj-->

<!--![GitHub Status](https://github.com/github/docs/actions/workflows/main.yml/badge.svg)-->
<!--[![PyPI pyversions](https://img.shields.io/pypi/pyversions/shap)](https://pypi.org/pypi/shap/)-->

**SDOH (Social Determinants of Health)** 

The Social Determinants of Health (SDOH) Code Repository was initially created for the research paper 'Social Determinants of Health and Limitation of Life-Sustaining Therapy in Neurocritical Care: A CHoRUS Pilot Project' but serves as a central hub for sharing, refining, and reusing code utilized in SDOH projects, including but not limited to CHoRUS projects (see [papers](#citations) for details and citations).

Neighborhood-level SDOH (NL-SDOH) involve factors that characterize a patient's residential area, such as proximity to the hospital. The Social Vulnerability Index (SVI) is one of a neighborhood-level measure quantifying healthcare disparities risk across various subdomains based on the patient's neighborhood. NL-SDOH data in this repository were obtained from the Center for Disease Control and Prevention Agency for Toxic Substances and Disease Registry (CDC/ATSDR) of the U.S. Census Bureau. Later, other databases will be also included.

**Geomapping pipeline** 

A geomapping pipeline was developed to associate NL-SDOH. By leveraging patient home addresses within the electronic health record, we identified a unique census tract, enabling the linkage of individual patients to specific NL-SDOH (SVI) values. Address-to-SVI data linkages were then conducted offline on a secure institutional computer to ensure data privacy and security. Refer to the above figure for an illustration of this pipeline.

1. The initial step in the NL-SDOH data linkage process involved acquiring a geographic information dictionary from the Census Bureau's Topologically Integrated Geographic Encoding and Referencing/Line (TIGER/Line) databases. 

2. The subsequent step included determining Census tracts (Federal Information Processing Standards code [FIPS]) by utilizing geocodes associated with patient addresses or zip codes, through the TIGER/Line dictionary. Census tracts might not precisely align with a single zip code, establishing a potential many-to-many relationship with multiple zip codes. In instances where a patient only provided a zip code, we coded it to be linked with the relevant census tract FIPS code using proportion ratios, indicating the percentage of the zip code associated with specific census tracts. It's noteworthy that none of these cases were present in our final cohort.

3. Subsequently, patient records were linked with their corresponding Social Vulnerability Index (SVI) values from the relevant database, based on their residence at the time of admission. To ensure a balanced representation, for geographic analyses, adjacent census tracts within the same county were consolidated using Federal Information Processing Standards (FIPS) codes, with a minimum threshold of 10 patients per census tract. This approach aimed to prevent overrepresentation and enhance the validity of the analysis.

## Install Required Packages

All the required packages can be installed as below:
<pre>
pip install package_name (packages: pandas, numpy, gc, pyshp)
</pre>

## Load Packages 

```python
import pandas as pd
import numpy as np
#import datetime
#from datetime import timedelta
#import hashlib
import gc
import os
import geopandas as gpd
from shapely.geometry import Polygon
from shapely.geometry import Polygon, Point, MultiPolygon
import shapefile 
from geopandas import GeoDataFrame
import pandas as pd
```

## Data Load

```python
gc.collect()
df = pd.read_csv('C:/Users/username/NCC_SDOH_eLLST/patient_information.csv', low_memory=False)

print("df shape:", df.shape, len(df['PatientID'].unique()), len(df['PatientEncounterID'].unique()))

Address_columns = ['PatientID', 'PatientEncounterID', 'LineNBR', 'AddressLine01TXT', 'CityNM', 'StateDSC', 'CountryDSC', 'zipCD', 'AddressTXT']
```

## Data pre-processing
1. Identify patients lacking documented home addresses
   For example,
   1) no information
   2) homeless/shelter
   3) hospice
   4) rehab
   5) unknown
      
2. Identify patients with incomplete addresses
   For example,
   1) zipCD is null
   2) CountryDSC != 'United States of America'
    
   However, often if CountryDSC is null, that can be considered as USA.
   
3. Clean and standardize patient address data
   For example,
   1) In the address, there can be 'post box' or 'email like @cultea.com', in County or any place. We would like to remove it through rule based methods.


## SVI Linkage
1. Yearly Linkage

```python
df['HospAdmissionDate_Year'] = pd.to_datetime(df['HospAdmissionDate']).dt.year
df['SVILink_Year'] = np.where(df['HospAdmissionDate_Year'].isin([2016,2017]), 2018, df['HospAdmissionDate_Year'] )
df['SVILink_Year'] = np.where(df['SVILink_Year'].isin([2018,2019,2020,2021,2022]), 2018, df['SVILink_Year'] )


df_addr['HospAdmissionDate_Year'] = pd.to_datetime(df_addr['HospAdmissionDate']).dt.year
df_addr['SVILink_Year'] = np.where(df_addr['HospAdmissionDate_Year'].isin([2016,2017]), 2018, df_addr['HospAdmissionDate_Year'] )
df_addr['SVILink_Year'] = np.where(df_addr['SVILink_Year'].isin([2018,2019,2020,2021,2022]), 2018, df_addr['SVILink_Year'] )


```

## Load SVI Database
1.
path = 'C:\\username\\NCC_SDOH_eLLST\\ZipCode\\zcta520\\' 
total_zc_shp_dictionary = gpd.read_file(f"{path}\{'tl_2020_us_zcta520.shp'}").to_crs('EPSG:4326') 
total_zc_shp_dictionary.ZCTA5CE20 = total_zc_shp_dictionary.ZCTA5CE20.astype('int64')

path = 'C:\\username\\NCC_SDOH_eLLST\\CensusTractShpFile2022\\allinone' 
total_shp_dictionary = gpd.read_file(f"{path}\{'total_shp.shp'}").to_crs('EPSG:4326') 





<p align="center">
  <img width="811" src="./docs/artwork/california_instance.png" />
</p>

If we take many force plot explanations such as the one shown above, rotate them 90 degrees, and then stack them horizontally, we can see explanations for an entire dataset (in the notebook this plot is interactive):

```python
# visualize all the training set predictions
shap.plots.force(shap_values[:500])
```

<p align="center">
  <img width="811" src="./docs/artwork/california_dataset.png" />
</p>

To understand how a single feature effects the output of the model we can plot the SHAP value of that feature vs. the value of the feature for all the examples in a dataset. Since SHAP values represent a feature's responsibility for a change in the model output, the plot below represents the change in predicted house price as the latitude changes. Vertical dispersion at a single value of latitude represents interaction effects with other features. To help reveal these interactions we can color by another feature. If we pass the whole explanation tensor to the `color` argument the scatter plot will pick the best feature to color by. In this case it picks longitude.

```python
# create a dependence scatter plot to show the effect of a single feature across the whole dataset
shap.plots.scatter(shap_values[:, "Latitude"], color=shap_values)
```

## Sample notebooks

The notebooks below demonstrate different use cases for SHAP. Look inside the notebooks directory of the repository if you want to try playing with the original notebooks yourself.

## Citations

The algorithms and visualizations used in this package came primarily out of research in [Su-In Lee's lab](https://suinlee.cs.washington.edu) at the University of Washington, and Microsoft Research. If you use SHAP in your research we would appreciate a citation to the appropriate paper(s):

- For general use of SHAP you can read/cite our [NeurIPS paper](http://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions) ([bibtex](https://raw.githubusercontent.com/shap/shap/master/docs/references/shap_nips.bib)).
- For TreeExplainer you can read/cite our [Nature Machine Intelligence paper](https://www.nature.com/articles/s42256-019-0138-9) ([bibtex](https://raw.githubusercontent.com/shap/shap/master/docs/references/tree_explainer.bib); [free access](https://rdcu.be/b0z70)).
- For GPUTreeExplainer you can read/cite [this article](https://arxiv.org/abs/2010.13972).
- For `force_plot` visualizations and medical applications you can read/cite our [Nature Biomedical Engineering paper](https://www.nature.com/articles/s41551-018-0304-0) ([bibtex](https://raw.githubusercontent.com/shap/shap/master/docs/references/nature_bme.bib); [free access](https://rdcu.be/baVbR)).

<img height="1" width="1" style="display:none" src="https://www.facebook.com/tr?id=189147091855991&ev=PageView&noscript=1" />
