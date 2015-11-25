This wiki page has been migrated from the [associated Google Code page](https://code.google.com/p/osmdroid/wiki/HowToIncludeInYourProject).

---
# "Hello osmdroid World"
osmdroid's MapView is basically a replacement for Google's MapView class. 
First of all, create your Android project, and follow [HowToMaven](HowToMaven) if you're using Maven, or follow [HowToGradle](How-to-add-the-osmdroid-library-via-Gradle) if you're using Gradle/Android Studio. This will help you get the binaries for osmdroid included in your project. 

## Manifest
In most cases, you will have to set the following authorizations in your AndroidManifest.xml:
```xml
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/> 
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

## Layout
Create a "src/main/res/layouts/main.xml" layout like this one. With Android Studio, it probably created one already called. The default is "src/main/res/layouts/activity_main.xml":
```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools"
        android:orientation="vertical" 
        android:layout_width="fill_parent"
        android:layout_height="fill_parent">
        <org.osmdroid.views.MapView android:id="@+id/map"
                android:layout_width="fill_parent" 
                android:layout_height="fill_parent" />
</LinearLayout>
```

## Images for Buttons and whatnot
For OSMDroid 4.3 and older, there's a number of resources that the map uses for various user interface helpers, such as zoom in/out buttons, the device's current location when GPS is available and more. These resources are loaded via the "ResourceProxy". The idea is that you can either bring your own images or borrow the ones from OsmDroid. If you're borrowing, then you'll want to grab the files located [here](https://github.com/osmdroid/osmdroid/tree/master/OpenStreetMapViewer/src/main/res/drawable) and add them to your project "src/main/res/drawable".

For OSMDroid 5.0 and newer, the drawables are included with the AAR package. The resource proxy is still present and used so you can override values and images as needed.

## Main Activity
We now create the main activity (MainActivity.java):

```java
public class MainActivity extends Activity {
    @Override public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        MapView map = (MapView) findViewById(R.id.map);
        map.setTileSource(TileSourceFactory.MAPNIK);
    }
}
```

And that's enough to give it a try, and see the world map. 

Then we add default zoom buttons, and ability to zoom with 2 fingers (multi-touch)
```java
        map.setBuiltInZoomControls(true);
        map.setMultiTouchControls(true);
```

We can move the map on a default view point. For this, we need access to the map controller:
```java
        IMapController mapController = map.getController();
        mapController.setZoom(9);
        GeoPoint startPoint = new GeoPoint(48.8583, 2,2944);
        mapController.setCenter(startPoint);
```


# Advanced tutorial
The best example of how to use the osmdroid library is our [OpenStreetMapViewer sample project](https://github.com/osmdroid/osmdroid/tree/master/OpenStreetMapViewer). It contains a basic osmdroid application plus a few special-use examples. It is recommended you use this project as an example for building your application.

# Adding a MapView

You can add a `MapView` to your xml layout using:

```xml
<org.osmdroid.views.MapView
    android:id="@+id/mapview"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tilesource="Mapnik" />
```

This will allow you to configure the tile source imagery for your `MapView` but not much else.

However, for more control over your `MapView`, you will want to create a `MapView` programmatically.

```java
@Override
public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    mResourceProxy = new ResourceProxyImpl(inflater.getContext().getApplicationContext());
    mMapView = new MapView(inflater.getContext(), 256, mResourceProxy);
    return mMapView;
}
```

# Creating a tile provider chain

One of the features of osmdroid is the customizable tile provider chain. This provides the ability to mix and match various tile provider modules to create a specific tile retrieval strategy. The `MapTileProviderBasic` tile provider provides a default set of tile provider modules that includes a file cache, an archive provider, and a download provider. For most users this covers the basics, but you can build you own custom tile provider chain.

```java
final Context context = getActivity();
final Context applicationContext = context.getApplicationContext();
final IRegisterReceiver registerReceiver = new SimpleRegisterReceiver(applicationContext);

// Create a custom tile source
final ITileSource tileSource = new XYTileSource(
    "Mapnik", ResourceProxy.string.mapnik, 1, 18, 256, ".png", "http://tile.openstreetmap.org/");

// Create a file cache modular provider
final TileWriter tileWriter = new TileWriter();
final MapTileFilesystemProvider fileSystemProvider = new MapTileFilesystemProvider(
    registerReceiver, tileSource);

// Create an archive file modular tile provider
GEMFFileArchive gemfFileArchive = GEMFFileArchive.getGEMFFileArchive(mGemfArchiveFilename); // Requires try/catch
MapTileFileArchiveProvider fileArchiveProvider = new MapTileFileArchiveProvider(
    registerReceiver, tileSource, new IArchiveFile[] { gemfFileArchive });

// Create a download modular tile provider
final NetworkAvailabliltyCheck networkAvailabliltyCheck = new NetworkAvailabliltyCheck(context);
final MapTileDownloader downloaderProvider = new MapTileDownloader(
    tileSource, tileWriter, networkAvailablityCheck);

// Create a custom tile provider array with the custom tile source and the custom tile providers
final MapTileProviderArray tileProviderArray = new MapTileProviderArray(
    tileSource, registerReceiver, new MapTileModuleProviderBase[] {
        fileSystemProvider, fileArchiveProvider, downloaderProvider });

// Create the mapview with the custom tile provider array
mMapView = new MapView(context, 256, new DefaultResourceProxyImpl(context), tileProviderArray);
```

# Create a custom Resource Proxy
As mentioned above, the Resource Proxy is a bit of a strange animal that OsmDroid uses to load some images for user interface controls. If you're using any of the build in controls that need images (zoom in/out, person icon, etc) you'll either need to provide your own images, borrow the images from OsmDroid's example app, or provide your own implementation of Resource Proxy.

The example OsmDroid app includes an example of this called CustomResourceProxy (included with > 4.3 OsmDroid). All it does is change the my location drawable (person) to an alternate image. The example is below.

````
public class CustomResourceProxy extends DefaultResourceProxyImpl {

     private final Context mContext;
     public CustomResourceProxy(Context pContext) {
          super(pContext);
		mContext = pContext;
     }
     
     @Override
	public Bitmap getBitmap(final bitmap pResId) {
		switch (pResId){
               case person:
                    //your image goes here!!!
                    return BitmapFactory.decodeResource(mContext.getResources(),org.osmdroid.example.R.drawable.sfgpuci);
          }
          return super.getBitmap(pResId);
	}

	@Override
	public Drawable getDrawable(final bitmap pResId) {
		switch (pResId){
               case person:
                    return mContext.getResources().getDrawable(org.osmdroid.example.R.drawable.sfgpuci);
          }
          return super.getDrawable(pResId);
	}
}
````

Then you can use your instance using the following snippet.

````
mResourceProxy = new CustomResourceProxy(getApplicationContext());
final RelativeLayout rl = new RelativeLayout(this);
this.mOsmv = new MapView(this,mResourceProxy);
````

In order to see any difference with our example (changes the person icon), we'll need to get a location fix and add it to the map layers.

````
this.mLocationOverlay = new MyLocationNewOverlay(new GpsMyLocationProvider(this), mOsmv, mResourceProxy);
this.mLocationOverlay.enableMyLocation();
this.mOsmv.getOverlays().add(mLocationOverlay);
this.mOsmv.setMultiTouchControls(true);
````


## Tile providers vs Tile Source

OsmDroid uses a Tile Provider to figure out how to load tiles (online, offline, assets folders, etc) and a Tile Source (Bing, Mapquest, Mapnik, etc) to figure out how to load tiles. The default Tile Provider, searches the following for your Tile Source, Assets, Offline zip/sqite/etc in (/sdcard/osmdroid), Downloaded tile cache (/sdcard/osmdroid/tiles) and then finally the downloader.

## Using a different Tile Source
OsmDroid comes with a bunch of tile sources preprogrammed for sources available on the internet. Some require API keys or additional information due to usage restrictions, developers accounts, pay schemes, etc. The following example will show you how to switch tile sources at runtime.

To set to MapQuest satelite:
````
mMapView.setTileSource(TileSourceFactory.MAPQUESTAERIAL);
````

To set to MapQuest road maps:
````
mMapView.setTileSource(TileSourceFactory.MAPQUESTOSM);
````

To set to a custom map server/tile source. In this case, we are using the USGS Topographic maps. This tile source is a bit different and requires some explanation. Most OSM based map sources use a URL pattern  similar to Zoom/X/Y.png. USGS, as well as many other ArcGis based sources, use Zoom/Y/X and thus require a different URL pattern.

OSMDroid version <= 4.3

````
mMapView.setTileSource(new OnlineTileSourceBase("USGS Topo", ResourceProxy.string.custom, 0, 18, 256, "", 
               new String[] { "http://basemap.nationalmap.gov/ArcGIS/rest/services/USGSTopo/MapServer/tile/" }) {
               @Override
               public String getTileURLString(MapTile aTile) {
                    return getBaseUrl() + aTile.getZoomLevel() + "/" + aTile.getY() + "/" + aTile.getX()
				+ mImageFilenameEnding;
               }
          });
````

OSMDroid version >= 5

````
mMapView.setTileSource(new OnlineTileSourceBase("USGS Topo", 0, 18, 256, "", 
               new String[] { "http://basemap.nationalmap.gov/ArcGIS/rest/services/USGSTopo/MapServer/tile/" }) {
               @Override
               public String getTileURLString(MapTile aTile) {
                    return getBaseUrl() + aTile.getZoomLevel() + "/" + aTile.getY() + "/" + aTile.getX()
				+ mImageFilenameEnding;
               }
          });
````


# Map Overlays

## How to add the My Location overlay

````
this.mLocationOverlay = new MyLocationNewOverlay(context, new GpsMyLocationProvider(context),mMapView);
mMapView.getOverlays().add(this.mLocationOverlay);
````

## How to add a compass overlay

````
this.mCompassOverlay = new CompassOverlay(context, new InternalCompassOrientationProvider(context), mMapView);
mMapView.getOverlays().add(this.mCompassOverlay);
````

## How to enable rotation gestures

````
mRotationGestureOverlay = new RotationGestureOverlay(context, mMapView);
mRotationGestureOverlay.setEnabled(true);
mMapView.setMultiTouchControls(true);
mMapView.getOverlays().add(this.mRotationGestureOverlay);
````

## How to add Map Scale bar overlay

````
mScaleBarOverlay = new ScaleBarOverlay(context);
mScaleBarOverlay.setCentred(true);
//play around with these values to get the location on screen in the right place for your applicatio
mScaleBarOverlay.setScaleBarOffset(dm.widthPixels / 2, 10);
mMapView.getOverlays().add(this.mScaleBarOverlay);
````

## How to add the built in Minimap

Note: do not use when rotation is enabled!

````
mMinimapOverlay = new MinimapOverlay(context, mMapView.getTileRequestCompleteHandler());
mMinimapOverlay.setWidth(dm.widthPixels / 5);
mMinimapOverlay.setHeight(dm.heightPixels / 5);
//optionally, you can set the minimap to a different tile source
//mMinimapOverlay.setTileSource(....);
mMapView.getOverlays().add(this.mMinimapOverlay);
````

Pro tip: If you want the minimap to stay put when rotation is enabled, create a second map view in your layout file, then wire up a change listener on the main map and use that to set the location on the minimap.




## How to adjust the tile cache (in memory)

Since many devices have support for `android:largeHeap="true"` settings, which enables your app to use more memory than "normal". This code snippet will allow you to increase the tile cache (in memory) to meet your needs. It's normally set to the exact number of tiles to fill the screen.

````
Iterator<Overlay> iterator = mMapView.getOverlays().iterator();
while(iterator.hasNext()){
	Overlay next = iterator.next();
	if (next instanceof TilesOverlay){
		TilesOverlay x = (TilesOverlay)next;
		x.setOvershootTileCache(x.getOvershootTileCache() * 2);
		Toast.makeText(getActivity(), "Tiles overlay cache set to " + x.getOvershootTileCache(), Toast.LENGTH_LONG).show();
		break;
	}
}
````

## Adjust the size of the cache on disk

The primary usage is downloaded map tiles

````
//this will set the disk cache size in MB to 1GB , 9GB trim size
OpenStreetMapTileProviderConstants.setCacheSizes(1000L, 900L);
````

## How do I place icons on the map with a click listener?

````
//your items
ArrayList<OverlayItem> items = new ArrayList<OverlayItem>();
items.add(new OverlayItem("Title", "Description", new GeoPoint(0.0d,0.0d))); // Lat/Lon decimal degrees

//the overlay
ItemizedOverlayWithFocus<OverlayItem> mOverlay = new ItemizedOverlayWithFocus<OverlayItem>(items,
	new ItemizedIconOverlay.OnItemGestureListener<OverlayItem>() {
	@Override
	public boolean onItemSingleTapUp(final int index, final OverlayItem item) {
	//do something
	    return true;
	}
	@Override
	public boolean onItemLongPress(final int index, final OverlayItem item) {
		return false;
	}
}, mResourceProxy);
mOverlay.setFocusItemsOnTap(true);

mMapView.getOverlays().add(mOverlay);
````

## How many icons can I put on the map?

The answer is greatly dependent on what hardware the OSMDroid based app is ran on. A Samsung S5 (no endorsement intended) ran just fine at 3k icons and was noticeably choppy at 6k icons. Your mileage may vary.

If you're also drawing paths, lines, polygons, etc, then this also changes the equation. Drawing multipoint graphics is computationally more expensive and thus negatively affects performance under higher loads. To mitigate performance issues with multipoint graphics, one strategy would be to reduce the amount of points handed off to the map engine when at a higher zoom level (numerically lower), then increase the fidelity as the user zoom's in.