# Important notes on using osmdroid in your app

There's a few things you need to know about osmdroid before deploying your app to public repositories, app stores, fdroid, etc. These settings should be configured individually for every app that uses osmdroid.

## Set the HTTP User-Agent variable

... by adding

````
   //pre 5.6
   OpenStreetMapTileProviderConstants.setUserAgentValue(BuildConfig.APPLICATION_ID);
   //5.6 and newer
   Configuration.getInstance().setUserAgentValue(BuildConfig.APPLICATION_ID)
````

in onCreate in the Map Activity, Fragment or Application.

Background: This setting identifies your app uniquely to tile servers. It's not the end user's identity, but the name of your app. If your users abuse the tile server or your app does in some way, this will prevent everyone that uses osmdroid from getting banned rather than just the users of your app.
`org.osmdroid.tileprovider.constants.OpenStreetMapTileProviderConstants#setUserAgentValue`


## Number of concurrent download threads

We set the default for [Open Street Map's tile usage policy](http://wiki.openstreetmap.org/wiki/Tile_usage_policy), but you can adjust it based on your tile source and device capabilities (don't set it to 1000 or something ridiculous like that). We limit it to 12 as a max.

`org.osmdroid.tileprovider.constants.OpenStreetMapTileProviderConstants#setNumberOfTileDownloadThreads`


## Boost performance with a larger in memory cache

Advantage: it's faster
Disadvantage: it uses a more memory and many android devices have limited capacity, even with the large heap setting. Unfortunately, there's no way to tell what the ceiling is. On many modern devices, it's 500MB, but not all. This is inclusive of all of the heap space for your app, so use with caution. 

````
TilesOverlay x=this.mMapView.getOverlayManager().getTilesOverlay();
x.setOvershootTileCache(x.getOvershootTileCache() * 2);
````

## Changing the loading tile grid colors

By default, osmdroid renders a gray background with a grid on it while tiles are loading or if no tiles are available. You can override the colors through the TilesOverlay.

````
this.mMapView.getOverlayManager().getTilesOverlay().setLoadingBackgroundColor(android.R.color.black);
this.mMapView.getOverlayManager().getTilesOverlay().setLoadingLineColor(Color.argb(255,0,255,0));
````

## Notes on programmatic construction of the MapView

Osmdroid supports the manual creating (not XML Layout based) construction of MapViews. In fact, it's used throughout the examples. BE CAREFUL creating the view manually, it's very easy to accidentally introduce memory leaks in your application this way. You'll have to handle onDestoryView and manually call MapView#onDetach.

Prefer using MapViews via xml layouts instead as demonstrated in 

* OpenStreetMapViewer/src/main/res/layout/activity_starter_mapview.xml

## How to credit OpenStreetMap

OpenStreetMap requires that you add “© OpenStreetMap contributors” to the map, if you use their data. For more dedails see http://www.openstreetmap.org/copyright/en 

## Handling Cache Storage

osmdroid does not work without somewhere to cache the tiles. The first time starting an application using osmdroid will scan for all available, writable mount points on the device. It auto selects the largest mount point. This, of course can be changed via the `IConfigurationProvider`. If that mount point suddenly becomes read only or disappears, osmdroid will not handle this scenario. Instead, users will get the familiar gray grid with no map tiles being displayed (uh oh). To overcome this, applications using osmdroid should probably check on startup to see what the selected cache location is via `ConfigurationProvider.getOsmdroidTileCache()` [source](https://github.com/osmdroid/osmdroid/blob/master/osmdroid-android/src/main/java/org/osmdroid/config/DefaultConfigurationProvider.java#L234) then by checking `StorageUtils.isWritable`. If it is writable, no further action is required. Otherwise, you should probably prompt the user or autoselect the next best location.