# Tile Caching

osmdroid provides a few mechanisms for tile caching (for when you're internet connected and tiles need to be downloaded). 

 - TileWriter - uses the file system as the tile store, default is /sdcard/osmdroid/tiles/source/z/x/y.png
 - SqlTileWriter - uses a Sqlite database similar to the archival database structure that is used for offline archives. It adds expiration time.

# What do I need to know about tile caching

When osmdroid downloads tiles, if the tile cannot be stored to disk, it will not be displayed to the user. Osmdroid needs a location that's writable for storing the tile cache. The location is determined by OpenStreetMapsTileProviderConstants.TILE_BASE_PATH. Android is strange in terms of storage locations.

## Changes in versions of osmdroid

| Version | Behavioral Changes
| ---     | ---
| 5.6     | On first startup of the map, the mount point points are discovered and it auto selects the first storage location that's writable that has the largest amount of free space. You'll probably want your application's users to be able to change this. In addition, if there are no writable mount points on the device, you can also use your application's private storage (/data/data/package/....). See the sample application use example usage patterns.
| 5.5     | Default is now Sqlite database for API10+, API8-9 still uses TileWriter due to android strangeness. Default location is Environment.getExternalStorageDir/osmdroid/tiles
| older than 5.5 | TileWriter, default location is Environment.getExternalStorageDir/osmdroid/tiles


# Tile Cache Settings

This article is meant to describe how osmdroid caches tiles and how to tweak the settings if needed. The defaults work for most applications. Use with cautio

## In memory LRU Cache

````
//initial setting
org.osmdroid.tileprovider.constants.OpenStreetMapTileProviderConstants#CACHE_MAPTILECOUNT_DEFAULT
//programmatic construction
org.osmdroid.tileprovider.MapTileCache(int size)
````

## In memory TileOverlay overshoot cache

````
TilesOverlay x=this.mMapView.getOverlayManager().getTilesOverlay();
x.setOvershootTileCache(x.getOvershootTileCache() * 2);
````


## On disk cache (sqlite and file system storage)
````
OpenStreetMapTileProviderConstants.setCacheSizes(max, trim);
````
