This articles discusses how to perform some common interactions with this following osmdroid capabilities

- Geospatially referenced icons
  - ItemizedIconOverlay (oldie but a goodie)
  - Marker - near API compatible with Google Maps v2 
  - Fast Icon Overlays
- Geospatially referenced Lines and Polygons

##  Geospatially referenced icons

Pushpins, markers, icons, symbols, etc

### Itemized Icon Overlay with click listener

The itemized icon overlay was based on a Google Maps v1 API.

````java
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
});
mOverlay.setFocusItemsOnTap(true);

mMapView.getOverlays().add(mOverlay);
````

### Marker

The Marker overlay was generously donated to osmdroid from osmdroidbonuspack. It mirrors the Google Maps v2 API.

![Marker](https://github.com/osmdroid/osmdroid/raw/master/osmdroid-android/src/main/java/org/osmdroid/views/overlay/doc-files/marker-classes.png)


```
Marker startMarker = new Marker(map);
startMarker.setPosition(startPoint);
startMarker.setAnchor(Marker.ANCHOR_CENTER, Marker.ANCHOR_BOTTOM);
map.getOverlays().add(startMarker);
```

Clicking on it opens an empty cartoon-bubble.

Changing the icon, and setting a title to be shown in the bubble:
```java
startMarker.setIcon(getResources().getDrawable(R.drawable.ic_launcher));
startMarker.setTitle("Start point");
```

The bubble can be customized with your own layouts too

![](https://github.com/osmdroid/osmdroid/raw/master/osmdroid-android/src/main/java/org/osmdroid/views/overlay/doc-files/marker-infowindow-classes.png)

#### Marker using a text label instead of an icon

The marker class can render a simple text label for icons without an icon. This is an opt in feature and is global. It is super useful for certain KML scenarios (osmbonuspack required).

```java
//enables this opt in feature
Marker.ENABLE_TEXT_LABELS_WHEN_NO_IMAGE = true;
//build the marker
Marker m = new Marker(mapView);
m.setTextLabelBackgroundColor(backgroundColor);
m.setTextLabelFontSize(fontSizeDp);
m.setTextLabelForegroundColor(fontColor);
m.setTitle("hello world");
//must set the icon to null last
m.setIcon(null);
m.setPosition(new GeoPoint(0d,0d));
map.getOverlays().add(m);
```

### Fast Overlay

The fast overlay is great if you have a huge number points to render and they all share the same icon. It is optimized to render over 100k points, however performance will vary based on hardware.

```java
// create 10k labelled points
// in most cases, there will be no problems of displaying >100k points, feel free to try
List<IGeoPoint> points = new ArrayList<>();
for (int i = 0; i < 10000; i++) {
	points.add(new LabelledGeoPoint(37 + Math.random() * 5, -8 + Math.random() * 5
			, "Point #" + i));
}

// wrap them in a theme
SimplePointTheme pt = new SimplePointTheme(points, true);

// create label style
Paint textStyle = new Paint();
textStyle.setStyle(Paint.Style.FILL);
textStyle.setColor(Color.parseColor("#0000ff"));
textStyle.setTextAlign(Paint.Align.CENTER);
textStyle.setTextSize(24);

// set some visual options for the overlay
// we use here MAXIMUM_OPTIMIZATION algorithm, which works well with >100k points
SimpleFastPointOverlayOptions opt = SimpleFastPointOverlayOptions.getDefaultStyle()
		.setAlgorithm(SimpleFastPointOverlayOptions.RenderingAlgorithm.MAXIMUM_OPTIMIZATION)
		.setRadius(7).setIsClickable(true).setCellSize(15).setTextStyle(textStyle);

// create the overlay with the theme
final SimpleFastPointOverlay sfpo = new SimpleFastPointOverlay(pt, opt);

// onClick callback
sfpo.setOnClickListener(new SimpleFastPointOverlay.OnClickListener() {
	@Override
	public void onClick(SimpleFastPointOverlay.PointAdapter points, Integer point) {
		Toast.makeText(mMapView.getContext()
				, "You clicked " + ((LabelledGeoPoint) points.get(point)).getLabel()
				, Toast.LENGTH_SHORT).show();
	}
});

// add overlay
mMapView.getOverlays().add(sfpo);
```

## Geospatially referenced Lines and Polygons

osmdroid includes two classes that more or less mirror Google Maps v2 API, the `Polyline` and `Polygon`

### Polylines

```java
List<GeoPoint> geoPoints = new ArrayList<>();
//add your points here
Polyline line = new Polyline();
line.setPoints(geoPoints);
line.setOnClickListener(new Polyline.OnClickListener() {
	@Override
	public boolean onClick(Polyline polyline, MapView mapView, GeoPoint eventPos) {
		Toast.makeText(mapView.getContext(), "polyline with " + polyline.getPoints().size() + "pts was tapped", Toast.LENGTH_LONG).show();
		return false;
	}
});
map.getOverlayManager().add(line);
```


### Polygons

```java
List<GeoPoint> geoPoints = new ArrayList<>();
//add your points here
Polygon polygon = new Polygon();
polygon.setFillColor(Color.argb(75, 255,0,0));
geoPoints.add(geoPoints.get(0));    //forces the loop to close
polygon.setPoints(geoPoints);
polygon.setTitle("A sample polygon");

//polygons supports holes too, points should be in a clockwise order
List<List<GeoPoint>> holes = new ArrayList<>();
holes.add(geoPoints);
polygon.setHoles(holes);

map.getOverlayManager().add(polygon);
```

## How much stuff can I put on the map?

| Device         | Markers | Itemized Icon Overlay | Fast Overlay | Polylines | Polygons |
| -------------- | ------- | --------------------- | ------------ | --------- | -------- |
| Samsung S5     | 5k      | 3-6k                  | 100k         |           |


The answer is greatly dependent on what hardware the osmdroid based app is running on. A Samsung S5 (no endorsement intended) ran just fine at 3k icons and was noticeably choppy at 6k icons. Your mileage may vary. X86 Android running on modern hardware will perform great at even higher numbers. However, it's recommended to limit the amount of stuff you're rendering, if at all possible.

If you're also drawing paths, lines, polygons, etc, then this also changes the equation. Drawing multipoint graphics is computationally more expensive and thus negatively affects performance under higher loads. To mitigate performance issues with multipoint graphics, one strategy would be to reduce the number of points handed off to the map engine when at a higher zoom level (numerically lower), then increase the fidelity as the user zoom's in. In effect, you would be clipping the visible data at the map view bounds so that the map view only "knows" about what's on screen and doesn't have to loop through all 10k icons that you want on the map. Although you can give the map view all 10k objects, but every time the map moves or zooms, it will iterate over all 10k items to calculate where to draw them (if at all). Using this mechanism paired with map motion listeners and a database query that supports geographic bounds, you can support a rich experience for users with lots of data and still have reasonable performance.

Reusing drawables for icons will help with memory usage too. 