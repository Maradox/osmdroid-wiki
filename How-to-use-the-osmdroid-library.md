This wiki page has been migrated from the [associated Google Code page](https://code.google.com/p/osmdroid/wiki/HowToIncludeInYourProject).

---

The best example of how to use the osmdroid library is our [OpenStreetMapViewer sample project](https://github.com/osmdroid/osmdroid/tree/master/OpenStreetMapViewer). It contains a basic osmdroid application plus a few special-use examples. It is recommended you use this project as a starting point for building your application.

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