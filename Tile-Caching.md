This article is meant to describe how osmdroid caches tiles and how to tweak the settings if needed. The defaults work for most applications. Use with cautio

## In memory LRU Cache

````
//initial setting
org.osmdroid.tileprovider.constants.OpenStreetMapTileProviderConstants#CACHE_MAPTILECOUNT_DEFAULT
````

## In memory TileOverlay overshoot cache

````
TilesOverlay x=this.mMapView.getOverlayManager().getTilesOverlay();
x.setOvershootTileCache(x.getOvershootTileCache() * 2);
````


## On disk cache (file system storage)

## On disk cache (sqlite database storage)