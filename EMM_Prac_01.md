# Environmental Monitoring and Modelling (ENV306/506)
Prac 1 - Introduction to Google Earth Engine and JavaScript
--------------

### Acknowledgments 
- Google Earth Engine Team
- Earth Engine Beginning Curriculum

------

### Prerequisites
-------------

Completion of this Prac exercise requires the use of the Google Chrome browser and a Google Earth Engine account. If you have not yet signed up - please do so now in a new tab: [Earth Engine account registration](https://signup.earthengine.google.com/)

Once registered you can access the Earth Engine environment here: https://code.earthengine.google.com

------------------------------------------------------------------------

### Objective
---------
The objective of this lab is to give you an introduction to the Google Earth Engine processing environment and basic JavaScript.  


## 1. Signup for the GEE account
1. On google chrome, navigate to https://code.earthengine.google.com/signup/(https://code.earthengine.google.com/signup/)
2. Follow the signup and activation process.

## 2. Introduction to the GEE environment interface
Google Earth Engine uses the JavaScript programming language. We will cover the very basics of this language during this course. Read the introduction provided here for more detail: https://developers.google.com/earth-engine/tutorials/tutorial_js_01(https://developers.google.com/earth-engine/tutorials/tutorial_js_01)


## 2. Navigate through the GEE environment interface.
1. Open up the Google Earth Engine environment by going to this address in the Chrome browser: https://code.earthengine.google.com(https://code.earthengine.google.com). You should see the GEE landing page as below

![Figure 1. The Google Earth Engine environment](Prac01/LandingPage.png)

2. Notice GEE environment is divided up into four panels: 
- The top-left panel with tabs for Scripts, Docs and Assets  
- The editor (top-centre) panel is for writing and running JavaScript commands  
- The top-right panel with tabs for the console, inspector and tasks  
- The bottom panel is Map interface with geometry features 

3. Navigate through the four panels and try to understand what each panel does. Take the feature tour.

![Figure 1. The Google Earth Engine environment](Prac01/FeatureTour.png)   

4. Check out all the buttons that are available to you and try to understand them. 

##3. Basic introduction to JavaScript

1. Time to write your first JavaScript for Earth Engine! In the editor panel, paste below script and hit run. "Hello World!" will be printed to the console tab. 
 
```JavaScript
print("Hello World!");
```

2. The line above is a JavaScript statement. In JavaScript, statements end in a semicolon. Earth Engine programs are made up of a set of statements like this one. You can think of these statements as a set of tasks you want the earth engine to perform.

Self assessment question: Modify the above script to print your name in the console.

3.When writing a rather long JavaScript program, it is a good practice to put lots of comments in your code, to describe what you're trying to do. Commenting will improve code readability. To comment, use "//" befor the comment. for example: 

```JavaScript
// Printing hello world to the console
print("Hello World!");
```

4. In the above script, the content after "//" are ignored by the Earth Engine. These comments are for us to understand whats happening in the script. 

5. Using variables to store objects and primitives helps code readability. For example, a variable that stores a string object is defined by single ' or double " quotes but don't mix them. In below script, first we make a new string and store it in a variable called toPrint. Second, we print the variable toPrint which actually is 'Ahoy there!' . So, if you run the script, it will print 'Ahoy there!'

```JavaScript
// Use single (or double) quotes to make a string.
var toPrint = 'Ahoy there!';
// Use parentheses to pass arguments to functions.
print(toPrint);
```
 
6. You may have noticed that we used a keyword "var" at the begining of the script. "var" is always used when defining a variable. The name "toPrint" that we gave to the variable can be anything we want. Try changing the name of the variable and run the script. Try modifying the script to print something else. 

7. Variables can also store numbers. We define the number variables using the same way we just dont need to put numbers in quotes. 

```JavaScript
// Store a number in a variable.
var height = 165;
print('I am ', height, 'cm tall');
```

8. Try modifying the the variable name "height" as well as the variable value to print your height.

Self assessment question: Write a script that prints a persons age. Use two variables to store the persons name and the age. 

---------

## 4. Getting started with Sentinel-2 satellite 

Now you are ready to get started with images and remote sensing. 
1. 
1. Navigate to Darwin and zoom in using the mouse wheel.

![Figure 2. Zoom to Darwin](Prac1/navigate2darwin.png)


2. If the script area is not clear, clear the script workspace by selecting "Clear script" from the Reset button dropdown menu.

![Figure 3. Clear script](Prac1/clearscript.png)

3. Search for "elevation" or "SRTM" and click on the "NASA SRTM Digital Elevation 30m" result to show the dataset description.

![Figure 4. Search for elevation data](Prac1/searchelevation.png)

4. View the information on the dataset - look under "description" and "bands". Once you are happy, click on "Import", which moves the variable to the Imports section at the top of your script.

![Figure 4. View elevation datasource and import](Prac1/viewinfo.png)

Question: How many bands do this data have and whats the spatial resolution?

5. Rename the default variable name "image" to anything you like. Here we will rename it to "theSRTM".

![Figure 5. Rename image](Prac1/rename.png)

6. Print/add the image object to the console by copying the script below into the code editor, and click "run" :

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

## 6. Complete script
```JavaScript
// The SRTM dataset
var theSRTM = ee.Image("USGS/SRTMGL1_003");

// Print data details to console
print(theSRTM);

// Add the SRTM data to the interactive map
Map.addLayer(theSRTM);

// Add the data again, but with restricted value ranges for better visualisation
Map.addLayer(theSRTM, {min: 0, max: 300});

// Add the data again, with value ranges, and a useful title for teh Layer tab
Map.addLayer(theSRTM, {min: 0, max: 300}, 'Elevation above sea level');

// Adding color scale to the elevation data
Map.addLayer(theSRTM, {min: 0, max: 300, palette: ['blue', 'yellow', 'red']}, 'Elevation above sea level');

// Create hillshade and map it
var hillshade = ee.Terrain.hillshade(theSRTM);
Map.addLayer(hillshade, {min: 150, max:255}, 'Hillshade');

// Create terrain slope and map it
var slope = ee.Terrain.slope(theSRTM);
Map.addLayer(slope, {min: 0, max: 20}, 'Slope');
```

-------
### Thank you

I hope you found that useful. A recorded video of this tutorial can be found on my YouTube Channel's [Introduction to Remote Sensing of the Environment Playlist](https://www.youtube.com/playlist?list=PLf6lu3bePWHDi3-lrSqiyInMGQXM34TSV).

#### Kind regards, Shaun R Levick (edit Deepak Gautam)
------


### The end