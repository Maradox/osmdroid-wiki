# Capabilities 

What can you do with osmdroid:

 * osmdroid is a 2D map engine, small, lightweight, with an API similar to Google Maps V1
 * Raster tile based (no vector), with support for numerous [tile providers](Map-Sources) - free or not - most of them based on OpenStreetMap. 
 * Extensible architecture for map tile support with adapters for:
   * [Geopackage](Geopackage-Support)
   * Mapsforge (vector)
   * Basic [WMS support](WMS-Support)
 * Tiles available online/offline/embedded, with pre-caching options
 * [Offline support](Offline-Map-Tiles) using a variety of tile archive types
 * Supports zoom 0 (1:15Mkm) to 29 (1:0.05m) at the equator (inches of screen real estate)
 * Multi-touch support with some basic gestures (pinch zoom, fling, etc)
 * Map can be rotated and oriented in any direction
 * Rich set of Overlays - "things you want to draw on the map": 
   * Markers, Polylines and Polygons - with popup bubbles if needed
   * Compass, minimap, scalebar
   * My current location

And with add-ons:
 * Support for KML and GeoJson (with [OSMBonusPack](https://github.com/MKergall/osmbonuspack/))
 * POI search (with [OSMBonusPack](https://github.com/MKergall/osmbonuspack/))
 * Routing (with [OSMBonusPack](https://github.com/MKergall/osmbonuspack/))
 * Marker clustering (with [OSMBonusPack](https://github.com/MKergall/osmbonuspack/))