# Building osmdroid from the command line
osmdroid's current, authoritative, build environment is with Maven. We also maintain Gradle build files to make life a bit easier with Android Studio. As of Jan 2015, all of the Maven projects have been migrated over to Gradle. This means you can build the whole thing with Maven or Gradle.

## Building with Maven
 * Download the latest Android SDK and update the packages. The command line build requires the **Google-Play-Services** package.
   * Create the **ANDROID_HOME** path variable, pointing to your Android SDK install location.
     * Windows: Right Click on My Computer, select Properties, Select Environment Variables, and create the variable ANDROID_HOME with the location of your Android SDK
     * Linux: `export ANDROID_HOME=(path to Android SDK install)`
 * Download [Maven](http://maven.apache.org/download.html) (Project requires at least version 3.2.1)
   * Add the **maven/bin** to your **PATH**.
     * Windows: `set PATH=$PATH%;(path to maven/bin)`.
     * Linux: `export PATH=$PATH:(path to maven/bin)`
 * Download [Maven Android SDK Deployer](https://github.com/simpligility/maven-android-sdk-deployer). This project will make available the necessary Android dependencies for a maven build.
   * `git clone https://github.com/simpligility/maven-android-sdk-deployer`
   * Navigate to Maven Android SDK Deployer folder and run the command **mvn install -fn**
     * This command will try to make available all Android packages for Maven, do not worry about the errors for the platforms we are not using.
 * Checkout the osmdroid source from git to a local folder.
   * `git clone https://github.com/osmdroid/osmdroid.git`
 * Navigate to the osmdroid folder and run the command **mvn clean install**. NOTE: You will need a device or emulator running.

You have now completed all steps to compile osmdroid using Maven

## Building with Gradle
 * Checkout the osmdroid source from git to a local folder.
   * `git clone https://github.com/osmdroid/osmdroid.git`
 * Navigate to the osmdroid folder and run the command **gradle clean build**. 

You have now completed all steps to compile osmdroid using Gradle

# Building osmdroid from Eclipse
 * Follow the above instructions for command line builds first.
 * Install Maven support for Eclipse from Eclipse Marketplace. Search for "android m2e" and install "Android Connector for Maven". http://rgladwell.github.io/m2e-android/
 * In Eclipse Import -> Maven -> Existing Maven project. Import all the osmdroid projects.
 * Right click on osmdroid-parent project. Click Run as --> Maven install.

# Building osmdroid using Android Studio
Note, this config isn't 100% supported due to Android Studio and Intellij's lack of support for maven based Android builds.

 * Follow the above instructions for command line builds first.
 * Ensure you can build from the command line first!
 * Start up Android Studio and import the project in your checkout_folder/OpenStreetMapViewer


# Building osmdroid from NetBeans
 * Follow the above instructions for command line builds first.
 * As long as the environment variable for ANDROID_HOME is set, it should build by right clicking on "osmdroid parent", then "Clean and Build"