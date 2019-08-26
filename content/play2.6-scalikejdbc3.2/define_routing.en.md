---
title: Defining Routing
---

## Preparation for Bootstrap

Project created by `sbt new` command creates file `app/views/main.scala.html` as default layout template. We will add CSS and JavaScript files to be used by Bootstrap.

```html
@(title: String)(content: Html)

<!DOCTYPE html>
<html lang="en">
  <head>
    <title>@title</title>
    <link rel="stylesheet" media="screen" href="@routes.Assets.versioned("stylesheets/main.css")">
    <link rel="shortcut icon" type="image/png" href="@routes.Assets.versioned("images/favicon.png")">
    @* ↓↓↓↓add from here↓↓↓↓ *@
    <link rel="stylesheet" media="screen" href="//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap-theme.min.css">
    <link rel="stylesheet" media="screen" href="//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css">
    <script src="//netdna.bootstrapcdn.com/bootstrap/3.1.1/js/bootstrap.min.js" type="text/javascript"></script>
    @* ↑↑↑↑end↑↑↑↑ *@
  </head>
  <body>
    @content
    <script src="@routes.Assets.versioned("javascripts/main.js")" type="text/javascript"></script>
  </body>
</html>
```

Also, to prevent read access of above mentioned external CDN files such as CSS and JavaScripts,  `Content-Security-Policy` header returns `default-src 'self'`. Add following setting in `conf/application` file to prevent the read access.

```
play.filters.headers.contentSecurityPolicy=null
```

## Create a controller template

Create `UserController` class in `controllers` package.

```scala
package controllers

import play.api.mvc._
import play.api.data._
import play.api.data.Forms._
import javax.inject.Inject
import scalikejdbc._
import models._

class UserController @Inject()(components: MessagesControllerComponents)
  extends MessagesAbstractController(components) {

  /**
   * display list
   */
  def list = TODO

  /**
   * display edit page
   */
  def edit(id: Option[Long]) = TODO

  /**
   * create account
   */
  def create = TODO

  /**
   * update account
   */
  def update = TODO

  /**
   * delete account
   */
  def remove(id: Long) = TODO

}
```

`@Inject` annotation and 2 arguments has been added into the constructor to utilize the DI function of Google Guice(introduced in Play2.4). 

The above controller uses DI function for the following purposes.


-`MessagesControllerComponents` ... to use the internationalization function of Play (The application created by this hands-on does not use the internationalization function, but the helper used in the template described later requires an instance of i18n)

Also, we will inherit `MessagesAbstractController` class to actually take advantage of database access and internationalization features within the controller.

> **POINT**
>
> * `@Inject`is annotation for DI.
> * To use Internationalization feature we DI `MessagesControllerComponents` and inherit `MessagesAbstractController` class.
> * `TODO`methods are represented as `Action not implemented yet.` and an `501 NOT_IMPLEMENTED` response is returned.

## Routing setup

Requests sent from the client are routed to the controller's method according to the settings defined in `conf/routes`.
We will add the following settings

```bash
# Mapping to /user/list
GET     /user/list                  controllers.UserController.list
# Mapping to /user/edit or /user/edit?id=<number>
GET     /user/edit                  controllers.UserController.edit(id: Option[Long] ?= None)
# Mapping to /user/create
POST    /user/create                controllers.UserController.create
# Mapping to /user/update
POST    /user/update                controllers.UserController.update
# Mapping to /user/remove/<number>
POST    /user/remove/:id            controllers.UserController.remove(id: Long)
```

> **POINT**
>
> * If we omit the argument type in the mapping definition, it will be defined as `String`.
> * If we write Japanese comments in routes files then it results into a compiler error.
