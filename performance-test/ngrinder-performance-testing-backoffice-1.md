좋아. **`backoffice-1` = 약관/약관유형 + 방옵션/방유형** 전문 뽑아줄게.

대상은 총 **20개**야.

```plain text
backoffice/
├── BoTermsTypeListGetTest.groovy
├── BoTermTypeUpdatePutTest.groovy
├── BoTermTypeCreatePostTest.groovy
├── BoTermTypeDeleteTest.groovy
├── BoTermsCreatePostTest.groovy
├── BoTermsDraftUpdatePutTest.groovy
├── BoTermsListGetTest.groovy
├── BoTermsDetailGetTest.groovy
├── BoTermsDeleteTest.groovy
├── BoTermsPublishPutTest.groovy
├── BoRoomAddOptionCreateMultipartTest.groovy
├── BoRoomAddOptionListGetTest.groovy
├── BoRoomAddOptionUpdateMultipartTest.groovy
├── BoRoomAddOptionDeleteTest.groovy
├── BoRoomAddOptionDetailGetTest.groovy
├── BoRoomTypeCreateMultipartTest.groovy
├── BoRoomTypeListGetTest.groovy
├── BoRoomTypeUpdateMultipartTest.groovy
├── BoRoomTypeDeleteTest.groovy
└── BoRoomTypeDetailGetTest.groovy
```


---

# `backoffice/BoTermsTypeListGetTest.groovy`

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
class BoTermsTypeListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9001, "GET /bo/type/terms")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/type/terms")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/type/terms failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoTermTypeUpdatePutTest.groovy`

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
class BoTermTypeUpdatePutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minTermTypeId = 1
    public static int maxTermTypeId = 20

    private Random random = new Random()
    private Long termTypeId
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9002, "PUT /bo/type/term/{termTypeId}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        termTypeId = minTermTypeId + random.nextInt(maxTermTypeId - minTermTypeId + 1)
        requestBody = """
        {
          "name": "nGrinder 약관유형 수정 ${System.currentTimeMillis()}",
          "required": true
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.PUT(
            targetHost + "/bo/type/term/" + termTypeId,
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 400, 404, 409])) {
            grinder.logger.error("PUT /bo/type/term/{} failed. status={}, body={}", termTypeId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoTermTypeCreatePostTest.groovy`

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
class BoTermTypeCreatePostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9003, "POST /bo/type/terms")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        int suffix = random.nextInt(1000000)
        requestBody = """
        {
          "name": "nGrinder 약관유형 ${suffix}",
          "required": true
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.POST(
            targetHost + "/bo/type/terms",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /bo/type/terms failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `backoffice/BoTermTypeDeleteTest.groovy`

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
class BoTermTypeDeleteTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minTermTypeId = 1
    public static int maxTermTypeId = 20

    private Random random = new Random()
    private Long termTypeId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9004, "DELETE /bo/type/term/{termTypeId}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        termTypeId = minTermTypeId + random.nextInt(maxTermTypeId - minTermTypeId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.DELETE(targetHost + "/bo/type/term/" + termTypeId)

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("DELETE /bo/type/term/{} failed. status={}, body={}", termTypeId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoTermsCreatePostTest.groovy`

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
class BoTermsCreatePostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9005, "POST /bo/terms")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        int suffix = random.nextInt(1000000)
        requestBody = """
        {
          "termTypeId": 1,
          "title": "nGrinder 약관 ${suffix}",
          "content": "nGrinder terms content ${suffix}",
          "required": true
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.POST(
            targetHost + "/bo/terms",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /bo/terms failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoTermsDraftUpdatePutTest.groovy`

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
class BoTermsDraftUpdatePutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minTermsId = 1
    public static int maxTermsId = 100

    private Random random = new Random()
    private Long termsId
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9006, "PUT /bo/terms/{termsId}/draft")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        termsId = minTermsId + random.nextInt(maxTermsId - minTermsId + 1)
        requestBody = """
        {
          "termTypeId": 1,
          "title": "nGrinder 임시저장 약관 ${System.currentTimeMillis()}",
          "content": "nGrinder draft terms content",
          "required": true
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.PUT(
            targetHost + "/bo/terms/" + termsId + "/draft",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 400, 404, 409])) {
            grinder.logger.error("PUT /bo/terms/{}/draft failed. status={}, body={}", termsId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoTermsListGetTest.groovy`

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
class BoTermsListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9007, "GET /bo/terms")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        params = new ArrayList<>()
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(20))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "createdAt,DESC"))

        if (random.nextBoolean()) {
            params.add(new BasicNameValuePair("keyword", "약관"))
        }
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/terms", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/terms failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoTermsDetailGetTest.groovy`

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
class BoTermsDetailGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minTermsId = 1
    public static int maxTermsId = 100

    private Random random = new Random()
    private Long termsId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9008, "GET /bo/terms/{termsId}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        termsId = minTermsId + random.nextInt(maxTermsId - minTermsId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/terms/" + termsId)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /bo/terms/{} failed. status={}, body={}", termsId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

# `backoffice/BoTermsDeleteTest.groovy`

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
class BoTermsDeleteTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minTermsId = 1
    public static int maxTermsId = 50

    private Random random = new Random()
    private Long termsId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9009, "DELETE /bo/terms/{termsId}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        termsId = minTermsId + random.nextInt(maxTermsId - minTermsId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.DELETE(targetHost + "/bo/terms/" + termsId)

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("DELETE /bo/terms/{} failed. status={}, body={}", termsId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoTermsPublishPutTest.groovy`

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
class BoTermsPublishPutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minTermsId = 1
    public static int maxTermsId = 100

    private Random random = new Random()
    private Long termsId
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9010, "PUT /bo/terms/{termsId}/publish")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        termsId = minTermsId + random.nextInt(maxTermsId - minTermsId + 1)
        requestBody = """
        {
          "termTypeId": 1,
          "title": "nGrinder 게시 약관 ${System.currentTimeMillis()}",
          "content": "nGrinder publish terms content",
          "required": true
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.PUT(
            targetHost + "/bo/terms/" + termsId + "/publish",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 400, 404, 409])) {
            grinder.logger.error("PUT /bo/terms/{}/publish failed. status={}, body={}", termsId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoRoomAddOptionCreateMultipartTest.groovy`

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
class BoRoomAddOptionCreateMultipartTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"
    public static String imagePath = "/tmp/ngrinder/option-100kb.jpg"

    private Random random = new Random()
    private String requestJson

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(30000)
        test = new GTest(9011, "POST /bo/room-add-options multipart")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        requestJson = """
        {
          "name": "nGrinder 추가옵션 ${random.nextInt(1000000)}",
          "description": "nGrinder room add option"
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

        HTTPResponse response = request.POST(targetHost + "/bo/room-add-options", builder.build())

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /bo/room-add-options multipart failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `backoffice/BoRoomAddOptionListGetTest.groovy`

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
class BoRoomAddOptionListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9012, "GET /bo/room-add-options")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        params = new ArrayList<>()
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(10))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "id,DESC"))
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/room-add-options", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/room-add-options failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoRoomAddOptionUpdateMultipartTest.groovy`

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
class BoRoomAddOptionUpdateMultipartTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"
    public static String imagePath = "/tmp/ngrinder/option-100kb.jpg"

    public static int minOptionId = 1
    public static int maxOptionId = 100

    private Random random = new Random()
    private Long optionId
    private String requestJson

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(30000)
        test = new GTest(9013, "PUT /bo/room-add-options/{id} multipart")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        optionId = minOptionId + random.nextInt(maxOptionId - minOptionId + 1)
        requestJson = """
        {
          "name": "nGrinder 추가옵션 수정 ${System.currentTimeMillis()}",
          "description": "nGrinder updated room add option"
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

        HTTPResponse response = request.PUT(targetHost + "/bo/room-add-options/" + optionId, builder.build())

        if (!(response.statusCode in [200, 400, 404, 409])) {
            grinder.logger.error("PUT /bo/room-add-options/{} multipart failed. status={}, body={}", optionId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoRoomAddOptionDeleteTest.groovy`

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
class BoRoomAddOptionDeleteTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minOptionId = 1
    public static int maxOptionId = 50

    private Random random = new Random()
    private Long optionId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9014, "DELETE /bo/room-add-options/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        optionId = minOptionId + random.nextInt(maxOptionId - minOptionId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.DELETE(targetHost + "/bo/room-add-options/" + optionId)

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("DELETE /bo/room-add-options/{} failed. status={}, body={}", optionId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoRoomAddOptionDetailGetTest.groovy`

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
class BoRoomAddOptionDetailGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minOptionId = 1
    public static int maxOptionId = 100

    private Random random = new Random()
    private Long optionId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9015, "GET /bo/room-add-options/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        optionId = minOptionId + random.nextInt(maxOptionId - minOptionId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/room-add-options/" + optionId)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /bo/room-add-options/{} failed. status={}, body={}", optionId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

# `backoffice/BoRoomTypeCreateMultipartTest.groovy`

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
class BoRoomTypeCreateMultipartTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"
    public static String imagePath = "/tmp/ngrinder/room-type-100kb.jpg"

    private Random random = new Random()
    private String requestJson

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(30000)
        test = new GTest(9016, "POST /bo/room-types multipart")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        requestJson = """
        {
          "name": "nGrinder 방유형 ${random.nextInt(1000000)}",
          "description": "nGrinder room type"
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

        HTTPResponse response = request.POST(targetHost + "/bo/room-types", builder.build())

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /bo/room-types multipart failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `backoffice/BoRoomTypeListGetTest.groovy`

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
class BoRoomTypeListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9017, "GET /bo/room-types")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        params = new ArrayList<>()
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(10))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "id,DESC"))
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/room-types", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/room-types failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoRoomTypeUpdateMultipartTest.groovy`

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
class BoRoomTypeUpdateMultipartTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"
    public static String imagePath = "/tmp/ngrinder/room-type-100kb.jpg"

    public static int minRoomTypeId = 1
    public static int maxRoomTypeId = 100

    private Random random = new Random()
    private Long roomTypeId
    private String requestJson

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(30000)
        test = new GTest(9018, "PUT /bo/room-types/{id} multipart")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        roomTypeId = minRoomTypeId + random.nextInt(maxRoomTypeId - minRoomTypeId + 1)
        requestJson = """
        {
          "name": "nGrinder 방유형 수정 ${System.currentTimeMillis()}",
          "description": "nGrinder updated room type"
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

        HTTPResponse response = request.PUT(targetHost + "/bo/room-types/" + roomTypeId, builder.build())

        if (!(response.statusCode in [200, 400, 404, 409])) {
            grinder.logger.error("PUT /bo/room-types/{} multipart failed. status={}, body={}", roomTypeId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoRoomTypeDeleteTest.groovy`

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
class BoRoomTypeDeleteTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minRoomTypeId = 1
    public static int maxRoomTypeId = 50

    private Random random = new Random()
    private Long roomTypeId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9019, "DELETE /bo/room-types/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        roomTypeId = minRoomTypeId + random.nextInt(maxRoomTypeId - minRoomTypeId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.DELETE(targetHost + "/bo/room-types/" + roomTypeId)

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("DELETE /bo/room-types/{} failed. status={}, body={}", roomTypeId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoRoomTypeDetailGetTest.groovy`

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
class BoRoomTypeDetailGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minRoomTypeId = 1
    public static int maxRoomTypeId = 100

    private Random random = new Random()
    private Long roomTypeId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9020, "GET /bo/room-types/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        roomTypeId = minRoomTypeId + random.nextInt(maxRoomTypeId - minRoomTypeId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/room-types/" + roomTypeId)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /bo/room-types/{} failed. status={}, body={}", roomTypeId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

여기까지가 **`backoffice-1` 20개 전문**이야.

다음은 **`backoffice-2`**:

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


원하면 이어서 **`backoffice-2` 24개 전문** 바로 뽑아줄게.