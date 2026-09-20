좋아. 이번엔 **`alarm/` 4개 + `inquiry/` 4개**, 총 **8개 nGrinder Groovy 스크립트 전문**이야.

---

# `alarm/AlarmListGetTest.groovy`

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
class AlarmListGetTest {

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

        test = new GTest(8001, "GET /alarms")
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
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(20))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "createdAt,DESC"))
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/alarms", params, headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /alarms failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `alarm/AlarmReadPatchTest.groovy`

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
class AlarmReadPatchTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minAlarmId = 1
    public static int maxAlarmId = 10000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long alarmId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(8002, "PATCH /alarms/{id}/read")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        alarmId = minAlarmId + random.nextInt(maxAlarmId - minAlarmId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PATCH(
            targetHost + "/alarms/" + alarmId + "/read",
            "".getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 204, 400, 403, 404, 409])) {
            grinder.logger.error("PATCH /alarms/{}/read failed. status={}, body={}", alarmId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `alarm/AlarmReadAllPatchTest.groovy`

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
class AlarmReadAllPatchTest {

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

        test = new GTest(8003, "PATCH /alarms/read-all")
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

        HTTPResponse response = request.PATCH(
            targetHost + "/alarms/read-all",
            "".getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("PATCH /alarms/read-all failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

# `alarm/AlarmSseSubscribeGetTest.groovy`

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
class AlarmSseSubscribeGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3",
        "TOKEN_USER_4",
        "TOKEN_USER_5"
    ]

    private String userToken

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(10000)
        HTTPRequestControl.setReadTimeout(120000)

        test = new GTest(8004, "GET /alarms/subscribe SSE")
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
            new BasicHeader("Accept", "text/event-stream"),
            new BasicHeader("Cache-Control", "no-cache")
        ]

        HTTPResponse response = request.GET(targetHost + "/alarms/subscribe", [], headers)

        if (!(response.statusCode in [200, 503])) {
            grinder.logger.error("GET /alarms/subscribe failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(503)))
    }
}
```


---

# `inquiry/InquiryListGetTest.groovy`

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
class InquiryListGetTest {

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

        test = new GTest(8101, "GET /inquiries")
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
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(20))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "createdAt,DESC"))
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/inquiries", params, headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /inquiries failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `inquiry/InquiryDetailGetTest.groovy`

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
class InquiryDetailGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minInquiryId = 1
    public static int maxInquiryId = 10000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long inquiryId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(8102, "GET /inquiries/{inquiryId}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        inquiryId = minInquiryId + random.nextInt(maxInquiryId - minInquiryId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/inquiries/" + inquiryId, [], headers)

        if (!(response.statusCode in [200, 403, 404])) {
            grinder.logger.error("GET /inquiries/{} failed. status={}, body={}", inquiryId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(403), is(404)))
    }
}
```


---

# `inquiry/InquiryCategoriesGetTest.groovy`

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
class InquiryCategoriesGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(8103, "GET /inquiries/categorys")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/inquiries/categorys")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /inquiries/categorys failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `inquiry/InquiryCreatePostTest.groovy`

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
class InquiryCreatePostTest {

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

        test = new GTest(8104, "POST /inquiries")
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
          "categoryId": 1,
          "title": "nGrinder 문의 ${suffix}",
          "content": "nGrinder inquiry content ${suffix}"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/inquiries", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /inquiries failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

여기까지가 **`alarm/` + `inquiry/` 총 8개 전문**이야.

남은 큰 덩어리는 이제 **`backoffice/` 전체**야.  
백오피스는 API 수가 많아서 한 번에 다 넣으면 너무 길어지니까 보통 이렇게 쪼개는 게 낫다.

```plain text
backoffice-1: 약관/약관유형 + 방옵션/방유형
backoffice-2: 라이프스타일/인증 + 공지/문의/FAQ
backoffice-3: 앱버전/인증이메일 + 회원/신고/게시글
```


원하면 바로 이어서 **`backoffice-1` 전문** 뽑아줄게.