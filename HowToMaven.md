##Introduction

This Wiki is migrated from [Google Code](https://code.google.com/p/osmdroid/wiki/HowToMaven).

How to use in Maven projects and how to build osmdroid using Maven.

##How to use osmdroid in your Maven project

If your project already uses Maven and you want to include osmdroid then it's as simple as

```xml
<dependency>
  <groupId>org.osmdroid</groupId>
  <artifactId>osmdroid-android</artifactId>
  <version>4.2</version>
</dependency>
```

You will also need [Maven Android SDK Deployer](https://github.com/mosabua/maven-android-sdk-deployer) in order to get the Google dependencies into your local repository.

##How to use osmdroid in your non-Maven project

If you are not yet using Maven you can include osmdroid by downloading from the link on the home page or directly from [Maven Central](https://oss.sonatype.org/content/groups/public/org/osmdroid/) and then include the jar in your libs folder.


##How to build osmdroid using Eclipse

 * (optional) Install Maven. http://maven.apache.org/download.html
 * Install Maven support for Eclipse from Eclipse Marketplace. Search for "android m2e" and install "Android Connector for Maven". http://rgladwell.github.io/m2e-android/
 * Checkout the osmdroid source from svn to a local folder.
 * In Eclipse Import -> Maven -> Existing Maven project. Import all the osmdroid projects.
 * Download Android SDK dependency using [Android SDK Deployer](https://github.com/mosabua/maven-android-sdk-deployer).  Follow instructions to deploy android sdk package to local repository.
 * Right click on osmdroid-parent project. Click Run as --> Maven install.

The project should then build successfully.

##How to build osmdroid using Android Studio

Follow the instructions for [Importing Project from Maven Model](http://www.jetbrains.com/idea/webhelp/importing-project-from-maven-model.html).