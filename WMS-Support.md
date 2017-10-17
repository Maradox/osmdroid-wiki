# WMS Support

This article is a basic integration guide for using WMS tile servers with osmdroid.

It's not perfect and will not work with many use cases.

## Architecture and workflow

![WMS workflow](https://raw.githubusercontent.com/osmdroid/osmdroid/master/src/site/images/osmdroid-wms.png)

1. Starting with a WMS endpoint, fetch and parse the `GetCapabilities` document
2. `WMSEndpoint` contains the bare minimum needed for layer selection and some minor presentation details
3. `WMSEndpoint`'s layers can then be presented to the user for selection
4. Upon selection, the WMSTileSource is created from the `WMSEndpoint` and the `WMSLayer`
5. Finally, tell osmdroid to use the newly created WMSTileSource

## What's supported and what's not?

Server's we have tested against
 - Geoserver, works in most cases
 - NASA's endpoints, most do NOT work.

The following coordinate/spatial reference systems (CRS/SRS) are supported
- WGS84, EPSG:4326, CRS:84, this your standard lat/lon
- EPSG:900913

General WMS server requirements 
 - The WMS server needs to support producing a tile for a specific bounding box at a specific pixel size. 
 - Layers that require additional input parameters, such as time offsets, is not supported.
 - Layers that require a HTTP post message is not supported.
 - Layers that use non-square pixels (i.e. the height and width of the image are not equal) is not supported.
 - Only layers that use the supported CRS/SRS can be used

Styling per layer is supported, the first style is auto selected.

Multiple tile layers active at the same time is not currently supported (this would be useful for getting the best tile available as you zoom in and out).

## Integration Guide

### Add the dependency

WMS library was added in 6.0 of osmdroid. It may not yet be published, see the osmdroid readme for details.

````
dependencies {
    compile 'org.osmdroid:osmdroid-wms:<VERSION>'
}
````

### Parse the WMS GetCapabilities XML

Note, error handling is omitted for brievity

````
HttpURLConnection c = null;
InputStream is = null;
WMSEndpoint wmsEndpoint;

c = (HttpURLConnection) new URL(youEditTextValue).openConnection();
is = c.getInputStream();
wmsEndpoint = WMSParser.parse(is);

is.close();
c.disconnect();
````

Here is a great spot to prompt the user for some kind of layer selection.

### Show the selected layer

Here we are selecting the first layer and telling osmdroid to use it. 

````
WMSTileSource source = WMSTileSource.createFrom(wmsEndpoint, wmsEndpoint.getLayers().get(0) );
if (layer.getBbox() != null) {
	//center map on this location
	mMapView.zoomToBoundingBox(layer.getBbox(),true);
}

mMapView.setTileSource(source);
````

