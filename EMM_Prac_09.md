# Environmental Monitoring and Modelling (ENV306/506)

## Prac09 - Working with vector datasets

### Acknowledgments 

- Google Earth Engine Team
- [GEARS Lab](https://www.gears-lab.com/emm_lab_9/)

### Objective

The objective of this lab is to familiarise yourself with Features and FeatureCollections in Earth Engine. Vector/table datasets are useful for stratifying study sites, and for defining regions of interest (roi) for reducing and clipping images and image collections. Remember how we used roi (point, polygon, and complex) in previous pracs, all ot them are vector dataset.

---------------------------------------------------
## 1. Visualising features and feature collection.
1. Open the Google Earth Engine environment by going to [https://code.earthengine.google.com] in the Chrome browser.

2. Do you know the difference between vecotr and raster dataset? Do a google search and have a read about it. In a nutshell, raster dataset is made up of pixels lots of pixels such as our satellite images. Vector dataset is made up of geometries e.g. points, lines, polygons such as our roi. 

3. In GEE, we have used geometry (point, polygon etc.) to creat rois. These are the fundamental vector data type in GEE. A geometry object can also store some attribute properties forming a whats called "Feature". A set of features can be grouped together in a "FeatureCollection". In this exercise, we will load feature collections that are available to us, similar to the loading of satellite image collection.

4. Search for RESOLVE ecoregions dataset. Import the RESOLVE Ecoregions 2017 dataset and rename the default "table" to "ecoregions". Alternatively, use the script below. 

```JavaScript
// Load a FeatureCollection from a table dataset: 'RESOLVE' ecoregions.
var ecoregions = ee.FeatureCollection('RESOLVE/ECOREGIONS/2017');
```

5. Please read through the description of this dataset. This dataset is not intuitive to understand as you would a satellite image. The RESOLVE Ecoregions 2017 dataset provides a depiction of the 846 terrestrial ecoregions (each coded with unique ID "ECO_ID") that represent our living planet. Ecoregions, in the simplest definition, are ecosystems of regional extent. Specifically, ecoregions represent distinct assemblages of biodiversity ― all taxa, not just vegetation ― whose boundaries include the space required to sustain ecological processes. 

5. Visualisation of feature collection is done using Map.addLayer command - similar to how we visualise the raster image. You can do a quick and easy default display as below. The default visualization will display the vectors with solid black lines and semi-opaque black fill.

```JavaScript
// Try the default display
Map.addLayer(ecoregions, {}, 'default display');

```

![Figure 1. Reference EVI Northern Australia](Prac08/default.png)

6. What you see is global ecoregions displayed with solid black lines and semi-opaque black fill. Zoom in, have a look around and explore. You can use the inspector tab to click and explore on different ecoregions.

![Figure 1. Reference EVI Northern Australia](Prac08/inspector.png)

7. You can control the color by defining the color. Note that in the raster display we used "palette"

```JavaScript
// add a custom color
Map.addLayer(ecoregions, {color: 'red'}, 'red colour display');
```
![Figure 1. Reference EVI Northern Australia](Prac08/redcolor.png)


## 2. Controlling the boundary lines

1. For additional display options, use featureCollection.draw(). Specifically, parameter strokeWidth controls the size of the boundary lines in the rendered FeatureCollection. (similarly pointRadius controls the size of points - not applicable here)

```JavaScript
// Use featureCollection.draw() to display in custom colours and linewidth 
Map.addLayer(ecoregions.draw({color: 'green', strokeWidth: 5}), {}, 'drawn display');
```
![Figure 1. Reference EVI Northern Australia](Prac08/drawdisplay.png)

2. Note that although you have control the boundary lines of the features, the features are also filled with the same colour.  For more control over how a FetureCollection is displayed, you will need to use ".paint()" command. The above ".draw()" outputs a three-band 8-bit display image, the ".paint()" outputs an image with a specif numeric value painted into it. To paint, you will first need to create an empty image - kind of like grabbing a empty sheet of paper to paint on it. 

```JavaScript
// create an empty image into which to paint the features, cast to a byte.
var emptyImage = ee.Image().byte();
// you can add the empty image to your mapping layer - its just empty
Map.addLayer(emptyImage,{},'empty image');
```

![Figure 1. Reference EVI Northern Australia](Prac08/drawdisplay.png)

3. Now, on the empty image, you can paint all the polygon edges with ths same color and width.

```JavaScript
// Paint the polygon edges onto the empty image
var outlineImage = emptyImage.paint({
  featureCollection: ecoregions, // which feature collection you want to be painted
  strokeWidth: 3 // width of the polygon boundary line
});
```

4. The resulting painted "outlineImage" is an image and can be added to your mapping layer as you would a raster image. 

```JavaScript
// add the above outline image to the mapping layer
Map.addLayer(outlineImage, {palette: 'red'}, 'edges only');
```
![Figure 1. Reference EVI Northern Australia](Prac08/outlineonly.png)

8. There you have it the outline of the ecoregions that you can control the color and the width of the boundary line. However, the edges of the above image shows all the ecoregions and biomes with same color. Now there are lots of ecoregion to assign a unique color. But, what we can do is color the different biome with unique color. Under the "Table Schema" of the dataset tells you that each biome has a unique "BIOME_NUM". We can assign unique color based on the stored "BIOME_NUM" - there are 14 biomes in total, so, 14 colours sounds okay. Similarly, you could also make the width of the boundary edge variable showing for example the "NNH" category. 

```JavaScript
// Repeat the edge painting on the empty image
var uniqueOutlineImage = emptyImage.paint({
  featureCollection: ecoregions, // feature collection to paint
  color: 'BIOME_NUM', // color assigned based on Biome Number
  width: 'NNH' // width assigned based on NNH value
});

// add the unique outline to the mapping layer
Map.addLayer(uniqueOutlineImage, {palette: ['red', 'green', 'blue'], max: 14}, 'unique colour edges');
```
![Figure 1. Reference EVI Northern Australia](Prac08/uniqueoutline.png)

*Question:* This dataset is quite heavy in information. For example, how did I know that BIOME_NUM 4 represents "Temperate broadleaf and mixed forests"? Do your researh and can you find out what does BIOME_NUM 5 represent? -- hint hint dataset description window. 

## 3. Controlling the interior fill

1. Now we have complete control over the boundary lines, we also want to control the interior fill. Controlling interior fill is quite simple. Using same script as above, if the "width" is not provided, the interior of the feature is painted instead of boundary.

```JavaScript
// Paint the interior of the polygons with different colours.
var fillImage = emptyImage.paint({
  featureCollection: ecoregions, // feature collection to paint
  color: 'BIOME_NUM', // color assigned based on Biome Number
}); // no width means paint the interior

//Add the interior paint to mapping layer
Map.addLayer(fillImage, {palette: ['red', 'green', 'blue'], max: 14}, 'fills only');
``` 
![Figure 1. Reference EVI Northern Australia](Prac08/fillonly.png)

2. To render both the interior and edges of the features, you will need to paint the empty image twice: First paint the interior and on top of that, paint the edges. Note below I painted the interior using “BIOME_NUM” and edges using black color. If you paint both the interior and edges using "BIOME_NUM", you cannot distinguish between two adjacent features with same “BIOME_NUM”.

```JavaScript
// Paint both the interior and the edges. First paint the interior using biome number. Then paint the edges using
var doublePaintedImage = emptyImage.paint(ecoregions, 'BIOME_NUM').paint(ecoregions, 0, 1);

// add the interior/exterior painted image to mapping layer
Map.addLayer(doublePaintedImage, {palette: ['black','red', 'green', 'blue'], max: 14}, 'edges and fills');
```
![Figure 1. Reference EVI Northern Australia](Prac08/fillandedge.png)

## 4. Filtering a FeatureCollection

7. Don't forget to save the script before you exit. 

## 5. Ungraded exercise

1. Explore one of the category 5 cyclones in the Australian region [wikipedia link](https://en.wikipedia.org/wiki/List_of_Category_5_Australian_region_severe_tropical_cyclones). What effect would you expect the cyclone to have on the long term EVI trend? For example, I explored the Shoalwater Bay region which was affected by the cyclone Marcia. I saw a sharp drop in the EVI anomaly following the cyclone which also seems to have reversed the previous upward trend in the EVI anomaly. 
2. Think about how you can adapt this approach to other types of data, such as Land Surface Temperature or rainfall for example.


## The complete script

```JavaScript

  
```

-------
### Thank you

I hope you found this prac useful. A recorded video of this prac can be found on your learnline.

#### Kind regards, Deepak Gautam
------
### The end
