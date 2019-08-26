---
title: Implementing of User registration and update API
---

User information is received in JSON format and used for user registration or update accordingly.

## Define Reads

In the companion object of `JsonController`, define a case class to receive the user information and `Reads` to convert that userInfo to that case class from JSON.

```scala
object JsonController {
  ...

  // Case class to get the user information
  case class UserForm(id: Option[Long], name: String, companyId: Option[Int])

  // Reads to convert the JSON to UserForm
  implicit val userFormReads = (
    (__ \ "id"       ).readNullable[Long] and
    (__ \ "name"     ).read[String]       and
    (__ \ "companyId").readNullable[Int]
  )(UserForm)
}
```

Similar to `Writes` defined earlier, we can also write the `Reads` without using DSL. 

```scala
implicit val userFormFormat = new Reads[UserForm]{
  def reads(js: JsValue): UserForm = {
    UserForm(
      id        = (js \ "id"       ).asOpt[Long],
      name      = (js \ "name"     ).as[String],
      companyId = (js \ "companyId").asOpt[Int]
    )
  }
}
```

`Reads`や`Writes`は上記のように明示的にマッピングを定義する方法に加え、以下のようにマクロを使ってシンプルに記述することもできます（`Json.reads`や`Json.writes`はコンパイル時に上記のようなマッピングを自動生成してくれるマクロです）。
In addition to the `Reads` and` Writes` method of explicitly defining mapping as described above, we can also the same simply using macros as follows. (`Json.reads` and` Json.writes ` automatically generates the above mapping at compile time).

```scala
implicit val userFormReads  = Json.reads[UserForm]
implicit val userFormWrites = Json.writes[UserForm]
```

We can also use the `Json.format` macro if both` Reads` and `Writes` are required. Defining `Format` has the same meaning as defining both` Reads` and `Writes`.

```scala
implicit val userFormFormat = Json.format[UserForm]
```

## Controller

Define the `create` method of `JsonController` as following.

While receiving the JSON request,

* `Action(parse.json) { ... }`

we write the `parse.json` in Action. The`request.body.validate` method converts the JSON to case class, and calls the `recoverTotal` method in case of failure.

```scala
def create = Action(parse.json) { implicit request =>
  request.body.validate[UserForm].map { form =>
    // OK, register the user
    DB.localTx { implicit session =>
      Users.create(form.name, form.companyId)
      Ok(Json.obj("result" -> "success"))
    }
  }.recoverTotal { e =>
    // NG, return the validation error
    BadRequest(Json.obj("result" -> "failure", "error" -> JsError.toJson(e)))
  }
}
```

Similarly implement the `update` method.

```scala
def update = Action(parse.json) { implicit request =>
  request.body.validate[UserForm].map { form =>
    // OK, update the user info
    DB.localTx { implicit session =>
      Users.find(form.id.get).foreach { user =>
        Users.save(user.copy(name = form.name, companyId = form.companyId))
      }
      Ok(Json.obj("result" -> "success"))
    }
  }.recoverTotal { e =>
    // NG, return the validation error
    BadRequest(Json.obj("result" -> "failure", "error" -> JsError.toJson(e)))
  }
}
```

> **POINT**
>
> * `parse.json` is called a body parser and determines how to process the request body.

## Run

Confirm the registration of user information and updated user information by executing the following command from the command line.

登録：
```
curl -H "Content-type: application/json" -XPOST -d '{"name":"TestUser", "companyId":1}' http://localhost:9000/json/create
```

更新：
```
curl -H "Content-type: application/json" -XPOST -d '{"id":1, "name":"TestUser", "companyId":1}' http://localhost:9000/json/update
```

If case of success both will return the following result.

```json
{"result":"success"}
```

To check the error response, we can send an invalid request as following(changed the property name to `userName` from `name`).
```
curl -H "Content-type: application/json" -XPOST -d '{"userName":"TestUser"}' http://localhost:9000/json/create
```

If we run the above query, it results the following error object.

```json
{"result":"failure","error":{"obj.name":[{"msg":["error.path.missing"],"args":[]}]}}
```
