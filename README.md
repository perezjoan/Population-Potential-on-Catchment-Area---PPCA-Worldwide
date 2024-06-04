# PPCA-worldwide-protocol
 Population Potential on Catchment Area - Worldwide Protocol

This repository is part of the emc2 research project.
[emc2 project](https://emc2-dut.org/)  
[ESPACE laboratory](https://www.umrespace.org/)

## Global objectives :

This repository contains the implementation of the PPCA (Population Potential on Catchment Areas) protocol, which is a part of the [EMC2 research project](https://emc2-dut.org/). The project aims to evaluate population potential within specified catchment areas using various data sources and machine learning techniques. For more information about the EMC2 project, visit EMC2 Research Project.

The protocol is designed to work globally; you only need to provide the coordinates of the bounding box for your area of interest.

The code is written in Python, and each script requires a specific environment. The environments are detailed below, with links to text files containing the necessary conda commands (e.g., conda create, conda install, etc.).

_1.0 Data Download:_ This script connects to OSM and Google Earth Engine to automatically downloads OSM and GHS data based on provided coordinates.\
_2.0 Data Filtering:_ Filters the downloaded data, categorizing roads into pedestrian and non-pedestrian, and buildings into residential and non-residential, among other classifications.\
_3.0 Machine Learning:_ Uses machine learning to evaluate and classify buildings as residential or non-residential. (WORK UNDER PROGRESS)\
_4.0 Population Evaluation:_ Estimates the population within the evaluated buildings. (WORK UNDER PROGRESS)\
_5.0 Population to Catchment Areas:_ Maps the estimated population data to the defined catchment areas. (WORK UNDER PROGRESS)\

## Installation Steps

Follow these steps to run the Python algorithms :
- Install the [Anaconda distribution of Python](https://www.anaconda.com/download)
- Navigate to the relevant section and create a specific environment (detailed environment settings are provided in each section).
- Activate an environment and run the related Python scripts

## Project sections
**1.0 Data Download**- Automated download of GHS and OSM data: [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20Import_ghs_osm_data.ipynb)

_Description:_

This script facilitates the acquisition of spatial data using a combination of Google Earth Engine, OpenStreetMap, and QGIS tools. 
It starts by authenticating and initializing Earth Engine and downloading Global Human Settlement (GHS) raster data for a specified year. 
The GHS data is then exported as a raster image for a defined geographical area. Once downloaded, the raster data is converted to vector data using
QGIS and saved in a local GeoPackage. The script proceeds to extract building data within the same geographical area from OpenStreetMap, cleans the
data by removing any list-type columns, and saves the cleaned data into the GeoPackage. Additionally, the script extracts street data from 
OpenStreetMap, converting it into a GeoDataFrame format and filtering it to separate pedestrian streets from primary roads. Both sets of street data
are visualized and saved in the GeoPackage. The result is a set of spatial data layers, including GHS population data, building footprints, and 
street layers.

_Requirements:_
- A specific working environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20environment.txt)
- Authentication on google earth engine [Link to EE engine Authentication](https://code.earthengine.google.com/)

_Guide to run the script:_
- Fill box 0.2 within the code 
- Put the output of step 1. (GHS raster) in your working directory

**2.0 Filter on Downloaded Data**: [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20Import_ghs_osm_data.ipynb)
