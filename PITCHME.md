---?image=presentation/img/st-b.svg
<h3 style="color:rgba(255,255,255,0.2)">Presentation @</h3>
## boyd.xyz/k
### [repo](https://github.com/boydx/lidar-processing) 

---?image=presentation/img/nmp-uky.png

---?image=presentation/img/pot.jpg&opacity=20
## Boyd Shearer 
### Lecturer, Mapping & GIS
([video](https://newmapsplus.as.uky.edu/video/introduction-new-maps-plus-0))


---?image=https://outrageGIS.com/gorge/animations/sun.gif&opacity=50
# Lidar
### Processing and visualization techniques in 
## ArcGIS Pro

---?image=https://outragegis.com/download/t/grid.gif&opacity=10

---?image=https://outragegis.com/download/t/site.gif&opacity=10


---
## Outline
@ul[squares]
* Project origins
* GOES-16
* An archive of imagery?
* Demo live examples (fearlessly?)
@ulend

---?image=https://www.outragegis.com/weather/img/animation/190710/PurchaseKnob-small.gif&opacity=20
## Origins
@ul[squares]
* Collect weather imagery
* Webcams and satellites
* Look at sunsets/sunrises on demand
@ulend

---?image=https://outragegis.com/weather/img/animation/170201/LookRock-small.gif&opacity=20
# 2007
@ul[squares]
* Year of the webcam
* Collect images every 15 minutes
* Give web page access
@ulend

---?image=presentation/img/p006.jpg

---?image=presentation/img/p006.jpg&opacity=25
## Great Smoky Mountain National Park
[weather station](https://www.outragegis.com/weather/grsm/)

---?image=https://i1.wp.com/www.outragegis.com/weather/090309/LookRock-090309-moonrise.jpg&opacity=30
## Tech stack
@ul[squares]
* Ubuntu Linux
* CLI text utilities, [grep](https://www.pcre.org/original/doc/html/pcregrep.html), [sed](https://www.gnu.org/software/sed/manual/sed.html), etc.
* [Imagemagick](https://www.imagemagick.org/)
* task scheduler [cron](https://en.wikipedia.org/wiki/Cron)
* Brute force scraping
@ulend

---
## Brief example
@[2]
@[3]
@[4]
@[5]
```bash
#!/bin/bash
rm -f *.jpg 
date +%l:%M%P\ %a,\ %b\ %d,\ %Y > date.txt
wget http://www.nature.nps.gov/air/webcams/parks/grsmcam/grsm.jpg
montage -geometry +0+0 -background white -label "@date.txt" grsm.jpg LookRock.jpg
```

---?image=https://www.outragegis.com/weather/img/LookRock.jpg&size=contain

---?image=https://www.outragegis.com/weather/img/LookRock.jpg&opacity=30&size=contain
# Yesterday 
## in the Great Smokies

---
## Date stamp
@[2]
@[3]
```bash
#!/bin/bash
t=$(date +%H%M) # 24-hour time with minutes, e.g., 1530
cp LookRock.jpg yesterday/LookRock-$t.jpg
```

---
## Make yesterday
@[2]
@[3]
@[4]
@[5]
@[6-7]
```bash
#!/bin/bash
x=$(ls -1) #list one file per line
convert -delay 50 $x -loop 0 LookRock.gif
date +%0y%m%d > ../date.txt # 190215
mkdir ../$(cat date.txt)
cp -r ../yesterday/ ../$(cat date.txt)/
```

---
<iframe width="100%" height="500px" src="https://www.outragegis.com/weather/img/animation/yesterday"><iframe>




---?image=presentation/img/p007.jpg&size=contain

---?image=presentation/img/p007.jpg&size=contain&opacity=25
## Archive
@ul[squares]
* Keep three years online
* One year approximately 8 GB
* [yesterdays archived](https://www.outragegis.com/weather/img/animation/)
* ~775 (x3) sunsets (and sunrises)
@ulend


---
## Problems?
@ul[squares]
* HTML scraping is an alley fight 
* Need a new satellite
@ulend

---
```bash
# Brute force! Ridiculous but works.
curl https://nws.weather.gov/forecast > Smokies.txt
pcregrep -M -A 90 "<style>" Smokies.txt | sed 's_<html><head>__g' | sed 's_font-family: Arial !important;__g' | sed 's_<img src="/images/wtf/12.gif" border=0 height=35 width=30 alt=Print>__g' | sed 's_<img src="_<img src="http://forecast.weather.gov/_g' | sed 's_<a href="showsigwx_<a href="http://forecast.weather.gov/showsigwx_g' | sed 's_table width="800"_table width="100%"_g' | sed 's_<hr><br>__g' | sed 's_<br><br><br><br><br>_<br>_g' > SmokiesForecast.txt
# Changed three times in 10 years
# 😘 Dark Sky API
```

---?image=https://www.outragegis.com/weather/img/cuga-vis.jpg

---?image=https://www.outragegis.com/weather/img/cuga-vis.jpg&opacity=25
## Not detailed enough
### Is there a better public source?

---?image=https://www.outragegis.com/weather/img/animation/180621/PurchaseKnob.gif&opacity=20
# 2017
@ul[squares]
* Year of the satellite
* GOES-R => GOES-16 => GOES East
* Live feed and archive [on AWS](https://registry.opendata.aws/noaa-goes/) 
@ulend


---?image=presentation/img/20190441300_GOES16-ABI-FD-GEOCOLOR-1808x1808.jpg&size=contain

---?image=presentation/img/20190441300_GOES16-ABI-FD-GEOCOLOR-1808x1808.jpg&size=contain
@snap[midpoint]
# ❤️

---?image=presentation/img/20190441300_GOES16-ABI-FD-GEOCOLOR-1808x1808.jpg&size=contain&opacity=30
## GOES East
@ul[squares]
* 16 spectral bands
* Red @ 0.31 mi per pixel [download](https://outrageGIS.com/weather/goes16/C02_F_19021414.jpg)
* Veggie @ 0.62 mi per pixel
* Blue @ 0.62 mi per pixel
* Not RGB
@ulend

---?image=https://www.nesdis.noaa.gov/sites/default/files/goes_west_goes_east_fleet.png&size=contain


---?image=presentation/img/p009.jpg&opacity=30
## Kentucky's view
@ul[squares]
* [Gray](https://www.outragegis.com/weather/goes16/gray.jpg) (2 MB)
* [Pseudo true-color](https://www.outragegis.com/weather/goes16/rgb.jpg) (500 KB)
* Processed every 15 minutes
@ulend

---?image=https://www.outragegis.com/weather/goes16/rgb.jpg&opacity=20
## Additional tech
@ul[squares]
* [GDAL](https://www.gdal.org)
* [AWS CLI](https://aws.amazon.com/cli/)
* [Miniconda](https://docs.conda.io/en/latest/miniconda.html) for Python and conda package manager
@ulend

<!-- aws s3 --no-sign-request ls --recursive noaa-goes16/ABI-L1b-RadF/2019/045/17 -->


---?image=presentation/img/rgb.jpg&opacity=20
## Conclusion on Kentucky's view
@ul[squares]
* [Gray](https://www.outragegis.com/weather/goes16/temp/gray.jpg) & [Pseudo true-color](https://www.outragegis.com/weather/goes16/temp/rgb.jpg)
* Computationally intense (mostly in conversion to TIFF)
* Using only portion of data
@ulend

---?image=https://www.outragegis.com/weather/img/animation/180616/PurchaseKnob-small.gif&opacity=20
## Opportunity
@ul[squares]
* Create a raster tileset for contiguous states
* Wrap processes in Python script – no scraping
* Pause at time of Kentucky's sunset
@ulend



---?image=https://www.outragegis.com/weather/img/animation/180616/GrsmVis-large.gif&opacity=20
# Demo?

---
# Python

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
---?image=presentation/img/p011.jpg&opacity=20
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

---
<a data-flickr-embed="true" href="https://www.flickr.com/photos/28640579@N02/46863938895/in/album-72157668647475382/" title="Twin Knobs Recreation Area"><img src="https://live.staticflickr.com/31337/46863938895_adf2708490_h.jpg" width="1600" height="900" alt="Twin Knobs Recreation Area"></a><script async src="//embedr.flickr.com/assets/client-code.js" charset="utf-8"></script>

---?image=https://www.outragegis.com/weather/img/NewGap.jpg&opacity=80
### Thank you!
