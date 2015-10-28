# Changelog

This list only includes major highlights or breaking changes. Check [here](https://github.com/osmdroid/osmdroid/commits/master) for full commit logs.

## 5.0 (not yet released)
 * Target output and publication to maven central is AAR which includes the default resource files (person icon, etc)
 * Tile inversion for a rudimentary night mode
 * Better support for Android x86 with online map sources
 * (Pending) using doubles internally for points instead of ints (increased precision)
 * Better support for application specific control of map tile loading
   * Can now use offline tiles from just a single zip/sqlite/etc which don't have to be in /sdcard/osmdroid
 * Ability to change the cache folder and offline zip/database location
 * Itemized icon overlay icons no longer scale while zooming
 * My location icon can now be changed at runtime
 * Read/write permissions change for sqlite databases (now is read only)
 * Fling can be disabled programmatically
 * Better handling of map motion events for listeners
 * Updated build tech. Gradle build files also updated to reflect the changes
 * SLF4J dependency dropped. All logging now goes to the standard logcat
 * New Map source added for USGS maps
 * Fix for MapBox data sources
 * Many additional examples and samples on the demo app.
 * Android 6.0 support and the removal of Apache Http Client as a dependency
 * Rotation gestures now have reduced jitter
 * Can now set the HTTP User Agent for all HTTP connections

### BREAKING CHANGES!!

 * The API structure for IArchiveFile has changed. If you have a custom one, it can now be registered by file extension via ArchiveFileFactory.registerArchiveFileProvier(Class, file extension)
 * The API structure ITileSource and everything related to it has changed. All methods that required a ResourceProxy.string value were modified to no longer require the ResourceProxy.string value. It wasn't really used anywhere, was unnecessary and only added confusion. 
 * Apache Http Client removed
 * Rotation Gesture detector is now included in osmdroid-android@aar and is in a new name space


## 4.3 (current latest stable release)
 * Fixing issue #22: MapController.setCenter not centering when used in onCreate (and more generally: all actions on mapView positionning done in onCreate). 
 * Fixing issues related to high density screens. 

## 4.2 

#### Note - breaking changes
We have made a number of changes to the maps that may require some changes for users that write their own Overlays.

* The Projection class is the singular authority for lat/long <-> pixel conversions. Do NOT use TileSystem unless you really know what you're doing.
* The Projection methods have been simplified and now strictly follow the Google Maps v1 API. One major change is that toMapPixels() is now correctly called toPixels(). Redundant and confusing methods were removed.
* The Projection class is broken out into its own class. It can be extended to add additional functionality by users. Override MapView.getProjection() to pass back your own instance.
* The "Safe" canvas classes have been eliminated. All "Safe" classes can be reverted back to their normal counterparts - so SafePaint can be reverted to Paint, SafeCanvas to Canvas, etc...
* We have implemented an OsmPath which behaves similarly to the old SafePath class. By calling onDrawCycle() in the draw() method of your overlay, it will adjust the location of the path so as to avoid having to recalculate the points on every draw.

#### Other changes

* Added experimental HW-acceleration support. See issue 413.
* Issue 426, issue 479, issue 489, issue 520, issue 521.

## 4.1

* Fix issue with OSM tile servers rejecting GETs due to missing user-agent. See issue 515.
* Issue 417, issue 477, issue 489, issue 491, issue 498, issue 500, issue 507.

## 4.0

* Added compatibility layer for Google Maps API v2.
* Added !BitmapPool to reuse !MapTiles and prevent constant Bitmap allocation during scrolling.
* Added fix to ignore !MapView 'clickable' setting that can prevent scrolling.
* Added http client factory to !MapTileDownloader.
* Some changes to clean up the !MapController. See issue 471.
* Changed zoom animations to match pinch-to-zoom animations for consistency and to allow overlays to prevent their contents from scrolling. See issue 453.
* Issue 298, issue 408, issue 427, issue 437, issue 438, issue 441, issue 442, issue 447, issue 450, issue 451, issue 483.

## 3.0.10

* Added limited scrolling area
* Added simple setable min/max zoomlevel on the !MapView that overrides the values returned by the tile provider.
* Pinch-to-zoom is relative to the pinch point, not the center of the screen.
* New samples project with modern Fragments.