##Introduction

This page is to list some of the more important guidelines for developers contributing code to osmdroid.  This is not intended to strictly enforce a particular style - more to keep the code internally consistent.

###General guidelines

 * Use spaces for intending, not tabs (**recent change**)
 * Make variables final where possible
 * Prefix member variables with m
 * Prefix method arguments with p
 * Use org.slf4j.Logger, not android.util.Log
 * Use 100 character line width
 * Use final wherever possible, especially parameters
 * Eclipse formatting and clean-up XML settings files (both of which cover most of the above) are included in the repository. Import them, and use them to make your life easier!

###Guidelines for osmdroid-android project

osmdroid-android references android.jar but the unit tests don't run on an Android device.  This means that the unit tests cannot invoke any Android methods.  In order to avoid this, proxies are used in the most common cases:

 * Use org.osmdroid.ResourceProxy, not Context.getResource
 * Use org.slf4j.Logger, not android.util.Log

Those are the main less obvious guidelines.  In general, try and follow the existing style.