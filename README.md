# Working with web maps 

CSV to Web Map...Easy Right?

Note: this tutorial is still a work in progress. this is a rough draft!

### Slides
http://slides.com/kimph/deck-4#/



### Learning Objectives
* Manipulate tabular data progammatically to extract geonames and create location-based data
* Understand and apply the basic concepts of web mapping to design your own web map

### Getting Started

Setup a working environment - create a directory to work from
Download scripts from github if you want to follow along https://github.com/kimpham54/proghist-mappingAPI

We're going to take a plain csv file and plug it into a web map! 
The original csv is here: https://github.com/Robinlovelace/Creating-maps-in-R/blob/master/data/census-historic-population-borough.csv
http://data.london.gov.uk/dataset/historic-census-population

### Getting Data: Download the csv
1. To download it quickly you can do it here: https://docs.google.com/spreadsheets/d/1GN0DuxNn7xpkGNnFDWP4-5mdYs1XJqoSt1G2fDwu1SA/edit#gid=1761435061
2. Census with country https://docs.google.com/spreadsheets/d/1Zune3eb8zH5KKGmWpGDqY9jP-DMxHAa4xIDUYzhBybo/edit#gid=1761435061

If you're following along in Github: https://github.com/kimpham54/proghist-mappingAPI/tree/master/step1-csv

### Geocode the placenames: in the CSV using Geopy, Pandas
You can make a little script to geocode placenames

Lets geocode the placenames on the CSV.  You can do this by using python.  Geopy is a python library that gives you access to the various geocoding APIs.  APIs include: geopy makes it easy for Python developers to locate the coordinates of addresses, cities, countries, and landmarks across the globe using third-party geocoders and other data sources.

geopy includes geocoder classes for the OpenStreetMap Nominatim, ESRI ArcGIS, Google Geocoding API (V3), Baidu Maps, Bing Maps API, Yahoo! PlaceFinder, Yandex, IGN France, GeoNames, NaviData, OpenMapQuest, What3Words, OpenCage, SmartyStreets, geocoder.us, and GeocodeFarm geocoder services. 

explain pandas
pandas "data frame" similar to R
http://pandas.pydata.org/pandas-docs/stable/dsintro.html#dataframe

**Notes:**
Nominatim is OSM, open source and SLOW
GoogleV3 is Google's API.  fast
Don't run it too many times because you get a timeout error, like this if you use the googlev3:
```bash
'The given key has gone over the requests limit in the 24'
geopy.exc.GeocoderQuotaExceeded: The given key has gone over the requests limit in the 24 hour period or has submitted too many requests in too short a period of time.

```
https://developers.google.com/maps/documentation/geocoding/intro#Limits
http://stackoverflow.com/questions/21646676/geopy-geocoderquotaexceeded-error
http://wiki.openstreetmap.org/wiki/Nominatim_usage_policy

https://github.com/geopy/geopy


Pandas is a python data analysis library that can be used to manipulate csv and other files.  It share some functionality with R - using data frames, able to select, plot, index and analyse data.



```bash
pip install geopy
pip install pandas
```

The following script was made using the python programming language

```python

import os, csv, sys, geopy
import pandas
from geopy.geocoders import Nominatim, GoogleV3
# geopy 1.10.0, pandas 0.16.2

def main():
	io = pandas.read_csv('onerow3.csv', index_col=False, header=0, sep=",")
	geolocator = Nominatim()
	# geolocator = GoogleV3()
	io['latitude'] = io['Area_Name'].apply(geolocator.geocode).apply(lambda x: (x.latitude))
	io['longitude'] = io['Area_Name'].apply(geolocator.geocode).apply(lambda x: (x.longitude))
	io.to_csv('geocoding-output.csv')

if __name__ == '__main__':
  main()

```

If you're following along in Github: https://github.com/kimpham54/proghist-mappingAPI/tree/master/step2-geocode

### Making GeoJSON

There are a couple ways to make GeoJSON

1. UI tool http://geojson.io (highly recommended) or http://www.convertcsv.com/csv-to-geojson.htm. update: the supremely easy way of creating geojson is to click and drag your csv into geojson.io http://www.nypl.org/blog/2015/01/05/web-maps-primer?utm_campaign=SocialFlow&utm_source=facebook.com&utm_medium=referral

2. or use ogr2ogr http://gis.stackexchange.com/questions/140219/what-are-some-ways-to-convert-a-csv-file-to-geojson-while-preserving-data-types

- install gdal http://www.kyngchaos.com/software/frameworks, which comes with ogr2ogr
- explain what is GDAL?  commonly used in GIS, for writing python scripts and automating processes.  you can for instance batch convert 500 csv files into GEOJSON if you wanted to

run this in the CLI


```bash
export PATH=/Library/Frameworks/GDAL.framework/Programs:$PATH

```

Create a VRT file.  For more documentation: http://www.gdal.org/drv_vrt.html.  Make sure your layername, filename, srcdatasource all have the same name.  Indicate all properties/attributes you want to have in your geojson. 

- talk about how geojson is not like json.  json is a data format, geojson is a json specification with geoproperties.  that's why it's not so straightforward to convert csv to geojson 


```xml
<OGRVRTDataSource>
    <OGRVRTLayer name="census_geocoded">
        <SrcDataSource>census_geocoded.csv</SrcDataSource>
        <GeometryType>wkbPoint</GeometryType>
        <LayerSRS>WGS84</LayerSRS>
        <GeometryField encoding="PointFromColumns" x="longitude" y="latitude"/>
        <Field name="ID" src="ID" type="String"/>
        <Field name="Area_Name" src="Area_Name" type="String"/>
        <Field name="Area_Code" src="Area_Code" type="String"/>
        <Field name="Pop_1801" src="Pop_1801" type="Integer"/>
		<Field name="Pop_1811" src="Pop_1811" type="Integer"/>
		<Field name="Pop_1821" src="Pop_1821" type="Integer"/>
		<Field name="Pop_1831" src="Pop_1831" type="Integer"/>
		<Field name="Pop_1841" src="Pop_1841" type="Integer"/>
		<Field name="Pop_1851" src="Pop_1851" type="Integer"/>
		<Field name="Pop_1861" src="Pop_1861" type="Integer"/>
		<Field name="Pop_1871" src="Pop_1871" type="Integer"/>
		<Field name="Pop_1881" src="Pop_1881" type="Integer"/>
		<Field name="Pop_1891" src="Pop_1891" type="Integer"/>
		<Field name="Pop_1901" src="Pop_1901" type="Integer"/>
		<Field name="Pop_1911" src="Pop_1911" type="Integer"/>
		<Field name="Pop_1921" src="Pop_1921" type="Integer"/>
		<Field name="Pop_1931" src="Pop_1931" type="Integer"/>
		<Field name="Pop_1939" src="Pop_1939" type="Integer"/>
		<Field name="Pop_1951" src="Pop_1951" type="Integer"/>
		<Field name="Pop_1961" src="Pop_1961" type="Integer"/>
		<Field name="Pop_1971" src="Pop_1971" type="Integer"/>
		<Field name="Pop_1981" src="Pop_1981" type="Integer"/>
		<Field name="Pop_1991" src="Pop_1991" type="Integer"/>
		<Field name="Pop_2001" src="Pop_2001" type="Integer"/>
    </OGRVRTLayer>
</OGRVRTDataSource>

```

### Run the command to output your geojson

```bash
ogr2ogr -f GeoJSON output.geojson census_geocoded.vrt
```

Your geoJSON should look something like this:
(this is only the head of the data file)

```json
{
"type": "FeatureCollection",
"crs": { "type": "name", "properties": { "name": "urn:ogc:def:crs:OGC:1.3:CRS84" } },
                                                                                
"features": [
{ "type": "Feature", "properties": { "ID": "1000", "Area_Name": "City of London", "Area_Code": "00AA", "Pop_1801": 129000, "Pop_1811": 121000, "Pop_1821": 125000, "Pop_1831": 123000, "Pop_1841": 124000, "Pop_1851": 128000, "Pop_1861": 112000, "Pop_1871": 75000, "Pop_1881": 51000, "Pop_1891": 38000, "Pop_1901": 27000, "Pop_1911": 20000, "Pop_1921": 14000, "Pop_1931": 11000, "Pop_1939": 9000, "Pop_1951": 5000, "Pop_1961": 4767, "Pop_1971": 4000, "Pop_1981": 5864, "Pop_1991": 4230, "Pop_2001": 7181 }, "geometry": { "type": "Point", "coordinates": [ -0.1277583, 51.5073509 ] } },
{ "type": "Feature", "properties": { "ID": "1001", "Area_Name": "Barking and Dagenham", "Area_Code": "00AB", "Pop_1801": 3000, "Pop_1811": 4000, "Pop_1821": 5000, "Pop_1831": 6000, "Pop_1841": 7000, "Pop_1851": 8000, "Pop_1861": 8000, "Pop_1871": 10000, "Pop_1881": 13000, "Pop_1891": 19000, "Pop_1901": 27000, "Pop_1911": 39000, "Pop_1921": 44000, "Pop_1931": 138000, "Pop_1939": 184000, "Pop_1951": 189000, "Pop_1961": 177092, "Pop_1971": 161000, "Pop_1981": 149786, "Pop_1991": 140728, "Pop_2001": 163944 }, "geometry": { "type": "Point", "coordinates": [ 0.1293497, 51.5464828 ] } },
```

definitely validate/test it out using http://geojson.io.  It's a useful tool!

If you're following along in Github: https://github.com/kimpham54/proghist-mappingAPI/tree/master/step3-vrt


### You finally have GeoJSON.  Lets put it in a map!

### link to this example on google site

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Data Layer: Styling</title>
    <meta name="viewport" content="initial-scale=1.0">
    <meta charset="utf-8">
    <style>
      html, body {
        height: 100%;
        margin: 0;
        padding: 0;
      }
      #map {
        height: 100%;
      }
    </style>
  </head>
  <body>
    <div id="map"></div>
    <script>

var map;
function initMap() {
  map = new google.maps.Map(document.getElementById('map'), {
    zoom: 4,
    center: {lat: -0.1, lng: 51}
  });


  map.data.loadGeoJson('census.geojson');

// Set the stroke width, and fill color for each polygon
  map.data.setStyle({
    fillColor: 'green',
    strokeWeight: 1
  });
  // [END snippet-style]
}

    </script>
    <script async defer
        src="https://maps.googleapis.com/maps/api/js?signed_in=true&callback=initMap"></script>
  </body>
</html>
```

run 
```
python -m SimpleHTTPServer
```
you may need to adjust the paths or change the source file if your html file isn't in the same directory as your data file
access at localhost:8000, navigate to your html file and you'll see your map

if you're diehard open source, use 

### link to this example on leaflet site

```html
<!DOCTYPE html>
<head>
<link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet-0.6.4/leaflet.css" />
<!--[if lte IE 8]>
    <link rel="stylesheet" href="http://cdn.leafletjs.com/leaflet-0.6.4/leaflet.ie.css" />
<![endif]-->
 
<script src="http://cdn.leafletjs.com/leaflet-0.6.4/leaflet.js"></script>
<script src="output-weirdness2.js" type="text/javascript"></script>
<style>
#map {
	width:960px;
	height:500px;
}
</style>
</head>
 
<body>
	<div id="map"></div>
<script>
window.onload = function () {
 	var map = L.map('map').setView([0.0,-10.0], 2);
 	
	L.tileLayer('http://{s}.tile.osm.org/{z}/{x}/{y}.png', {
		attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
	}).addTo(map);

	var countryStyle = {
		'color': "#000",
		'weight': 2,
		'opacity': 0.6
	};
	
	function popup(feature, layer) {
		if (feature.properties && feature.properties.name) {
			layer.bindPopup(feature.properties.name);
		}
	}
	
	L.geoJson(countries, {
		onEachFeature: popup,
		style: countryStyle
	}).addTo(map);
};
</script>
</body>
</html>

```

you'll need to manipulate the geojson and turn it into a js data file.  add a "var countries = " to the beginning of the file

```js
var countries = {
"type": "FeatureCollection",
"crs": { "type": "name", "properties": { "name": "urn:ogc:def:crs:OGC:1.3:CRS84" } },
                                                                                
"features": [
{ "type": "Feature", "properties": { "ID": "1000", "Area_Name": "City of London", "Area_Code": "00AA", "Pop_1801": 129000, "Pop_1811": 121000, "Pop_1821": 125000, "Pop_1831": 123000, "Pop_1841": 124000, "Pop_1851": 128000, "Pop_1861": 112000, "Pop_1871": 75000, "Pop_1881": 51000, "Pop_1891": 38000, "Pop_1901": 27000, "Pop_1911": 20000, "Pop_1921": 14000, "Pop_1931": 11000, "Pop_1939": 9000, "Pop_1951": 5000, "Pop_1961": 4767, "Pop_1971": 4000, "Pop_1981": 5864, "Pop_1991": 4230, "Pop_2001": 7181 }, "geometry": { "type": "Point", "coordinates": [ -0.1277583, 51.5073509 ] } },
{ "type": "Feature", "properties": { "ID": "1001", "Area_Name": "Barking and Dagenham", "Area_Code": "00AB", "Pop_1801": 3000, "Pop_1811": 4000, "Pop_1821": 5000, "Pop_1831": 6000, "Pop_1841": 7000, "Pop_1851": 8000, "Pop_1861": 8000, "Pop_1871": 10000, "Pop_1881": 13000, "Pop_1891": 19000, "Pop_1901": 27000, "Pop_1911": 39000, "Pop_1921": 44000, "Pop_1931": 138000, "Pop_1939": 184000, "Pop_1951": 189000, "Pop_1961": 177092, "Pop_1971": 161000, "Pop_1981": 149786, "Pop_1991": 140728, "Pop_2001": 163944 }, "geometry": { "type": "Point", "coordinates": [ 0.1293497, 51.5464828 ] } },

```

If you're following along in Github: https://github.com/kimpham54/proghist-mappingAPI/tree/master/step4-webmap

### OK WHAT did I just make?

You made a web map!  Web maps use map tiles, which are pixel based images (rasters) of maps that contain geographical data. This mean that each pixel of a map tile has been georeferenced, or assigned a coordinate based on the location that they represent.  When you zoom in and out of a web map, you are getting a whole new set of tiles to display at each zoom level.

Web maps typically display data using the GeoJSON data standard


EXCEPTionS
on a real-world location on that map.
what3words
not always real world based coordinates

https://i-msdn.sec.s-msft.com/dynimg/IC96238.jpg



Introduce the following concepts of web mapping:

* tiles
* vector/raster
* layers
* source
* coordinates
* map states
* plugins
* mashups - stamen, mapbox, cartodb
* controls
* events
http://leafletjs.com/reference.html



### Next part of the lesson

Mapbox, Cartodb, stamen, so many other plugins you can use with leaflet
popup of info for points
time based data
county boundaries https://github.com/martinjc/UK-GeoJSON, https://gist.github.com/kimpham54/2ecf1ad08de64c2d6a8e

