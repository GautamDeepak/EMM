# Environmental Monitoring and Modelling (ENV306/506)

## Prac05 - Exploring land surface temperature trends over time

### Acknowledgments 

- Google Earth Engine Team
- [GEARS Lab](https://www.gears-lab.com/emm_lab_4/)

### Objective

The objective of this lab is to delve deeper into climate variables with Google Earth Engine. By the end of this lab you will be able to explore long-term trends in temperature data for specific regions of interest. 

---------------------------------------------------
## 1. Accessing the MODIS LST dataset.
The MOD11A2 V6 product provides an average 8-day land surface temperature (LST) in a 1200 x 1200-kilometre grid. Each pixel value in MOD11A2 is a simple average of all the corresponding MOD11A1 LST pixels collected within those 8 days. 


1. Open up the Google Earth Engine environment by going to [https://code.earthengine.google.com] in the Chrome browser.

2. In this prac, we will make use of the Landsat 8 TOA Tier 1 collection (LANDSAT/LC08/C01/T1_TOA). You have already used this image collection in Prac03. Import the image collection and rename the image collection to "l8". You can use two methods to import the image collection. a) using the techniques learnt in Prac03, b) by simply running the below script. Both the method will do exactly the same thing - provide you with access to the Landsat 8 TOA Tier 1 collection.

```JavaScript
// import the Landsat- 8 image collection
var l8 = ee.ImageCollection('LANDSAT/LC08/C01/T1_TOA');
```

![Figure 1. NSW DPI report](Prac05/nswdpi.png)

6. Don't forget to save the script before you exit. 

## 5. Ungraded exercise

1. Move your region of interest (roi) around different parts of Australia and explore how current drought conditions are affecting dam levels in different parts of the country. Can you query the water dam from your region? Generate the max innundation for each year since 2013.

2. Think about how you might go about expanding the time series and going further back in time to look at longer-term inundation trends e.g. since the start of this century.

## The complete script

```JavaScript
// import the Landsat- 8 image collection
var l8 = ee.ImageCollection('LANDSAT/LC08/C01/T1_TOA');

// Define the desired start and end dates upfront
var startDate = ee.Date('2013-05-01');
var endDate = ee.Date(Date.now());

// Select Landsat 8 images within the specified time and spatial range
var l8Images = l8.filterDate(startDate, endDate).filterBounds(roi);

// print the filtered images
print(l8Images);

// define a threshold value to mask out the cloud. 0 = no clouds, 100 = completly cloudy
var cloudThreshold = 20;

// Write a function to mask out the cloud 
function cloudMask (anImage){
  // Next line of script  goes through each pixel of the input image
  // and gives a simple cloud score for each pixels. 
  // The algorithm uses brightness, temperature, and NDSI (normalise difference snow index)
	var cloudScore = ee.Algorithms.Landsat.simpleCloudScore(anImage);
	// next line of script uses the threshold we set earlier to identify the cloudy pixels
	var cloudy = cloudScore.select('cloud').gt(cloudThreshold);
	// next line of script applies the mask and returns the masked image
	return anImage.updateMask(cloudy.not());}

// apply cloudMask function to the image collection l8Images
var l8CloudMasked = l8Images.map(cloudMask);

// display an image to test the masking
Map.addLayer(l8CloudMasked.first(), {min: 0, max: 0.3, bands: ['B4', 'B3', 'B2']}, 'Testing the cloudMask');

//map the temporally reduced median of the image collection
Map.addLayer(l8CloudMasked.median().clip(roi), {min: 0, max: 0.5, bands: ['B4', 'B3', 'B2']}, 'Median, clipped true-color');

// Function to compute compute and return NDWI for an input Landsat image
function computeNdwiImage(anImage) {
   // Input the bands in the correct order to match the NDWI formula and rename the band name to NDWI
  var ndwiTemp = anImage.normalizedDifference(['B3', 'B5']).rename('NDWI');
  // Clip the NDWI to roi and return the clipped NDWI whilst retaining the timestamp from input image
  return ndwiTemp.clip(roi).copyProperties(anImage, ['system:time_start']);
}

// Apply the computeNdwiImage function to our masked image collection 
var l8Ndwi = l8CloudMasked.map(computeNdwiImage);

// Map the maximum extent of the water bodies since 2013
Map.addLayer(l8Ndwi.max().mask(l8Ndwi.max()),{min: 0, max:1, palette: ['blue','darkblue']}, 'Max extent of water');

// The year I am interested to map the NDWI
var year = 2019;

// filter the NDWI for the year
var ndwi2019 = l8Ndwi.filterDate(ee.Date.fromYMD(year, 1, 1), ee.Date.fromYMD(year, 12, 31));

// Add the maximum water body extent for that 1 year
Map.addLayer(ndwi2019.max().mask(ndwi2019.max()), {min:0,max:1,palette:['darkblue']}, 'Max extent 2019');

//Function to return the binary water status: 0 = not water, 1= water 
function waterBinary(anImage) {
	return anImage.gt(0);}

// Map the waterBinary function over the l8ndwi collection. 
var innundationStatus = l8Ndwi.map(waterBinary);

// computing frequency of inundation = Sum of innundation/ Count of innundation
var frequency = innundationStatus.sum().divide(innundationStatus.count());

//Add the frequency of inundation image to the mapping layer
Map.addLayer(frequency.mask(frequency),
{palette: ['white', 'magenta', 'blue','darkblue']}, 'Water inundation frequency');
  
```

-------
### Thank you

I hope you found this prac useful. A recorded video of this prac can be found on your learnline.

#### Kind regards, Deepak Gautam
------
### The end
