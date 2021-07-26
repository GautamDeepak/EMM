# Environmental Monitoring and Modelling (ENV306/506)
## Prac 2 - Getting deeper into Earth Engine

### Acknowledgments 
- Google Earth Engine Team
- Geospatial Analysis Lab, University of San Francisco (especially Nicholas Clinton and David Saah)
- [GEARS Lab](https://www.gears-lab.com/emm_lab_2/)

### Objective
In Prac 1 we learnt how to search an archive for a single cloud-free image, display it in the map environment, and compute indices. From an environmental monitoring perspective, we will often be interested in how and where landscapes, and different components of that landscape, change over time. Understanding landscape context is very important, as ecological processes change with topographic position. In this Prac, we will learn how to access, visualise and query digital elevation data for any study location.
 
---------------------------------------------------


4. DO NOT forget to save your script. Click on the Save button and follow the prompt to save the script.

*Note:* There are other digital elevation data available to you on the google earth engine. Try searching for them. One useful data for Australian application is called “Australian 5M DEM”. This data covers Australia's populated coastal zone; floodplain surveys within the Murray Darling Basin, and individual surveys of major and minor population centres. 

## 7. Exercise
Prac01 taught you how to visualise Sentinel-2 data and compute indices. Prac02 taught you how to visualise elevation data, apply computation, and reducer.  

- Locate a cloud-free Sentinel image over Greater Darwin region from before and after Cyclone Marcus (17 March 2018). 
- Using the cloud-free Sentinel image, derive NDVI layers for before and after the Cyclone Marcus and visually compare the effects of the cyclone.
- Overlay the elevation data, apply spatial reducer and compute mean elevation of the areas which had a severe, moderate, and minimal effect by the Cyclone. 

## The complete script

```JavaScript
// Print data details to console
print(theSRTM);

// Add the SRTM data to the interactive map
Map.addLayer(theSRTM);

// Add the data again, but with restricted value ranges for better visualisation
Map.addLayer(theSRTM, {min: 0, max: 300});

// Add the data again, with value ranges, and a useful title for the Layer tab
Map.addLayer(theSRTM, {min: 0, max: 300}, 'Elevation above sea level');

// Adding colour scale to the elevation data
Map.addLayer(theSRTM, {min: 0, max: 300, palette: ['blue', 'yellow', 'red']}, 'Elevation above sea level');

// Create hillshade and map it
var hillshade = ee.Terrain.hillshade(theSRTM);
Map.addLayer(hillshade, {min: 150, max:255}, 'Hillshade');

// Create terrain slope and map it
var slope = ee.Terrain.slope(theSRTM);
Map.addLayer(slope, {min: 0, max: 20}, 'Slope');

// computation: Terrain that has elevation over 200 m
var high = theSRTM.gt(200);
Map.addLayer(high, {}, 'Above 200m');

// Masking out the terrains below 200m from the DEM
var DEMover200 = theSRTM.updateMask(high);
Map.addLayer(DEMover200, {min: 200, max: 300, palette: ['blue', 'yellow', 'red']}, 'DEM>200');

// Apply spatial reducer to compute mean elevation over the roi
var meanElevation = theSRTM.reduceRegion({
        reducer: 'mean',
        geometry: roi,
        scale: 90
});

// print the mean elevation
print('Mean elevation', meanElevation.get('elevation'));
```

-------
### Thank you

I hope you found this prac useful. A recorded video of this prac can be found on your learnline.

#### Kind regards, Deepak Gautam
------
### The end
