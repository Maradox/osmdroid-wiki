This document has the definitive guide for using Osmdroid without a network connection.

Most of this was written by @MKer (osmbounspack lead)

# What are the support storage formats?

Osmdroid provides out of the box support for several different types of offline map tile caches.

 * OsmDroid's flavor of a sqlite database
 * MBTiles
 * Zip
 * GEMF
 * GeoPackage (still under development)

# How can I create them?

Osmdroid comes with a tool to enable you to download tiles and storage them for offline use called the `OSM Map Tile Packager`.

# What legal things do I need to know?

Many map sources have disclaimers and legal statements that specifically state to NOT cache and rehost their map imagery. Some may have wording to disallows you to use their map imagery offline or to download large portions of the world.  Make sure you read the fine print!! Osmdroid takes no responsibility for map imagery misuse. That's between you and the imagery owner.