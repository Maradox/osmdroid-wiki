

#osmdroid-android (map engine)

If your project already uses Maven and you want to include osmdroid then it's as simple as

```xml
<dependency>
  <groupId>org.osmdroid</groupId>
  <artifactId>osmdroid-android</artifactId>
  <version>5.0.1</version>
  <type>aar</type>
</dependency>
```

If you're new to using maven with android, you'll probably want to either follow our [guide](https://github.com/osmdroid/osmdroid/wiki/How-to-build-OsmDroid-from-source).

#osmdroid-thirdparty (google maps and bing support)
osmdroid also has an optional library to provide addition features of 3rd party organizations.

```xml
<dependency>
  <groupId>org.osmdroid</groupId>
  <artifactId>osmdroid-third-party</artifactId>
  <version>5.0.1</version>
  <type>aar</type>
</dependency>
```