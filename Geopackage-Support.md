# Geopackage support for osmdroid

NOTICE: 

Geopackage support for osmdroid is still evolving. This guide may be out of date
or change frequently. 

## What is Geopackage?

Basically, it's a sqlite database that can contain:

- 0 or more raster tile sources (think jpg, or png)
- 0 or more features sources (think vector graphics)

Geopackage internally uses a different tile numbering system
than osmdroid or anything else based on the slippy tile format (z/x/y)
thus a support library is required in order to chop up, rearrange,
and transform whatever is in the geopackage database into
something more usable.

## Sources

This code was adopted from NGA's Geopackage support library
and their adapter for Google Maps. Since the APIs between
Google Maps and Osmdroid are so similiar, much of the code was
used with only minor modifications.
https://github.com/ngageoint/geopackage-android

## License

This library: Inherited from the NGA code base
Dependencies: NGA code is MIT
See source code files for specific license info

## How to use

Min SDK: 14

Add it your project

````
compile 'org.osmdroid:osmdroid-geopackage:<VERSION>'
````


### Adding a geopackage raster tile set to the osmdroid map

Note: this was added in 5.6.4

For a complete working example see:

https://github.com/osmdroid/osmdroid/blob/master/OpenStreetMapViewer/src/main/java/org/osmdroid/samplefragments/tileproviders/GeopackageSample.java

First let's up our map source, geopackage needs you to explicitly specify which map files to load

````

GeoPackageProvider.TileSourceBounds tileSourceBounds;
XYTileSource currentSource = null;
GeoPackageProvider geoPackageProvider = null;


//this bit does some basic file system scanning
Set<File> mapfiles = findMapFiles();
//do a simple scan of local storage for .gpkg files.
File[] maps = new File[mapfiles.size()];
maps = mapfiles.toArray(maps);
````

Set the tile provider
````
geoPackageProvider = new GeoPackageProvider(maps, this.getContext());
mMapView.setTileProvider(geoPackageProvider);
````

Set the tile source by selecting a tile table in the geopackage database
````
//get the list of sources
List<GeoPackageMapTileModuleProvider.Container> tileSources = geoPackageProvider.geoPackageMapTileModuleProvider().getTileSources();

//here we're keeping track of the current tile source so we can reference it later, primarly for
//displaying the bounds of the tile set
boolean sourceSet = false;
for (int i = 0; i < tileSources.size(); i++) {
    //this is a list of geopackages, since we only support tile tables, pick the first one of those
    //ideally this should populate a spinner so that the user can select whatever tile source they want
    if (tileSources.get(i) != null && !tileSources.get(i).tiles.isEmpty()) {
        currentSource = (XYTileSource) geoPackageProvider.getTileSource(tileSources.get(i).database,
            tileSources.get(0).tiles.get(i)
        );
        mMapView.setTileSource(currentSource);
        sourceSet = true;
        break;
    }
}

````

Finally, since you're probably looking at a grey grid, center the map where the tiles are

````
tileSourceBounds = geoPackageProvider.getTileSourceBounds();
if (tileSourceBounds != null) {
    mMapView.zoomToBoundingBox(tileSourceBounds.bounds, true);
    mMapView.getController().setZoom(tileSourceBounds.minzoom);
}
````


### Adding a geopackage features to the osmdroid map

Note: this was added after 5.6.5.

For smale feature sets, you can use the following code to convert all
features in a given geopackage feature table into osmdroid overlays (Marker, Polyline and Polygon).

Find our geopackages

````
Set<File> mapfiles = findMapFiles();
//do a simple scan of local storage for .gpkg files.
File[] maps = new File[mapfiles.size()];
maps = mapfiles.toArray(maps);
````

Import them into the NGA manager
````
// Get a manager
GeoPackageManager manager = GeoPackageFactory.getManager(getContext());

// Available databases
List<String> databases = manager.databases();

// Import database
for (File f : maps) {
    try {
        boolean imported = manager.importGeoPackage(f);
    } catch (Exception ex) {
        ex.printStackTrace();
    }
}
````

Start the conversion

````
// Open database, we are assuming the first one is the one we want
// good place to ask the user
GeoPackage geoPackage = manager.open(databases.get(0));

OsmMapShapeConverter converter = new OsmMapShapeConverter(null);

// Feature tile tables
List<String> features = geoPackage.getFeatureTables();

// Query Features
// get the first table, this is an assumption, ask the user what they want

String featureTable = features.get(0);
FeatureDao featureDao = geoPackage.getFeatureDao(featureTable);
FeatureCursor featureCursor = featureDao.queryForAll();
try {
    while (featureCursor.moveToNext()) {
        try {
            FeatureRow featureRow = featureCursor.getRow();
            GeoPackageGeometryData geometryData = featureRow.getGeometry();
            Geometry geometry = geometryData.getGeometry();
            converter.addToMap(mMapView, geometry);
        } catch (Exception ex) {
            ex.printStackTrace();
        }
        // ...
    }
} finally {
    featureCursor.close();
}

````


### Adding a geopackage feature tile set to the osmdroid map

This was added after 5.6.5

The following code opens a Geopackage database and adds 1 tile overlay to the map per feature tile table.

````
 // Get a manager
GeoPackageManager manager = GeoPackageFactory.getManager(getContext());

// Available databases
List<String> databases = manager.databases();

// Import database
for (File f : maps) {
	try {
		boolean imported = manager.importGeoPackage(f);
	} catch (Exception ex) {
		ex.printStackTrace();
	}
}

if (!databases.isEmpty()) {
	for (int k = 0; k < databases.size(); k++) {
		// Open database
		GeoPackage geoPackage = manager.open(databases.get(k));
		// Feature tile tables
		List<String> features = geoPackage.getFeatureTables();
		// Query Features
		if (!features.isEmpty()) {
			for (int i = 0; i < features.size(); i++) {
				GeoPackageFeatureTileProvider provider = new GeoPackageFeatureTileProvider(
					new XYTileSource(databases.get(k) + ":" + features.get(i),0,22,256,"png",new String[0])
				);
				GeopackageFeatureTilesOverlay overlay = new GeopackageFeatureTilesOverlay(provider,getContext());
				overlay.setDatabaseAndFeatureTable(databases.get(k),  features.get(i));
				mMapView.getOverlayManager().add(overlay);
			}
		}
	}
}
````
