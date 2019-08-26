---
title: Tips
---

## Play2

* Setting VM options while running play
  * Use environment variable `JAVA_OPTS` to set JVM startup options such as heap settings and proxy settings
* Local repository used by Play
  * Earlier versions of Play had its own local repository and cache under the `PLAY_HOME/repository` directory, but Play 2.3 uses the SBT standard` HOME/.ivy2` directory.
* Official Play documentation can be found here https://www.playframework.com/documentation/2.3.x/Home

## Slick

* Slick Reference https://github.com/bizreach/slick-reference

## ScalaIDE

* Scala IDE has an updated site according to the versions of Eclipse and Scala
  * Use ScalaIDE official page for latest reference http://scala-ide.org/download/current.html

## IntelliJ IDEA

* KeyBoard Shortcuts
  * (Windows) http://www.jetbrains.com/idea/docs/IntelliJIDEA_ReferenceCard.pdf
  * (Mac) http://www.jetbrains.com/idea/docs/IntelliJIDEA_ReferenceCard_Mac.pdf
* Changing IDEA theme
  * [Appearance]→[Theme]→[change theme from "Default" to "Darcula"]
* Changing classPath in IntelliJ
  * If JDK path is not selected then Java shows an error to not being able to recognize the basis types like String 
    * Select JDK from installed version [File]→[Project Structure...]
![Select JDK](../images/play2.3-slick2.1/idea_jdk.png)
  * If classPath doesn't change after fixing `build.sbt` then Invalidate IntelliJ cache and restart「File」→「Invalidate Caches / Restart...」