# Debugging osmdroid

This article contains a collection of tips, tricks, tools, and procedures that you can use to help debug issues with osmdroid.

## Debugging tile loading issues

When your app starts up, try the following. This will produce a lot of log output to help you narrow down where the issue is.
````
OpenStreetMapTileProviderConstants.DEBUG_TILE_PROVIDERS=true;
OpenStreetMapTileProviderConstants.DEBUGMODE=true;
MapTileDownloader.DEBUG = true
````

## More logging settings

````
CloudmadeUtil.DEBUGMODE=true
MapViewConstants.DEBUGMODE=true
OverlayConstants.DEBUGMODE=true
````
