##Introduction


##How to use osmdroid in your Maven project

If your project already uses Maven and you want to include osmdroid then it's as simple as

```xml
<dependency>
  <groupId>org.osmdroid</groupId>
  <artifactId>osmdroid-android</artifactId>
  <version>5.0.1</version>
  <type>aar</type>
</dependency>
```

You will also need [Maven Android SDK Deployer](https://github.com/simpligility/maven-android-sdk-deployer) in order to get the Google dependencies into your local repository.

