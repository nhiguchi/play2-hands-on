---
title: Setup DB 
---

## Preparing the tool project

Download the [h2.zip](../downloads/h2.zip) file and extract into the same directory of `play2-hands-on` project.

```
+-/play2-hands-on
|   |
|   +-/app
|   |
|   +-/conf
|   |
|   +-...
|
+-/h2
    |
    +-create.sql
    |
    +-data.mv.db
    |
    +-...
```

## Start H2

**Windows**

First, start H2 database by double-clicking `h2/start.bat`. The table with the following schema will be created in the database.

**Mac**

```
cd h2/
sh start.sh
```
※Do not close the terminal tab after the startup.

![ER diagram used in application](../images/play2.6-scalikejdbc3.2/er_diagram.png)

## Autogenerate the models

In ScalikeJDBC, it is necessary to prepare a model class in order to use a type-safe API and it can be automatically generated from the DB schema by using the generator provided by ScalikeJDBC as an sbt plug-in.

Enable the ScalikeJDBC automatic generation tool in the `play2-hands-on` project. Add the following settings to `project / plugins.sbt`.

```scala
libraryDependencies += "com.h2database" % "h2" % "1.4.196"
addSbtPlugin("org.scalikejdbc" %% "scalikejdbc-mapper-generator" % "3.2.2")
```

Also, create a file called `project / scalikejdbc.properties` with the following contents.

```properties
# ---
# jdbc settings

jdbc.driver=org.h2.Driver
jdbc.url=jdbc:h2:tcp://localhost/data
jdbc.username=sa
jdbc.password=sa
jdbc.schema=PUBLIC

# ---
# source code generator settings

generator.packageName=models
# generator.lineBreak: LF/CRLF
generator.lineBreak=LF
# generator.template: interpolation/queryDsl
generator.template=queryDsl
# generator.testTemplate: specs2unit/specs2acceptance/ScalaTestFlatSpec
generator.testTemplate=ScalaTestFlatSpec
generator.encoding=UTF-8
# When you're using Scala 2.11 or higher, you can use case classes for 22+ columns tables
generator.caseClassOnly=true
# Set AutoSession for implicit DBSession parameter's default value
generator.defaultAutoSession=true
# Use autoConstruct macro (default: false)
generator.autoConstruct=false
# joda-time (org.joda.time.DateTime) or JSR-310 (java.time.ZonedDateTime java.time.OffsetDateTime)
generator.dateTimeClass=java.time.OffsetDateTime
```

At last, add the following to `buils.sbt`. You can now use the `scalikejdbcGen` task in this project.

```scala
enablePlugins(ScalikejdbcPlugin)
```

Execute the following command in the root directory of the `play2-hands-on` project to autogenerate the models.

```
sbt "scalikejdbcGenAll"
```

`app/models` package will get generated in `play2-hands-on` project.

## DB connection setup

Add the following settings to `conf/application.conf` file in `play2-hands-on` project. In addition to database connection settings, we will also add settings to link Play and ScalikeJDBC.

```properties
# Database configuration
# ~~~~~
# You can declare as many datasources as you want.
# By convention, the default datasource is named `default`
db.default.driver=org.h2.Driver
db.default.url="jdbc:h2:tcp://localhost/data"
db.default.username=sa
db.default.password=sa

# ScalikeJDBC original configuration
#db.default.poolInitialSize=10
#db.default.poolMaxSize=10
#db.default.poolValidationQuery=

scalikejdbc.global.loggingSQLAndTime.enabled=true
scalikejdbc.global.loggingSQLAndTime.singleLineMode=false
scalikejdbc.global.loggingSQLAndTime.logLevel=debug
scalikejdbc.global.loggingSQLAndTime.warningEnabled=true
scalikejdbc.global.loggingSQLAndTime.warningThresholdMillis=5
scalikejdbc.global.loggingSQLAndTime.warningLogLevel=warn

play.modules.enabled += "scalikejdbc.PlayModule"
# scalikejdbc.PlayModule doesn't depend on Play's DBModule
play.modules.disabled += "play.api.db.DBModule"
```
