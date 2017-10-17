# Developer Guidelines

## Introduction

This page is to list some of the more important guidelines for developers **contributing code to osmdroid**.  This is not intended to strictly enforce a particular style - more to keep the code internally consistent.

## General guidelines

 * Use spaces for indenting, not tabs
 * Make variables final where possible
 * Prefix member variables with m
 * Prefix method arguments with p
 * For logging, use android.util.Log. The log tag we use is in IMapView.LOG_TAG
 * Use 100 character line width
 * Use final wherever possible, especially parameters
 * Eclipse formatting and clean-up XML settings files (both of which cover most of the above) are included in the repository. Import them, and use them to make your life easier!

## Documentation

### Wiki and the osmdroid site

Recent change (Oct 2017) - osmdroid now uses a git submodule against the osmdroid github wiki. This helps enforce getting you the most up to date documentation available. The wiki pages are also used for generating the osmdroid site. As a bonus, you can still edit the docs in browser at https://github.com/osmdroid/osmdroid/wiki/.

### Javadocs

 * For ours and your sanity, when adding new features or new methods to osmdroid, please add the `@since <VERSION>` Javadoc tag all new classes and methods.
 * For ours and your sanity, when adding new features or methods to osmdroid, leave some javadocs or inline comments. Working with maps is complex enough. Please leave use from breadcrumbs so that we can understand what's happening.
 * Take credit for your work! Don't be afraid to add your name and/or email address to the Javadoc `@author` tags
 * Since we're using gradle fury, `doc-files` are supported for Javadocs, meaning that if you have a fancy diagram or a silly cat picture that you shop'd up, it can be included and referenced in the source.
 * We are also using graphviz with the UML doclet, meaning that when the javadocs are generated, we have automagic class diagrams

## Guidelines for building and testing changes to osmdroid

Build it! `gradlew clean install`

Test it! `gradle connectedCheck`

More instructions and developer environment here https://github.com/osmdroid/osmdroid/wiki/How-to-build-OsmDroid-from-source
