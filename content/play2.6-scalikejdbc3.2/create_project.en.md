---
title: Creating Project
---

## Install sbt

We will first install sbt, building tool for Scala.

**Windows**

Download the installer from following link and install the sbt tool.

https://github.com/sbt/sbt/releases/download/v1.1.4/sbt-1.1.4.msi

**Mac**

Install from Homebrew.

```
brew update
brew install sbt@1
```

After installing sbt from above commands, check if sbt version is >=  1.1.4.

```
sbt sbtVersion
[info] Loading project definition from /Users/takako.shimamoto/project
[info] Set current project to takako-shimamoto (in build file:/Users/takako.shimamoto/)
[info] 1.1.4
```

## Creating new project

Run the following command from command prompt.

```
sbt new playframework/play-scala-seed.g8 --branch 2.6.x
```

You will be asked for the project name. Here, the project name is `play2-hands-on`, and the other items will be created with the default values.

![Creating Project](../images/play2.6-scalikejdbc3.2/create_project.png)

We will add the following settings in `build.sbt`to use ScalikeJDBC as ORM. The `build.sbt` file is located in `play2-hands-on` directory.

```scala
name := """play2-hands-on"""
organization := "com.example"

version := "1.0-SNAPSHOT"

lazy val root = (project in file(".")).enablePlugins(PlayScala)

scalaVersion := "2.12.4"

libraryDependencies += guice
libraryDependencies += "org.scalatestplus.play" %% "scalatestplus-play" % "3.1.2" % Test

// ↓↓↓↓start↓↓↓↓
libraryDependencies ++= Seq(
  "com.h2database" % "h2" % "1.4.196",
  "org.scalikejdbc" %% "scalikejdbc" % "3.2.2",
  "org.scalikejdbc" %% "scalikejdbc-config" % "3.2.2",
  "org.scalikejdbc" %% "scalikejdbc-play-initializer" % "2.6.0-scalikejdbc-3.2"
)
// ↑↑↑↑end↑↑↑↑

// Adds additional packages into Twirl
//TwirlKeys.templateImports += "com.example.controllers._"

// Adds additional packages into conf/routes
// play.sbt.routes.RoutesKeys.routesImport += "com.example.binders._"
```

## Startup

Move into the `play2-hands-on` directory and run the following command.

```
sbt run
```

You can confirm the below image from browser http://localhost:9000/ . 

![Play2 Welcome Page](../images/play2.6-scalikejdbc3.2/welcome.png)

Check if `Welcome to Play!` is written on the page.

> **POINT**
>
> * `sbt run` process run as hot deploy so changes made can be reflected at real time.
> * To stop the `sbt run`, use CTRL+D.
> * If you make too many changes while running `sbt  run`, it may lead to insufficient heap size and can end the process or result into error response.
> * If above results into ending the sbt process, you can restart the process by ruinng the `sbt run` command
> * If above results into an error response then once close the command prompt and again run the `sbt run` command.