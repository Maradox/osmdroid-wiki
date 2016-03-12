#osmdroid-android (the map engine)

If you use [Gradle](http://www.gradle.org) you have to add the following dependency to your `build.gradle` file:

```groovy
dependencies {
    compile 'org.osmdroid:osmdroid-android:5.1@aar'
    //Note as of 5.0, this is no longer needed!  compile 'org.slf4j:slf4j-simple:1.6.1'
}
```

#osmdroid-third-party (google maps and bing support)
osmdroid also has an optional library to provide addition features of 3rd party organizations.

```groovy
dependencies {
    compile 'org.osmdroid:osmdroid-third-party:5.0.1@aar'
    //Note as of 5.0, this is no longer needed!  compile 'org.slf4j:slf4j-simple:1.6.1'
}
```