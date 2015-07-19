## opencv-artifacts-deployer

### Release notes

* Version 0.10.2 - 2015-07-19
  * Changed repo URL.
* Version 0.10.1 - 2015-07-07
  * Moved to Github. Same artifact, updated documentation.
* Version 0.10 - 2015-01-14
  * First version.

### The problem

In order to build a javacv application you declare a depencency to org.bytedeco:javacv. This is sufficient to build the
application, but not to run it. If you try to run it you get the error:

```
Exception in thread "main" java.lang.UnsatisfiedLinkError: no jniopencv_core in java.library.path
```

The reason to this is that the opencv jar files needed in runtime is not declared as maven dependencies. The currently
existing way of solving that is to add them to your classpath manually.  I find this inconvenient and this is the
reason I created this module.

The missing files are:

```
artoolkitplus-<version>-<platform>.jar
ffmpeg-<version>-<platform>.jar
flandmark-<version>-<platform>.jar
flycapture-<version>-<platform>.jar
libdc1394-<version>-<platform>.jar
libfreenect-<version>-<platform>.jar
opencv-<version>-<platform>.jar
videoinput-<version>-<platform>.jar
```

 This module is not the Maven way, but it is a reasonable way I found to create a repeatable deployment
 procedure of the opencv jar files that are not declared as maven dependencies. The proper way in the long run is
 to make the opencv runtime jars maven artifacts as well. This is currently a well known problem and for what I
 understand there is nothing stopping anyone for helping out with that. Until you, I or someone else fix that,
 this is a workaround.

 I used this module to deploy these jars in my repository.

### Maven usage in a javacv application

Usage example:

```
 <dependency>
    <groupId>org.bytedeco</groupId>
    <artifactId>javacv</artifactId>
    <version>0.10</version>
 </dependency>
 <dependency>
     <groupId>se.marell</groupId>
     <artifactId>opencv-macosx-x86_64</artifactId>
     <version>2.4.10-0.10</version>
     <scope>runtime</scope>
 </dependency>
 ...
 <repository>
   <id>marell</id>
   <url>http://marell.se/nexus/content/repositories/releases/</url>
 </repository>
```

The above example assumes you want to run on macosx and only use functionality from the "opencv" library. If you need to
support more platforms, add more dependencies:

```
<dependency>
    <groupId>se.marell</groupId>
    <artifactId>opencv-linux-x86_64</artifactId>
    <version>2.4.10-0.10</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>se.marell</groupId>
    <artifactId>opencv-macosx-x86_64</artifactId>
    <version>2.4.10-0.10</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>se.marell</groupId>
    <artifactId>opencv-windows-x86_64</artifactId>
    <version>2.4.10-0.10</version>
    <scope>runtime</scope>
</dependency>
```

Or all artifacts (for linux-x86_64 only):

```
<dependency>
    <groupId>se.marell</groupId>
    <artifactId>artoolkitplus-macosx-x86_64</artifactId>
    <version>2.3.1-0.10</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>se.marell</groupId>
    <artifactId>ffmpeg-macosx-x86_64</artifactId>
    <version>2.5.1-0.10</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>se.marell</groupId>
    <artifactId>flandmark-macosx-x86_64</artifactId>
    <version>1.07-0.10</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>se.marell</groupId>
    <artifactId>flycapture-macosx-x86_64</artifactId>
    <version>2.7.3.13-0.10</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>se.marell</groupId>
    <artifactId>libdc1394-macosx-x86_64</artifactId>
    <version>2.2.2-0.10</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>se.marell</groupId>
    <artifactId>libfreenect-macosx-x86_64</artifactId>
    <version>0.5.1-0.10</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>se.marell</groupId>
    <artifactId>opencv-macosx-x86_64</artifactId>
    <version>2.4.10-0.10</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>se.marell</groupId>
    <artifactId>videoinput-macosx-x86_64</artifactId>
    <version>0.200-0.10</version>
    <scope>runtime</scope>
</dependency>
```

### Deploying your own opencv libraries

If you do not want to use the artifacts in my repository, you can check out the code of this module and deploy these
jars in your repository, local or remote.

```
$ mvn -Dplatform.dependencies=true deploy
```

I'm not sure whether setting platform.dependencies is needed or not, because it seems like the jars for all platform
seem to download regardless if I set it or not. But the javacv documentation says that it should be set in order to
download these jars:

```
...
opencv-2.4.10-0.10-linux-x86_64.jar
opencv-2.4.10-0.10-macosx-x86_64.jar
opencv-2.4.10-0.10-windows-x86_64.jar
...
```

The module creates these artifacts:

```
...
se.marell:opencv-linux-x86_64:2.4.10-0.10
se.marell:opencv-macosx-x86_64:2.4.10-0.10
se.marell:opencv-windows-x86_64:2.4.10-0.10
...
```

and installs the artifacts (including self) to your local repo and deploys the same artifacts to your remote repo.

The deploy assumes there is a server "deployment" with a configured username and password with permissions
to deploy to this repo. Modify the repo url i pom.xml first.

If you are happy with installing the artifacts in your local mven repository, just run mvn install instead of deploy.

/Daniel Marell