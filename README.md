# PPCA-worldwide-protocol
 Population Potential on Catchment Area - Worldwide Protocol

This repository is part of the emc2 research project.
[emc2 project](https://emc2-dut.org/)  
[ESPACE laboratory](https://www.umrespace.org/)

## Global objectives :

This repository contains the implementation of the PPCA (Population Potential on Catchment Areas) protocol, which is a part of the [EMC2 research project](https://emc2-dut.org/). The project aims to evaluate population potential within specified catchment areas using various data sources and machine learning techniques.

The protocol is designed to work globally; you only need to provide the coordinates of the bounding box for your area of interest. Coordinate examples are provided [here](https://github.com/perezjoan/PPCA-codes/blob/main/Case%20studies%20Coordinate%20Examples.txt)

The code is written in Python, and each script requires a specific environment. The environments are detailed below, with links to text files containing the necessary conda commands (e.g., conda create, conda install, etc.).

_1.0 Data Download:_ This script connects to OSM and Google Earth Engine to automatically downloads OSM and GHS data based on provided coordinates.\
_2.0 Data Filtering:_ Filters the downloaded data, categorizing roads into pedestrian and non-pedestrian, and buildings into residential and non-residential, among other classifications.\
(3+) Under progress

## Installation Steps

Follow these steps to run the Python algorithms :
- Install the [Anaconda distribution of Python](https://www.anaconda.com/download)
- Navigate to the relevant section and create a specific environment (detailed environment settings are provided in each section).
- Activate an environment and run the related Python scripts

## Project sections
**PPCA 1.0 Data download : GHS and OSM data acquisition** [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20Import_ghs_osm_data.ipynb)

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
- 
_Outputs :_
- A raster file with the GHS population data
- A geopackage file with 4 layers :
    * 'ghs_{date}_vector'(Polygon),  GHS population data at a given date
    * 'osm_all_area_categories ' (Polygon), OSM land use data with non-populated areas
    * 'osm_all_buildings' (Polygon), OSM all buildings
    * 'osm_all_streets' (LineString), OSM all streets

**PPCA 2.0 Data filter / Residential & Non-residential Buildings Classification based on Attribute values**: [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20Import_ghs_osm_data.ipynb)

_Description:_

The script processes and filter spatial data from OSM and GHS sources for further analysis. It performs four main tasks: (1) It reads and filters 
Global Human Settlement (GHS) data by rounding values and removing meshes with zero population (2) It filters OpenStreetMap (OSM) streets data to 
separate pedestrian and non-pedestrian streets (3) It filters OSM land use data to identify non-populated areas.

_Requirements:_
- A specific working environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/2.0%20environment.txt)
- Output file from PPCA 1.0 (Geopackage)

_Guide to run the script:_
- Fill 0.2 box within the script

_Outputs :_
- A geopackage file with 4 layers :
    * 'ghs_populated_2020_vector'(Polygon),  GHS population data with non null values
    * 'osm_non_populated_areas ' (Polygon), OSM land use data with non-populated areas
    * 'pedestrian_streets' (LineString), OSM pedestrian streets
    * 'non_pedestrian_streets' (LineString), OSM non-pedestrian streets

**PPCA 3.0 Morphometry on Buildings**: [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20Import_ghs_osm_data.ipynb)

_Description:_

This script performs several calculations and transformations on a layer of OSM buildings. It begins by ensuring the columns 'height' and 'building
are numeric, converting any non-numeric entries to NaN. The script then fills missing 'height' values by multiplying floors by 3, assuming an 
average floor height of 3 meters. Conversely, it fills missing building values by dividing 'height' by 3 and rounding the result. It calculates and
prints the number and percentage of rows with NaN in both 'height' and 'building. Several new columns are computed: 'FL' for the number of floors,
'A' for the surface area, 'P' for the perimeter, 'E' for elongation, 'C' for convexity, 'FA' for floor area, 'ECA' for a product involving 
elongation, convexity, and area, 'EA' for another elongation-area product, and 'SW' for shared walls ratio. Finally, the script renames 
'building:floors' to 'FL'.

_Requirements:_
- A specific working environment [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/3.0%20morphometry%20%2B%20height.ipynb)
- Output file from PPCA 1.0 (Geopackage)

_Guide to run the script:_
- Fill 0.2 box

_Output :_
- A geopackage file with a single layer
    * 'osm_all_buildings_ind' (Polygon), osm buildings with height/floor values completed and with morphometric indicators

