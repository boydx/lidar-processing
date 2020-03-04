---?image=https://www.outragegis.com/weather/img/animation/yesterday/LookRock.gif
## Lidar + Python + ArcGIS
### [repo](https://github.com/boydx/lidar-processing) | [contact](https://boydx.github.io)

---?image=presentation/img/pot.jpg&opacity=20
## Boyd Shearer 
### Lecturer, Mapping & GIS
([video](https://newmapsplus.as.uky.edu/video/introduction-new-maps-plus-0))

---?image=presentation/img/nmp-uky.png

---
### Mapping & GIS Minor
@ul[squares]
* GEO 109 - Digital Mapping
* GEO 309 - Intro to GIS
* GEO 409 - Advanced GIS
* GEO 305 - Cartography
* GEO 419 - Remote Sensing
* GEO 509 - Workshop
@ulend

---?image=presentation/img/outragegis.jpg&opacity=30
## Let's Hike!
[outrageGIS.com](https://outrageGIS.com)


---?image=https://outrageGIS.com/gorge/animations/sun.gif&opacity=50
# Lidar
### Processing & visualization with Python and
## ArcGIS Pro

---
# History
First applications with lidar data


---?image=https://live.staticflickr.com/8560/29239711024_0a23da2985_k.jpg&opacity=30
## 2016
@ul[squares]
* Build point cloud inside New Circle in ArcMap
* Extracted portions for student use
* Illumination surface for [fall sunrise](https://flic.kr/p/LxPaoG)
* Elevation profiles of [downtown pedways](https://www.flickr.com/photos/28640579@N02/29138854162/in/album-72157668647475382/)
@ulend

---?image=https://live.staticflickr.com/930/41884170490_c18dec8542_o.jpg&opacity=30
### Trees!
<iframe width="100%" height="500px" src="https://bluegrassland.carto.com/builder/50150569-e092-4c28-a67f-eb4bfbc7a50e/embed"></iframe>
[link](https://bluegrassland.carto.com/builder/50150569-e092-4c28-a67f-eb4bfbc7a50e/embed)


---?image=https://live.staticflickr.com/930/41884170490_c18dec8542_o.jpg&opacity=30
### Trees!
<iframe width="100%" height="500px" src="https://outragegis.com/pointclouds/legacy/"></iframe>
[link](https://outragegis.com/pointclouds/legacy/)
---


# Code    
# [Demo](https://github.com/boydx/lidar-processing)

---
@[1-2]
@[3-4]
@[5-6]
@[7-8]
@[9-10]
@[11-12]
```python
# IMPORT modules
import arcpy
# Subprocess allows us to issue commands on the command line
import subprocess
# Module to download files with an URL
from urllib.request import urlretrieve
# Zip utlity to extract files
from zipfile import ZipFile
# Utility to compress folder
import shutil
# Examine point cloud stats
import pandas as pd
```
---
@[2]
@[3-4]
@[5]
@[6]
```python
# SET custom Variables
out_directory = "x:\\BoydsGIS\\3DScenes\\"
project = "kamp_cp" # Project directory name
point_name = "cp_pt" # Name of point layer
buffer_distance = 1000 # Distance in feet from feature
las_ground = [2] # Codes for elevation model
```

---
## Grid layers
Use these well-attributed [grids](https://github.com/boydx/lidar-processing/blob/master/assets/download_grids.gdb.zip) to find the correct tiles to download.

---
@[2]
@[3-5]
@[6]
@[7-13]
```python
# SET Program Variables
out_geodb = "workspace.gdb"  # Output geodatabase name
las_grid = "x:\\BoydsGIS\\data\\download_grids.gdb\\KY_5k_PointClound_grid"
naip_grid = "x:\\BoydsGIS\\data\\download_grids.gdb\\Kentucky_10K_NAIP"
naip_prefix = "ky_2ft_naip_2016_" # NAIP files have prefix
las_tools = ["x:\\BoydsGIS\\data\\lidar\\", "laszip64.exe"]
# Downloads folder
downloads = f'{out_directory}{project}\\downloads\\'
lidar = f'{out_directory}{project}\\lidar\\'
lidar_extract = f'{out_directory}{project}\\lidar_extract\\'
lidar_color = f'{out_directory}{project}\\lidar_color\\'
# LAS dataset name and location
las_dataset = f'{lidar}{project}.lasd'
```
---
@[2]
@[3-4]
@[5]
@[7]
```python
# Create folders and copy laszip64.exe
folders = [f'{out_directory}{project}', downloads, lidar, lidar_extract, lidar_color]
for folder in folders:
    subprocess.run(f"mkdir {folder}", shell=True)
completed = subprocess.run(f'dir {out_directory}{project}', shell=True, stdout=subprocess.PIPE)
print(completed.stdout.decode('UTF-8'))
completed = subprocess.run(f'copy {las_tools[0]}{las_tools[1]} {downloads}', shell=True, stdout=subprocess.PIPE)
print(completed.stdout.decode('UTF-8'))
```
---
@[2]
@[8]
```python
arcpy.env.overwriteOutput = True # Overwrite it!
arcpy.CreateFileGDB_management(f'{out_directory}{project}', out_geodb)
out_path = f'{out_directory}{project}\\{out_geodb}' 
arcpy.env.workspace = out_path

# Create empty feature layer
spatial_reference = arcpy.Describe(las_grid).spatialReference
arcpy.CreateFeatureclass_management(out_path, point_name, "POINT", "#", "#", "#", spatial_reference)
```
---?image=presentation/img/pro.jpg&opacity=20
## Open ArcGIS Pro
@ul[squares]
* Create a feature that locates AOI
* Point, line, or Polygon
* 🙏💙
@ulend

---
@[2]
@[5]
```python
# Buffer point
arcpy.Buffer_analysis(point_name, f'{point_name}_{buffer_distance}ft', buffer_distance)

# Create a temp layer to find which LAS files to download
arcpy.Intersect_analysis ([f'{point_name}_{buffer_distance}ft', las_grid], "temp")
```
---
@[2]
@[5]
@[6-8]
@[10]
@[12]
```python
# Find URLs and download them and use laszip64.exe to convert 
cursor = arcpy.da.SearchCursor("temp", ['ftppath', 'LASVersion', 'Year'])
i = 0
las_names = []
for row in cursor:
    url = row[0]
    name = url[-12:]
    las_names.append(f'{lidar}{url[-12:-4]}.las')
    print(las_names[i])
    urlretrieve(url, f'{downloads}{name}')
    print(f'{las_tools} -i {downloads}{name} -o {las_names[i]}')
    completed = subprocess.run(f'{las_tools} -i {downloads}{name} -o {las_names[i]}', shell=True, stdout=subprocess.PIPE)
    print(completed.stdout.decode('UTF-8'))
    i += 1
```

---
@[2]
@[4]
@[5-8]
@[9-10]
```python
# Create LAS dataset
arcpy.CreateLasDataset_management (las_names, las_dataset, "#", "#", spatial_reference, True, True)
# Get Stats
arcpy.LasDatasetStatistics_management (las_dataset, "#", f'{out_directory}{project}\\stats.csv', "#", "#", "#")
with open(f'{out_directory}{project}\\stats.csv', encoding='utf-8') as csv:
    reader = pd.read_csv(csv)
    # Create pandas data frame that
    pdData = pd.DataFrame(reader)
# View point cloud stats
pdData[pdData["Category"] == "ClassCodes"]
```

---
@[2]
@[4]
@[5]
@[6]
```python
# default ground classes
las_ground = [2]
# Filter for ground points and create DEM and hillshade
arcpy.MakeLasDatasetLayer_management (las_dataset, f'{lidar}ground', las_ground)
arcpy.LasDatasetToRaster_conversion (f'{lidar}ground', f'{project}_dem_5ft', "#", "#", "#", "#", 5)
arcpy.HillShade_3d(f'{project}_dem_5ft', f'{project}_hillshade', 270, 55)
```

---
@[2-3]
@[5]
@[14-16]
```python
# Create a temp layer to find which NAIP files to download
arcpy.RasterDomain_3d (f'{project}_hillshade', 'domain', 'POLYGON')
arcpy.Intersect_analysis (['domain', naip_grid], "temp")
# Find URLs, download them and extract 
cursor = arcpy.da.SearchCursor("temp", ['ftppath16', 'TileName'])
i = 0
naip_names = []
for row in cursor:
    url = row[0]
    name = row[1]
    naip_names.append(name)
    print(naip_names)
    urlretrieve(url, f'{downloads}{name}.zip')
    with ZipFile(f'{downloads}{name}.zip', 'r') as zip: 
        zip.extractall(f'{downloads}{name}') 
    arcpy.CopyRaster_management (f'{downloads}{name}\\{naip_prefix}{name}.jpg', name)
    i += 1
```

---
@[1-4]
```python
# If multiple NAIPs, then mosaic to new raster and clip
if len(naip_names) > 1:
    arcpy.MosaicToNewRaster_management (naip_names, out_path, "temp", None, "8_BIT_UNSIGNED", None, 3)
    arcpy.Clip_management ('temp', '#', f'{project}_naip', 'domain')
else:
    arcpy.Clip_management (naip_names[0], '#', f'{project}_naip', 'domain')
arcpy.Delete_management (f'{out_directory}{project}\\{out_geodb}\\temp')    
```

---
@[2]
@[3]
```python
# Extract LAS points in buffer and colorize
arcpy.ExtractLas_3d (las_dataset, f'{lidar_extract}', f'{point_name}_{buffer_distance}ft', "#", "#", "_extract", "#", "#", False, f'{lidar_extract}temp.lasd')
arcpy.ColorizeLas_3d (f'{lidar_extract}temp.lasd', f'{project}_naip', 'RED Band_1; GREEN Band_2; BLUE Band_3', lidar_color, "_color", "#",  "#",  "#",  "#", True, f'{lidar_color}{project}_rgb.lasd')
```

---?image=presentation/img/animation.jpg
## Now what?
@ul[squares]
* ### Animation!
* ### YouTube!
@ulend

---?image=https://outrageGIS.com/download/t/cuga.gif

---
<iframe width="100%" height="500" src="https://www.youtube.com/embed/nFV8ftGN0aM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

---
<iframe src="https://player.vimeo.com/video/301603400" width="100%" height="500" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe>

---?image=presentation/img/geo409.jpg
### [Link](https://uky-gis.github.io/maps/rrg-arches/)


---
<iframe src="https://outragegis.com/pointclouds/lex/" width="100%" height="500" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe>


---?image=presentation/img/st-b.svg
<h3 style="color:rgba(255,255,255,0.2)">Presentation @</h3>
## boyd.xyz/k
## Thank you!
### [repo](https://github.com/boydx/lidar-processing) | [contact](https://boydx.github.io)
