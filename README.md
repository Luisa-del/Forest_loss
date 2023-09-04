# Google Earth Engine (GEE) App for forest loss statistics and Forest Continuity Index 

## Overview
* Workflow authors: Hyeonmin Kang (University of Würzburg), Cornelia Zygar (University of Würzburg)
* Manual authors: Hyeonmin Kang (University of Würzburg), Jürgen Niedballa (Leibniz-IZW)
* Contact: niedballa@izw-berlin.de
* Date: 2022-12-12
* Data source: Hansen Global Forest Change v1.9 (2000-2021)
* Spatial extent: global, with preset for Viet Nam
* Temporal extent: 2000-2021
* Spatial resolution: 30m
* Workflow requirements:
  * Google Earth Engine Account
  * for data visualization and tiling large rasters: QGIS
  * for MSPA calculation: GuidosToolbox (LINK), or MSPA plugin for QGIS (download) 

![](".png")

This document provides a tutorial for a workflow for calculating forest loss statistics from 2000-2022 in any region of the world. It furthermore allows users to download spatial raster data about forest cover and forest loss, provides input for Morphological Spatial Pattern Analysis (MSPA), and uses the MSPA data for calculating Forest Continuity Index (FCI) and other derived metrics for user-defined areas of interest. 

All analyses are based on the Hansen Global Forest Change dataset.

![](".png")

1. [How-to-open GEE app code](#1-How-to-open-GEE-app-code)
2. [Forest loss statistic](#2-forest-loss-statistic)
3. [Morphological Spatial Pattern Analysis (MSPA)](#3-morphological-spatial-pattern-analysis-MSPA)
4. [Forest continuity index (FCI)](#4-forest-continuity-index-FCI)
5. [Background Information and GEE guides](#5-Background-Information-and-GEE-guides)


![](".png")

# 1. How-to-open GEE app code

1. Follow [this link](https://code.earthengine.google.com/9765a49494476e8be1bd0e990297c680) and open the application in Google Earth Engine's code editor mode. In case you are not already logged in to your Google account, you will be asked to log in or set up an account before you are directed to the application interface
2. Click “Run” button. The app interface will appear on the right side of the screen.

*Figure: Start page of GEE-PICX web application.*
<img src="https://github.com/Luisa-del/Forest_loss/blob/main/img/Picture1.png">

Link to GEE App ![](https://introgee-met3.users.earthengine.app/view/forest-cover-loss-vietnam) -> not working

Link to GEE source code ![](https://code.earthengine.google.com/?accept_repo=users/IntroGEE_MET3/WWF_FCI) (can also be used to run the app) -> not working


![](".png")

## 2. Forest loss statistic

1. Select Minimum Tree Cover Density (MTCD). MTCD used for delineating forest in the year 2000. Specifically, it is the tree canopy cover (defined as canopy closure for all vegetation taller than 5m in height) in the year 2000. The higher the MTCD value, the stricter the definition of forest and less areas considered as forest in 2000.

<img src="https://github.com/Luisa-del/Forest_loss/blob/main/img/Picture2.png">

2. Define the area of interest (AOI). Either use a province of Vietnam or your own asset. Provinces are selected under “Select a province in Vietnam”, assets are defined under “Or use your own asset as aoi”. If using an asset, it must be a polygon spatial dataset (e.g. protected area polygons) with a column defining the names of the polygons. The asset can contain multiple polygons, even overlapping.

<img src="https://github.com/Luisa-del/Forest_loss/blob/main/img/Picture3.png">

3. Under “Click on the feature you want to display”, you can click four buttons to show deforestation statistics for the selected AOI (graphs and tables).

<img src="https://github.com/Luisa-del/Forest_loss/blob/main/img/Picture4.png">

4. Click “get URL to export all the features” button to download deforestation statistics for all years and features in your AOI. After clicking, a “Download in csv format” button will appear. 

<img src="https://github.com/Luisa-del/Forest_loss/blob/main/img/Picture5.png">

5. To download yearly rasters of forest loss, click “Export forest loss raster to Google Drive”. To download yearly remaining forest rasters, click “Export remaining forest raster to Google Drive”. You can download rasters by clicking “Run” on the prepared list of rasters under the “Tasks” tab. The images will be saved in your Google Drive. **Note** that when using an asset, exported rasters will be clipped to the polygon outline. Use the “Draw your AOI” interface to define an area of interest for which to download rasters (without clipping to polygon outlines). 

<img src="https://github.com/Luisa-del/Forest_loss/blob/main/img/Picture6.png">


![](".png")

## 3. Morphological Spatial Pattern Analysis (MSPA)

1. For MSPA, download the remaining forest raster using the buttons with the “... (MSPA)” suffix. This ensures that the rasters are in the correct format for calculating MSPA and then Forest Continuity Index. Click the “Export remaining forest raster (MSPA)” button then (under the “Tasks” tab), click run to save the rasters to your GoogleDrive. From there, download to your local machine. 

<img src="https://github.com/Luisa-del/Forest_loss/blob/main/img/Picture7.png">
<img src="https://github.com/Luisa-del/Forest_loss/blob/main/img/Picture8.png">

2. MSPA can be conducted using GuidosToolbox (GTB) or the QGIS “MSPA” plugin. GTB is more powerful and recommended for larger areas (above ca. 8000x8000 pixels).

3. For smaller images, 
    1. in QGIS, 
        1. click the “Mspa” plugin button,
        2. select the image,
        3. (optionally change MSPA settings),
        4. click Start. 
    2. in GTB,
        1. File > Read image > GeoTiff
        2. Image Analysis > Pattern > Morphological > MSPA
        3. File > Save Image > GeoTiff

4. For larger images
    1. in QGIS,
        1. use “Retile” tool to cut large raster into smaller rasters (due to processing constraints in GTB). Retile Tool settings:
        2. Tile width / height: 8000 pixels
        3. overlap = 1000
        4. Profile: Low compression
        5. Output data type = Byte (very important!)
        6. Output directory: define, don't use temporary folder!
        7. After running the tool, delete the subfolder “1” in the output directory. 
    2. In GTB, batch-process all tiles to calculate MSPA. 
        1. File > Batch Process > Pattern > Morphological > MSPA
        2. select all raster tiles created above.
        3. Output MSPA tiles will be saved in folder “batch_MSPA” in the parent folder of the input image folder. Existing data in that folder will be deleted. 
        4. choose MSPA setting or accept default, and calculate MSPA for all tiles
    3. in QGIS, use “Merge” tool to combine all MSPA tiles into one raster. Settings: 
        1. Check “Grab pseudocolor table from first layer”
        2. Output data type = Byte
        3. Profile = Low compression
        4. Output file: define (with year name).

<img src="https://github.com/Luisa-del/Forest_loss/blob/main/img/Picture9.png">


![](".png")

## 4. Forest continuity index (FCI)

1. Upload the MSPA raster as a Google Earth Engine Asset. Ingestion of the image (after upload) can be time-consuming (about 1 hour for one scene of all of Viet Nam). Multiple rasters can be uploaded / ingested simultaneously.

<img src="https://github.com/Luisa-del/Forest_loss/blob/main/img/Picture10.png">

2. Enter the Image ID of the uploaded image into the blank field “Raster: users/your_username/asset_name” and click Enter. Download buttons for csv tables containing FCI values will appear. Click to download. Ensure you give the year in the file name (year information is not included in the table). 
The FCI information in the downloaded table are separated by features in the custom asset defined above (point 2 under [Forest loss statistics](#2-forest-loss-statistic)).

<img src="https://github.com/Luisa-del/Forest_loss/blob/main/img/Picture11.png">


![](".png")

## 5. Background Information and GEE guides 

* MSPA: [https://forest.jrc.ec.europa.eu/en/activities/lpa/mspa/](https://forest.jrc.ec.europa.eu/en/activities/lpa/mspa/)
* How to use GEE: [https://developers.google.com/earth-engine/guides/getstarted](https://developers.google.com/earth-engine/guides/getstarted)
* How to upload your polygon (shapefile) in GEE: [https://developers.google.com/earthengine/guides/table_upload](https://developers.google.com/earthengine/guides/table_upload)
* How to share the uploaded polygon so others can use: [https://developers.google.com/earth-engine/guides/asset_manager](https://developers.google.com/earth-engine/guides/asset_manager)

