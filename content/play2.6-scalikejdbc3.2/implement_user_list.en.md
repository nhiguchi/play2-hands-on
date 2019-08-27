---
title: Implement User List
---

Retrieve all items from the `USERS` table in ascending order of ID and display the user list screen.

## View

Templates are created in the `views` package. Create a `views.user` package under the app directory, and create a` list.scala.html` with the following contents.

```html
@* Arguments of this template *@
@(users: Seq[models.Users])(implicit request: RequestHeader)

@* Import available helpers for templates *@
@import helper._

@* call main.scala.html *@
@main("ユーザ一覧") {

<div>
  <a href="@routes.UserController.edit()" class="btn btn-success" role="button">新規作成</a>
</div>

<div class="col-xs-6">
  <table class="table table-hover">
    <thead>
      <tr>
        <th>ID</th>
        <th>名前</th>
        <th>&nbsp;</th>
      </tr>
    </thead>
    <tbody>
    @* Loop to display user list *@
    @users.map { user =>
      <tr>
        <td>@user.id</td>
        <td><a href="@routes.UserController.edit(Some(user.id))">@user.name</a></td>
        <td>@helper.form(CSRF(routes.UserController.remove(user.id))){
          <input type="submit" value="削除" class="btn btn-danger btn-xs"/>
        }
        </td>
      </tr>
    }
    </tbody>
  </table>
</div>

}
```

An implicit argument called `(implicit request: RequestHeader)` is defined in the first line of the template. This is necessary to use the helper `CSRF` in the template. By default, the filter for CSRF countermeasures is enabled in Play, and when specifying the form destination, we can just specify it as `@ helper.form (CSRF (...))`. We can use the CSRF function that uses tokens(if `CSRF` helper is not defined then an 403 error will occur after submitting the form).

> **POINT**
>
> * The first line of the template describes the arguments received from the controller.
> * We can embed Scala code in the template with `@`.
> * We can write import statements with `@ import`. We can import the standard helpers(functions that output forms, etc.)provided by Play with `@import helper._`.
> * Templates can be commented with `@ * ... * @`
> * Links and form URLs can be generated from routing by writing `@routes ....`
> * The CSRF filter is enabled by default, hence it is require that form destination must be enclosed in `CSRF (...)`.

## Controller

`list` method of `UserController` class is implemented as below.

```scala
def list = Action { implicit request =>
  val u = Users.syntax("u")

  DB.readOnly { implicit session =>
    // retrieve the user list
    val users = withSQL {
      select.from(Users as u).orderBy(u.id.asc)
    }.map(Users(u.resultName)).list.apply()

    // display the list page
    Ok(views.html.user.list(users))
  }
}
```


`val u = Users.syntax (" u ")` is required for each table when using ScalikeJDBC's QueryDSL(DSL for writing SQL in a type-safe manner). If the same method is used for various methods in a class, it may be defined as a field of the class as follows.

```scala
class UserController @Inject()(components: MessagesControllerComponents)
  extends MessagesAbstractController(components) {

  private val u = Users.syntax("u")

  ...
}
```

```scala
val users = withSQL {
  select.from(Users as u).orderBy(u.id.asc)
}.map(Users(u.resultName)).list.apply()
```

In this code, the part `select.from(Users as u).orderBy(u.id.asc)` has the same meaning as the following SQL.

```sql
SELECT * FROM USERS ORDER BY ID
```

> **POINT**
>
> * Play describes the actions in `Action {...}`
>   * `implicit request` is a description required to use an HTTP request implicitly in action processing.
> * We can specify the HTML template that we want to display by writing `views.html ....` in `Ok`.
>   * The argument specifies the parameter passed to the template.
> * In ScalikeJDBC, we can get a read-only session with `DB.readOnly {...}`
>  * We can use QueryDSL for writing typesafe SQL with `withSQL {...}`

## Run

After the implementation, access to http://localhost:9000/user/list .(run `sbt run` if sbt process is not running), then you can see the following screen. 

![User List Page](../images/play2.6-scalikejdbc3.2/user_list.png)
