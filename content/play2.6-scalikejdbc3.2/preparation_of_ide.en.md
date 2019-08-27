---
title: Setup IDE
---

## Install Plugins

This part assumes that Java8 + IntelliJ is already installed. Then, install the plug-in for Scala development in IntelliJ according to the following procedure.

* Click [IntelliJ IDEA]→[Preferences]→[Plugins]→[Install JetBrains plugin...]
* Select [Scala] and click [Download and Install]

※ Play plug-in can be used in IntelliJ Ultimate Edition, it also creates Play project in IntelliJ and adds an editor for editing HTML templates and configuration files.

## Import projects

IntelliJ's Scala plug-in has native support for SBT projects. Click "Import Project" and select the root directory of the Play2 project to import it as an SBT project.

![Import Project(1)](../images/play2.6-scalikejdbc3.2/open_project_intellij1.png)

![Import Project(2)](../images/play2.6-scalikejdbc3.2/open_project_intellij2.png)

The following dialog will be displayed while importing the project. At first, “Project JDK” may be unselected so Click "New ..." and select the directory where JDK is installed, then click "OK".

![Import Project(3)](../images/play2.6-scalikejdbc3.2/open_project_intellij3.png)

If you add a library by editing `build.sbt`, the following message will be displayed in the upper right of the window.

![Refresh Project](../images/play2.6-scalikejdbc3.2/re-import_project.png)

Clicking “Refresh” will re-import the project, and the library will be automatically downloaded over the Internet and added to the classpath. If you select "Enable auto-import", it will be automatically re-imported every time you change the build.sbt.
