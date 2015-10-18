##Introduction

This Wiki is migrated from [Google Code](https://code.google.com/p/osmdroid/wiki/HowToMaven).

How to use in Maven projects and how to build osmdroid using Maven.

##How to use osmdroid in your Maven project

If your project already uses Maven and you want to include osmdroid then it's as simple as

```xml
<dependency>
  <groupId>org.osmdroid</groupId>
  <artifactId>osmdroid-android</artifactId>
  <version>4.3</version>
</dependency>
```

You will also need [Maven Android SDK Deployer](https://github.com/mosabua/maven-android-sdk-deployer) in order to get the Google dependencies into your local repository.
