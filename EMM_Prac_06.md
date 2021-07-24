# Environmental Monitoring and Modelling (ENV306/506)

## Prac06 - Understanding patterns of historical deforestation

### Acknowledgments 

- Google Earth Engine Team
- [GEARS Lab](https://www.gears-lab.com/emm_lab_7/)

### Objective

The objective of this tutorial is to develop an important skill of environmental management, which is around understanding and managing the forest status specifically deforestation. In this tutorial, you will learn to look into the historical forest data and chart the yearly forest loss over any given spatial location.  

---------------------------------------------------
## 1. Getting to know the Hansen dataset.
1. Open the Google Earth Engine environment by going to [https://code.earthengine.google.com] in the Chrome browser.

2. 
![Figure 1. NT roi](Prac06/roi.png)

```JavaScript
// Load the rainfall dataset 
var CHIRPS= ee.ImageCollection('UCSB-CHG/CHIRPS/PENTAD');
```


6. Don't forget to save the script before you exit. 

## 5. Ungraded exercise

In today's prac we worked with 5-year rainfall data and looked into the techniques of mapping the spatial pattern and charting the temporal trend. We used a 5-year window and a 1-year window to look into the rainfall pattern and trend. The 5-year window is a short period in the context of ecological changes. Compare the rainfall pattern of the recent 5 years using pre-2000 as a baseline over the greater Broken Hill region of NSW. Plot the rainfall trend for the past 40 years. Does the rainfall trend reveal drought in the Broken Hill region?


## The complete script

```JavaScript
// Load the rainfall dataset 
var CHIRPS= ee.ImageCollection('UCSB-CHG/CHIRPS/PENTAD');

// print the dataset to console
print(CHIRPS);

// add the dataset to your mapping layer.
Map.addLayer(CHIRPS.first());

//filter the dataset with a date range of interest
var precip5Y = CHIRPS.filterDate('2015-01-01','2019-12-31');

// adding mean precipitation of five-year time series to the mapping panel 
Map.addLayer(precip5Y.mean().clip(roi), {min: 3, max: 25, palette:['lightblue','blue','darkblue']}, '5-year precip');

//filter the dataset with a date range of interest
var precip1Y=CHIRPS.filterDate('2020-01-01','2020-12-31');
// adding precipitation of 1 year to the mapping panel. 
Map.addLayer(precip1Y.mean().clip(roi), {min: 3, max: 25,palette:['lightblue','blue','darkblue']}, '1-year precip');

// Create the five year precipitation chart 
var TS5 = ui.Chart.image.series({
	imageCollection: precip5Y, // Name of the image collection that you want to extract data from
	region: roi, // Region where you want the data to come from
	reducer: ee.Reducer.mean(), // Spatial reducer that you want to use
	scale: 10000, // Spatial scale of data extraction
	xProperty: 'system:time_start'}) // Put the image acquisiton time as x-axis label
	.setOptions({ 
	title: 'Precipitation five years',  // set the title of the chart
	hAxis:{title: 'Time'}, // set the x-axis label
	vAxis: {title: 'mm/pentad'}});// set the vertical axis label

// print the chart
print(TS5);

// Create the one year precipitation chart 
var TS1 = ui.Chart.image.series({
	imageCollection: precip1Y, // Name of the image collection that you want to extract data from
	region: roi, // Region where you want the data to come from
	reducer: ee.Reducer.mean(), // Spatial reducer that you want to use
	scale: 10000, // Spatial scale of data extraction
	xProperty: 'system:time_start'}) // Put the image acquisiton time as x-axis label
	.setOptions({ 
	title: 'Precipitation one years',  // set the title of the chart
	hAxis:{title: 'Time'}, // set the x-axis label
	vAxis: {title: 'mm/pentad'}});// set the vertical axis label
	
// print the chart
print(TS1);
```

-------
### Thank you

I hope you found this prac useful. A recorded video of this prac can be found on your learnline.

#### Kind regards, Deepak Gautam
------
### The end
