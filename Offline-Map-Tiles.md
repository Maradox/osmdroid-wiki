This document has the definitive guide for using osmdroid without a network connection.

# What are the supported storage formats?

osmdroid provides out of the box support for several different types of offline map tile caches.

 * osmdroid's flavor of a sqlite database (recommended)
 * osmdroid ZIP
 * MBTiles
 * GEMF
 * GeoPackage (still under development)

# How can I create an offline storage archive?

There's a few different options.

## OSM Map Tile Packager
osmdroid comes with a tool to enable you to download tiles and storage them for offline use called the `OSM Map Tile Packager`.

TODO guide on operation

## Mobile Atlas Creator (MOBAC)

MOBAC is the easiest to use tool that there is and supports a huge array input map sources and output formats (and is open source).

General guidance

 1. Create a new atlas using of the supported types (osmdroid SQLite)
 2. Select your map source
 3. Select a bounding box for the area you want and the zoom levels you want to acquire
 4. Click 'Add Selection'
 5. Click 'Create Atlas'
 6. Once it's done, copy the atlas file (.sqlite, .zip, etc) onto your osmdroid powered device to /sdcard/osmdroid/
 7. Tell osmdroid to not use a network connection and set the map tile source. **

### Important note on tile source names.

When downloading map sources using MOBAC and using one of the following formats
 * osmdroid SQLite
 * osmdroid ZIP

In both of these cases the name of the map source becomes encoded in the database or zip file format and is used to preserve uniqueness (more than one tile source per archive). Therefore you MUST tell osmdroid exactly the name of the map source when requesting offline tiles.


````
@Override protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    map = (MapView) findViewById(R.id.map);
    map.setTileSource(new XYTileSource("YOUR MAP SOURCE", 0, 18, 256, ".jpg", new String[] {}));
    //....
    map.setUseDataConnection(false); //optional, but a good way to prevent loading from the network and test your zip loading. 
    IMapController mapController = map.getController();
    mapController.setZoom(_A ZOOM LEVEL YOU HAVE IN YOUR ZIP_);
    GeoPoint startPoint = new GeoPoint(_POSITION SOMEWHERE INSIDE YOUR MAP_);
    mapController.setCenter(startPoint);
}
````

### Gotchas

MOBAC imposes a limit on the number of tiles to download in one shot. This can be overcome by altering the source code. 

MOBAC, especially with huge tile sets, can be a memory hog. It's algorithms from at least 1.8 to at least the 1.9.x releases caches as much as possible in memory until it hits the JVM ceiling, then does batch inserts into sqlite. While this is the way to make SQLite inserts as fast as possible, it can lead to stability issues with MOBAC.

## OSMBonusPack

[OSMBonusPack](https://github.com/MKergall/osmbonuspack) is an addon Android library for osmdroid that has the ability to download and cache map tiles on device.

# What legal things do I need to know?

Many map sources have disclaimers and legal statements that specifically state to NOT cache and rehost their map imagery. Some may have wording to disallows you to use their map imagery offline or to download large portions of the world.  Make sure you read the fine print! osmdroid takes no responsibility for map imagery misuse. That's between you and the imagery owner.

# Can you support tile format `x`?

We'll happily take pull requests. That said, it's fairly simple to make a new tile source for osmdroid. Here are the basic steps:
 1. Create a class that implements IArchiveProvider. At the most basic level, the IArchiveProvider needs to turn an OSM tile coordinates (Zoom, X and Y coordinates) into an InputStream representing the tile if it's available.
 2. Register the IArchiveProvider with `ArchiveFileFactory`
 3. Build, deploy and start up osmdroid
