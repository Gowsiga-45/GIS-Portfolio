# Google Earth Engine – Flood Mapping Notes

## 1. Google Earth Engine Basics

### Centering the Map

```javascript
Map.setCenter(10.5, 51.2, 6);
```

### Purpose

Moves the map to a specific location and sets zoom level.

### Syntax

```javascript
Map.setCenter(longitude, latitude, zoom);
```

---

## 2. Variables

### Creating a Variable

```javascript
var germany;
```

### Purpose

Stores data for reuse later in the script.

### Syntax

```javascript
var variableName = value;
```

---

# 3. Loading Geographic Data

### Load Country Boundaries

```javascript
ee.FeatureCollection("FAO/GAUL/2015/level0")
```

### Purpose

Loads geographic boundary polygons.

### Terms

| Term              | Meaning                                  |
| ----------------- | ---------------------------------------- |
| ee                | Earth Engine library                     |
| FeatureCollection | Collection of geographic shapes/polygons |

### Dataset Used

```javascript
FAO/GAUL/2015/level0
```

World country boundary dataset.

---

# 4. Filtering Geographic Data

### Select Germany

```javascript
.filter(ee.Filter.eq('ADM0_NAME', 'Germany'))
```

### Purpose

Keeps only Germany from the dataset.

### Components

| Component      | Meaning              |
| -------------- | -------------------- |
| filter()       | Select matching data |
| ee.Filter.eq() | Equal condition      |
| ADM0_NAME      | Country name field   |
| Germany        | Target value         |

### Meaning

“Keep only the country named Germany.”

---

# 5. Displaying Data on Map

### Display Layer

```javascript
Map.addLayer(germany, {}, 'Germany');
```

### Purpose

Displays the layer on the map.

### Syntax

```javascript
Map.addLayer(data, style, layerName);
```

### Components

| Component | Meaning         |
| --------- | --------------- |
| germany   | Data to display |
| {}        | Styling options |
| 'Germany' | Layer name      |

---

# 6. Important Earth Engine Terms

| Term              | Meaning                |
| ----------------- | ---------------------- |
| Map               | Controls map display   |
| ee                | Earth Engine library   |
| FeatureCollection | Geographic polygons    |
| Image             | Single raster image    |
| ImageCollection   | Collection of images   |
| filter()          | Select data            |
| addLayer()        | Display layer          |
| print()           | Show output in Console |

---

# 7. Filtering Satellite Data

## Date Filter

```javascript
.filterDate('2024-01-01', '2024-01-31')
```

### Purpose

Selects images between specific dates.

---

## Area Filter

```javascript
.filterBounds(germany)
```

### Purpose

Selects images inside Germany boundary.

---

## Print Dataset Information

```javascript
print(data);
```

### Purpose

Displays dataset information in the Console panel.

### Important

`print()` does NOT display images on the map.

It shows:

* metadata
* image properties
* number of images
* available bands
* acquisition dates

---

# 8. Basic Earth Engine Workflow

```text
Load dataset
↓
Filter data
↓
Display on map
↓
Analyze results
↓
Export output
```

---

# 9. Sentinel-1 SAR Dataset

## Dataset Path

```javascript
'COPERNICUS/S1_GRD'
```

### Load Dataset

```javascript
var s1 = ee.ImageCollection('COPERNICUS/S1_GRD');
```

---

# 10. Sentinel-1 Characteristics

| Characteristic    | Description            |
| ----------------- | ---------------------- |
| SAR Radar         | Uses microwave signals |
| Day/Night         | Works 24 hours         |
| Cloud Penetration | Works through clouds   |
| Flood Mapping     | Very useful            |

---

# 11. SAR Concept

## SAR = Synthetic Aperture Radar

Instead of normal camera images:

* satellite sends microwave signals
* Earth reflects signals back
* satellite measures returned signal

### Flood Detection

Flood water appears very dark because:

* smooth water reflects radar away
* very little signal returns

So flooded areas become dark in SAR images.

---

# 12. ImageCollection Workflow

Typical workflow:

```text
Load images
↓
Filter by date
↓
Filter by area
↓
Select polarization
↓
Create composite
↓
Detect flood
```

---

# 13. Polarization Selection

```javascript
.select('VV')
```

### Common Polarizations

| Band | Meaning                                |
| ---- | -------------------------------------- |
| VV   | Vertical transmit → Vertical receive   |
| VH   | Vertical transmit → Horizontal receive |

---

# 14. Filtering Sentinel-1 Data

## Example

```javascript
var s1_filtered = s1
  .filterBounds(germany)
  .filterDate('2024-01-01', '2024-01-31');
```

### Purpose

Keeps only:

* Germany images
* January 2024 images

### Why Filtering is Important

Satellite datasets are huge.

Filtering:

* reduces processing time
* removes unnecessary data
* improves efficiency

---

# 15. Finding Dataset Paths

## Search Method

Use the search bar in Earth Engine.

Search examples:

* Sentinel-1
* Landsat
* MODIS
* SRTM

### Steps

1. Search dataset
2. Open dataset page
3. Copy Dataset ID
4. Use in code

---

# 16. Dataset Types

| Dataset Type      | Syntax                 |
| ----------------- | ---------------------- |
| ImageCollection   | ee.ImageCollection()   |
| Single Image      | ee.Image()             |
| Geographic Shapes | ee.FeatureCollection() |

### Examples

```javascript
ee.ImageCollection('COPERNICUS/S1_GRD')
```

```javascript
ee.Image('USGS/SRTMGL1_003')
```

---

# 17. first(), median(), mosaic()

## first()

```javascript
var image = s1_filtered.first();
```

### Purpose

Takes one image from the collection.

### Used For

* testing
* visualization
* learning

---

## median()

```javascript
var median = s1_filtered.median();
```

### Purpose

Combines many images into one cleaner image.

### Advantages

* reduces noise
* smoother result
* useful for flood analysis

---

## Why median() is Better than first()

`first()` uses only one image.

`median()` combines many images:

* more stable
* less noisy
* better flood detection

---

# 18. Clipping Images

## Clip to Study Area

```javascript
var clipped = image.clip(germany);
```

### Purpose

Cuts the image using study area boundary.

### Result

Only Germany remains visible.

---

# 19. Displaying Sentinel-1

```javascript
Map.addLayer(
  clipped,
  {bands:['VV'], min:-25, max:5},
  'Sentinel-1 VV'
);
```

### Purpose

Displays VV radar band on the map.

### Visualization Parameters

| Parameter | Meaning         |
| --------- | --------------- |
| bands     | Selected band   |
| min/max   | Display stretch |

---

# 20. Before and After Flood Images

## Before Flood

```javascript
var before = s1
  .filterBounds(ahr)
  .filterDate('2021-06-13', '2021-07-13')
  .select('VV')
  .median();
```

## After Flood

```javascript
var after = s1
  .filterBounds(ahr)
  .filterDate('2021-07-16', '2021-08-16')
  .select('VV')
  .median();
```

### Purpose

Compare satellite images before and after flooding.

---

# 21. Flood Detection

## Difference Method

```javascript
var difference = before.subtract(after);
```

### Purpose

Measures backscatter change.

### Interpretation

Flooded areas become darker after flood.

---

# 22. Thresholding

## Detect Flood Pixels

```javascript
var flood = difference.gt(3);
```

### Meaning

Keep pixels where:

```text
difference > 3
```

### Important Functions

| Function | Meaning               |
| -------- | --------------------- |
| gt()     | Greater than          |
| lt()     | Less than             |
| gte()    | Greater than or equal |
| lte()    | Less than or equal    |
| eq()     | Equal                 |

---

# 23. Noise Removal

## Connected Pixel Filtering

```javascript
var flood_clean = flood.updateMask(
  flood.connectedPixelCount(8).gte(8)
);
```

### Purpose

Removes isolated noisy pixels.

### Explanation

Small scattered detections are often false flood signals.

`connectedPixelCount()`
counts neighboring connected pixels.

`gte(8)`
keeps only groups with at least 8 connected pixels.

### Result

Cleaner flood map.

---

# 24. Permanent Water Removal

```javascript
var water = ee.Image('JRC/GSW1_4/GlobalSurfaceWater')
  .select('occurrence');

var permanentWater = water.gte(90);

var flood_no_water = flood_clean.updateMask(
  permanentWater.not()
);
```

### Purpose

Removes permanent rivers/lakes from flood map.

### Why Important

Permanent water is NOT flood water.

---

# 25. Ratio Method

```javascript
var ratio = after.divide(before);

var flood_ratio = ratio.lt(0.7);
```

### Purpose

Alternative flood detection method.

### Why Useful

SAR flood mapping often works better with ratios.

---

# 26. Flood Area Calculation

## Calculate Area

```javascript
var floodArea = flood_clean
  .multiply(ee.Image.pixelArea())
  .reduceRegion({
    reducer: ee.Reducer.sum(),
    geometry: ahr,
    scale: 10,
    maxPixels: 1e13
  });
```

---

# 27. Area Calculation Logic

## ee.Image.pixelArea()

Creates image where each pixel stores area in m².

At 10 m resolution:

```text
10 m × 10 m = 100 m²
```

---

## multiply()

Keeps area only for flood pixels.

Flood pixel:

```text
1 × pixel area
```

Non-flood pixel:

```text
0
```

---

## reduceRegion()

Combines all pixel values inside study area.

---

## Reducer.sum()

Adds all flooded pixel areas.

### Final Result

Total flood area in square meters.

---

# 28. Convert to km²

```javascript
var floodAreaKm2 =
  ee.Number(floodArea.get('VV'))
  .divide(1e6);
```

### Conversion

```text
1 km² = 1,000,000 m²
```

---

# 29. User Interface (UI)

## Meaning

`ui`
= tools for creating interactive layouts in Earth Engine.

### Examples

* legends
* panels
* split maps
* labels

---

# 30. Side-by-Side Comparison

```javascript
ui.SplitPanel(leftMap, rightMap, 'horizontal', true)
```

### Purpose

Compare:

* before flood
* after flood

side by side.

---

# 31. Legend Example

```javascript
Map.addLayer(
  flood_clean,
  {palette:['cyan']},
  'Flood'
);
```

### Meaning

Cyan color represents flood areas.

---

# 32. Export Results

```javascript
Export.image.toDrive({
  image: flood_clean,
  description: 'Flood_Map',
  region: ahr,
  scale: 10,
  maxPixels: 1e13
});
```

### Purpose

Exports flood map to Google Drive.

---

# 33. Complete Flood Mapping Workflow

```text
Load Sentinel-1
↓
Filter by date and location
↓
Create before image
↓
Create after image
↓
Compare SAR backscatter
↓
Threshold flood pixels
↓
Remove noise
↓
Remove permanent water
↓
Calculate flood area
↓
Visualize and export results
```
