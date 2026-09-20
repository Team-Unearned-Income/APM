좋아. 이번엔 **`block/` 3개 + `verification/` 5개 + `roommate-request/` 5개**, 총 **13개 nGrinder Groovy 스크립트 전문**이야.

---

# `block/BlockCreatePostTest.groovy`

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
class BlockCreatePostTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static boolean fixedTarget = false
    public static Long fixedTargetMemberId = 2L
    public static int minTargetMemberId = 1
    public static int maxTargetMemberId = 10000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3",
        "TOKEN_USER_4",
        "TOKEN_USER_5"
    ]

    private Random random = new Random()
    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(6001, "POST /blocks")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        Long targetMemberId = fixedTarget
                ? fixedTargetMemberId
                : minTargetMemberId + random.nextInt(maxTargetMemberId - minTargetMemberId + 1)

        requestBody = """
        {
          "targetMemberId": ${targetMemberId},
          "reason": "nGrinder block test"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/blocks", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /blocks failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

# `block/BlockListGetTest.groovy`

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
class BlockListGetTest {

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

        test = new GTest(6002, "GET /blocks")
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

        HTTPResponse response = request.GET(targetHost + "/blocks", params, headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /blocks failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `block/BlockDeleteTest.groovy`

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
class BlockDeleteTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minBlockId = 1
    public static int maxBlockId = 1000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long blockId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(6003, "DELETE /blocks/{blockId}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        blockId = minBlockId + random.nextInt(maxBlockId - minBlockId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.DELETE(targetHost + "/blocks/" + blockId, headers)

        if (!(response.statusCode in [200, 204, 400, 403, 404, 409])) {
            grinder.logger.error("DELETE /blocks/{} failed. status={}, body={}", blockId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `verification/StudentVerifySendPostTest.groovy`

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
class StudentVerifySendPostTest {

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

        test = new GTest(6101, "POST /auth/verify/student/send")
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
          "email": "student${suffix}@test.ac.kr"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/auth/verify/student/send", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 409, 429])) {
            grinder.logger.error("POST /auth/verify/student/send failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409), is(429)))
    }
}
```


---

# `verification/CompanyVerifySendPostTest.groovy`

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
class CompanyVerifySendPostTest {

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

        test = new GTest(6102, "POST /auth/verify/company/send")
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
          "email": "company${suffix}@test.com"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/auth/verify/company/send", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 409, 429])) {
            grinder.logger.error("POST /auth/verify/company/send failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409), is(429)))
    }
}
```


---

# `verification/StudentVerifyConfirmPostTest.groovy`

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
class StudentVerifyConfirmPostTest {

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

        test = new GTest(6103, "POST /auth/verify/student/confirm")
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
          "email": "student@test.ac.kr",
          "code": "123456"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/auth/verify/student/confirm", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /auth/verify/student/confirm failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

# `verification/CompanyVerifyConfirmPostTest.groovy`

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
class CompanyVerifyConfirmPostTest {

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

        test = new GTest(6104, "POST /auth/verify/company/confirm")
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
          "email": "company@test.com",
          "code": "123456"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/auth/verify/company/confirm", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /auth/verify/company/confirm failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

# `verification/VerifyWebhookPostTest.groovy`

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
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.message.BasicHeader

@RunWith(GrinderRunner)
class VerifyWebhookPostTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(6105, "POST /auth/verify/webhook")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        requestBody = """
        {
          "type": "email.verified",
          "data": {
            "email": "webhook@test.com",
            "status": "VERIFIED"
          }
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Content-Type", "application/json"),
            new BasicHeader("svix-id", "msg_" + System.currentTimeMillis()),
            new BasicHeader("svix-timestamp", String.valueOf(System.currentTimeMillis() / 1000)),
            new BasicHeader("svix-signature", "v1,test-signature")
        ]

        HTTPResponse response = request.POST(targetHost + "/auth/verify/webhook", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 204, 400, 401, 403])) {
            grinder.logger.error("POST /auth/verify/webhook failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(204), is(400), is(401), is(403)))
    }
}
```


---

# `roommate-request/RoommateRequestCreatePostTest.groovy`

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
class RoommateRequestCreatePostTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minReceiverId = 1
    public static int maxReceiverId = 10000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3",
        "TOKEN_USER_4",
        "TOKEN_USER_5"
    ]

    private Random random = new Random()
    private String userToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)

        test = new GTest(6201, "POST /roommate-requests")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int receiverId = minReceiverId + random.nextInt(maxReceiverId - minReceiverId + 1)

        requestBody = """
        {
          "receiverId": ${receiverId},
          "message": "nGrinder roommate request ${System.currentTimeMillis()}"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/roommate-requests", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /roommate-requests failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

# `roommate-request/RoommateRequestAcceptPostTest.groovy`

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
class RoommateRequestAcceptPostTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static boolean fixedTarget = true
    public static Long fixedRequestId = 1L
    public static int minRequestId = 1
    public static int maxRequestId = 1000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_RECEIVER_1",
        "TOKEN_RECEIVER_2",
        "TOKEN_RECEIVER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long requestId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)

        test = new GTest(6202, "POST /roommate-requests/{requestId}/accept")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestId = fixedTarget ? fixedRequestId : minRequestId + random.nextInt(maxRequestId - minRequestId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/roommate-requests/" + requestId + "/accept", "".getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 204, 400, 403, 404, 409])) {
            grinder.logger.error("POST /roommate-requests/{}/accept failed. status={}, body={}", requestId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(204), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `roommate-request/RoommateRequestRejectPostTest.groovy`

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
class RoommateRequestRejectPostTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static boolean fixedTarget = true
    public static Long fixedRequestId = 1L
    public static int minRequestId = 1
    public static int maxRequestId = 1000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_RECEIVER_1",
        "TOKEN_RECEIVER_2",
        "TOKEN_RECEIVER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long requestId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(6203, "POST /roommate-requests/{requestId}/reject")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestId = fixedTarget ? fixedRequestId : minRequestId + random.nextInt(maxRequestId - minRequestId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/roommate-requests/" + requestId + "/reject", "".getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 204, 400, 403, 404, 409])) {
            grinder.logger.error("POST /roommate-requests/{}/reject failed. status={}, body={}", requestId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(204), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `roommate-request/RoommateRequestCancelPostTest.groovy`

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
class RoommateRequestCancelPostTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static boolean fixedTarget = true
    public static Long fixedRequestId = 1L
    public static int minRequestId = 1
    public static int maxRequestId = 1000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_REQUESTER_1",
        "TOKEN_REQUESTER_2",
        "TOKEN_REQUESTER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long requestId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(6204, "POST /roommate-requests/{requestId}/cancel")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestId = fixedTarget ? fixedRequestId : minRequestId + random.nextInt(maxRequestId - minRequestId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/roommate-requests/" + requestId + "/cancel", "".getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 204, 400, 403, 404, 409])) {
            grinder.logger.error("POST /roommate-requests/{}/cancel failed. status={}, body={}", requestId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(204), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `roommate-request/RoommateRequestListGetTest.groovy`

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
class RoommateRequestListGetTest {

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

        test = new GTest(6205, "GET /roommate-requests")
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

        HTTPResponse response = request.GET(targetHost + "/roommate-requests", params, headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /roommate-requests failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

여기까지가 **`block/` + `verification/` + `roommate-request/` 총 13개 전문**이야.

다음 묶음은 이거:

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


원하면 바로 이어서 **`roommate-management/` 17개 전문** 뽑아줄게.