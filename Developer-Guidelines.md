##Introduction

This Wiki is migrated from [Google Code](https://code.google.com/p/osmdroid/wiki/DeveloperGuidelines).

This page is to list some of the more important guidelines for developers contributing code to osmdroid.  This is not intended to strictly enforce a particular style - more to keep the code internally consistent.

###General guidelines

 * Use spaces for indenting, not tabs (**recent change**)
 * Make variables final where possible
 * Prefix member variables with m
 * Prefix method arguments with p
 * Versions <= 4.3, Use org.slf4j.Logger. Verions > 4.3 use android.util.Log
 * Use 100 character line width
 * Use final wherever possible, especially parameters
 * Eclipse formatting and clean-up XML settings files (both of which cover most of the above) are included in the repository. Import them, and use them to make your life easier!

###Guidelines for osmdroid-android project

osmdroid-android references android.jar but the unit tests don't run on an Android device.  This means that the unit tests cannot invoke any Android methods.  In order to avoid this, proxies are used in the most common cases:

 * Use org.osmdroid.ResourceProxy, not Context.getResource
 * Use android.util.Log (slf4j is no longer used)

Those are the main less obvious guidelines.  In general, try and follow the existing style.

## Building

Make sure you build with the follow commands before opening a pull request. There are several related wiki pages on setting up the development environment (Maven is the only oddball) See https://github.com/osmdroid/osmdroid/wiki/How-to-build-OsmDroid-from-source

```` mvn clean install ````

```` gradle clean build ````
