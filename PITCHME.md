---?image=presentation/img/st-b.svg
<h3 style="color:rgba(255,255,255,0.2)">Presentation @</h3>
## boyd.xyz/u
### [repo](https://github.com/boydx/weather-stations) 
current [map](https://www.outragegis.com/weather/goes16/map/)

---?image=https://farm6.staticflickr.com/5688/23373057330_7c3e0cba0c_h.jpg&opacity=30
## Weather mapping in 
# HD
### a season of Kentucky sunsets


---?image=presentation/img/p004.jpg&opacity=20
## Open source CLI mapping 

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
###############################################
#  Make RGB GOES 16 tileset every 15 minutes. #
###############################################

###############################################
# LICENSE
# Copyright (C) 2019 Boyd Shearer and Dr. Marcial Garbanzo Salas
# This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.
# This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.
# You should have received a copy of the GNU General Public License along with this program. If not, see http://www.gnu.org/licenses/.
###############################################

###############################################
# AUTHOR
# This program was created at the University of Kentucky and University of Costa Rica (UCR)
###############################################

###############################################
# REQUIREMENTS
# GDAL 2.3.3, released 2018/12/14 or greater
# ImageMagick 6.7.7-10 2018-09-28 Q16 http://www.imagemagick.org or greater
# aws-cli/1.16.81 Python/3.7.1 Linux/3.13.0-37-generic botocore/1.12.71 or greater
###############################################

# Import modules
from datetime import datetime
from astral import Astral
import subprocess
import json
import pytz

# Get current time in Kentucky
datetime.now()
dt = datetime.now()
dt = pytz.timezone('US/Eastern').localize(dt)
print(dt)

# Set location and find local sun times
city_name = 'Louisville'
a = Astral()
a.solar_depression = 'civil'
city = a[city_name]

# Get UTC time for GOES16 harvest
years = datetime.utcnow().strftime("%Y")
days = datetime.utcnow().strftime("%j")
hours = datetime.utcnow().strftime("%H")
minutes = datetime.utcnow().strftime("%M")

# Make label
label = f"{years}_d{days}_h{hours}_m{minutes}"

# Set GEOS 16 projection
proj = "'+proj=geos +lon_0=-75 +h=35786023 +x_0=0 +y_0=0 +ellps=GRS80 +units=m +no_defs  +sweep=x'"

# Set products desired
products = "L1b-RadC"

# Select bands based on time
sun = city.sun(date=datetime.now(), local=True)
if (dt > sun["sunrise"]) and (dt < sun["sunset"]):
    channels = "C01 C02 C03"
    mode = "day"
elif (dt < sun["sunrise"]):
    channels = "C16"
    mode = "night"
elif (dt > sun["sunset"]):
    channels = "C16"
    mode = "night"
else:
    channels = "C16"
    mode = "night"
    
# Check the details!
print(mode) 
print(f"Time is {mode}: {dt}. Downloading {channels}.")

# Clean before running
shellDelete = f"""
echo "Remove past run..."
rm -v *.tif
rm -v *.xml
rm -v FullList.txt
rm -v all-info.txt
rm -v DesiredData.txt
"""

Delete = subprocess.run(shellDelete, shell=True, stdout=subprocess.PIPE)
print(Delete.stdout.decode('UTF-8'))

# Find the NetCDF files
awk_ = "awk '{print $4}'"
shellGetImg = f"""
for PRODUCT in {products}; do
 for YEAR in {years}; do 
  for DAY in {days}; do 
   for HOUR in {hours}; do                         
    aws s3 --no-sign-request ls --recursive noaa-goes16/ABI-$PRODUCT/$YEAR/$DAY/$HOUR | {awk_} >> FullList.txt 
    aws s3 --no-sign-request ls --recursive noaa-goes16/ABI-$PRODUCT/$YEAR/$DAY/$HOUR >> all-info.txt 
   done
  done
  done
done
for CHANNEL in {channels}; do
 grep $CHANNEL FullList.txt | tail -n 1 >> DesiredData.txt
#  aws s3 --no-sign-request ls --recursive noaa-goes16/ABI-L1b-RadC/2019/167/12 | awk '{print $4}' | grep 'C01' | tail -n 1
done
cat DesiredData.txt
"""
GetImg = subprocess.run(shellGetImg, shell=True, stdout=subprocess.PIPE)
print(GetImg.stdout.decode('UTF-8'))

# Downlaod the NetCDF files
shellDownload = f"""
for x in $(cat DesiredData.txt)
do
 FULLNAME=$(echo $x)
 NAME=$(echo $x | cut -d"/" -f5)
 CH=$(echo $NAME | cut -c 19-21)
 echo "************* Downloaded "$NAME": "$CH
 aws s3 --no-sign-request cp s3://noaa-goes16/$FULLNAME ./$CH".nc"
done
"""
Download = subprocess.run(shellDownload, shell=True, stdout=subprocess.PIPE)
print(Download.stdout.decode('UTF-8'))

# Convert to TIFF
for i in channels.split():
    print(f"Translating {i} to GTiff format... ")
    shellTranslate = f"""
    gdal_translate NETCDF:{i}.nc:Rad {i}.tif
    rm {i}.nc
    gdalinfo -stats {i}.tif
    """
    completed = subprocess.run(shellTranslate, shell=True, stdout=subprocess.PIPE)
    print(completed.stdout.decode('UTF-8'))

# Create projected and toned images
for i in channels.split():
    bandinfo = subprocess.run(f"gdalinfo -stats -json {i}.tif", shell=True, stdout=subprocess.PIPE)
    bandmeta = json.loads(bandinfo.stdout.decode('UTF-8'))
    # print(bandmeta)
    hi = bandmeta['bands'][0]['mean'] + (bandmeta['bands'][0]['stdDev']*3)
    lo = bandmeta['bands'][0]['min'] #+ (bandmeta['bands'][0]['stdDev']*0.5)
    print(f"************* For {i} applying minimum: {lo} and maximum: {hi} -- no worries about GDAL errors! *************")
    shellScaleDay = f"""
    gdal_translate -ot Byte -of Gtiff -scale {lo} {hi} 0 255 {i}.tif '_scale_'{i}.tif
    gdalwarp '_scale_'{i}.tif -t_srs EPSG:3857 -s_srs {proj} -r cubic -of Gtiff  '_prj_'{i}.tif
    gdalwarp -cutline us.geojson -crop_to_cutline '_prj_'{i}.tif '_us_'{i}.tif
    gdalwarp -cutline aoi.geojson -crop_to_cutline '_prj_'{i}.tif '_ky_'{i}.tif
    rm '_scale_'{i}.tif; rm '_prj_'{i}.tif #rm {i}.tif; 
    """
    shellScaleNight = f"""
    gdal_translate -ot Byte -of Gtiff -scale {lo} {hi} 255 0 {i}.tif '_scale_'{i}.tif
    gdalwarp '_scale_'{i}.tif -t_srs EPSG:3857 -s_srs {proj} -r cubic -of Gtiff  '_prj_'{i}.tif
    gdalwarp -cutline us.geojson -crop_to_cutline '_prj_'{i}.tif '_us_'{i}.tif
    gdalwarp -cutline aoi.geojson -crop_to_cutline '_prj_'{i}.tif '_ky_'{i}.tif
    rm '_scale_'{i}.tif; rm '_prj_'{i}.tif #rm {i}.tif; 
    """
    if mode == "day":
        Scale = subprocess.run(shellScaleDay, shell=True, stdout=subprocess.PIPE)
    else:
        Scale = subprocess.run(shellScaleNight, shell=True, stdout=subprocess.PIPE)
    
    print(Scale.stdout.decode('UTF-8'))
    
# Make tilesets
bands = channels.split()

ratioColor = '"(0.3*A)+(0.45*B)+(0.25*C)"'

if mode == "day":
    print(f"************* Processing day bands for RGB *************")
    shellMergeDay = f"""
    gdalwarp -ts 5158 3222 -r cubic '_us_'{bands[1]}.tif '_resize_'{bands[1]}.tif
    gdal_calc.py -A '_resize_'{bands[1]}.tif -B '_us_'{bands[2]}.tif -C '_us_'{bands[0]}.tif --outfile=_green_.tif --calc={ratioColor} 
    gdal_merge.py -separate -a_nodata 255 255 255 -of GTiff -o _rgb_.tif --optfile bands.txt
    gdal2tiles.py -p mercator -z 0-8 -w all -r average -a 0.0 _rgb_.tif tiles
    """
    Merge = subprocess.run(shellMergeDay, shell=True, stdout=subprocess.PIPE)
else:
    print(f"************* Processing night band for grayscale *************")
    shellMergeNight = f"""
    gdal2tiles.py -p mercator -z 0-8 -w all -r average -a 0.0 '_us_'{bands[0]}.tif tiles
    """
    Merge = subprocess.run(shellMergeNight, shell=True, stdout=subprocess.PIPE)
    
print(Merge.stdout.decode('UTF-8'))

shellTilesProduction = f"""
echo "Added production tiles."
cp -rf tiles/* ~/www/weather/goes16/tiles/
rm -r tiles/*
"""

TilesProduction = subprocess.run(shellTilesProduction, shell=True, stdout=subprocess.PIPE)
print(TilesProduction.stdout.decode('UTF-8'))

if mode == "day":
    print(f"************* Making C02 high res tileset *************")
    shellTilesGrayProduction = f"""
    gdal2tiles.py -p mercator -z 0-9 -w all -r average -a 0.0 _us_C02.tif tilesgray
    cp -rf tilesgray/* ~/www/weather/goes16/tilesgray/
    rm -r tilesgray/*
    mv -f _rgb_.tif ~/www/weather/goes16/rgb.tif
    mv -f _us_C02.tif ~/www/weather/goes16/gray.tif
    """
    TilesGrayProduction = subprocess.run(shellTilesGrayProduction, shell=True, stdout=subprocess.PIPE)
    print(TilesProduction.stdout.decode('UTF-8'))
```
---?image=presentation/img/p011.jpg&opacity=20
## Current view
@ul[squares]
* [Updated](https://www.outragegis.com/weather/goes16/map) every 15 minutes
* Make GeoTIFFs available
* Pause at time of [Kentucky's sunset](https://www.outragegis.com/weather/goes16/sunset/190214) 💙
@ulend


---?image=https://www.outragegis.com/weather/img/animation/180616/ColdMountain-small.gif&opacity=20
# 2019
@ul[squares]
* One week of tilesets is 8 GB
* Archive not sustainable


---?image=https://www.outragegis.com/weather/img/NewGap.jpg&opacity=80
### Thank you!
