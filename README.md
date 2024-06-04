# PPCA-worldwide-protocol
 Population Potential on Catchment Area - Worldwide Protocol

This repository is part of the emc2 research project.
[emc2 project](https://emc2-dut.org/)  
[ESPACE laboratory](https://www.umrespace.org/)

## Global objectives :

This repository contains the implementation of the PPCA (Population Potential on Catchment Areas) protocol, which is a part of the EMC2 research project. The project aims to evaluate population potential within specified catchment areas using various data sources and machine learning techniques. For more information about the EMC2 project, visit EMC2 Research Project.

The protocol is designed to work globally; you only need to provide the coordinates of the bounding box for your area of interest.

The code is written in Python, and each script requires a specific environment. The environments are detailed below, with links to text files containing the necessary conda commands (e.g., conda create, conda install, etc.).

_1.0 Data Download:_ This script connects to OSM and Google Earth Engine to automatically downloads OSM and GHS data based on provided coordinates.\
_2.0 Data Filtering:_ Filters the downloaded data, categorizing roads into pedestrian and non-pedestrian, and buildings into residential and non-residential, among other classifications.\
_3.0 Machine Learning:_ Uses machine learning to evaluate and classify buildings as residential or non-residential. (WORK UNDER PROGRESS)\
_4.0 Population Evaluation:_ Estimates the population within the evaluated buildings. (WORK UNDER PROGRESS)\
_5.0 Population to Catchment Areas:_ Maps the estimated population data to the defined catchment areas. (WORK UNDER PROGRESS)\

## Project sections
**1.0 Data Download**- Automated download of GHS and OSM data: [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20Import_ghs_osm_data.ipynb)
_Description:_
This script acquires and processes spatial data using Google Earth Engine, OpenStreetMap, and QGIS tools. It downloads GHS raster data (population estimate), OSM data (roads, buildings and land cover) and converts it to vector format, and saves it in a GeoPackage.

_Requirements:_
- A specific working environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20environment.txt)
- Authentication on google earth engine [Link EE engine Authentication](https://code.earthengine.google.com/)

_Guide to run the script:_
- Fill box 0.2 within the code 
- Put the output of step 1. (GHS raster) in your working directory

**2.0 Filter on Downloaded Data**: [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20Import_ghs_osm_data.ipynb)
