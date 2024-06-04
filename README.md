# PPCA-worldwide-protocol
 Population Potential on Catchment Area - Worldwide Protocol

This repository is part of the emc2 research project.
[emc2 project](https://emc2-dut.org/)  
[ESPACE laboratory](https://www.umrespace.org/)

# Global objectives :


## Project sections
- **1.0 Data Automated Download - GHS and OSM**: [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20Import_ghs_osm_data.ipynb)
This script acquires and processes spatial data using Google Earth Engine, OpenStreetMap, and QGIS tools. It downloads GHS raster data (population estimate), OSM data (roads, buildings and land cover) and converts it to vector format, and saves it in a GeoPackage.

`Requirements:`
- A specific working environment [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20environment.txt)
- Authentication on google earth engine

`Guide to run the script:`
- Fill 0.2 box
- Put the output of step 1. in your working directory

- **2.0 Filter on Downloaded Data**: [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20Import_ghs_osm_data.ipynb)

The following dependencies are required to run the Python scripts
      `conda install -c conda-forge geopandas`    
      `conda install -c conda-forge pyogrio`    
      `conda install -c conda-forge contextily`   
      `conda install -c conda-forge momepy`
      `pip install --upgrade cityseer`     

   *0.1 Pre-processing  - MAIN GeoPackage & SUBSET preparation - Python script*
