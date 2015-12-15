# Building OsmDroid from the command line/bash
 * Install the Android SDK and update it
 * Set a system environment variable for `ANDROID_HOME`, pointing to your Android SDK install location. Windows (My Computer, properties, advanced, environment variables...), Linux `export ANDROID_HOME=(path to android sdk install)`
 * Install Maven. http://maven.apache.org/download.html (you need at least version 3.2.1). You'll also want to add Maven to the system path. Windows `set PATH=$PATH%;(path to maven/bin)`. Linux `export PATH=$PATH:(path to maven/bin)`
 * Checkout the osmdroid source from git to a local folder. (`git clone https://github.com/osmdroid/osmdroid.git`)
 * Download Maven Android SDK dependency using [Maven Android SDK Deployer](https://github.com/simpligility/maven-android-sdk-deployer).  Follow instructions to deploy android sdk package to local repository. (Suggest `mvn install -fn`)
 * From the OsmDroid check out location, run the following `mvn clean install`. You will need a device or emulator running.

The project should then build successfully.



# Building OsmDroid from Eclipse
 * Follow the above instructions for command line builds first.
 * Install Maven support for Eclipse from Eclipse Marketplace. Search for "android m2e" and install "Android Connector for Maven". http://rgladwell.github.io/m2e-android/
 * In Eclipse Import -> Maven -> Existing Maven project. Import all the osmdroid projects.
 * Right click on osmdroid-parent project. Click Run as --> Maven install.

# Building OsmDroid using Android Studio
Note, this config isn't 100% supported due to Android Studio and Intellij's lack of support for maven based Android builds.

 * Follow the above instructions for command line builds first.
 * Ensure you can build from the command line first!
 * Start up Android Studio and import the project in your checkout_folder/OpenStreetMapViewer


# Building OsmDroid from NetBeans
 * Follow the above instructions for command line builds first.
 * As long as the environment variable for ANDROID_HOME is set, it should build by right clicking on "OSMDroid parent", then "Clean and Build"

