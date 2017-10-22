# Mapsforge Support

## Introduction

This article will walk you through the setup, usage and known limitations of using osmdroid's MapsForge adapter

## What is Mapsforge?

[MapsForge](https://github.com/mapsforge/mapsforge) is a vector map library written in Java with support for Android and JRE. It works by rendering osm data on demand (on device).

## Sources

Osmdroid's adapter for MapsForge is located [here](https://github.com/osmdroid/osmdroid/tree/master/osmdroid-mapsforge). This was migrated from [osmbonuspack](https://github.com/MKergall/osmbonuspack/) in version 5.2.

## License

MapsForge code is LGPLv3. osmdroid's adapter for mapsforge is ASF 2.0. 

## How to Use

### Adding the dependency

This is pretty basic. The maps forge adapter is published to Maven Central as an Android Archive (.aar) file. Add the following under `dependencies` in your `gradle.build` file.

`compile "org.osmdroid:osmdroid-mapsforge:<VERSION>"`

The minimum API level required by MapsForge is API 10.

### Code setup

```java

File[] maps = null;  //TODO scan/prompt for map files (.map)

//null is ok here, uses the default rendering theme if it's not set
XmlRenderTheme theme = null;
try {
//this file should be picked up by the mapsforge dependencies
	theme = new AssetsRenderTheme(getContext().getApplicationContext(), "renderthemes/", "rendertheme-v4.xml");
} catch (Exception ex) {
	ex.printStackTrace();
}

MapsForgeTileSource fromFiles = MapsForgeTileSource.createFromFiles(maps, theme, "rendertheme-v4");
MapsForgeTileProvider forge = new MapsForgeTileProvider(
	new SimpleRegisterReceiver(getContext()),
	fromFiles, null);

mMapView.setTileProvider(forge);

//now for a magic trick
//since we have no idea what will be on the
//user's device and what geographic area it is, this will attempt to center the map
//on whatever the map data provides
mMapView.getController().setZoom(fromFiles.getMinimumZoomLevel());
mMapView.zoomToBoundingBox(fromFiles.getBoundsOsmdroid(), true);
```

### Lifecycle/resource management issues

 * There are cleanup tasks required if you are switching providers around on the fly. See the function example for details.
 * The MapsForge adapter will honor the `IConfigurationProvider.getTileFileSystemThreads()` for the maximum number of concurrent rendering threads.

### Functioning Examples

For a complete function example, see [MapsforgeTileProviderSample](https://github.com/osmdroid/osmdroid/blob/master/OpenStreetMapViewer/src/main/java/org/osmdroid/samplefragments/tileproviders/MapsforgeTileProviderSample.java)

### Combining Mapsforge tiles with other sources

It is possible to use multiple tile providers at the same time. This can be done by having multiple `TileOverlay`s. The MapView will always have one of them, which can be interacted with by `MapView.getOverlayManager().getTilesOverlay()`. There's an example of this [here](https://github.com/osmdroid/osmdroid/blob/master/OpenStreetMapViewer/src/main/java/org/osmdroid/samplefragments/tileproviders/OfflinePickerSample.java). 

### How does this work?

The MapsForge adapter implements the [tile provider pattern](Modular-Tile-Provider-Architecture). It is not a simple tile source since there are lifecycle issues and Mapforge  tile source didn't seem possible.

## Limitations

None that are known

