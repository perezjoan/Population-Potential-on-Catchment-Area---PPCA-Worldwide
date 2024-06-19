# PPCA-worldwide-protocol
 Population Potential on Catchment Area - Worldwide Protocol

This repository is part of the emc2 research project.
[emc2 project](https://emc2-dut.org/)  
[ESPACE laboratory](https://www.umrespace.org/)

## Global Objectives

This repository contains the implementation of the PPCA (Population Potential on Catchment Areas) protocol, which is a part of the [EMC2 research project](https://emc2-dut.org/). The project aims to evaluate population potential within specified catchment areas using various data sources and machine learning techniques. 

## Sample Data

The protocol is designed to work globally. The user only needs to provide the coordinates of a bounding box for the area of interest. Coordinate examples are provided [here](https://github.com/perezjoan/PPCA-codes/blob/main/Case%20studies%20Coordinate%20Examples.txt).

## Installation Steps

The code is written in Python, and each script requires a specific environment. The environments are detailed below with information containing the necessary commands to install the environnements.

Follow these steps to run the Python algorithms :
- Install the [Anaconda distribution of Python](https://www.anaconda.com/download)
- Navigate to the relevant section and create a specific environment (detailed environment settings are provided [here](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt).
- Activate an environment and run the related Python scripts

## Project sections
**PPCA 1.0 : GHS and OSM automated data acquisition** [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20Import_ghs_osm_data.ipynb)

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
- A specific working environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Authentication on google earth engine [Link to EE engine Authentication](https://code.earthengine.google.com/)

_Guide to run the script:_
- Fill box 0.2 within the code 
- Put the output of step 1. in your working directory before running step 2.
  
_Outputs :_
- A raster file with the GHS population data
- A geopackage file with 4 layers :
    * 'ghs_{date}_vector'(Polygon),  GHS population data at a given date
    * 'osm_all_area_categories ' (Polygon), OSM land use data with non-populated areas
    * 'osm_all_buildings' (Polygon), OSM all buildings
    * 'osm_all_streets' (LineString), OSM all streets

**PPCA 2.0 : Data filter / Preparation**: [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/1.0%20Import_ghs_osm_data.ipynb)

_Description:_

The script processes and filter spatial data from OSM and GHS sources for further analysis. It performs four main tasks: (1) It reads and filters 
Global Human Settlement (GHS) data by rounding values and removing meshes with zero population (2) It filters OpenStreetMap (OSM) streets data to 
separate pedestrian and non-pedestrian streets (3) It filters OSM land use data to identify non-populated areas.

_Requirements:_
- A specific working environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Output file from PPCA 1.0 ('ghs_{date}_vector'(Polygon),  GHS population data at a given date ; 'osm_all_area_categories ' (Polygon), OSM land use
data with non-populated areas ; 'osm_all_streets' (LineString), OSM all streets)

_Guide to run the script:_
- Fill 0.2 box within the script

_Outputs :_
- A geopackage file with 4 layers :
    * 'ghs_populated_2020_vector'(Polygon),  GHS population data with non null values
    * 'osm_non_populated_areas ' (Polygon), OSM land use data with non-populated areas
    * 'pedestrian_streets' (LineString), OSM pedestrian streets
    * 'non_pedestrian_streets' (LineString), OSM non-pedestrian streets

**PPCA 3.0 Morphometry on Buildings**: [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/3.0%20morphometry%20%2B%20height.ipynb)

_Description:_

This script performs several calculations and transformations on a layer of OSM buildings. It begins by ensuring the columns 'height' and 'building
are numeric, converting any non-numeric entries to NaN. The script then fills missing 'height' values by multiplying floors by 3, assuming an 
average floor height of 3 meters. Conversely, it fills missing building values by dividing 'height' by 3 and rounding the result. It calculates and
prints the number and percentage of rows with NaN in both 'height' and 'building. Several new columns are computed: 'FL' for the number of floors,
'A' for the surface area, 'P' for the perimeter, 'E' for elongation, 'C' for convexity, 'FA' for floor area, 'ECA' for a product involving 
elongation, convexity, and area, 'EA' for another elongation-area product, and 'SW' for shared walls ratio. Finally, the script renames 
'building:floors' to 'FL'.

_Requirements:_
- A specific working environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Output file from PPCA 1.0 ('osm_all_buildings' (Polygon), OSM all buildings)

_Guide to run the script:_
- Fill 0.2 box

_Output :_
- A geopackage file with a single layer
    * 'osm_all_buildings_ind' (Polygon), osm buildings with height/floor values completed and with morphometric indicators
 
**PPCA 4.0 Residential & non-residential buildings : classification based on attributes**: [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/4.0%20classif%20based%20on%20attributes.ipynb)

_Description:_

This script filter out buildings with a footprint area less than 15 m² and optionally filters out buildings that have no walls, if the 'wall' column
exists. It then create a column 'type' within the OSM building data with three possible values (# 0 : NA ; 1 : residential or mixed-use ; 
2 : non-residential). Values are filled using the OSM attributes 'building_type' : apartments', 'barracks', 'house', 'residential', 'bungalow', 
'cabin', 'detached', 'dormitory', 'farm', 'static_caravan', 'semidetached_house' & 'stilt_house' are considered as residential or mixed-use 
buildings. Finally, the classification is refined by attributing 0 values to null values based on the spatial relationships with non-populated 
OSM land use areas. Final score of classified buildings vs buldings with null values are printed and mapped.

_Requirements:_
- A specific working environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Output file from PPCA 3.0 ('osm_all_buildings_ind' (Polygon), OSM all buildings)
- Output file from PPCA 2.0 ('osm_non_populated_areas' (Polygon), OSM land use data with non-populated areas)

_Guide to run the script:_
- Fill 0.2 box

Output :
- A geopackage file with a single layer
    * 'osm_all_buildings_res_type_with_null' (Polygon), osm buildings with residential classification

**PPCA 5.0 Residential & non-residential buildings : fill values with decision tree classifier**: [Link to code](https://github.com/perezjoan/PPCA-codes/blob/main/5.0%20Residential%20classification%20fill%20null%20with%20machine%20learning.ipynb)

_Description:_

This script trains and evaluates a Decision Tree Classifier on OSM building data. Initially, it splits the dataset into training and testing subsets 
based on a specified training ratio. It then trains the classifier using the training set and evaluates its accuracy on the test set. Subsequently,
it applies the trained model to predict missing 'type' values on the OSM building data with missing values for 'type'. Within the output, a new 
variabme containing named 'type_filled' is created with two modalities (1 : residential or mixed-use ; 2 : non-residential). 'type_filled' takes
the value of the OSM 'type' varaible for non null values, and the model prediction for null values. The script also visualizes the decision tree, 
map the results and examines how the classifier's accuracy varies with different proportions of training data, plotting the accuracy as a function
of the training data size.

_Requirements:_
- A specific working environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Output file from PPCA 4.0 ('osm_all_buildings_res_type_with_null' (Polygon), OSM all buildings)

_Guide to run the script:_
- Fill 0.2 box

_Output :_
- A geopackage file with a single layer :
    * 'osm_all_buildings_res_type_filled' (Polygon), osm buildings with residential classification null filled by Decision Tree Classifier

**PPCA 6.0 : Population potential estimation per building** _Work in progress_

**PPCA 7.0 : Population potential estimation per catchment areas** _Work in progress_

## Acknowledgement 
This resource was produced within the emc2 project, which is funded by ANR (France), FFG (Austria), MUR (Italy) and Vinnova (Sweden) under the Driving Urban Transition Partnership, which has been co-funded by the European Commission.

## License
The emc2 project is licensed under the [Attribution-ShareAlike 4.0 International]. See the [LICENSE](https://github.com/perezjoan/PPCA-codes?tab=CC-BY-SA-4.0-1-ov-file) file for details.
