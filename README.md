# Quality Tools for Android

<img src="https://raw.github.com/stephanenicolas/Quality-Tools-for-Android/master/gfx/bugdroid-duke-armor.jpg" 
width="350px" />

This is an Android sample app + tests that will be used to work on various project to increase the quality of the Android platform.

The idea is that Android programming is still in its infancy compared to the Java world. 
The Android community needs more robustness in Android apps and it looks like a good idea to build on the Java world experience and use its best tools for Quality Analysis.

We want to provide a full featured industrial development environment that can be used to create more
robust projects on Android, by using any of the most interesting and popular technologies.

Here are [some slides](https://speakerdeck.com/stephanenicolas/devoxx-2013-fr-beef-you-android-apps-using-java-tools) to present Quality Tools for Android.

# Already integrated :

* Standard Android testing framework and code coverage using emma, reported in Sonar. That also covers robotium, easy mock and mockito technologies.
* Robolectric testing framework and code coverage using Cobertura, reported in Sonar.
* UI Automator testing through a new android maven plugin goal (to be released in android-maven-plugin-3.5.2) and result in sonar.
* Configuration works out of the box in eclipse
* Lint integration via Maven.
* PMD, findbugs, checkstyle integration via Maven, reported in Sonar.
* [lint android maven lint](https://github.com/lewisd32/lint-maven-plugin) integration (pom checker)
* Monkey testing is now automated and reported in Sonar.
* Add [classycle](http://classycle.sourceforge.net/) support, to enforce architectural constraints, through [classycle maven plugin](https://github.com/hcoles/classycle-maven-plugin)
* [Spoon from square](https://github.com/square/spoon), including screenshots during tests.
*
* [maven-android-sdk-deployer](https://github.com/mosabua/maven-android-sdk-deployer) to deliver android jars (including uiautomator)
* [sonar android lint plugin](https://github.com/jeromevdl/sonar-android-lint-plugin) 
* [FEST Android](https://github.com/square/fest-android).
* Jacoco [offline instrumentation](https://github.com/jacoco/jacoco/pull/64#issuecomment-12150910) for both robolectric and standard junit tests.
* Testing  technologies integrated : 
    * Standard Android tests   
        * easymock
        * mockito
        * mockwebserver
        * robotium
        * fest-android
    * robolectric tests
        * hamcrest 
        * easymock
        * mockito
* [Screenshot lib](https://github.com/rtyley/android-screenshot-lib) works during UIAutomator tests.
* support for [Travis CI](https://travis-ci.org/stephanenicolas/Quality-Tools-for-Android). 
* Build Status on Travis: [![Build Status on Travis:](https://api.travis-ci.org/stephanenicolas/Quality-Tools-for-Android.png)](https://api.travis-ci.org/stephanenicolas/Quality-Tools-for-Android)



# What is missing (TODO/INTEGRATE) : 

1. get aggregated tests and code coverage for all testing technologies inside a nice Sonar dashboard for Android.
2. add support for monkey runner through maven
3. add calabash support.

# Usage

This section describes how to build & test the project using those different testing technologies.

Please note that this project is under active development.
Some goals may require a snapshot version of the maven android plugin available 
on [sonatype snapshot repo](https://oss.sonatype.org/content/repositories/jayway-snapshots/).

## Install Android Latest SDK through Android SDK Manager

This can be done graphically, or [via command line (for CI servers)](http://stackoverflow.com/q/4681697/693752).

## Install the Android SDK through maven-android-sdk-deployer

As it takes time to get android jars in maven central, including android UI automator jars in maven central,
we recommend to use [maven-android-sdk-deployer](https://github.com/mosabua/maven-android-sdk-deployer) to obtain android artefacts.
This step can also be executed on a CI server.

```bash
#install Android SDK 17 local files to local maven repo  
git clone git@github.com:mosabua/maven-android-sdk-deployer.git
cd maven-android-sdk-deployer/
mvn install -P 4.2
#Add V4 support library (to use FEST Android)
cd extras/compatibility-v4/
mvn install
```

## Standard Android testing APIs and code coverage using emma

To build the sample project and run the sample app on a plugged rooted device / running emulator : 

```bash
# in parent folder
mvn clean install -P emma
mvn sonar:sonar -P emma
```

you will get tests results in : target/surefire-reports/.
you will get tests coverage in : target/emma/.

Here is the result in sonar : 
<img src="https://raw.github.com/stephanenicolas/Quality-Tools-for-Android/master/gfx/screenshot-sonar-emma-config.png" width=450px/>

You may need to restart adb as root to be able to pull the emma coverage file. In a terminal, type :
```bash
adb root
```

## Robolectric and code coverage using cobertura

```bash
# in parent folder
mvn clean cobertura:cobertura -P cobertura
mvn sonar:sonar -P cobertura
```
Here is the result in sonar : 
<img src="https://raw.github.com/stephanenicolas/Quality-Tools-for-Android/master/gfx/screenshot-sonar-robolectric-config.png" width=450px/>


## Unified code coverage for both Robolectric and standard Android Junit tests via Jacoco

Using offline instrumentation of Jacoco, it is possilbe to completly replace emma by jacoco for instrumentation.
This allows to get both robolectric and standard tests code coverage inside the same project dashboard with sonar.

* Robolectric are considered standard unit tests.
* standard Android Junit tests are considered as standard integration tests.
This makes sense as Robolectric tests mock an android platform and can be considered more "unit" tests thant standard android tests because the latter needs a real android platform and relies on networking, disk, locale, etc. 
It would be better to be able to give names to the test suites inside the widget, and even to add more test suites, for instance to add UI testing (black box testing) or monkey testing. 

```bash
# in parent folder
mvn clean install -P jacoco
mvn sonar:sonar -P jacoco
```
Here is the result in sonar : 
<img src="https://raw.github.com/stephanenicolas/Quality-Tools-for-Android/master/gfx/screenshot-sonar-jacoco.png" width=450px/>


## UI Automator 


```bash
# in parent folder
mvn clean install -P uiautomator
mvn sonar:sonar -P uiautomator
```
Here is the result in sonar : 
<img src="https://raw.github.com/stephanenicolas/Quality-Tools-for-Android/master/gfx/screenshot-uiautomator.png" width=450px/>

## Spoon from Squareup

```bash
# in parent folder
mvn clean install -P spoon

#then browse to android-sample-tests/target/spoon-output/index.html
```

Here is the result in a browser : 
<img src="https://raw.github.com/stephanenicolas/Quality-Tools-for-Android/master/gfx/screenshot-spoon.png" width=450px/>

## Monkey testing

Monkey is part of Android SDK and allows to harness Application UI and test their robustness.
We contributed to a neww maven android plugin goal to use monkey automatically and get reports in junit format.

The results can be displayed inside sonar and will appear as normal unit tests.

```bash
# in parent folder
mvn clean compile -P monkey
mvn sonar:sonar -P monkey
```
Here is the result in sonar : 
<img src="https://raw.github.com/stephanenicolas/Quality-Tools-for-Android/master/gfx/screenshot-sonar-monkey.png" width=450px/>


## Package cycles check via classycle

You will need a JDK 1.7 for this profile to work correctly.

```bash
# in parent folder
mvn clean compile -P cycle
```

Will check package cycles (also called package tangling in Sonar) and check the build if given cycles are detected.
[Classycle](http://classycle.sourceforge.net/) lets you define architectural constraints that can be checked automatically. 

Depedency definition files are very simple to edit. Here is an example : 
````
show allResults

###define packages / groups of packages of interest

## layers
[ui] = com.octo.android.sample.ui.*
[other] = com.octo.android.sample.* excluding [ui]

###check layers integrity
check [other] independentOf [ui]
````
## Robolectric development in eclipse

To enable Robolectric development in this configuration. In eclipse, switch to maven profile "cobertura" in maven settings on the main app.

# Thanks to
 * [OCTO Technology](http://www.octo.com/en) to provide us with free time to work on that project.
 * Henri Treblay from [OCTO Technology](http://www.octo.com/en) for having ported [EasyMock](http://www.easymock.org/) to Android.
 * Thanks to [Jayway](http://www.jayway.com/blog) for their [Android Maven Plugin](http://code.google.com/p/maven-android-plugin/).
 * Thanks to [Sonar Source](http://www.sonarsource.org/) for supporting this effort, especially for this [project's configuration](https://github.com/SonarSource/sonar-examples/tree/master/projects/android).
 * Thanks to  Jake Wharton and Pierre-Yves Ricaud for mentionning FEST-Android.

## Quality Tools for Android in the news !!
* [Android Weekly issue #55 !] (http://androidweekly.net/#latest-issue)
* [Android Dev Weekly, issue #49 !] (http://androiddevweekly.com/2013/03/11/Issue-49.html?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed%3A+AndroidDevWeekly+%28%23AndroidDev+Weekly%29)
* Our presentation at [Devoxx France 2013](https://speakerdeck.com/stephanenicolas/devoxx-2013-fr-beef-you-android-apps-using-java-tools).
