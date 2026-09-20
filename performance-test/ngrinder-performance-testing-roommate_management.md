좋아. 이번엔 **`roommate-management/` 전체 17개 nGrinder Groovy 스크립트 전문**이야.

대상:

```plain text
roommate-management/
├── MyRoommateGetTest.groovy
├── MyRoommateDeleteTest.groovy
├── HouseRuleCreatePostTest.groovy
├── HouseRuleListGetTest.groovy
├── HouseRuleDetailGetTest.groovy
├── HouseRuleUpdatePutTest.groovy
├── HouseRuleDeleteTest.groovy
├── CalendarMonthListGetTest.groovy
├── CalendarDayListGetTest.groovy
├── CalendarDetailGetTest.groovy
├── CalendarCategoryGetTest.groovy
├── CalendarEditFormGetTest.groovy
├── CalendarCreatePostTest.groovy
├── CalendarRepeatCreatePostTest.groovy
├── CalendarUpdatePutTest.groovy
├── CalendarRepeatUpdatePutTest.groovy
└── CalendarDeleteTest.groovy
```


---

# `roommate-management/MyRoommateGetTest.groovy`

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
class MyRoommateGetTest {

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

        test = new GTest(7001, "GET /roommates/me")
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

        HTTPResponse response = request.GET(targetHost + "/roommates/me", [], headers)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /roommates/me failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

# `roommate-management/MyRoommateDeleteTest.groovy`

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
class MyRoommateDeleteTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minRoommateId = 1
    public static int maxRoommateId = 100

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long roommateId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(7002, "DELETE /roommates/me/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        roommateId = minRoommateId + random.nextInt(maxRoommateId - minRoommateId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.DELETE(targetHost + "/roommates/me/" + roommateId, headers)

        if (!(response.statusCode in [200, 204, 400, 403, 404, 409])) {
            grinder.logger.error("DELETE /roommates/me/{} failed. status={}, body={}", roommateId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `roommate-management/HouseRuleCreatePostTest.groovy`

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
class HouseRuleCreatePostTest {

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

        test = new GTest(7003, "POST /roommates/me/house-rule")
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
          "title": "nGrinder 하우스룰 ${suffix}",
          "content": "nGrinder house rule content ${suffix}"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/roommates/me/house-rule", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /roommates/me/house-rule failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

# `roommate-management/HouseRuleListGetTest.groovy`

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
class HouseRuleListGetTest {

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

        test = new GTest(7004, "GET /roommates/me/house-rule")
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

        HTTPResponse response = request.GET(targetHost + "/roommates/me/house-rule", [], headers)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /roommates/me/house-rule failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

# `roommate-management/HouseRuleDetailGetTest.groovy`

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
class HouseRuleDetailGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minHouseRuleId = 1
    public static int maxHouseRuleId = 1000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long houseRuleId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(7005, "GET /roommates/me/house-rule/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        houseRuleId = minHouseRuleId + random.nextInt(maxHouseRuleId - minHouseRuleId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/roommates/me/house-rule/" + houseRuleId, [], headers)

        if (!(response.statusCode in [200, 403, 404])) {
            grinder.logger.error("GET /roommates/me/house-rule/{} failed. status={}, body={}", houseRuleId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(403), is(404)))
    }
}
```


---

# `roommate-management/HouseRuleUpdatePutTest.groovy`

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
class HouseRuleUpdatePutTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minHouseRuleId = 1
    public static int maxHouseRuleId = 1000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long houseRuleId
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(7006, "PUT /roommates/me/house-rule/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        houseRuleId = minHouseRuleId + random.nextInt(maxHouseRuleId - minHouseRuleId + 1)
        int suffix = random.nextInt(1000000)

        requestBody = """
        {
          "title": "nGrinder 수정 하우스룰 ${suffix}",
          "content": "nGrinder updated house rule content ${suffix}"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PUT(targetHost + "/roommates/me/house-rule/" + houseRuleId, requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400, 403, 404])) {
            grinder.logger.error("PUT /roommates/me/house-rule/{} failed. status={}, body={}", houseRuleId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(403), is(404)))
    }
}
```


---

# `roommate-management/HouseRuleDeleteTest.groovy`

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
class HouseRuleDeleteTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minHouseRuleId = 1
    public static int maxHouseRuleId = 100

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long houseRuleId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(7007, "DELETE /roommates/me/house-rule/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        houseRuleId = minHouseRuleId + random.nextInt(maxHouseRuleId - minHouseRuleId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.DELETE(targetHost + "/roommates/me/house-rule/" + houseRuleId, headers)

        if (!(response.statusCode in [200, 204, 400, 403, 404, 409])) {
            grinder.logger.error("DELETE /roommates/me/house-rule/{} failed. status={}, body={}", houseRuleId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `roommate-management/CalendarMonthListGetTest.groovy`

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
class CalendarMonthListGetTest {

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
        HTTPRequestControl.setReadTimeout(15000)

        test = new GTest(7008, "GET /roommates/me/calendar month")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int month = 1 + random.nextInt(12)

        params = new ArrayList<>()
        params.add(new BasicNameValuePair("year", "2026"))
        params.add(new BasicNameValuePair("month", String.valueOf(month)))
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/roommates/me/calendar", params, headers)

        if (!(response.statusCode in [200, 400, 404])) {
            grinder.logger.error("GET /roommates/me/calendar month failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(404)))
    }
}
```


---

# `roommate-management/CalendarDayListGetTest.groovy`

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
class CalendarDayListGetTest {

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
        HTTPRequestControl.setReadTimeout(15000)

        test = new GTest(7009, "GET /roommates/me/calendar day")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int month = 1 + random.nextInt(12)
        int day = 1 + random.nextInt(28)

        params = new ArrayList<>()
        params.add(new BasicNameValuePair("year", "2026"))
        params.add(new BasicNameValuePair("month", String.valueOf(month)))
        params.add(new BasicNameValuePair("day", String.valueOf(day)))
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/roommates/me/calendar", params, headers)

        if (!(response.statusCode in [200, 400, 404])) {
            grinder.logger.error("GET /roommates/me/calendar day failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(404)))
    }
}
```


---

# `roommate-management/CalendarDetailGetTest.groovy`

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
class CalendarDetailGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minCalendarId = 1
    public static int maxCalendarId = 1000

    private Random random = new Random()
    private Long calendarId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(7010, "GET /roommates/me/calendar/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        calendarId = minCalendarId + random.nextInt(maxCalendarId - minCalendarId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/roommates/me/calendar/" + calendarId)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /roommates/me/calendar/{} failed. status={}, body={}", calendarId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

# `roommate-management/CalendarCategoryGetTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.hamcrest.Matchers.*
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith
import org.ngrinder.http.HTTPRequest
import org.ngrinder.http.HTTPRequestControl
import org.ngrinder.http.HTTPResponse

@RunWith(GrinderRunner)
class CalendarCategoryGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(7011, "GET /roommates/me/calendar/categories")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/roommates/me/calendar/categories")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /roommates/me/calendar/categories failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `roommate-management/CalendarEditFormGetTest.groovy`

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
class CalendarEditFormGetTest {

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

        test = new GTest(7012, "GET /roommates/me/calendar/edit")
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

        HTTPResponse response = request.GET(targetHost + "/roommates/me/calendar/edit", [], headers)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /roommates/me/calendar/edit failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

# `roommate-management/CalendarCreatePostTest.groovy`

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
class CalendarCreatePostTest {

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

        test = new GTest(7013, "POST /roommates/me/calendar")
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
        int month = 1 + random.nextInt(12)
        int day = 1 + random.nextInt(28)

        requestBody = """
        {
          "title": "nGrinder 일정 ${suffix}",
          "content": "nGrinder calendar content ${suffix}",
          "categoryName": "청소",
          "date": "2026-${String.format("%02d", month)}-${String.format("%02d", day)}",
          "startTime": "09:00",
          "endTime": "10:00"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/roommates/me/calendar", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /roommates/me/calendar failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

# `roommate-management/CalendarRepeatCreatePostTest.groovy`

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
class CalendarRepeatCreatePostTest {

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
        HTTPRequestControl.setReadTimeout(20000)

        test = new GTest(7014, "POST /roommates/me/calendar/repeat")
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
          "title": "nGrinder 반복 일정 ${suffix}",
          "content": "nGrinder repeat calendar content ${suffix}",
          "categoryName": "청소",
          "startDate": "2026-09-01",
          "endDate": "2026-10-31",
          "startTime": "09:00",
          "endTime": "10:00",
          "repeatType": "WEEKLY",
          "repeatDays": ["MONDAY", "WEDNESDAY", "FRIDAY"]
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/roommates/me/calendar/repeat", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /roommates/me/calendar/repeat failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

# `roommate-management/CalendarUpdatePutTest.groovy`

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
class CalendarUpdatePutTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minCalendarId = 1
    public static int maxCalendarId = 1000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long calendarId
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)

        test = new GTest(7015, "PUT /roommates/me/calendar/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        calendarId = minCalendarId + random.nextInt(maxCalendarId - minCalendarId + 1)
        int suffix = random.nextInt(1000000)

        requestBody = """
        {
          "title": "nGrinder 수정 일정 ${suffix}",
          "content": "nGrinder updated calendar content ${suffix}",
          "categoryName": "청소",
          "date": "2026-09-15",
          "startTime": "10:00",
          "endTime": "11:00"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PUT(targetHost + "/roommates/me/calendar/" + calendarId, requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400, 403, 404, 409])) {
            grinder.logger.error("PUT /roommates/me/calendar/{} failed. status={}, body={}", calendarId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `roommate-management/CalendarRepeatUpdatePutTest.groovy`

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
class CalendarRepeatUpdatePutTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minRepeatCalendarId = 1
    public static int maxRepeatCalendarId = 1000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long repeatCalendarId
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(20000)

        test = new GTest(7016, "PUT /roommates/me/calendar/repeat/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        repeatCalendarId = minRepeatCalendarId + random.nextInt(maxRepeatCalendarId - minRepeatCalendarId + 1)
        int suffix = random.nextInt(1000000)

        requestBody = """
        {
          "title": "nGrinder 수정 반복 일정 ${suffix}",
          "content": "nGrinder updated repeat calendar content ${suffix}",
          "categoryName": "청소",
          "startDate": "2026-09-01",
          "endDate": "2026-11-30",
          "startTime": "11:00",
          "endTime": "12:00",
          "repeatType": "WEEKLY",
          "repeatDays": ["TUESDAY", "THURSDAY"]
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PUT(targetHost + "/roommates/me/calendar/repeat/" + repeatCalendarId, requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400, 403, 404, 409])) {
            grinder.logger.error("PUT /roommates/me/calendar/repeat/{} failed. status={}, body={}", repeatCalendarId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `roommate-management/CalendarDeleteTest.groovy`

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
class CalendarDeleteTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minCalendarId = 1
    public static int maxCalendarId = 100

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long calendarId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(7017, "DELETE /roommates/me/calendar/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        calendarId = minCalendarId + random.nextInt(maxCalendarId - minCalendarId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.DELETE(targetHost + "/roommates/me/calendar/" + calendarId, headers)

        if (!(response.statusCode in [200, 204, 400, 403, 404, 409])) {
            grinder.logger.error("DELETE /roommates/me/calendar/{} failed. status={}, body={}", calendarId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(403), is(404), is(409)))
    }
}
```


---

여기까지가 **`roommate-management/` 17개 전체 스크립트 전문**이야.

다음 묶음은 이거:

```plain text
alarm/
├── AlarmListGetTest.groovy
├── AlarmReadPatchTest.groovy
├── AlarmReadAllPatchTest.groovy
└── AlarmSseSubscribeGetTest.groovy

inquiry/
├── InquiryListGetTest.groovy
├── InquiryDetailGetTest.groovy
├── InquiryCategoriesGetTest.groovy
└── InquiryCreatePostTest.groovy
```


원하면 바로 이어서 **`alarm/` + `inquiry/` 총 8개 전문** 뽑아줄게.