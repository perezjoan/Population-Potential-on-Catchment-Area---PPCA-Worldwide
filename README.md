# PPCA-worldwide-protocol
 Population Potential on Catchment Area - Worldwide Protocol

This repository is part of the emc2 research project.
[emc2 project](https://emc2-dut.org/)  

# Objectives

This repository contains the implementation of the PPCA (Population Potential on Catchment Areas) protocol, a four-step procedure for acquiring, processing, and analyzing geospatial data from [OpenStreetMap (OSM)](https://www.openstreetmap.org/) and [Global Human Settlement (GHS)](https://human-settlement.emergency.copernicus.eu/ghs_pop2023.php) data using Python and associated libraries. The project aims to evaluate the potential number of people accessible on foot within specified proximity catchment areas. The protocol works globally by requiring only bounding box coordinates instead of sample data. The first step involves authenticating with Google Earth Engine to download GHS raster data for a specified area, converting it to vector format, and extracting and filtering OSM data for buildings, streets, and land use. The first step also calculates various morphometric indicators for buildings. The second step produces a complete classification of buildings in two categories : 1. residential or mixed-use 2. non residential. A Decision Tree Classifier is used to estimate missing values for this feature from building morphometrics. The third step determines the number of floors and the floor area for each residential or mixed-use building. Once again, a Decision Tree Classifier is used to estimate missing values from building morphometrics. The final step estimates population distribution within residential buildings based on floor area, associates these estimates with a pedestrian street network, and performs spatial analysis to map the population accessible on foot from the street network across various catchment areas. Each step outputs cleaned and processed geospatial data in the form of geopackage files, which can be used for further analysis and mapping.

# Sample Data

The protocol is designed for global application, requiring only the coordinates of a bounding box for the area of interest. Examples of coordinates are provided [here](https://github.com/perezjoan/PPCA-codes/blob/main/Case%20studies%20Coordinate%20Examples.txt), and this [tool](https://coordinates-converter.com/) can be used to quickly identify the necessary coordinates.

# Installation Steps

Follow these steps to run the Python algorithms on Windows:
- Install the [Anaconda distribution of Python](https://www.anaconda.com/download) Anaconda3-2024.10-1-Windows-x86_64 - Python 3.12
- Create a specific environment (detailed PPCA environment settings are provided [here](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt))
- Activate the environment and run the related Python scripts

# Releases
- v1.0.5 on 11/27/2024 - Added Appendix A1 to perform slope calculation and clustering for catchment creas (on points) and changes on the street filter of STEP 1 (added 'primary link' to pedestrian streets, removed some maxspeed values (60, 70, 80)
- v1.0.4 on 10/23/2024 - Added a function to fill small segments with NA average values in step 4
- v1.0.3 on 9/02/2024 - Added a function to remove zero length edges in step 4
- v1.0.2 on 8/27/2024 - Added the mean population potential per pedestrian street in step 4
- v1.0.1 on 7/31/2024 - Added a function (consolidate from cityseer) to remove close-by nodes in step 4
- v1.0.0 on 7/30/2024 - First version of the four-step procedure.

# Project sections

## STEP 1: DATA ACQUISITION - FILTERS - MORPHOMETRY [Link to code](https://github.com/perezjoan/PPCA-codes/tree/main/current%20release)

_Description:_

This script facilitates the acquisition of OpenStreetMap (OSM) and Global Human Settlement (GHS) data using Google Earth Engine, the osmnx library, and QGIS for processing. It requires the coordinates of a bounding box (WGS 84 decimal degrees) to define the geographical area. The script starts by authenticating and initializing Earth Engine to download GHS raster data for a specified year and geographical area (bounding box). The GHS data is saved on the cloud (Google Drive) as a raster image (Step 1.1), which is then manually moved to the local working directory.

Using QGIS, the raster image is converted to vector data (Step 1.2) via the pixelstopolygons algorithm and saved as a geopackage. The script proceeds to extract building, street, and land use data from OpenStreetMap (OSM) for the same area. It removes non-polygon geometries, cleans up columns (removing list-type columns), and keeps only essential attributes for buildings and streets.

The script performs several filtering operations:

    - Buildings are filtered based on a minimum footprint area of 15 m², removal of underground buildings, and optional filtering for buildings with no walls (Step 2.1).
    - GHS data is filtered by rounding population values and removing meshes with zero population (Step 2.2).
    - OSM streets are filtered to separate pedestrian and non-pedestrian streets by excluding streets with the following attributes: "motorway", "motorway_link", "trunk", "trunk_link", "busway" and "cycleway". Streets are also excluded if they are in tunnels (attribute "tunnel" = 'yes') or if their maxspeed is greater than or equal to 90 km/h (values 90, 100). Streets suitable for pedestrian use are then retained by ensuring they do not match these attributes or speed conditions (Step 2.3).
    - OSM land use data is filtered to exclude non-populated areas by removing polygons with the following land use categories: "construction", "cemetery", "education", "healthcare", "industrial", "military", "railway", "religious", "port", and "winter_sports". Areas that do not fall under these land use categories are considered populated (Step 2.4).

Morphometric indicators are computed for retained buildings:

    - FL for the number of floors
    - A for the surface area
    - P for perimeter
    - E for elongation
    - C for convexity
    - FA for floor area
    - ECA for a product involving elongation, convexity, and area
    - EA for an elongation-area product
    - SW for the shared walls ratio

Data are saved into two geopackages: cleaned raw data and retained features. The final output consists of filtered and cleaned vector data for GHS population, OSM buildings, streets, and land use areas. Optionally, a report with maps and statistics can be produced (Appendix 2).

_Requirements_
- The PPCA environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Authentication on google earth engine [Link to EE engine Authentication](https://code.earthengine.google.com/)
- Coordinates of a bounding box (WGS 84 decimal degrees) and EPSG reference

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

## STEP 2: BUILDING CLASSIFICATION [Link to code](https://github.com/perezjoan/PPCA-codes/tree/main/current%20release)

_Description_

This script creates a 'type' column within the OSM building data with three possible values (0: Null, 1: residential or mixed-use, 2: non-residential). The initial classification is based on the 'building' attribute in OSM, where buildings such as 'apartments', 'barracks', 'house', 'residential', 'bungalow', 'cabin', 'detached', 'dormitory', 'farm', 'static_caravan', 'semidetached_house', and 'stilt_house' are classified as residential or mixed-use (1).

Next, the classification is refined through a spatial join with non-populated OSM land use areas. Buildings within non-populated areas are assigned as non-residential (2) if their 'type' is initially Null (0). The final classification statistics of buildings (classified vs Null) are printed and mapped.

The script then estimates 'type' for the remaining Null values using a Decision Tree Classifier, trained on the morphometric indicators calculated in PPCA STEP 1 (e.g., area, perimeter, elongation, convexity). The data is split into training and testing subsets based on a specified training ratio, and the classifier's accuracy is evaluated on the test set. The trained model is then used to predict the 'type' values 1 or 2 for buildings having Null value (0) in OSM.

The output includes a new variable 'type_filled', where non-null values from the original 'type' column are retained, and predicted values from the model are used to fill the previously Null entries. The script also visualizes the decision tree, maps the results, and explores how the classifier's accuracy varies with different training data sizes, plotting accuracy as a function of training size.

_Requirements_
- The PPCA environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Output file PPCA_1-2_{Name}_retained.gpkg ('osm_non_populated_areas' (Polygon), 'osm_building_filtered' (Polygon))

_Guide to run PPCA STEP 2_
- Fill 0.1 box and run the script

_Output_
- PPCA_2-1_{Name}_TYPE: Building types. A geopackage file with a single layer
     * 'osm_buildings_res_type' (Polygon), osm buildings with building type filled by Decision Tree Classifier

## STEP 3: NUMBER OF FLOORS ESTIMATION [Link to code](https://github.com/perezjoan/PPCA-codes/tree/main/current%20release)

_Description_

This script processes OSM building data by focusing exclusively on residential or mixed-use buildings (type_filled = 1). It begins by ensuring that the 'height' and 'building
' columns are numeric, converting any non-numeric entries to Null. For these residential buildings, missing 'height' values are filled by multiplying the number of floors ('building
') by 3, assuming an average floor height of 3 meters. Conversely, missing 'building
' values are filled by dividing the height by 3 and rounding the result.

The script calculates and prints the number and percentage of rows where both 'height' and 'building
' remain null. After renaming 'building
' to 'FL' (floors), it recalculates the floor-area ('FA') by multiplying the number of floors ('FL') by the building footprint ('A').

Next, the script applies a Decision Tree Classifier to predict missing 'FL' values using morphometric indicators (such as area, perimeter, elongation, and convexity) calculated in PPCA STEP 1. The data is split into training and testing sets based on a specified training ratio. The classifier is trained on the training set, and its accuracy is evaluated on the test set.

The trained model is then applied to predict missing 'FL' values in the OSM building data. The output includes a new variable named 'FL_filled', which contains the original 'FL' values for non-null entries and model predictions for null entries. Since FA (floor-area) was calculated earlier in the script, it is now modified by correcting it with the newly predicted 'FL_filled' values.

Additionally, the script visualizes the decision tree, evaluates the classifier's accuracy, and explores how accuracy varies with different proportions of training data.

_Requirements_
- The PPCA environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Output file PPCA_1-2_{Name}_retained.gpkg ('osm_non_populated_areas' (Polygon), OSM land use data with non-populated areas)
- Output file PPCA_2-1_{Name}_TYPE.gpkg ('osm_buildings_res_type' (Polygon), osm buildings with building type filled by Decision Tree Classifier

_Guide to run PPCA STEP 3_
- Fill 0.1 box and run the script

_Output_
- PPCA_3-1_{Name}_IND_FL: Indicators and floors. A geopackage file with a single layer
     * 'osm_buildings_FL_filled' (Polygon), osm buildings with morphometric indicators, residential classification and missing number of floors filled by 
     Decision Tree Classifier
 
## STEP 4: POPULATION POTENTIAL PER BUILDING & PER CATCHMENT AREA [Link to code](https://github.com/perezjoan/PPCA-codes/tree/main/current%20release)

_Description_

This script estimates population distribution within residential and mixed-use buildings based on floor area. Using the centroids of these buildings, it conducts a spatial join with the GHS population data to associate each building with its respective population values. The population estimates ('VALUE') are disaggregated based on the ratio of each building's floor area ('FA') to the total floor area within the same GHS population grid cell. This results in a population estimation ('Pop_estimation') for each building.

The population estimation is then integrated into a pedestrian street network analysis using cityseer. Points are generated at regular intervals along pedestrian streets, with an optional offset applied to the first point. The population potential is associated with these points within various catchment areas, which are parameterized by customizable distances. At the building level, the output variable of interest is 'Pop_estimation'. At the pedestrian network level, the output variable of interest is 'cc_Pop_estimation_sum_{catchment_area_distance}_nw'.

An optional step allows filling null values for small street segments by averaging nearby segments' population data.

_Requirements_
- The PPCA environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Output file PPCA_1-2_{Name}_retained ('ghs_populated_{Date}_vector'(Polygon),  GHS population data with non null values)
- Output file PPCA 3-1_{Name}_FL_filled ('osm_buildings_FL_filled' (Polygon), osm buildings with morphometric indicators, residential
classification and missing number of floors filled by Decision Tree Classifier

_Guide to run PPCA STEP 4_
- Fill 0.1 box and run the script

_Output_
- PPCA_4-1_{Name}_POP_CAT: Population and Catchment Areas. A geopackage file with 2 layers
    * 'osm_buildings_pop_estimate' (Points), centroid of osm residential and mixed-use buildings with population estimations
    * 'points_catchment_stats' (Points), points generated along the pedestrian streets with population potential for different catchment
areas (sum, mean, maximum, minimum, variability)
    * 'pedestrian_streets_avg_pop' (Lines), pedestrian streets with population potential (mean) for different catchment areas
    * 'splited_street' (Lines), pedestrian streets splited at regular intervals

## APPENDIX A1: SLOPE CALCULATION AND CLUSTERING FOR CATCHMENT AREAS [Link to code](https://github.com/perezjoan/PPCA-codes/tree/main/current%20release)

_Description_

This script processes population data calculated in STEP 4 of the PPCA protocol, focusing on analysis and clustering based on population estimates across four predefined catchment area distances. This script only works with four distances values (catchment areas calculated in STEP 4). Population values are log-transformed to ensure comparability across scales, and slopes between distances are computed for each observation. The script generates outputs to analyze population distributions and identify clusters with distinct profiles. The clustering workflow combines hierarchical clustering (to define initial cluster centers using a sample of 10,000 observations) and k-means clustering (to cluster the full dataset based on three slopes and the log-transformed population estimate at the second distance). The clustering results are validated using silhouette scores and visualized through feature curves representing the average population and slopes for each cluster. The output includes a geopackage file containing the processed dataset with slope values, clustering labels for solutions ranging from 5 to 19 clusters, and metadata for further analysis. Visualizations such as dendrograms, silhouette score plots, and feature curves allow users to interpret the clustering results and assess the distribution of population estimates across the study area.

_Guide to run PPCA A1_
- Fill 0.1 box and run the script

_Requirements_
- The PPCA environment on Python [Link to environment](https://github.com/perezjoan/PPCA-codes/blob/main/Environment%20settings.txt)
- Output file PPCA_4-1_{Name}_POP_CAT ('Population and Catchment Areas'(Points))

 _Output_
- PPCA_A1_{Name}: A geopackage file with 1 layer
    * 'Points_with_reg_and_hckmeans' (Points), points along the pedestrian street network with slope results accross four distances values and clustering labels for solutions ranging from 5 to 19 clusters

# Acknowledgement 
This resource was produced within the emc2 project, which is funded by ANR (France), FFG (Austria), MUR (Italy) and Vinnova (Sweden) under the Driving Urban Transition Partnership, which has been co-funded by the European Commission.

# License
The emc2 project is licensed under the [Attribution-ShareAlike 4.0 International]. See the [LICENSE](https://github.com/perezjoan/PPCA-codes?tab=CC-BY-SA-4.0-1-ov-file) file for details.
