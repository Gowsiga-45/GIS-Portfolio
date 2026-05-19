# Flood Mapping using Sentinel-1 SAR in Google Earth Engine

## Project Overview

This project focuses on flood detection using Sentinel-1 SAR imagery in Google Earth Engine (GEE). The workflow compares pre-flood and post-flood satellite images to identify flood-affected regions using SAR backscatter changes.

The analysis includes:

* SAR image preprocessing
* Before and after flood comparison
* Difference image generation
* Threshold-based flood extraction
* Noise removal
* Flood area estimation

---

# Study Area

The study area is located in Germany during the July 2021 flood event.

The flood event caused severe damage in western Germany due to heavy rainfall and river overflow.

## Germany Boundary 

```
Map.setCenter(10.5, 51.2, 6);

var germany = ee.FeatureCollection("FAO/GAUL/2015/level0")
  .filter(ee.Filter.eq('ADM0_NAME', 'Germany'));
```

---

# Dataset Used

| Dataset             | Purpose                         |
| ------------------- | ------------------------------- |
| Sentinel-1 SAR      | Flood detection                 |
| VV Polarization     | Water detection                 |
| Google Earth Engine | Cloud-based geospatial analysis |

---

# Methodology

## 1. Load Sentinel-1 Dataset

```
var s1 = ee.ImageCollection('COPERNICUS/S1_GRD');
```

---

## 2. Create Before and After Flood Images

### Before Flood

```
var before = s1
  .filterDate('2021-06-13', '2021-07-13')
  .median();
```

### After Flood

```
var after = s1
  .filterDate('2021-07-16', '2021-08-16')
  .median();
```

---

## 3. Generate Difference Image

```
var difference = before.subtract(after);
```

---

## 4. Flood Thresholding

```
var flood = difference.gt(6);
```

---

## 5. Noise Removal

```
var flood_clean = flood.updateMask(
  flood.connectedPixelCount(8).gte(8)
);
```

---

# Results

## Before Flood Image

![Before_Flood](Before_Flood.png)

---

## After Flood Image

(Add Screenshot Here)

---

## Difference Image

(Add Screenshot Here)

---

## Flood Mask

(Add Screenshot Here)

---

# Key Observations

* Flooded regions appeared darker in post-flood SAR imagery.
* Difference analysis successfully identified flood-prone regions.
* Noise filtering removed isolated false flood pixels.
* Connected pixel filtering improved flood mask quality.

---

# Skills Learned

* Google Earth Engine
* SAR image interpretation
* Flood mapping workflow
* Image differencing
* Threshold classification
* Noise removal techniques
* GIS visualization

---

# Conclusion

This project successfully demonstrated flood detection using Sentinel-1 SAR imagery in Google Earth Engine. The workflow can support disaster management, environmental monitoring, and rapid flood assessment applications.

---

# Technologies Used

* Google Earth Engine
* JavaScript
* Sentinel-1 SAR Data
* GIS & Remote Sensing Techniques
