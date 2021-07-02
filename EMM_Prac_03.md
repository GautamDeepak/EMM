# Environmental Monitoring and Modelling (ENV306/506)
## Prac03 - Reducing and applying functions to images collection

### Acknowledgments 
- Google Earth Engine Team
- [GEARS Lab](https://www.gears-lab.com/emm_lab_3/)

### Objective
In Prac01 and Prac02 we learnt how to search for images (multispectral and elevation) and display them in the map environment, and we ran some basic computations. Now we will go a step further and learn how to apply the computations over a full collection of images. 
---------------------------------------------------
## 1. Load and filter and visualise Landsat image collection.
In this prac, we will work with the Landsat 8 image collection over Mitchell pateau, which is a area in north western Australia. This area is challenging because there is often a lot of cloud in this area. So, quite tricky to get a cloud-free mosaic from this region.
1. Open up the Google Earth Engine environment by going to [https://code.earthengine.google.com] in the Chrome browser.
1. In the search bar, search for Mitchell plateau and navigate to the area. 
![Figure 1. Navigate 2 mitchell plateau](Prac03/navigate.png)

2. Now search for “Landsat 8 TOA” satellite data. From the list of results, open the “USGS Landsat 8 Collection 1 Tier 1 TOA Reflectance”.
![Figure 2. L8 TOA reflectance](Prac03/l8toa.png)
3. Have a read about the dataset description and band information and then click “Import” to import the dataset to your scripting pane. Once imported, rename the imageCollection to "l8".
![Figure 3. Search for L8TOA rename](Prac03/rename.png)

4. Use the rectangle geometry tool to draw a polygon over the Northern Australia. Rename the default geometry import to “roi” and untick the “roi” from Geometry Imports
![Figure 4. draw geometry](Prac03/geometry.png)

5. In Prac01, we learnt an extensive method of filtering image collection. Here we will just use the date range and the roi to return a collection of images, unlike a single image in Prac01. 
```JavaScript
// filter the data collection to from 2021 up to now.
var filtered = l8.filterDate('2021-01-01', Date.now()).filterBounds(roi);
```

4. Now print the filtered image collection to the console. 
```JavaScript
// print the image collection to the console
print(filtered);
```
5. Explore the printed information in the console. 
![Figure 5. print console](Prac03/console.png)

*Question:* How many Landsat-8 images were collected from the Northern Australia since the start of this year?

4. Now lets put all the images (I had 1179 images) into the mapping layer using Map.addLayer command. Notice the image tiles have hard boundaries and does not provide a synergetic view. 

```JavaScript
// display the filtered image collection into mapping layer
Map.addLayer(filtered,{min: 0, max: 0.3, bands:['B4', 'B3', 'B2']}, 'filtered');
```

![Figure 5. Northern Australia images](Prac03/1179images.png)

5. Notice subtle differences in the visualisation parameters between the Sentinel-2 and Landsat-8. In Sentinel-2 we had min-max set to 0-3000 whereas in Landsat 8  the appropriate min-max is 0-0.3.


## 2. Reducing image collections (temporal reducer)
1. Temporal reducers aggregates data over time. Imaging same Landsat scene being caputred approximately twice a month for 12 months. That means, on a given year, a particlular Landsat-8 image tile, will have about 24 images sitting right underneath captured at different time. Temporal reducers aggregates those images together to produce a composite. A temporal reducer drills down' temporally' thorugh each pixel and return the aggregated data for us.

![Figure 5. Temporal reducer](Prac03/temporalReducer.png)


2. Lets apply the median() temporal reducer, which drills down temporally through each pixel and return the median value over time.

```JavaScript
// add the median image to the mapping layer
Map.addLayer(filtered.median(), {min: 0, max: 0.3, bands:['B4', 'B3', 'B2']}, 'median RGB');
```
![Figure 5. median reducer](Prac03/median.png)

3. Explore the displayed mapping layer. Most of the clouds is removed and the hard boundaries between the image tiles are minimal the scene looks more blended. Try other reducers here e.g. mean(), min(), max(). Which reducer worked best for you?

4. Similarly, we can map median false color composite. Remember false color composite highlights the photosynthetically active vegetation in bright red. 

```JavaScript
// add the median false color composite to the mapping layer
Map.addLayer(filtered.median(), {min: 0, max: 0.3, bands:['B5', 'B4', 'B3']}, 'median false color');
```

![Figure 5. median reducer](Prac03/falseColor.png)

## 3. Writing functions e.g. to compute NDVI 

1. What is a function?

1. In Lab01, we used image.expression() to compute NDVI. Here, let us use ee.Image.normalizedDifference() method to compute the NDVI. Have a read about this method in the docs tab. Compute and add the NDVI to the mapping layer

```JavaScript
// compute NDVI and add the NDVI image to the mapping area
var ndvi = cloudFreeImage.normalizedDifference(['B5', 'B4']);
Map.addLayer(ndvi, {min: 0, max: 1, palette:['brown','yellow','green']}, 'NDVI');
```
2. Our next goal is to create a new band called NDVI on the Landsat imageCollection. To do so, we first need to refactor our code to use a function, which can later be applied to all images in a collection

3. Start by writing a function that adds a NDVI band to a single image:
```JavaScript
// add a new ndvi band a single l8 image
function addNDVIband(anImage) {
  var ndviTemp = anImage.normalizedDifference(['B5', 'B4']);
  return anImage.addBands(ndviTemp.rename('NDVI'));}
```
4. It is always a good idea to test your functions. We can test this function on the cloudFreeImage that has be created earlier. 

```JavaScript
// test the function on a single image
var ndviAdded = addNDVIband(cloudFreeImage);
print ('Before adding NDVI', cloudFreeImage);
print ('After adding NDVI', ndviAdded);
```

5. Now explore the printed information. Expand to see if an extra band called, “NDVI” has been added to the Landsat image.


## 4. Map the function over the Landsat image collection

1. Now that we have a function that works on a single image, we will 'map' the function across the Landsat 8 collection called “filtered”.

```JavaScript
// map the function to the image collection
var ndviMapped = filtered.map(addNDVIband);
print ('After mapping NDVI', ndviMapped);
```

2. Now you have an entire collection of Landsat image with an extra band containing NDVI. You can now treat this NDVI as a regular band and visualise it or apply temporal reducers. 

```JavaScript
// apply temporal reducer to the NDVI band that has been added to the image collection. 
Map.addLayer(ndviMapped.median(), {bands: 'NDVI', min: 0, max: 1, palette:['brown','yellow','green']}, 'median ndvi');
```
## 5. Charting NDVI over time
1. Add the following line of script to make a chart of NDVI over time for your area of interest. 

```JavaScript
// chart the ndvi over time
var ndviChart = ui.Chart.image.series({
	imageCollection:ndviMapped.select("NDVI"),
  	region:roi,
  	reducer:ee.Reducer.median(),
  	scale:90,
  	xProperty:'system:time_start'});
print(ndviChart);
```
2. Try out the interactivity of the chart by hovering; expand it to full screen, and testing out the SVG/PNG/CSV download buttons.

## 6. Exporting image
The source data that you work with can have many different characteristics (single band, multispectral, bit depth, etc.), and the visualization tools in Earth Engine allow you to display the data in a variety of ways. You can also export the data in a variety of ways, such as a multi¬band image. This section will demonstrate how to export a 3¬band (RGB) 8-bit image that can be easily displayed in other tools outside of Earth Engine. In this exercise, we will export the cloud-free image extracted from the collection

1. Export the image to your Google Drive account. 

```JavaScript
// export the cloud-free image to your google drive account
Export.image.toDrive({
	image:cloudFreeImage.visualize(rgb_vis), // RGB visualisation parameter
	description:"Export", // Name of the task
	folder:"EarthEngine", // folder name on your google drive
	fileNamePrefix:"ENV306506_Lab03_cloudFreeImage", // file name
	region:roi, // export from this region
	scale:30}); // at this spatial resolution
```

2. After you run the script, you need to go to the task tab in the right panel and click run to export.

3. Go to your appropriate folder in the google drive to see the exported image

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
