# Project 1: Geospatial Data Cleaning & CRS Correction

## Objective
This project demonstrates how to clean geospatial data and correct coordinate reference systems (CRS) using QGIS.

## Input Dataset Information

| Attribute        | Details                          |
|-----------------|----------------------------------|
| Source          | Natural Earth                    |
| Dataset         | Admin 0 – Countries              |
| Format          | Shapefile (.shp)                 |
| CRS             | EPSG:4326 (WGS84)                |
| Data Type       | Polygon                          |

## Tools Used
- QGIS
  
## Tasks Performed

* Loaded global country dataset (Natural Earth)
* Verified Coordinate Reference System (CRS)
* Reprojected dataset to a common CRS (UTM)
* Identified and fixed geometry errors
* Performed topology validation
* Extracted Germany as a separate feature
* Calculated area of Germany polygon
* Visualized and labeled the final output

## Workflow & Results

### 1. Raw Dataset

Initial global country boundaries loaded into QGIS.

![Raw Dataset](Screenshot%201.png)


### 2. Geometry Issues

Visualization of invalid geometries before cleaning (distorted polygons).

![Geometry Issues](Screenshot%202.png)

### 3. Final Output – Germany

Extracted Germany polygon after cleaning and processing.

![Germany Polygon](Screenshot%203.png)

### 4. Area Calculation

Germany polygon with calculated area displayed.

**Germany Area: 357,674 km²**

![Area Calculation](Screenshot%204.png)

## Output Summary

* Cleaned and validated geospatial dataset
* Extracted country-level feature (Germany)
* Accurate area calculation using projected CRS
* Ready-to-use processed dataset for further analysis

## Key Learning Outcomes

* Handling real-world geospatial datasets
* Fixing invalid geometries in QGIS
* Understanding CRS and reprojection
* Feature extraction using attribute queries
* Spatial calculations (area measurement)
* Data visualization and map presentation

## Notes

All processing was performed using QGIS geoprocessing and geometry tools.
Area was calculated after reprojection to a projected coordinate system to ensure accuracy.


