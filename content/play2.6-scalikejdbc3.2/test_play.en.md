---
title: Test Play2
---

Play2 provides functions for unit testing of applications developed with Play2. By default, a test case for `HomeController` is generated, but let's create a test for the JSON API created by hands-on.

Create `JsonControllerSpec` in` test/controllers` with the following contents.

```scala
package controllers

import org.scalatestplus.play._
import org.scalatestplus.play.guice._
import play.api.libs.json._
import play.api.test._
import play.api.test.Helpers._

class JsonControllerSpec extends PlaySpec with GuiceOneAppPerSuite with Injecting {
  "JsonController GET" should {
    "request users list from the application" in {
      // Get controller instance from Guice
      val controller = inject[JsonController]
      // Call the controller method using FakeRequest
      val result = controller.list.apply(FakeRequest())

      // Check the response status
      status(result) mustBe OK
      // Check the Content-Type of response
      contentType(result) mustBe Some("application/json")
      //  Check response JSON
      val resultJson = contentAsJson(result)
      val expectedJson = Json.parse(
        """{"users":[{"id":1,"name":"Taro Yamada","companyId":1},{"id":2,"name":"Jiro Sato"}]}"""
      )
      resultJson mustEqual expectedJson
    }
  }
}
```

In this test case, instead of actually sending an HTTP request, the controller is called using a dummy request called `FakeRequest`. In the test case, we can get the response with the test helper method, and test if response is as expected.

Run the test using following command.

```
sbt test
```

If the test is successful, we should see something like this.

```
[info] Total number of tests run: 24
[info] Suites: completed 4, aborted 0
[info] Tests: succeeded 24, failed 0, canceled 0, ignored 0, pending 0
[info] All tests passed.
[info] Passed: Total 24, Failed 0, Errors 0, Passed 24
```

We can also run the specific tests.

```
sbt "testOnly controllers.JsonControllerSpec"
```


If the test DB has changed from the initial state, the tests will fail and in practice it is necessary to rollback the changes for each test. ScalikeJDBC provides a test fixture for Play2 applications, so it's better to use such libraries.

- https://github.com/scalikejdbc/scalikejdbc-cookbook/blob/master/ja/10_play.md#scalikejdbc-play-fixture
