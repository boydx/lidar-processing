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
## Python
@[25-30]
@[32-36]
@[38-42]
@[44-48]
@[60-63]
@[78-89]
@[93-108]
@[138-143]
@[147]
@[172]
@[178]
@[202]
```python
# Import ArcGIS package
import arcpy
# Subprocess allows us to issue commands on the command line
import subprocess
# Module to download files with an URL
from urllib.request import urlretrieve
# Zip utlity to extract files
from zipfile import ZipFile
# Utility to compress folder
import shutil
# pandas to view table and possibly analyze data in the future?
import pandas as pd
```

```python
# Directory for project 
# Got to use double backslash because of other modules
out_directory = "x:\\BoydsGIS\\3DScenes\\"

# Project name - creates a folder in project directoru
project = "kamp_cp"

# Name of point layer and buffer distance from point
point_name = "cp_pt"
buffer_distance = 1000

# Output geodatabase name
out_geodb = "workspace.gdb"

# Locations of index grids
las_grid = "x:\\BoydsGIS\\data\\download_grids.gdb\\KY_5k_PointClound_grid"
naip_grid = "x:\\BoydsGIS\\data\\download_grids.gdb\\Kentucky_10K_NAIP"

# NAIP files have prefix
naip_prefix = "ky_2ft_naip_2016_"

# Point the script to the directory with the laszip64.exe
las_tools = ["x:\\BoydsGIS\\data\\lidar\\", "laszip64.exe"]

# Downloads folder
downloads = f'{out_directory}{project}\\downloads\\'
lidar = f'{out_directory}{project}\\lidar\\'
lidar_extract = f'{out_directory}{project}\\lidar_extract\\'
lidar_color = f'{out_directory}{project}\\lidar_color\\'

# LAS dataset name, temp list, and class codes
las_dataset = f'{lidar}{project}.lasd'
las_names = []
```
---?image=presentation/img/p011.jpg&opacity=20
## Current view
@ul[squares]
* [Updated](https://www.outragegis.com/weather/goes16/map) every 15 minutes
* Make GeoTIFFs available
* Pause at time of [Kentucky's sunset](https://www.outragegis.com/weather/goes16/sunset/190214) 💙
@ulend


---?image=https://www.outragegis.com/weather/img/NewGap.jpg&opacity=80
### Thank you!
