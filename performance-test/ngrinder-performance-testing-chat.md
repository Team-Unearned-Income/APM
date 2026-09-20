좋아. 이번엔 **`chat-request/` 6개 + `chat/` 6개**, 총 **12개 nGrinder 스크립트 전문**이야.

> `TOKEN_USER_*`, `chatRoomId`, `requestId`, multipart 파일 경로만 실제 테스트 데이터에 맞게 바꾸면 됨.

---

# `chat-request/ChatRequestListGetTest.groovy`

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
class ChatRequestListGetTest {

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

        test = new GTest(5001, "GET /chat-requests")
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

        HTTPResponse response = request.GET(targetHost + "/chat-requests", [], headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /chat-requests failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `chat-request/ChatRequestDetailGetTest.groovy`

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
class ChatRequestDetailGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minRequestId = 1
    public static int maxRequestId = 1000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long requestId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(5002, "GET /chat-requests/{requestId}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        requestId = minRequestId + random.nextInt(maxRequestId - minRequestId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/chat-requests/" + requestId, [], headers)

        if (!(response.statusCode in [200, 403, 404])) {
            grinder.logger.error("GET /chat-requests/{} failed. status={}, body={}", requestId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(403), is(404)))
    }
}
```


---

# `chat-request/ChatRequestCreatePostTest.groovy`

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
class ChatRequestCreatePostTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minReceiverId = 1
    public static int maxReceiverId = 1000

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

        test = new GTest(5003, "POST /chat-requests")
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
          "message": "nGrinder chat request ${System.currentTimeMillis()}"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(
            targetHost + "/chat-requests",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /chat-requests failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

# `chat-request/ChatRequestAcceptPostTest.groovy`

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
class ChatRequestAcceptPostTest {

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

        test = new GTest(5004, "POST /chat-requests/{requestId}/accept")
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

        HTTPResponse response = request.POST(
            targetHost + "/chat-requests/" + requestId + "/accept",
            "".getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 201, 204, 400, 403, 404, 409])) {
            grinder.logger.error("POST /chat-requests/{}/accept failed. status={}, body={}", requestId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(204), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `chat-request/ChatRequestRejectPostTest.groovy`

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
class ChatRequestRejectPostTest {

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

        test = new GTest(5005, "POST /chat-requests/{requestId}/reject")
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

        HTTPResponse response = request.POST(
            targetHost + "/chat-requests/" + requestId + "/reject",
            "".getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 201, 204, 400, 403, 404, 409])) {
            grinder.logger.error("POST /chat-requests/{}/reject failed. status={}, body={}", requestId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(204), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `chat-request/ChatRequestCancelPostTest.groovy`

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
class ChatRequestCancelPostTest {

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

        test = new GTest(5006, "POST /chat-requests/{requestId}/cancel")
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

        HTTPResponse response = request.POST(
            targetHost + "/chat-requests/" + requestId + "/cancel",
            "".getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 201, 204, 400, 403, 404, 409])) {
            grinder.logger.error("POST /chat-requests/{}/cancel failed. status={}, body={}", requestId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(204), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `chat/ChatRoomListGetTest.groovy`

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
class ChatRoomListGetTest {

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

        test = new GTest(5101, "GET /chats")
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

        HTTPResponse response = request.GET(targetHost + "/chats", [], headers)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /chats failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `chat/ChatRoomDetailGetTest.groovy`

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
class ChatRoomDetailGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minChatRoomId = 1
    public static int maxChatRoomId = 5000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_USER_1",
        "TOKEN_USER_2",
        "TOKEN_USER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long chatRoomId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)

        test = new GTest(5102, "GET /chats/{chatRoomId}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        chatRoomId = minChatRoomId + random.nextInt(maxChatRoomId - minChatRoomId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        HTTPResponse response = request.GET(targetHost + "/chats/" + chatRoomId, [], headers)

        if (!(response.statusCode in [200, 403, 404])) {
            grinder.logger.error("GET /chats/{} failed. status={}, body={}", chatRoomId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(403), is(404)))
    }
}
```


---

# `chat/ChatRoomCreatePostTest.groovy`

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
class ChatRoomCreatePostTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minTargetMemberId = 1
    public static int maxTargetMemberId = 1000

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

        test = new GTest(5103, "POST /chats")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int targetMemberId = minTargetMemberId + random.nextInt(maxTargetMemberId - minTargetMemberId + 1)

        requestBody = """
        {
          "targetMemberId": ${targetMemberId}
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(
            targetHost + "/chats",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 201, 400, 403, 404, 409])) {
            grinder.logger.error("POST /chats failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `chat/ChatRoomLeavePostTest.groovy`

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
class ChatRoomLeavePostTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minChatRoomId = 1
    public static int maxChatRoomId = 1000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_ROOM_MEMBER_1",
        "TOKEN_ROOM_MEMBER_2",
        "TOKEN_ROOM_MEMBER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long chatRoomId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(5104, "POST /chats/{chatRoomId}/leave")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        chatRoomId = minChatRoomId + random.nextInt(maxChatRoomId - minChatRoomId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(
            targetHost + "/chats/" + chatRoomId + "/leave",
            "".getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 204, 400, 403, 404, 409])) {
            grinder.logger.error("POST /chats/{}/leave failed. status={}, body={}", chatRoomId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(403), is(404), is(409)))
    }
}
```


---

# `chat/ChatRoomImageUploadMultipartTest.groovy`

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
import org.ngrinder.http.multipart.MultipartEntityBuilder
import org.apache.hc.core5.http.Header
import org.apache.hc.core5.http.ContentType
import org.apache.hc.core5.http.message.BasicHeader

import java.io.File
import java.util.Random

@RunWith(GrinderRunner)
class ChatRoomImageUploadMultipartTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static String imagePath = "/tmp/ngrinder/chat-image-500kb.jpg"

    public static int minChatRoomId = 1
    public static int maxChatRoomId = 1000

    public static final List<String> TOKEN_POOL = [
        "TOKEN_ROOM_MEMBER_1",
        "TOKEN_ROOM_MEMBER_2",
        "TOKEN_ROOM_MEMBER_3"
    ]

    private Random random = new Random()
    private String userToken
    private Long chatRoomId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(30000)

        test = new GTest(5105, "POST /chats/{chatRoomId}/images multipart")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Before
    public void before() {
        chatRoomId = minChatRoomId + random.nextInt(maxChatRoomId - minChatRoomId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + userToken)
        ]

        def builder = MultipartEntityBuilder.create()

        File image = new File(imagePath)
        if (image.exists()) {
            builder.addBinaryBody("file", image, ContentType.IMAGE_JPEG, image.getName())
        } else {
            grinder.logger.error("Image file does not exist: {}", imagePath)
            grinder.statistics.forLastTest.success = false
            return
        }

        def entity = builder.build()

        HTTPResponse response = request.POST(
            targetHost + "/chats/" + chatRoomId + "/images",
            entity,
            headers
        )

        if (!(response.statusCode in [200, 201, 400, 403, 404])) {
            grinder.logger.error("POST /chats/{}/images failed. status={}, body={}", chatRoomId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(403), is(404)))
    }
}
```


---

# `chat/ChatMessageWebSocketTest.groovy`

```groovy
import static net.grinder.script.Grinder.grinder
import static org.junit.Assert.*

import net.grinder.script.GTest
import net.grinder.scriptengine.groovy.junit.GrinderRunner
import net.grinder.scriptengine.groovy.junit.annotation.BeforeProcess
import net.grinder.scriptengine.groovy.junit.annotation.BeforeThread
import net.grinder.scriptengine.groovy.junit.annotation.Test

import org.junit.runner.RunWith

import java.net.URI
import java.util.concurrent.CountDownLatch
import java.util.concurrent.TimeUnit

import jakarta.websocket.ClientEndpoint
import jakarta.websocket.ContainerProvider
import jakarta.websocket.OnClose
import jakarta.websocket.OnError
import jakarta.websocket.OnMessage
import jakarta.websocket.OnOpen
import jakarta.websocket.Session
import jakarta.websocket.WebSocketContainer

@RunWith(GrinderRunner)
class ChatMessageWebSocketTest {

    public static GTest test

    public static String websocketUrl = "ws://host.docker.internal:8080/ws"
    public static Long chatRoomId = 1L

    public static final List<String> TOKEN_POOL = [
        "TOKEN_ROOM_MEMBER_1",
        "TOKEN_ROOM_MEMBER_2",
        "TOKEN_ROOM_MEMBER_3"
    ]

    private String userToken

    @BeforeProcess
    public static void beforeProcess() {
        test = new GTest(5106, "WebSocket /app/chats/{chatRoomId}/messages")
    }

    @BeforeThread
    public void beforeThread() {
        userToken = TOKEN_POOL.get(grinder.threadNumber % TOKEN_POOL.size())
    }

    @Test
    public void test() {
        test.record(this, "sendWebSocketMessage")
        sendWebSocketMessage()
    }

    public void sendWebSocketMessage() {
        CountDownLatch latch = new CountDownLatch(1)

        try {
            WebSocketContainer container = ContainerProvider.getWebSocketContainer()
            URI uri = new URI(websocketUrl + "?token=" + userToken)

            Session session = container.connectToServer(TestClientEndpoint.class, uri)

            String connectFrame = "CONNECT\naccept-version:1.2\nAuthorization:Bearer " + userToken + "\n\n\u0000"
            session.getBasicRemote().sendText(connectFrame)

            String subscribeFrame = "SUBSCRIBE\nid:sub-0\ndestination:/topic/chats/" + chatRoomId + "\n\n\u0000"
            session.getBasicRemote().sendText(subscribeFrame)

            String body = "{\\"messageType\\":\\"TEXT\\",\\"content\\":\\"nGrinder websocket message " + System.currentTimeMillis() + "\\"}"

            String sendFrame = "SEND\ndestination:/app/chats/" + chatRoomId + "/messages\ncontent-type:application/json\n\n" + body + "\u0000"
            session.getBasicRemote().sendText(sendFrame)

            latch.await(500, TimeUnit.MILLISECONDS)
            session.close()
        } catch (Throwable e) {
            grinder.logger.error("WebSocket message test failed", e)
            grinder.statistics.forLastTest.success = false
        }
    }

    @ClientEndpoint
    public static class TestClientEndpoint {

        @OnOpen
        public void onOpen(Session session) {
        }

        @OnMessage
        public void onMessage(String message) {
        }

        @OnClose
        public void onClose(Session session) {
        }

        @OnError
        public void onError(Session session, Throwable throwable) {
        }
    }
}
```


---

여기까지가 **`chat-request/` + `chat/` 총 12개 전문**이야.

다음 묶음은 이거:

```plain text
block/
├── BlockCreatePostTest.groovy
├── BlockListGetTest.groovy
└── BlockDeleteTest.groovy

verification/
├── StudentVerifySendPostTest.groovy
├── CompanyVerifySendPostTest.groovy
├── StudentVerifyConfirmPostTest.groovy
├── CompanyVerifyConfirmPostTest.groovy
└── VerifyWebhookPostTest.groovy

roommate-request/
├── RoommateRequestCreatePostTest.groovy
├── RoommateRequestAcceptPostTest.groovy
├── RoommateRequestRejectPostTest.groovy
├── RoommateRequestCancelPostTest.groovy
└── RoommateRequestListGetTest.groovy
```


원하면 바로 이어서 **`block/` + `verification/` + `roommate-request/` 총 13개 전문** 뽑아줄게.