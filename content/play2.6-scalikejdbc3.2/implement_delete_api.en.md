---
title: Implement User Delete API
---

Removes user of the specified ID from the `USERS` table.

## Controller

Implement the `remove` method of `JsonController`.

```scala
def remove(id: Long) = Action { implicit request =>
  DB.localTx { implicit session =>
    // delete user
    Users.find(id).foreach { user =>
      Users.destroy(user)
    }
    Ok(Json.obj("result" -> "success"))
  }
}
```

## Run

Execute the following command from the command line to confirm that the user has been deleted.

```
curl -XPOST http://localhost:9000/json/remove/1
```
