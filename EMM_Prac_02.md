# Environmental Monitoring and Modelling (ENV306/506)
## Prac 2 - Getting deeper into Earth Engine

### Acknowledgments 
- Google Earth Engine Team
- Geospatial Analysis Lab, University of San Francisco (especially Nicholas Clinton and David Saah)
- [GEARS Lab](https://www.gears-lab.com/emm_lab_2/)

### Prerequisites
- Completion of this Prac exercise requires the use of the Google Chrome browser and a Google Earth Engine account. If you have not yet signed up - please do so now in a new tab: [Earth Engine account registration](https://signup.earthengine.google.com/)

- Once registered you can access the Earth Engine environment at [https://code.earthengine.google.com](https://code.earthengine.google.com)

### Objective
In Prac 1 we learnt how to search for a single image and display it in the map environment. From an environmental monitoring perspective we will often be interested in how and where landscapes, and different components of that landscape, change over time. Understanding landscape context is very important, as ecological processes change with topographic position. In this Prac we will learn how to access, visualise and query digital elevation data for any study location.
 
---------------------------------------------------
## 1. Getting started with elevation data
1. Navigate to darwin area - see prac 01 on how to. 

2. Search for "elevation" or "SRTM" and click on the "NASA SRTM Digital Elevation 30m" result to show the dataset description.

![Figure 4. Search for elevation data](Prac1/searchelevation.png)

3. Read the information on the dataset - look under "description" and "bands". Once done, click on "Import", which imports the dataset and places it under the Imports section at the top of your script.

![Figure 4. View elevation datasource and import](Prac1/viewinfo.png)

Question: How many bands do this data have and what is the spatial resolution?

5. Rename the default variable name "image" to anything you like. Here we will rename it to "theSRTM".

![Figure 5. Rename image](Prac1/rename.png)

6. Print the image object to the console by copying the script below into the code editor, and click "run" :

```JavaScript
print(theSRTM);
```
![Figure 6. Print SRTM](Prac1/printrun.png)


7. Browse through the information that was printed to the console window. Open the “bands” section to show the one band named “elevation”. Note that all this same information is automatically available for all variables in the Imports section.

![Figure 7. SRTM in console](Prac1/browseprint.png)

## 3. Adjusting visualisation parameters

1. Use the Map.addLayer() method to add/display the image to the interactive map. We will start simple, without using any of the optional parameters. After adding the script, hit "run" again. Every time you make changes to your script, you will need to run the script again.

```JavaScript
Map.addLayer(theSRTM);
```

The displayed map will look pretty flat grey because the default visualization parameters map the full 16­bit range of the data onto the black–white range, but the elevation range is much smaller than that in any particular location. We’ll fix it in a moment.

![Figure 8. Map SRTM](Prac1/flatgrey.png)

2. Select the Inspector tab. Then click on several points on the map to get a feel for the elevation range in this area.

![Figure 8. Inspect SRTM](Prac1/inspector.png)

3. Now you can set some more appropriate visualization parameters by adjusting the code as follows (units of the min and max values are in meters above sea level):

```JavaScript
Map.addLayer(theSRTM, {min: 0, max: 300});
```
![Figure 9. Visualise SRTM](Prac1/minmax.png)

4. You will now be able to see the variation in elevation range with low values in black and highest points in white. Layers added to the map will have default names like "Layer 1", "Layer 2", etc. To improve the readability, we can give each layer a human­-readable name, by adding a title with the syntax in the following code. Don't forget to click run.

```JavaScript
Map.addLayer(theSRTM, {min: 0, max: 300}, 'Elevation above sea level');
```
![Figure 10. Rename title](Prac1/layername.png)

## 4. Commenting and saving your scripts

1. Now the code has started to look a little bit messy. Imagine you coming back to this code after a year. Would you still be able to tell which line is doing what task? Hence, it is a good idea to always put comments to your code reminding you of what you did and why. We add comments with two forward slashes // :

```Javascript
// Print data details to console
print(theSRTM);

// Add the SRTM data to the interactive map
Map.addLayer(theSRTM);

// Add the data again, but with restricted value ranges for better visualisation
Map.addLayer(theSRTM, {min: 0, max: 300});

// Add the data again, with value ranges, and a useful title for the Layer tab
Map.addLayer(theSRTM, {min: 0, max: 300}, 'Elevation above sea level');
```
![Figure 11. Comment script](Prac1/commenting.png)

2. Also remember to save the code by clicking "Save". It will be saved in your private repository, and will be accessible the next time you log in to Earth Engine.

![Figure 12. Comment script](Prac1/save.png)

3. If you would like to experiment with different colour combinations, you can play with colour palettes as per the example below:

```Javascript
// Adding color scale to the elevation data
Map.addLayer(theSRTM, {min: 0, max: 300, palette: ['blue', 'yellow', 'red']}, 'Elevation above sea level');
```

![Figure 13. Colour scale elevation](Prac1/colorelevation.png)

## 5. Hillshading and slope

1. For better visualisation we can create a hillshade view of the elevation data. Remember you can use the Layer transparency options to create draped images for colourised hillshades.

```JavaScript
// Create hillshade and map it
var hillshade = ee.Terrain.hillshade(theSRTM);
Map.addLayer(hillshade, {min: 150, max:255}, 'Hillshade');
```

![Figure 14. Hillshade view](Prac1/hillsrtm.png)


2. Slope works in a similar way:

```javascript
// Create terrain slope and map it
var slope = ee.Terrain.slope(theSRTM);
Map.addLayer(slope, {min: 0, max: 20}, 'Slope');
```

![Figure 15. Slope map](Prac1/slope.png)

3. Don't forget to save your script before closing the Chrome tab.
```

## Exercise
Locate a cloud-free image over Darwin City from before and after Cyclone Marcus (17 March 2018). Derive NDVI layers for both images and visually compare the effects of the cyclone.

## The complete script

```JavaScript

```

-------
### Thank you

I hope you found this prac useful. A recorded video of this prac can be found on your learnline.

#### Kind regards, Deepak Gautam
------
### The end
