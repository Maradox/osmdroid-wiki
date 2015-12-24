# A guide for upgrading your app 

## From version 5.0.1 to 5.1

Under development

## From version v4.3 to v5.0.1
Output target is now AAR and the default osmdroid resources are now included (my location icon and whatnot). If you 'borrowed' those icons from osmdroid, you now will no longer need to include them.

If you use any custom map tile sources, a parameter was removed from the constructor so you'll need to adjust accordingly.

If you used a custom overlay manager, that class was interfaced and refactored. Please now extend from DefaultOverlayManager or implement the interface as appropriate.