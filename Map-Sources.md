# How tos for using various map sources


## Mapnik (aka Open Street Maps)

Required Java dependencies
 - osmdroid-android

Code Sample:

````
final ITileSource tileSource = TileSourceFactory.MAPNIK;
mMapView.setTileSource(tileSource);
````

## Mapquest Roads

Required Java dependencies
 - osmdroid-android

Code Sample:

````
final ITileSource tileSource = TileSourceFactory.MAPQUESTOSM;
mMapView.setTileSource(tileSource);
````

## Mapquest Aerial

Required Java dependencies
 - osmdroid-android

Code Sample:

````
final ITileSource tileSource = TileSourceFactory.MAPQUESTAERIAL;
mMapView.setTileSource(tileSource);
````

## Cloud made

Required Java dependencies
 - osmdroid-android

Manifest (optional):
Under manifest/application

````
<meta-data android:name="CLOUDMADE_KEY" android:value="YOUR KEY" />
````

Code Sample:

````
CloudmadeUtil.retrieveCloudmadeKey(context);
final ITileSource tileSource = TileSourceFactory.CLOUDMADESTANDARDTILES;
mMapView.setTileSource(tileSource);
````


## Map Box

Required Java dependencies
 - osmdroid-android

Manifest (optional):
Under manifest/application

````
<meta-data android:name="MAPBOX_MAPID" android:value="YOUR KEY" />
<meta-data android:name="ACCESS_TOKEN" android:value="YOUR TOKEN" />
````

Code Sample:

````
MapBoxTileSource.retrieveAccessToken(context);
MapBoxTileSource.retrieveMapBoxMapId(context);
final MapBoxTileSource tileSource = new MapBoxTileSource();
mMapView.setTileSource(tileSource);
````
## USGS Topo

Required Java dependencies
 - osmdroid-android

Code Sample:

````
final ITileSource tileSource = TileSourceFactory.USGS_TOPO;
mMapView.setTileSource(tileSource);
````

## Bing Maps

Required Java dependencies
 - osmdroid-android
 - osmdroid-third-party

Manifest (optional):
Under manifest/application

````
<meta-data android:name="BING_KEY" android:value="YOUR KEY" />
````

Code Sample:

````
//load from manifest
org.osmdroid.tileprovider.tilesource.bing.BingMapTileSource.retrieveBingKey(this);
//or load programmatically
org.osmdroid.tileprovider.tilesource.bing.BingMapTileSource.retrieveBingKey(this);
org.osmdroid.tileprovider.tilesource.bing.BingMapTileSource bing=new org.osmdroid.tileprovider.tilesource.bing.BingMapTileSource(null);
mapView.setTileSource(bing);
````

### Set the imagery options

#### Hybrid (Road + Aerial)

````
bing.setStyle(BingMapTileSource.IMAGERYSET_AERIALWITHLABELS);
````

#### Road

````
bing.setStyle(BingMapTileSource.IMAGERYSET_ROAD);
````

#### Aerial

````
bing.setStyle(BingMapTileSource.IMAGERYSET_AERIAL);
````

## Google Maps

Required Java dependencies
 - osmdroid-android
 - osmdroid-third-party

MinSDK 9

Code Sample:
See the example "GoogleMapsWrapper" application