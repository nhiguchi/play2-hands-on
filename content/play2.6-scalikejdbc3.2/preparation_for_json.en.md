---
title: JSON API
---

If the front end is AngularJS or a smartphone app, the server side must provide an API that returns JSON. For the CRUD processing of user information created so far, implement JSON-based Web API using the JSON support function of Play2.

## Create a controller template


Create a `JsonController` class in the` controllers` package with the following contents. Import statements are included in advance, to use ScalikeJDBC and Play2 JSON support.

```scala
package controllers

import play.api.mvc._
import play.api.libs.json._
import play.api.libs.functional.syntax._
import javax.inject.Inject
import scalikejdbc._
import models._

class JsonController @Inject()(components: ControllerComponents)
  extends AbstractController(components) {

  /**
   * Show user list
   */
  def list = TODO

  /**
   * Register user
   */
  def create = TODO

  /**
   * Update user
   */
  def update = TODO

  /**
   * Delete User
   */
  def remove(id: Long) = TODO
}
```

Unlike `UserController`, `JsonController` does not use the internationalization function in the template, hence `ControllerComponents` is used as DI and `AbstractController` class is inherited.

> **POINT**
>
> * Import statement`play.api.libs.json._` is necessary to use the JSON support function of Play2.

## Define Routing

Add following lines in `conf/routes` file.

```
# JSON API
GET         /json/list              controllers.JsonController.list
POST        /json/create            controllers.JsonController.create
POST        /json/update            controllers.JsonController.update
POST        /json/remove/:id        controllers.JsonController.remove(id: Long)
```
