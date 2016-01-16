#FAQ

## Map tiles dont seem to load in my application

Make sure your AndroidManifest.xml file has the android.permission.INTERNET permission. See Prerequisites.

##The map will not scroll when I drag my finger on the screen

Make sure you don't set the MapView to be "clickable". If you are using xml layouts make sure you don't have android:clickable="true" or if you are creating your MapView programmatically make sure you don't have mMapView.setClickable(true);. This should no longer be necessary starting with osmdroid-3.0.11.

## The map is drawing incorrectly/misaligned

Turn off hardware acceleration in your manifest. See Prerequisites.

Update, turning off hardware acceleration is generally not required unless you have specific problems with a given device

This condition can also occur if you somehow manage to change the screen resolution of your android device or virtual machine and do not reboot before launching osmdroid.


## Night Mode

osmdroid, since v5.0, has support for inverting the color scheme of map tiles to have a more user friendly experience at night. Your mileage may vary based on map sources. Lighter map sources, such as TOPO or road maps,  will appear darker at night. Satellite based tile sources may actually appear brighter at night.

````
this.mMapView.getController().setInvertedTiles(true);
````

Starting with 5.1, the API has changed a bit for this. See the change log.

## Debugging tile loading issues

When your app starts up, try the following. This will produce a lot of log output to help you narrow down where the issue is.
````
        OpenStreetMapTileProviderConstants.DEBUG_TILE_PROVIDERS=true;
        OpenStreetMapTileProviderConstants.DEBUGMODE=true;
````

## Does my device need an sd card or some kind of storage medium?

Yes. osmdroid downloads and caches map data on device and it needs to be stored on some writable medium. We use `Environment.getExternalStorage` and if that doesn't return a writable location then the cache won't be available, which well lead to significantly increased data usage.