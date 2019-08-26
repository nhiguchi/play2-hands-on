---
title: Implement User List API
---

Retrieving all entries from the `USERS` table in ascending order of ID and return the user list in JSON format.

## Writes definition

Play2's JSON support requires defining `Writes` for converting Scala objects to JSON and` Reads` to convert Scala objects into JSON.

To convert the query result list of `case class`, obtained by searching the `USERS` table, into JSON format, it is required to define the `Writes` in `Users` class of `Users` table. Similar to the `Form` class, we will add the companion object of the corresponding controller (here`JsonController`).

```scala
object JsonController {
  // Define Writes to convert Users into JSON
  implicit val usersWrites = (
    (__ \ "id"       ).write[Long]   and
    (__ \ "name"     ).write[String] and
    (__ \ "companyId").writeNullable[Int]
  )(unlift(Users.unapply))
}
```

Although mapping is defined using DSL provided by JSON support of Play2, it is possible to describe as follows without using DSL.

```scala
implicit val usersWritesFormat = new Writes[Users]{
  def writes(user: Users): JsValue = {
    Json.obj(
      "id"        -> user.id,
      "name"      -> user.name,
      "companyId" -> user.companyId
    )
  }
}
```

> **POINT**
>
> * Play2's JSON support requires defining mappings in `Reads` and` Writes` to convert between object and JSON
> * Play2's JSON support can not just do the simple case class conversions but also the more complex conversions and validations.

## Controller

Implement the `list`method of `JsonController` as following

```scala
// Import JsonController class to define Reads and Writes in companion object
import JsonController._

def list = Action { implicit request =>
  val u = Users.syntax("u")

  DB.readOnly { implicit session =>
    // fetch user list
    val users = withSQL {
      select.from(Users as u).orderBy(u.id.asc)
    }.map(Users(u.resultName)).list.apply()

    // return user list as JSON
    Ok(Json.obj("users" -> users))
  }
}
```

Although conversion from case class to JSON is performed by the `Json.obj` method, a compile error will occur if `Writes` method, corresponding to the case class is not defined at that time.

## Run

Run the following command from command prompt to check the user list.

```
curl -XGET http://localhost:9000/json/list
```

The output should match the following JSON object.

```json
{"users":[{"id":1,"name":"Taro Yamada","companyId":1},{"id":2,"name":"Jiro Sato"}]}
```
