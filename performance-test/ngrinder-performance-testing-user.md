좋아. 이번엔 **`user/` 전체 API 스크립트** 뽑아줄게.  
전부 `/users/me/**` 기준이고, **JWT 필요**하니까 각 파일의 `TOKEN_POOL`만 실제 토큰으로 바꿔.

> 주의: `POST/PUT/PATCH` body는 실제 Request DTO 필드명과 enum 값에 맞게 한 번 조정해야 함. 일단 부하 테스트용 기본 형태로 넣어둠.

---

# `user/UserDeleteTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserDeleteTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_DELETE_USER_1"
    ]

    private String userToken

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3001, "DELETE /users/me")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.DELETE(targetHost + "/users/me", headers)

        if (!(response.statusCode in [200, 204])) {
            grinder.logger.error("DELETE /users/me failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204)))
    }
}
```


---

# `user/UserProfileBasicPostTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

import java.util.Random

@RunWith(GrinderRunner)
class UserProfileBasicPostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3002, "POST /users/me/profile/basic")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int suffix = random.nextInt(1000000)
        requestBody = """
        {
          "nickname": "perf-user-${suffix}",
          "gender": "MALE",
          "birthDate": "1999-01-01",
          "introduction": "nGrinder basic profile test"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/users/me/profile/basic", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /users/me/profile/basic failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `user/UserProfileLifestylePostTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserProfileLifestylePostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3003, "POST /users/me/profile/lifestyle")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestBody = """
        {
          "lifestylePatternIds": [1, 2, 3],
          "sleepTime": "23:00",
          "wakeUpTime": "07:00",
          "smoking": false,
          "drinking": "SOMETIMES"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/users/me/profile/lifestyle", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /users/me/profile/lifestyle failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `user/UserProfileRoomInfoPostTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserProfileRoomInfoPostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3004, "POST /users/me/profile/roominfo")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestBody = """
        {
          "regionId": 1,
          "roomTypeId": 1,
          "roomAddOptionIds": [1, 2],
          "deposit": 500,
          "monthlyRent": 50,
          "maintenanceFee": 10
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/users/me/profile/roominfo", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /users/me/profile/roominfo failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `user/UserProfileAllPostTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

import java.util.Random

@RunWith(GrinderRunner)
class UserProfileAllPostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(3005, "POST /users/me/profile/all")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int suffix = random.nextInt(1000000)
        requestBody = """
        {
          "basicProfile": {
            "nickname": "perf-user-${suffix}",
            "gender": "MALE",
            "birthDate": "1999-01-01",
            "introduction": "nGrinder all profile test"
          },
          "lifestyleProfile": {
            "lifestylePatternIds": [1, 2, 3],
            "sleepTime": "23:00",
            "wakeUpTime": "07:00",
            "smoking": false,
            "drinking": "SOMETIMES"
          },
          "roomProfile": {
            "regionId": 1,
            "roomTypeId": 1,
            "roomAddOptionIds": [1, 2],
            "deposit": 500,
            "monthlyRent": 50,
            "maintenanceFee": 10
          }
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/users/me/profile/all", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /users/me/profile/all failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `user/UserProfileBasicPutMultipartTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader
import org.ngrinder.http.multipart.MultipartEntityBuilder

import java.io.File
import java.util.Random

@RunWith(GrinderRunner)
class UserProfileBasicPutMultipartTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static String imagePath = "/tmp/ngrinder/profile-100kb.jpg"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private String requestJson

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(20000)
        test = new GTest(3006, "PUT /users/me/profile/basic multipart")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int suffix = random.nextInt(1000000)
        requestJson = """
        {
          "nickname": "perf-modify-${suffix}",
          "gender": "MALE",
          "birthDate": "1999-01-01",
          "introduction": "nGrinder multipart profile update"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        def builder = MultipartEntityBuilder.create()
        builder.addTextBody("request", requestJson, org.apache.hc.core5.http.ContentType.APPLICATION_JSON)

        File image = new File(imagePath)
        if (image.exists()) {
            builder.addBinaryBody("file", image, org.apache.hc.core5.http.ContentType.IMAGE_JPEG, image.getName())
        }

        def entity = builder.build()
        HTTPResponse response = request.PUT(targetHost + "/users/me/profile/basic", entity, headers)

        if (!(response.statusCode in [200, 201, 400])) {
            grinder.logger.error("PUT /users/me/profile/basic multipart failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400)))
    }
}
```


---

# `user/UserProfileLifestylePutTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserProfileLifestylePutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3007, "PUT /users/me/profile/lifestyle")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestBody = """
        {
          "lifestylePatternIds": [1, 3, 5],
          "sleepTime": "00:00",
          "wakeUpTime": "08:00",
          "smoking": false,
          "drinking": "NO"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PUT(targetHost + "/users/me/profile/lifestyle", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400])) {
            grinder.logger.error("PUT /users/me/profile/lifestyle failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400)))
    }
}
```


---

# `user/UserProfileRoomInfoPutTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserProfileRoomInfoPutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3008, "PUT /users/me/profile/roominfo")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestBody = """
        {
          "regionId": 1,
          "roomTypeId": 1,
          "roomAddOptionIds": [1, 2, 3],
          "deposit": 1000,
          "monthlyRent": 60,
          "maintenanceFee": 12
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PUT(targetHost + "/users/me/profile/roominfo", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400])) {
            grinder.logger.error("PUT /users/me/profile/roominfo failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400)))
    }
}
```


---

# `user/UserProfileAllPutTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

import java.util.Random

@RunWith(GrinderRunner)
class UserProfileAllPutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(3009, "PUT /users/me/profile/all")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int suffix = random.nextInt(1000000)
        requestBody = """
        {
          "basicProfile": {
            "nickname": "perf-updated-${suffix}",
            "gender": "MALE",
            "birthDate": "1999-01-01",
            "introduction": "nGrinder all profile update"
          },
          "lifestyleProfile": {
            "lifestylePatternIds": [1, 3, 5],
            "sleepTime": "00:00",
            "wakeUpTime": "08:00",
            "smoking": false,
            "drinking": "NO"
          },
          "roomProfile": {
            "regionId": 1,
            "roomTypeId": 1,
            "roomAddOptionIds": [1, 2, 3],
            "deposit": 1000,
            "monthlyRent": 60,
            "maintenanceFee": 12
          }
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PUT(targetHost + "/users/me/profile/all", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400])) {
            grinder.logger.error("PUT /users/me/profile/all failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400)))
    }
}
```


---

# `user/UserPreferencesLifestylePostTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserPreferencesLifestylePostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3010, "POST /users/me/preferences/lifestyle")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestBody = """
        {
          "lifestylePatternIds": [1, 2, 3],
          "preferredSmoking": false,
          "preferredDrinking": "SOMETIMES"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/users/me/preferences/lifestyle", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /users/me/preferences/lifestyle failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `user/UserPreferencesConditionsPostTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserPreferencesConditionsPostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3011, "POST /users/me/preferences/conditions")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestBody = """
        {
          "preferredRegionIds": [1, 2],
          "preferredRoomTypeIds": [1],
          "minDeposit": 100,
          "maxDeposit": 1000,
          "minMonthlyRent": 30,
          "maxMonthlyRent": 80
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/users/me/preferences/conditions", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /users/me/preferences/conditions failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `user/UserPreferencesAllPostTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserPreferencesAllPostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(3012, "POST /users/me/preferences/all")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestBody = """
        {
          "lifestylePreferences": {
            "lifestylePatternIds": [1, 2, 3],
            "preferredSmoking": false,
            "preferredDrinking": "SOMETIMES"
          },
          "conditionPreferences": {
            "preferredRegionIds": [1, 2],
            "preferredRoomTypeIds": [1],
            "minDeposit": 100,
            "maxDeposit": 1000,
            "minMonthlyRent": 30,
            "maxMonthlyRent": 80
          }
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/users/me/preferences/all", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /users/me/preferences/all failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `user/UserPreferencesLifestylePutTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserPreferencesLifestylePutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3013, "PUT /users/me/preferences/lifestyle")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestBody = """
        {
          "lifestylePatternIds": [2, 4, 6],
          "preferredSmoking": false,
          "preferredDrinking": "NO"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PUT(targetHost + "/users/me/preferences/lifestyle", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400])) {
            grinder.logger.error("PUT /users/me/preferences/lifestyle failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400)))
    }
}
```


---

# `user/UserPreferencesConditionsPutTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserPreferencesConditionsPutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3014, "PUT /users/me/preferences/conditions")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestBody = """
        {
          "preferredRegionIds": [1, 3],
          "preferredRoomTypeIds": [1, 2],
          "minDeposit": 100,
          "maxDeposit": 1500,
          "minMonthlyRent": 30,
          "maxMonthlyRent": 90
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PUT(targetHost + "/users/me/preferences/conditions", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400])) {
            grinder.logger.error("PUT /users/me/preferences/conditions failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400)))
    }
}
```


---

# `user/UserPreferencesAllPutTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserPreferencesAllPutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(3015, "PUT /users/me/preferences/all")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestBody = """
        {
          "lifestylePreferences": {
            "lifestylePatternIds": [2, 4, 6],
            "preferredSmoking": false,
            "preferredDrinking": "NO"
          },
          "conditionPreferences": {
            "preferredRegionIds": [1, 3],
            "preferredRoomTypeIds": [1, 2],
            "minDeposit": 100,
            "maxDeposit": 1500,
            "minMonthlyRent": 30,
            "maxMonthlyRent": 90
          }
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PUT(targetHost + "/users/me/preferences/all", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400])) {
            grinder.logger.error("PUT /users/me/preferences/all failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400)))
    }
}
```


---

# `user/UserPreferencesAllGetTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserPreferencesAllGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3016, "GET /users/me/preferences/all")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/users/me/preferences/all", [], headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /users/me/preferences/all failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `user/UserProfileGetTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserProfileGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3017, "GET /users/me/profile")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/users/me/profile", [], headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /users/me/profile failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `user/UserProfileAllGetTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserProfileAllGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(3018, "GET /users/me/profile/all")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/users/me/profile/all", [], headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /users/me/profile/all failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `user/UserVisibilityPatchTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

import java.util.Random

@RunWith(GrinderRunner)
class UserVisibilityPatchTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3019, "PATCH /users/me/visibility")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestBody = """
        {
          "visible": ${random.nextBoolean()}
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PATCH(targetHost + "/users/me/visibility", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400])) {
            grinder.logger.error("PATCH /users/me/visibility failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400)))
    }
}
```


---

# `user/UserBoardsGetTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.NameValuePair
import org.apache.hc.core5.http.message.BasicHeader
import org.apache.hc.core5.http.message.BasicNameValuePair

import java.util.Random

@RunWith(GrinderRunner)
class UserBoardsGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3020, "GET /users/me/boards")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        params = new ArrayList<>()
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(10))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "createdAt,DESC"))
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/users/me/boards", params, headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /users/me/boards failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `user/UserVerificationsGetTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.NameValuePair
import org.apache.hc.core5.http.message.BasicHeader
import org.apache.hc.core5.http.message.BasicNameValuePair

import java.util.Random

@RunWith(GrinderRunner)
class UserVerificationsGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3021, "GET /users/me/verifications")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        params = new ArrayList<>()
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(5))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "createdAt,DESC"))
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/users/me/verifications", params, headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /users/me/verifications failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `user/UserNotificationSettingsGetTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserNotificationSettingsGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3022, "GET /users/me/notification-settings")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/users/me/notification-settings", [], headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /users/me/notification-settings failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `user/UserNotificationSettingsPatchTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

import java.util.Random

@RunWith(GrinderRunner)
class UserNotificationSettingsPatchTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3023, "PATCH /users/me/notification-settings")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestBody = """
        {
          "chatAlarm": ${random.nextBoolean()},
          "roommateRequestAlarm": ${random.nextBoolean()},
          "marketingAlarm": ${random.nextBoolean()}
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PATCH(targetHost + "/users/me/notification-settings", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400])) {
            grinder.logger.error("PATCH /users/me/notification-settings failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400)))
    }
}
```


---

# `user/UserNoticesGetTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.NameValuePair
import org.apache.hc.core5.http.message.BasicNameValuePair

import java.util.Random

@RunWith(GrinderRunner)
class UserNoticesGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3024, "GET /users/me/notices")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        params = new ArrayList<>()
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(10))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "createdAt,DESC"))
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/users/me/notices", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /users/me/notices failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `user/UserNoticeDetailGetTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse

import java.util.Random

@RunWith(GrinderRunner)
class UserNoticeDetailGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minNoticeId = 1
    public static int maxNoticeId = 100

    private Random random = new Random()
    private Long noticeId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3025, "GET /users/me/notices/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        noticeId = minNoticeId + random.nextInt(maxNoticeId - minNoticeId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/users/me/notices/" + noticeId)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /users/me/notices/{} failed. status={}, body={}", noticeId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `user/UserDevicePostTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.Before
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

import java.util.Random

@RunWith(GrinderRunner)
class UserDevicePostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3026, "POST /users/me/devices")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int suffix = random.nextInt(1000000)
        requestBody = """
        {
          "fcmToken": "perf-fcm-token-${suffix}",
          "deviceId": "perf-device-${suffix}",
          "platform": "ANDROID"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/users/me/devices", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400])) {
            grinder.logger.error("POST /users/me/devices failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400)))
    }
}
```


---

# `user/UserLogoutPostTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserLogoutPostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3027, "POST /users/me/logout")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/users/me/logout", "".getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 204])) {
            grinder.logger.error("POST /users/me/logout failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204)))
    }
}
```


---

# `user/UserAccountGetTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class UserAccountGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private String userToken

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(3028, "GET /users/me/account")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/users/me/account", [], headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /users/me/account failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

여기까지가 **`user/` 28개 전체 스크립트**야.

다음 순서는 보통 이거로 가면 됨.

```plain text
roommate/
├── RoommateBoardListGetTest.groovy
├── RoommateBoardDetailGetTest.groovy
├── RoommateBoardLikePostTest.groovy
├── RoommateBoardEditFormGetTest.groovy
├── RoommateBoardCreateMultipartTest.groovy
├── RoommateBoardUpdateMultipartTest.groovy
├── RoommateBoardReportPostTest.groovy
├── RoommateBoardDeleteTest.groovy
├── RoommateMatchListGetTest.groovy
├── RoommateMatchDetailGetTest.groovy
├── RoommateMatchScoreGetTest.groovy
├── RoommateMatchLikePostTest.groovy
└── RoommateMatchReportPostTest.groovy
```


원하면 바로 이어서 **`roommate/` 13개 전문** 뽑아줄게.