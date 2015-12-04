This document is all about Map Sources, Tile loading, caching, offline setups and more

# Map Projections

TODO

What map projection does Osmdroid use and or support?


# Tile providers vs Tile Source

OsmDroid uses two components to display map imagery, the Tile Provider and the Tile Source. The Tile Provider is used to determine how to load tiles (online, offline, assets folders, etc). The Tile Source determines what imagery set is displayed, such as Bing, Mapquest, Mapnik, etc. The default Tile Provider, searches the following for your Tile Source, Assets, Offline zip/sqlite/etc in (/sdcard/osmdroid), Downloaded tile cache (/sdcard/osmdroid/tiles) and then finally the downloader. There are other alternate providers included with OsmDroid that change the way tiles are loaded offline. OsmDroid BonusPack has a number of alternative provides that use other libraries like MapForge to generate tiles on the fly using OSM data while offline.

# Creating a custom tile provider chain

One of the features of osmdroid is the customizable tile provider chain. This provides the ability to mix and match various tile provider modules to create a specific tile retrieval strategy. The `MapTileProviderBasic` tile provider provides a default set of tile provider modules that includes a file cache, an archive provider, and a download provider. For most users this covers the basics, but you can build you own custom tile provider chain.

```java
final Context context = getActivity();
final Context applicationContext = context.getApplicationContext();
final IRegisterReceiver registerReceiver = new SimpleRegisterReceiver(applicationContext);

// Create a custom tile source
final ITileSource tileSource = new XYTileSource(
    "Mapnik", ResourceProxy.string.mapnik, 1, 18, 256, ".png", "http://tile.openstreetmap.org/");

// Create a file cache modular provider
final TileWriter tileWriter = new TileWriter();
final MapTileFilesystemProvider fileSystemProvider = new MapTileFilesystemProvider(
    registerReceiver, tileSource);

// Create an archive file modular tile provider
GEMFFileArchive gemfFileArchive = GEMFFileArchive.getGEMFFileArchive(mGemfArchiveFilename); // Requires try/catch
MapTileFileArchiveProvider fileArchiveProvider = new MapTileFileArchiveProvider(
    registerReceiver, tileSource, new IArchiveFile[] { gemfFileArchive });

// Create a download modular tile provider
final NetworkAvailabliltyCheck networkAvailabliltyCheck = new NetworkAvailabliltyCheck(context);
final MapTileDownloader downloaderProvider = new MapTileDownloader(
    tileSource, tileWriter, networkAvailablityCheck);

// Create a custom tile provider array with the custom tile source and the custom tile providers
final MapTileProviderArray tileProviderArray = new MapTileProviderArray(
    tileSource, registerReceiver, new MapTileModuleProviderBase[] {
        fileSystemProvider, fileArchiveProvider, downloaderProvider });

// Create the mapview with the custom tile provider array
mMapView = new MapView(context, 256, new DefaultResourceProxyImpl(context), tileProviderArray);
```




## Using a different Tile Source
OsmDroid comes with a bunch of tile sources preprogrammed for sources available on the internet. Some require API keys or additional information due to usage restrictions, developers accounts, pay schemes, etc. The following example will show you how to switch tile sources at runtime.

To set to MapQuest satellite:
````
mMapView.setTileSource(TileSourceFactory.MAPQUESTAERIAL);
````

To set to MapQuest road maps:
````
mMapView.setTileSource(TileSourceFactory.MAPQUESTOSM);
````

To set to a custom map server/tile source. In this case, we are using the USGS Topographic maps. This tile source is a bit different and requires some explanation. Most OSM based map sources use a URL pattern  similar to Zoom/X/Y.png. USGS, as well as many other ArcGis based sources, use Zoom/Y/X and thus require a different URL pattern.

OSMDroid version <= 4.3

````
mMapView.setTileSource(new OnlineTileSourceBase("USGS Topo", ResourceProxy.string.custom, 0, 18, 256, "", 
               new String[] { "http://basemap.nationalmap.gov/ArcGIS/rest/services/USGSTopo/MapServer/tile/" }) {
               @Override
               public String getTileURLString(MapTile aTile) {
                    return getBaseUrl() + aTile.getZoomLevel() + "/" + aTile.getY() + "/" + aTile.getX()
				+ mImageFilenameEnding;
               }
          });
````

OSMDroid version >= 5

````
mMapView.setTileSource(new OnlineTileSourceBase("USGS Topo", 0, 18, 256, "", 
               new String[] { "http://basemap.nationalmap.gov/ArcGIS/rest/services/USGSTopo/MapServer/tile/" }) {
               @Override
               public String getTileURLString(MapTile aTile) {
                    return getBaseUrl() + aTile.getZoomLevel() + "/" + aTile.getY() + "/" + aTile.getX()
				+ mImageFilenameEnding;
               }
          });
````



## How to adjust the tile cache (in memory)

Since many devices have support for `android:largeHeap="true"` settings, which enables your app to use more memory than "normal". This code snippet will allow you to increase the tile cache (in memory) to meet your needs. It's normally set to the exact number of tiles to fill the screen.

````
Iterator<Overlay> iterator = mMapView.getOverlays().iterator();
while(iterator.hasNext()){
	Overlay next = iterator.next();
	if (next instanceof TilesOverlay){
		TilesOverlay x = (TilesOverlay)next;
		x.setOvershootTileCache(x.getOvershootTileCache() * 2);
		Toast.makeText(getActivity(), "Tiles overlay cache set to " + x.getOvershootTileCache(), Toast.LENGTH_LONG).show();
		break;
	}
}
````

## Adjust the size of the cache on disk

The primary usage is downloaded map tiles

````
//this will set the disk cache size in MB to 1GB , 9GB trim size
OpenStreetMapTileProviderConstants.setCacheSizes(1000L, 900L);
````




# Online Map Sources (out of the box)

Osmdroid comes with a few map sources preconfigured for you to use. Usage access rules vary based on the source. Make sure you read the fine print.

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
org.osmdroid.tileprovider.tilesource.bing.BingMapTileSource.setBingKey("YOUR KEY");
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


## WMS Support

WMS Support on device is something that we're working on, but it's not a high priority on the moment.

You can however use MOBAC to connected to a WMS map provider, then convert those tiles into an offline map source in the mean time.

## Geopackage Support

Coming soon...


# Offline map tiles

See this article https://github.com/osmdroid/osmdroid/wiki/Offline-Map-Tiles