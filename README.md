# PPCA-worldwide-protocol
 Population Potential on Catchment Area - Worldwide Protocol

This repository is part of the emc2 research project.
[emc2 project](https://emc2-dut.org/)  

# Objectives

This repository contains the implementation of the PPCA (Population Potential on Catchment Areas) protocol, a four-step procedure for acquiring, processing, and analyzing geospatial data from [OpenStreetMap (OSM)](https://www.openstreetmap.org/) and [Global Human Settlement (GHS)](https://human-settlement.emergency.copernicus.eu/ghs_pop2023.php) data using Python and associated libraries. The project aims at evaluating population potentials within specified catchment areas. The protocol works globally by requiring only bounding box coordinates instead of sample data. The first step involves authenticating with Google Earth Engine to download GHS raster data for a specified area, converting it to vector format, and extracting and filtering OSM data for buildings, streets, and land use. The second step calculates various morphometric indicators for buildings and uses a Decision Tree Classifier to estimate missing values for the number of floors. The third step classifies buildings as residential or non-residential using OSM attributes and further refines the classification with a Decision Tree Classifier to fill in missing values. The final step estimates population distribution within residential buildings based on floor area, associates these estimates with a pedestrian street network, and performs spatial analysis to map potential population distribution across various catchment areas. Each step outputs cleaned and processed geospatial data in the form of geopackage files, which can be used for further analysis and mapping.

# Sample Data

The protocol is designed for global application, requiring only the coordinates of a bounding box for the area of interest. Examples of coordinates are provided [here](https://github.com/perezjoan/PPCA-codes/blob/main/Case%20studies%20Coordinate%20Examples.txt), and this [tool](https://coordinates-converter.com/) can be used to quickly identify the necessary coordinates.

# Installation Steps

Follow these steps to run the Python algorithms :
- Install the [Anaconda distribution of Python](https://www.anaconda.com/download)
- Create a specific environment (detailed environment settings are provided [here](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt))
- Activate the environment and run the related Python scripts

# Releases
- v1.0.0 on 7/30/2024 - First version of the four-step procedure.

# Project sections

## STEP 1: DATA ACQUISITION - FILTERS - MORPHOMETRY [Link to code](https://github.com/perezjoan/PPCA-codes/tree/main/current%20release)

_Description:_

This script facilitates the acquisition of OpenStreetMap (OSM) and Global Human Settlement (GHS) data using the Google Earth Engine and the
osmnx library. It only requires the coordinates of a bounding box (WGS 84 decimal degrees). It starts by authenticating and initializing 
Earth Engine in order to download Global Human Settlement (GHS) raster data for a specified year and for the defined geographical area 
(bounding box). The GHS data is then saved on the cloud (google drive) as a raster image (1.1). The raster  image shall then manually be put in 
the local working directory linked to this script. Then, the raster image is converted to vector data (1.2). The script proceeds to extract 
building, street, and land use data within the same geographical area from OpenStreetMap (up to date). Data are cleaned by removing 
list-type columns. Then, the script performs other important tasks: (2.1) filters out buildings with a footprint area less than 15 m², 
underground buildings, and optionally filters out buildings that have no walls, if the 'wall' column exists. (2.2) It reads and filters 
Global Human Settlement (GHS) data by rounding values and removing meshes with zero population (2.3) It filters OpenStreetMap (OSM) streets
data to separate pedestrian and non-pedestrian streets based on the following attributes: "motorway|motorway_link|trunk|trunk_link|cycleway"
(2.4) It filters OSM land use data to identify non-populated areas based on the following attributes:"construction|cemetery|education|
healthcare|industrial|military|railway|religious|port|winter_sports". (3) Several new morphometric indicators are computed: 'FL' for the number of
floors, 'A' for the surface area, 'P' for the perimeter, 'E' for elongation, 'C' for convexity, 'FA' for floor area, 'ECA' for a product involving
elongation, convexity, and area, 'EA' for another elongation-area product, and 'SW' for shared walls ratio. Data are saved into two geopackages:
cleaned raw data and retained features (Appendix 1). A report with maps and statistics can be produced (Appendix 2).

_Requirements_
- The PPCA environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Authentication on google earth engine [Link to EE engine Authentication](https://code.earthengine.google.com/)
- Coordinates of a bounding box (WGS 84 decimal degrees)

_Guide to run PPCA STEP 1_
- Fill 0.1 box and run the script
- Put the output of step # 1.1 (GHS raster file) in your working directory before running step # 1.2
  
_Output_
- A raster file with the GHS population data at a given date
- PPCA_1-1_{Name}_raw: Cleaned Raw data. A geopackage file with 4 layers
    * 'ghs_{date}_vector'(Polygon),  GHS population data at a given date
    * 'osm_all_area_categories ' (Polygon), OSM land use data with non-populated areas
    * 'osm_all_buildings' (Polygon), OSM all buildings
    * 'osm_all_streets' (LineString), OSM all streets
- PPCA_1-2_{Name}_retained: Retained features. A geopackage file with 4 layers
    * 'osm_building_filtered' (Polygon), OSM buildings with light structures filtered out
    * 'ghs_populated_2020_vector'(Polygon),  GHS population data with non null values
    * 'osm_non_populated_areas' (Polygon), OSM land use data with non-populated areas
    * 'pedestrian_streets' (LineString), OSM pedestrian streets

## STEP 2: RESIDENTIAL BUILDINGS CLASSIFICATION [Link to code](https://github.com/perezjoan/PPCA-codes/tree/main/current%20release)

_Description_

This script creates a column 'type' within the OSM building data with three possible values (# 0 : Null ; 1: residential or mixed-use ; 2: 
non-residential). Values are filled using the OSM attributes 'building_type' : apartments', 'barracks', 'house', 'residential', 'bungalow',
'cabin', 'detached', 'dormitory', 'farm', 'static_caravan', 'semidetached_house' & 'stilt_house' are considered as residential or mixed-use
buildings. The classification is refined by attributing 0 values to Null values based on the spatial relationships with non-populated OSM 
land use areas. Final score of classified buildings vs buldings with Null values are printed and mapped. This script then estimated the 
Null values using the morphometric indicators calculated in PPCA STEP 1 using a Decision Tree Classifier. It splits the dataset into training
and testing subsets based on a specified training ratio. It then trains the classifier using the training set and evaluates its accuracy on
the test set. the trained model is used to predict the Null values for 'type'. Within the output, a new variable 'type_filled' is created 
with two modalities (1 : residential or mixed-use ; 2 : non-residential). 'type_filled' takes the value of the OSM 'type' variable for non
Null values, and the model prediction for Null values. The script also visualizes the decision tree, maps the results and examines how the
classifier's accuracy varies with different proportions of training data, plotting the accuracy as a function of the training data size.

_Requirements_
- The PPCA environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Output file PPCA_1-2_{Name}_retained.gpkg ('osm_non_populated_areas' (Polygon), 'osm_building_filtered' (Polygon))

_Guide to run PPCA STEP 2_
- Fill 0.1 box and run the script

_Output_
- PPCA_2-1_{Name}_IND_FL: Indicators and floors. A geopackage file with a single layer
     * 'osm_buildings_FL_filled' (Polygon), osm buildings with morphometric indicators and missing number of floors filled by 
     Decision Tree Classifier

## STEP 3: MORPHOMETRY + FLOOR CLASSIFICATION [Link to code](https://github.com/perezjoan/PPCA-codes/tree/main/current%20release)

_Description_

This script begins by ensuring the columns 'height' and 'building:levels' are numeric, converting any non-numeric entries to Null. For residential
buildings, the script then fills missing 'height' values by multiplying floors by 3, assuming an average floor height of 3 meters. Conversely, it 
fills missing building values by dividing 'height' by 3 and rounding the result, again for residential buildings only. It calculates and prints the
number and percentage of rows with Null in both 'height' and 'building. Using the morphometric indicators calculated in STEP 1, the script uses a 
Decision Tree Classifier for evaluating the missing values for the number of floors per building ('FL'). The protocol then splits the data into 
training and testing subsets based on a specified training ratio. The classifier is then trained on the training set and its accuracy is evaluated 
on the test data set. Next, the trained model is used to predict missing 'FL' values (number of floors) in the OSM building data where 'FL' values
are null. The output includes a new variable named 'FL_filled', which contains the original'FL' values for non-null entries and model predictions 
for null entries. Floor-area ('FA') is corrected using the model. Additionally, the script visualizes the decision tree, maps the results, and 
explores how the classifier's accuracy varies with different proportions of training data, plotting accuracy as a function of the training data size.

_Requirements_
- The PPCA environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Output file PPCA_1-2_{Name}_retained.gpkg ('osm_non_populated_areas' (Polygon), OSM land use data with non-populated areas)
- Output file PPCA_2-1_{Name}_IND_FL.gpkg ('osm_buildings_FL_filled' (Polygon), osm buildings with morphometric indicators and missing 
number of floors filled by Decision Tree Classifier)

_Guide to run PPCA STEP 3_
- Fill 0.1 box and run the script

_Output_
- PPCA_3-1_{Name}_IND_FL: Indicators and floors. A geopackage file with a single layer
     * 'osm_buildings_FL_filled' (Polygon), osm buildings with morphometric indicators and missing number of floors filled by 
     Decision Tree Classifier
 
## STEP 4: POPULATION POTENTIAL PER BUILDING & PER CATCHMENT AREA [Link to code](https://github.com/perezjoan/PPCA-codes/tree/main/current%20release)

_Description_

This script estimates population distribution within residential buildings based on floor area. The script filters the buildings to retain
only residential types. Using the centroids of these buildings, it conducts a spatial join with the GHS population data to associate each
building with its respective population values. It then disaggregates the population estimates (VALUE) based on these FA ratios to derive 
a population estimation (Pop_estimation) for each building. This population estimation is then integrated into a pedestrian street network
analysis (graph using cityseer). Points are generated along pedestrian streets at regular intervals, and the potential population is
associated to these points within various catchment areas. The distance between the points to be generated along the network, as well
as the catchment area distances can be parameterized.

_Requirements_
- The PPCA environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Output file from PPCA_1-2_{Name}_retained ('ghs_populated_{Date}_vector'(Polygon),  GHS population data with non null values)
- Output file from PPCA 3-1_{Name}_TYPE ('osm_buildings_res_type' (Polygon), osm buildings with residential classification null filled by 
Decision Tree Classifier)


_Guide to run PPCA STEP 4_
- Fill 0.1 box and run the script

_Output_
- A geopackage file with a single layer
    * 'osm_all_buildings_res_type_with_null' (Polygon), osm buildings with residential classification

# Acknowledgement 
This resource was produced within the emc2 project, which is funded by ANR (France), FFG (Austria), MUR (Italy) and Vinnova (Sweden) under the Driving Urban Transition Partnership, which has been co-funded by the European Commission.

# License
The emc2 project is licensed under the [Attribution-ShareAlike 4.0 International]. See the [LICENSE](https://github.com/perezjoan/PPCA-codes?tab=CC-BY-SA-4.0-1-ov-file) file for details.
