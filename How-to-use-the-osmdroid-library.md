This wiki page has been migrated from the [associated Google Code page](https://code.google.com/p/osmdroid/wiki/HowToIncludeInYourProject).

---
# "Hello osmdroid World"
osmdroid's MapView is basically a replacement for Google's MapView class. 
First of all, create your Android project, and follow [HowToMaven](HowToMaven) if you're using Maven, or follow [HowToGradle[(HowToGradle) if you're using Gradle/Android Studio. This will help you get the binaries for osmdroid included in your project. 

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
For OsmDroid 4.3 and older, there's a number of resources that the map uses for various user interface helpers, such as zoom in/out buttons, the device's current location when GPS is available and more. These resources are loaded via the "ResourceProxy". The idea is that you can either bring your own images or borrow the ones from OsmDroid. If you're borrowing, then you'll want to grab the files located [here](https://github.com/osmdroid/osmdroid/tree/master/OpenStreetMapViewer/src/main/res/drawable) and add them to your project "src/main/res/drawable".

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
