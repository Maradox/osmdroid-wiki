There's a few things you need to know about osmdroid before deploying your app to public repositories, app stores, fdroid, etc. These settings should be configured individually for every app that uses osmdroid.

## Set the HTTP User-Agent variable

This setting identifies your app uniquely to tile servers. It's not the end user's identity, but the name of your app. If your users abuse the tile server or your app does in some way, this will prevent everyone that uses osmdroid from getting banned rather than just the users of your app.
`org.osmdroid.tileprovider.constants.OpenStreetMapTileProviderConstants#setUserAgentValue`


## Number of concurrent download threads

We set the default for Open Street Map's tile usage policy, but you can adjust it based on your tile source and device capabilities (don't set it to 1000 or something ridiculous like that). We limit it to 12 as a max.

`org.osmdroid.tileprovider.constants.OpenStreetMapTileProviderConstants#setNumberOfTileDownloadThreads`


## Boost performance with a larger in memory cache

Advantage: it's faster
Disadvantage: it uses a more memory and many android devices have limited capacity, even with the large heap setting. Unfortunately, there's no way to tell what the ceiling is. On many modern devices, it's 500MB, but not all. This is inclusive of all of the heap space for your app, so use with caution. 

````
Iterator<Overlay> iterator = mMapView.getOverlays().iterator();
		while(iterator.hasNext()){
			Overlay next = iterator.next();
			if (next instanceof TilesOverlay){
				TilesOverlay x = (TilesOverlay)next;
				x.setOvershootTileCache(x.getOvershootTileCache() * 2);
				break;
			}
		}
````

## Changing the loading tile grid colors

By default, osmdroid renders a gray background with a grid on it while tiles are loading or if no tiles are available. You can override the colors through the TilesOverlay.

````
this.mMapView.getOverlayManager().getTilesOverlay().setLoadingBackgroundColor(android.R.color.black);
this.mMapView.getOverlayManager().getTilesOverlay().setLoadingLineColor(Color.argb(255,0,255,0));
````