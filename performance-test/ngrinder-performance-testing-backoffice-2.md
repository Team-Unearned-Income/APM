좋아. 이번엔 **`backoffice-2` 24개 전문**이야.

대상:

```plain text
backoffice/
├── BoLifestylePatternListGetTest.groovy
├── BoLifestylePatternDetailGetTest.groovy
├── BoLifestylePatternCreateMultipartTest.groovy
├── BoLifestylePatternUpdateMultipartTest.groovy
├── BoLifestylePatternDeleteTest.groovy
├── BoVerificationApproveListGetTest.groovy
├── BoVerificationCancelListGetTest.groovy
├── BoVerificationWaitListGetTest.groovy
├── BoVerificationWaitDetailGetTest.groovy
├── BoVerificationApprovePatchTest.groovy
├── BoVerificationCancelPatchTest.groovy
├── BoNoticeCreatePostTest.groovy
├── BoNoticeListGetTest.groovy
├── BoNoticeDetailGetTest.groovy
├── BoNoticeUpdatePutTest.groovy
├── BoNoticeDeleteTest.groovy
├── BoInquiryAnswerPostTest.groovy
├── BoInquiryListGetTest.groovy
├── BoInquiryDetailGetTest.groovy
├── BoFaqCreatePostTest.groovy
├── BoFaqUpdatePutTest.groovy
├── BoFaqDeleteTest.groovy
├── BoFaqListGetTest.groovy
└── BoFaqDetailGetTest.groovy
```


---

# `backoffice/BoLifestylePatternListGetTest.groovy`

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
class BoLifestylePatternListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9021, "GET /bo/lifestyle-patterns")
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
        HTTPResponse response = request.GET(targetHost + "/bo/lifestyle-patterns", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/lifestyle-patterns failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoLifestylePatternDetailGetTest.groovy`

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
class BoLifestylePatternDetailGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minPatternId = 1
    public static int maxPatternId = 100

    private Random random = new Random()
    private Long patternId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9022, "GET /bo/lifestyle-patterns/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        patternId = minPatternId + random.nextInt(maxPatternId - minPatternId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/lifestyle-patterns/" + patternId)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /bo/lifestyle-patterns/{} failed. status={}, body={}", patternId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

# `backoffice/BoLifestylePatternCreateMultipartTest.groovy`

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
import org.ngrinder.http.multipart.MultipartEntityBuilder
import org.apache.hc.core5.http.ContentType

import java.io.File
import java.util.Random

@RunWith(GrinderRunner)
class BoLifestylePatternCreateMultipartTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"
    public static String imagePath = "/tmp/ngrinder/lifestyle-100kb.jpg"

    private Random random = new Random()
    private String requestJson

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(30000)
        test = new GTest(9023, "POST /bo/lifestyle-patterns multipart")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        requestJson = """
        {
          "name": "nGrinder 라이프스타일 ${random.nextInt(1000000)}",
          "description": "nGrinder lifestyle pattern"
        }
        """
    }

    @Test
    public void test() {
        def builder = MultipartEntityBuilder.create()
        builder.addTextBody("request", requestJson, ContentType.APPLICATION_JSON)

        File image = new File(imagePath)
        if (image.exists()) {
            builder.addBinaryBody("file", image, ContentType.IMAGE_JPEG, image.getName())
        }

        HTTPResponse response = request.POST(targetHost + "/bo/lifestyle-patterns", builder.build())

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /bo/lifestyle-patterns multipart failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `backoffice/BoLifestylePatternUpdateMultipartTest.groovy`

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
import org.ngrinder.http.multipart.MultipartEntityBuilder
import org.apache.hc.core5.http.ContentType

import java.io.File
import java.util.Random

@RunWith(GrinderRunner)
class BoLifestylePatternUpdateMultipartTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"
    public static String imagePath = "/tmp/ngrinder/lifestyle-100kb.jpg"

    public static int minPatternId = 1
    public static int maxPatternId = 100

    private Random random = new Random()
    private Long patternId
    private String requestJson

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(30000)
        test = new GTest(9024, "PUT /bo/lifestyle-patterns/{id} multipart")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        patternId = minPatternId + random.nextInt(maxPatternId - minPatternId + 1)
        requestJson = """
        {
          "name": "nGrinder 라이프스타일 수정 ${System.currentTimeMillis()}",
          "description": "nGrinder updated lifestyle pattern"
        }
        """
    }

    @Test
    public void test() {
        def builder = MultipartEntityBuilder.create()
        builder.addTextBody("request", requestJson, ContentType.APPLICATION_JSON)

        File image = new File(imagePath)
        if (image.exists()) {
            builder.addBinaryBody("file", image, ContentType.IMAGE_JPEG, image.getName())
        }

        HTTPResponse response = request.PUT(targetHost + "/bo/lifestyle-patterns/" + patternId, builder.build())

        if (!(response.statusCode in [200, 400, 404, 409])) {
            grinder.logger.error("PUT /bo/lifestyle-patterns/{} multipart failed. status={}, body={}", patternId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoLifestylePatternDeleteTest.groovy`

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
class BoLifestylePatternDeleteTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minPatternId = 1
    public static int maxPatternId = 50

    private Random random = new Random()
    private Long patternId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9025, "DELETE /bo/lifestyle-patterns/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        patternId = minPatternId + random.nextInt(maxPatternId - minPatternId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.DELETE(targetHost + "/bo/lifestyle-patterns/" + patternId)

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("DELETE /bo/lifestyle-patterns/{} failed. status={}, body={}", patternId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoVerificationApproveListGetTest.groovy`

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
class BoVerificationApproveListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9026, "GET /bo/verifications/approve")
        request = new HTTPRequest()
        test.record(request)
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
        HTTPResponse response = request.GET(targetHost + "/bo/verifications/approve", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/verifications/approve failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoVerificationCancelListGetTest.groovy`

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
class BoVerificationCancelListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9027, "GET /bo/verifications/cancel")
        request = new HTTPRequest()
        test.record(request)
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
        HTTPResponse response = request.GET(targetHost + "/bo/verifications/cancel", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/verifications/cancel failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoVerificationWaitListGetTest.groovy`

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
class BoVerificationWaitListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9028, "GET /bo/verifications/wait")
        request = new HTTPRequest()
        test.record(request)
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
        HTTPResponse response = request.GET(targetHost + "/bo/verifications/wait", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/verifications/wait failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoVerificationWaitDetailGetTest.groovy`

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
class BoVerificationWaitDetailGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minVerificationId = 1
    public static int maxVerificationId = 1000

    private Random random = new Random()
    private Long verificationId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9029, "GET /bo/verifications/wait/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        verificationId = minVerificationId + random.nextInt(maxVerificationId - minVerificationId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/verifications/wait/" + verificationId)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /bo/verifications/wait/{} failed. status={}, body={}", verificationId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

# `backoffice/BoVerificationApprovePatchTest.groovy`

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

import java.util.Random

@RunWith(GrinderRunner)
class BoVerificationApprovePatchTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static boolean fixedTarget = true
    public static Long fixedVerificationId = 1L
    public static int minVerificationId = 1
    public static int maxVerificationId = 1000

    private Random random = new Random()
    private Long verificationId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9030, "PATCH /bo/verifications/wait/{id}/approve")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        verificationId = fixedTarget ? fixedVerificationId : minVerificationId + random.nextInt(maxVerificationId - minVerificationId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.PATCH(
            targetHost + "/bo/verifications/wait/" + verificationId + "/approve",
            "".getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("PATCH /bo/verifications/wait/{}/approve failed. status={}, body={}", verificationId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoVerificationCancelPatchTest.groovy`

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

import java.util.Random

@RunWith(GrinderRunner)
class BoVerificationCancelPatchTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static boolean fixedTarget = true
    public static Long fixedVerificationId = 1L
    public static int minVerificationId = 1
    public static int maxVerificationId = 1000

    private Random random = new Random()
    private Long verificationId
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9031, "PATCH /bo/verifications/wait/{id}/cancel")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        verificationId = fixedTarget ? fixedVerificationId : minVerificationId + random.nextInt(maxVerificationId - minVerificationId + 1)
        requestBody = """
        {
          "rejectReason": "nGrinder reject reason ${System.currentTimeMillis()}"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.PATCH(
            targetHost + "/bo/verifications/wait/" + verificationId + "/cancel",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("PATCH /bo/verifications/wait/{}/cancel failed. status={}, body={}", verificationId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoNoticeCreatePostTest.groovy`

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
class BoNoticeCreatePostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> ADMIN_TOKEN_POOL = [
        "TOKEN_ADMIN_1",
        "TOKEN_ADMIN_2"
    ]

    private Random random = new Random()
    private String adminToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9032, "POST /bo/notices")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        adminToken = ADMIN_TOKEN_POOL.get(grinder.threadNumber % ADMIN_TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int suffix = random.nextInt(1000000)
        requestBody = """
        {
          "title": "nGrinder 공지 ${suffix}",
          "content": "nGrinder notice content ${suffix}",
          "pinned": false
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + adminToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/bo/notices", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 401, 403, 409])) {
            grinder.logger.error("POST /bo/notices failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(401), is(403), is(409)))
    }
}
```


---

# `backoffice/BoNoticeListGetTest.groovy`

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
class BoNoticeListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9033, "GET /bo/notices")
        request = new HTTPRequest()
        test.record(request)
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
        HTTPResponse response = request.GET(targetHost + "/bo/notices", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/notices failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoNoticeDetailGetTest.groovy`

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
class BoNoticeDetailGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minNoticeId = 1
    public static int maxNoticeId = 1000

    private Random random = new Random()
    private Long noticeId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9034, "GET /bo/notices/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        noticeId = minNoticeId + random.nextInt(maxNoticeId - minNoticeId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/notices/" + noticeId)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /bo/notices/{} failed. status={}, body={}", noticeId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

# `backoffice/BoNoticeUpdatePutTest.groovy`

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
class BoNoticeUpdatePutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minNoticeId = 1
    public static int maxNoticeId = 1000

    public static final List<String> ADMIN_TOKEN_POOL = [
        "TOKEN_ADMIN_1",
        "TOKEN_ADMIN_2"
    ]

    private Random random = new Random()
    private String adminToken
    private Long noticeId
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9035, "PUT /bo/notices/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        adminToken = ADMIN_TOKEN_POOL.get(grinder.threadNumber % ADMIN_TOKEN_POOL.size())
    }

    @Before
    public void before() {
        noticeId = minNoticeId + random.nextInt(maxNoticeId - minNoticeId + 1)
        requestBody = """
        {
          "title": "nGrinder 공지 수정 ${System.currentTimeMillis()}",
          "content": "nGrinder updated notice content",
          "pinned": false
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + adminToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PUT(targetHost + "/bo/notices/" + noticeId, requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400, 401, 403, 404, 409])) {
            grinder.logger.error("PUT /bo/notices/{} failed. status={}, body={}", noticeId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(401), is(403), is(404), is(409)))
    }
}
```


---

# `backoffice/BoNoticeDeleteTest.groovy`

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
class BoNoticeDeleteTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minNoticeId = 1
    public static int maxNoticeId = 100

    public static final List<String> ADMIN_TOKEN_POOL = [
        "TOKEN_ADMIN_1",
        "TOKEN_ADMIN_2"
    ]

    private Random random = new Random()
    private String adminToken
    private Long noticeId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9036, "DELETE /bo/notices/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        adminToken = ADMIN_TOKEN_POOL.get(grinder.threadNumber % ADMIN_TOKEN_POOL.size())
    }

    @Before
    public void before() {
        noticeId = minNoticeId + random.nextInt(maxNoticeId - minNoticeId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + adminToken)
        ]

        HTTPResponse response = request.DELETE(targetHost + "/bo/notices/" + noticeId, headers)

        if (!(response.statusCode in [200, 204, 400, 401, 403, 404, 409])) {
            grinder.logger.error("DELETE /bo/notices/{} failed. status={}, body={}", noticeId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(401), is(403), is(404), is(409)))
    }
}
```


---

# `backoffice/BoInquiryAnswerPostTest.groovy`

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
class BoInquiryAnswerPostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minInquiryId = 1
    public static int maxInquiryId = 1000

    public static final List<String> ADMIN_TOKEN_POOL = [
        "TOKEN_ADMIN_1",
        "TOKEN_ADMIN_2"
    ]

    private Random random = new Random()
    private String adminToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9037, "POST /bo/inquiries")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        adminToken = ADMIN_TOKEN_POOL.get(grinder.threadNumber % ADMIN_TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int inquiryId = minInquiryId + random.nextInt(maxInquiryId - minInquiryId + 1)
        requestBody = """
        {
          "inquiryId": ${inquiryId},
          "content": "nGrinder inquiry answer ${System.currentTimeMillis()}"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + adminToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/bo/inquiries", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 401, 403, 404, 409])) {
            grinder.logger.error("POST /bo/inquiries failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(401), is(403), is(404), is(409)))
    }
}
```


---

# `backoffice/BoInquiryListGetTest.groovy`

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
class BoInquiryListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9038, "GET /bo/inquiries")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        params = new ArrayList<>()
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(50))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "createdAt,DESC"))

        if (random.nextBoolean()) {
            params.add(new BasicNameValuePair("keyword", "문의"))
        }
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/inquiries", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/inquiries failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoInquiryDetailGetTest.groovy`

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
class BoInquiryDetailGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minInquiryId = 1
    public static int maxInquiryId = 1000

    private Random random = new Random()
    private Long inquiryId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9039, "GET /bo/inquiries/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        inquiryId = minInquiryId + random.nextInt(maxInquiryId - minInquiryId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/inquiries/" + inquiryId)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /bo/inquiries/{} failed. status={}, body={}", inquiryId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

# `backoffice/BoFaqCreatePostTest.groovy`

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
class BoFaqCreatePostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static final List<String> ADMIN_TOKEN_POOL = [
        "TOKEN_ADMIN_1",
        "TOKEN_ADMIN_2"
    ]

    private Random random = new Random()
    private String adminToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9040, "POST /bo/faq")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        adminToken = ADMIN_TOKEN_POOL.get(grinder.threadNumber % ADMIN_TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int suffix = random.nextInt(1000000)
        requestBody = """
        {
          "question": "nGrinder FAQ 질문 ${suffix}",
          "answer": "nGrinder FAQ 답변 ${suffix}",
          "category": "GENERAL"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + adminToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.POST(targetHost + "/bo/faq", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 201, 400, 401, 403, 409])) {
            grinder.logger.error("POST /bo/faq failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(401), is(403), is(409)))
    }
}
```


---

# `backoffice/BoFaqUpdatePutTest.groovy`

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
class BoFaqUpdatePutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minFaqId = 1
    public static int maxFaqId = 1000

    public static final List<String> ADMIN_TOKEN_POOL = [
        "TOKEN_ADMIN_1",
        "TOKEN_ADMIN_2"
    ]

    private Random random = new Random()
    private String adminToken
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9041, "PUT /bo/faq")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        adminToken = ADMIN_TOKEN_POOL.get(grinder.threadNumber % ADMIN_TOKEN_POOL.size())
    }

    @Before
    public void before() {
        int faqId = minFaqId + random.nextInt(maxFaqId - minFaqId + 1)
        requestBody = """
        {
          "id": ${faqId},
          "question": "nGrinder FAQ 수정 ${System.currentTimeMillis()}",
          "answer": "nGrinder FAQ updated answer",
          "category": "GENERAL"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + adminToken),
            new BasicHeader("Content-Type", "application/json")
        ]

        HTTPResponse response = request.PUT(targetHost + "/bo/faq", requestBody.getBytes("UTF-8"), headers)

        if (!(response.statusCode in [200, 400, 401, 403, 404, 409])) {
            grinder.logger.error("PUT /bo/faq failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(401), is(403), is(404), is(409)))
    }
}
```


---

# `backoffice/BoFaqDeleteTest.groovy`

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
class BoFaqDeleteTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minFaqId = 1
    public static int maxFaqId = 100

    public static final List<String> ADMIN_TOKEN_POOL = [
        "TOKEN_ADMIN_1",
        "TOKEN_ADMIN_2"
    ]

    private Random random = new Random()
    private String adminToken
    private Long faqId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9042, "DELETE /bo/faq/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @BeforeThread
    public void beforeThread() {
        adminToken = ADMIN_TOKEN_POOL.get(grinder.threadNumber % ADMIN_TOKEN_POOL.size())
    }

    @Before
    public void before() {
        faqId = minFaqId + random.nextInt(maxFaqId - minFaqId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [
            new BasicHeader("Authorization", "Bearer " + adminToken)
        ]

        HTTPResponse response = request.DELETE(targetHost + "/bo/faq/" + faqId, headers)

        if (!(response.statusCode in [200, 204, 400, 401, 403, 404, 409])) {
            grinder.logger.error("DELETE /bo/faq/{} failed. status={}, body={}", faqId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(401), is(403), is(404), is(409)))
    }
}
```


---

# `backoffice/BoFaqListGetTest.groovy`

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
class BoFaqListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9043, "GET /bo/faq")
        request = new HTTPRequest()
        test.record(request)
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
        HTTPResponse response = request.GET(targetHost + "/bo/faq", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/faq failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoFaqDetailGetTest.groovy`

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
class BoFaqDetailGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minFaqId = 1
    public static int maxFaqId = 1000

    private Random random = new Random()
    private Long faqId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9044, "GET /bo/faq/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        faqId = minFaqId + random.nextInt(maxFaqId - minFaqId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/faq/" + faqId)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /bo/faq/{} failed. status={}, body={}", faqId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

여기까지가 **`backoffice-2` 24개 전문**이야.

남은 건 **`backoffice-3`**:

```plain text
backoffice/
├── BoAppVersionGetTest.groovy
├── BoAppVersionIosGetTest.groovy
├── BoAppVersionAndroidGetTest.groovy
├── BoAppVersionCreatePostTest.groovy
├── BoAppVersionUpdatePutTest.groovy
├── BoAuthEmailListGetTest.groovy
├── BoAuthEmailCreatePostTest.groovy
├── BoAuthEmailUpdatePutTest.groovy
├── BoAuthEmailDeleteTest.groovy
├── BoMemberListGetTest.groovy
├── BoMemberDetailGetTest.groovy
├── BoMemberCancelPatchTest.groovy
├── BoMemberUncancelPatchTest.groovy
├── BoMemberAuthPatchTest.groovy
├── BoReportWaitListGetTest.groovy
├── BoReportDoneListGetTest.groovy
├── BoReportHiddenPostTest.groovy
├── BoReportNoActionPostTest.groovy
├── BoReportSuspendedPostTest.groovy
├── BoBoardListGetTest.groovy
├── BoBoardDetailGetTest.groovy
├── BoBoardDeleteTest.groovy
└── BoBoardRecoverPatchTest.groovy
```


원하면 이어서 **`backoffice-3` 23개 전문** 뽑아줄게.