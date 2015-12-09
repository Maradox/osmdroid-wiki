#FAQ

## Map tiles dont seem to load in my application

Make sure your AndroidManifest.xml file has the android.permission.INTERNET permission. See Prerequisites.

##The map will not scroll when I drag my finger on the screen

Make sure you don't set the MapView to be "clickable". If you are using xml layouts make sure you don't have android:clickable="true" or if you are creating your MapView programmatically make sure you don't have mMapView.setClickable(true);. This should no longer be necessary starting with osmdroid-3.0.11.

## The map is drawing incorrectly/misaligned

Turn off hardware acceleration in your manifest. See Prerequisites.

Update, turning off hardware acceleration is generally not required unless you have specific problems with a given device


## Night Mode

Osmdroid, since v5.0, has support for inverting the color scheme of map tiles to have a more user friendly experience at night. Your mileage may vary based on map sources. Lighter map sources, such as TOPO or road maps,  will appear darker at night. Satellite based tile sources may actually appear brighter at night.

````
this.mMapView.getController().setInvertedTiles(true);
````