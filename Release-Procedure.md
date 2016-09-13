## Pre-release steps

Edit gradle.properties and update the version information (android.versionCode and pom.version). Commit the change and tag with git.

Use the tag name: 'osmdroid-parent-{version}' to keep in line with the previous releases.

## Build and publish to nexus (maven central)

 - Edit gradle.properties and set your credentials for nexus. `NEXUS_USERNAME` and `NEXUS_PASSWORD`
 - Set the Android signing certificate passwords and whatnot (JKS file)

```
./gradlew clean
./gradlew install -Pprofile=sources,javadoc
./gradlew publishArtifacts -Pprofile=sources,javadoc
```
Edit gradle.properties and remove your credentials and JKS references.

**TODO** Update once gradle fury has been updated to support encryption and local.properties settings


## Prepare distribution package for Github
```
./gradlew distZip -Pprofile=dist
```

## Upload release zip on Github

https://github.com/osmdroid/osmdroid/releases/new

Output zip is at osmdroid-dist/build/distributions/

