좋아. 마지막 **`backoffice-3` 23개 전문**이야.

대상:

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


---

# `backoffice/BoAppVersionGetTest.groovy`

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
class BoAppVersionGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9045, "GET /bo/app-version")
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
        HTTPResponse response = request.GET(targetHost + "/bo/app-version", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/app-version failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoAppVersionIosGetTest.groovy`

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
class BoAppVersionIosGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9046, "GET /bo/app-version/ios")
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
        HTTPResponse response = request.GET(targetHost + "/bo/app-version/ios", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/app-version/ios failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoAppVersionAndroidGetTest.groovy`

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
class BoAppVersionAndroidGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9047, "GET /bo/app-version/android")
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
        HTTPResponse response = request.GET(targetHost + "/bo/app-version/android", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/app-version/android failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoAppVersionCreatePostTest.groovy`

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
class BoAppVersionCreatePostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9048, "POST /bo/app-version")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        int patch = random.nextInt(1000000)
        requestBody = """
        {
          "platform": "ANDROID",
          "version": "1.0.${patch}",
          "forceUpdate": false,
          "description": "nGrinder app version"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.POST(
            targetHost + "/bo/app-version",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /bo/app-version failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `backoffice/BoAppVersionUpdatePutTest.groovy`

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
class BoAppVersionUpdatePutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minAppVersionId = 1
    public static int maxAppVersionId = 100

    private Random random = new Random()
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9049, "PUT /bo/app-version")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        int appVersionId = minAppVersionId + random.nextInt(maxAppVersionId - minAppVersionId + 1)
        int patch = random.nextInt(1000000)

        requestBody = """
        {
          "id": ${appVersionId},
          "platform": "ANDROID",
          "version": "1.1.${patch}",
          "forceUpdate": false,
          "description": "nGrinder updated app version"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.PUT(
            targetHost + "/bo/app-version",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 400, 404, 409])) {
            grinder.logger.error("PUT /bo/app-version failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoAuthEmailListGetTest.groovy`

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
class BoAuthEmailListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9050, "GET /bo/auth-email")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/auth-email")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/auth-email failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoAuthEmailCreatePostTest.groovy`

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
class BoAuthEmailCreatePostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9051, "POST /bo/auth-email")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        int suffix = random.nextInt(1000000)
        requestBody = """
        {
          "domain": "ngrinder${suffix}.test.com",
          "type": "COMPANY"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.POST(
            targetHost + "/bo/auth-email",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 201, 400, 409])) {
            grinder.logger.error("POST /bo/auth-email failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(409)))
    }
}
```


---

# `backoffice/BoAuthEmailUpdatePutTest.groovy`

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
class BoAuthEmailUpdatePutTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minAuthEmailId = 1
    public static int maxAuthEmailId = 100

    private Random random = new Random()
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9052, "PUT /bo/auth-email")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        int authEmailId = minAuthEmailId + random.nextInt(maxAuthEmailId - minAuthEmailId + 1)

        requestBody = """
        {
          "id": ${authEmailId},
          "domain": "updated${authEmailId}.test.com",
          "type": "COMPANY"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.PUT(
            targetHost + "/bo/auth-email",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 400, 404, 409])) {
            grinder.logger.error("PUT /bo/auth-email failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoAuthEmailDeleteTest.groovy`

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
class BoAuthEmailDeleteTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minAuthEmailId = 1
    public static int maxAuthEmailId = 50

    private Random random = new Random()
    private Long authEmailId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9053, "DELETE /bo/auth-email/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        authEmailId = minAuthEmailId + random.nextInt(maxAuthEmailId - minAuthEmailId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.DELETE(targetHost + "/bo/auth-email/" + authEmailId)

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("DELETE /bo/auth-email/{} failed. status={}, body={}", authEmailId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoMemberListGetTest.groovy`

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
class BoMemberListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9054, "GET /bo/member")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        params = new ArrayList<>()
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(100))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "createdAt,DESC"))

        if (random.nextInt(100) < 30) {
            params.add(new BasicNameValuePair("keyword", ["test", "user", "admin", "knock"][random.nextInt(4)]))
        }
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/member", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/member failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoMemberDetailGetTest.groovy`

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
class BoMemberDetailGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minMemberId = 1
    public static int maxMemberId = 10000

    private Random random = new Random()
    private Long memberId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9055, "GET /bo/member/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        memberId = minMemberId + random.nextInt(maxMemberId - minMemberId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/member/" + memberId)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /bo/member/{} failed. status={}, body={}", memberId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

# `backoffice/BoMemberCancelPatchTest.groovy`

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
class BoMemberCancelPatchTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static boolean fixedTarget = true
    public static Long fixedMemberId = 1L
    public static int minMemberId = 1
    public static int maxMemberId = 10000

    private Random random = new Random()
    private Long memberId
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9056, "PATCH /bo/member/cancel/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        memberId = fixedTarget ? fixedMemberId : minMemberId + random.nextInt(maxMemberId - minMemberId + 1)
        requestBody = """
        {
          "reason": "nGrinder member cancel ${System.currentTimeMillis()}",
          "days": 7
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.PATCH(
            targetHost + "/bo/member/cancel/" + memberId,
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("PATCH /bo/member/cancel/{} failed. status={}, body={}", memberId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoMemberUncancelPatchTest.groovy`

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
class BoMemberUncancelPatchTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static boolean fixedTarget = true
    public static Long fixedMemberId = 1L
    public static int minMemberId = 1
    public static int maxMemberId = 10000

    private Random random = new Random()
    private Long memberId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9057, "PATCH /bo/member/uncancel/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        memberId = fixedTarget ? fixedMemberId : minMemberId + random.nextInt(maxMemberId - minMemberId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.PATCH(
            targetHost + "/bo/member/uncancel/" + memberId,
            "".getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("PATCH /bo/member/uncancel/{} failed. status={}, body={}", memberId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoMemberAuthPatchTest.groovy`

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
class BoMemberAuthPatchTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minMemberId = 1
    public static int maxMemberId = 10000

    private Random random = new Random()
    private Long memberId
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9058, "PATCH /bo/member/auth/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        memberId = minMemberId + random.nextInt(maxMemberId - minMemberId + 1)

        requestBody = """
        {
          "role": "USER"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.PATCH(
            targetHost + "/bo/member/auth/" + memberId,
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 400, 404, 409])) {
            grinder.logger.error("PATCH /bo/member/auth/{} failed. status={}, body={}", memberId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoReportWaitListGetTest.groovy`

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
class BoReportWaitListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9059, "GET /bo/report/wait")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        params = new ArrayList<>()
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(50))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "createdAt,DESC"))
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/report/wait", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/report/wait failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoReportDoneListGetTest.groovy`

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
class BoReportDoneListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9060, "GET /bo/report/done")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        params = new ArrayList<>()
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(50))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "createdAt,DESC"))
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/report/done", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/report/done failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoReportHiddenPostTest.groovy`

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
class BoReportHiddenPostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minReportId = 1
    public static int maxReportId = 1000

    private Random random = new Random()
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9061, "POST /bo/report/hidden")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        int reportId = minReportId + random.nextInt(maxReportId - minReportId + 1)

        requestBody = """
        {
          "reportId": ${reportId},
          "reason": "nGrinder hidden report ${System.currentTimeMillis()}"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.POST(
            targetHost + "/bo/report/hidden",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /bo/report/hidden failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoReportNoActionPostTest.groovy`

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
class BoReportNoActionPostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minReportId = 1
    public static int maxReportId = 1000

    private Random random = new Random()
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9062, "POST /bo/report/no-action")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        int reportId = minReportId + random.nextInt(maxReportId - minReportId + 1)

        requestBody = """
        {
          "reportId": ${reportId},
          "reason": "nGrinder no action report ${System.currentTimeMillis()}"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.POST(
            targetHost + "/bo/report/no-action",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /bo/report/no-action failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoReportSuspendedPostTest.groovy`

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
class BoReportSuspendedPostTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minReportId = 1
    public static int maxReportId = 1000

    private Random random = new Random()
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9063, "POST /bo/report/suspended")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        int reportId = minReportId + random.nextInt(maxReportId - minReportId + 1)

        requestBody = """
        {
          "reportId": ${reportId},
          "reason": "nGrinder suspended report ${System.currentTimeMillis()}",
          "days": 7
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.POST(
            targetHost + "/bo/report/suspended",
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 201, 400, 404, 409])) {
            grinder.logger.error("POST /bo/report/suspended failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(201), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoBoardListGetTest.groovy`

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
class BoBoardListGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9064, "GET /bo/boards")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        params = new ArrayList<>()
        params.add(new BasicNameValuePair("page", String.valueOf(random.nextInt(100))))
        params.add(new BasicNameValuePair("size", "20"))
        params.add(new BasicNameValuePair("sort", "createdAt,DESC"))

        if (random.nextInt(100) < 30) {
            params.add(new BasicNameValuePair("keyword", ["원룸", "투룸", "서울", "강남"][random.nextInt(4)]))
        }
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/boards", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /bo/boards failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `backoffice/BoBoardDetailGetTest.groovy`

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
class BoBoardDetailGetTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minBoardId = 1
    public static int maxBoardId = 10000

    private Random random = new Random()
    private Long boardId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)
        test = new GTest(9065, "GET /bo/board/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        boardId = minBoardId + random.nextInt(maxBoardId - minBoardId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/bo/board/" + boardId)

        if (!(response.statusCode in [200, 404])) {
            grinder.logger.error("GET /bo/board/{} failed. status={}, body={}", boardId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(404)))
    }
}
```


---

# `backoffice/BoBoardDeleteTest.groovy`

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
class BoBoardDeleteTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minBoardId = 1
    public static int maxBoardId = 100

    private Random random = new Random()
    private Long boardId
    private String requestBody

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9066, "DELETE /bo/board/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        boardId = minBoardId + random.nextInt(maxBoardId - minBoardId + 1)

        requestBody = """
        {
          "reason": "nGrinder board delete ${System.currentTimeMillis()}"
        }
        """
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.DELETE(
            targetHost + "/bo/board/" + boardId,
            requestBody.getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("DELETE /bo/board/{} failed. status={}, body={}", boardId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

# `backoffice/BoBoardRecoverPatchTest.groovy`

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
class BoBoardRecoverPatchTest {
    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minBoardId = 1
    public static int maxBoardId = 100

    private Random random = new Random()
    private Long boardId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)
        test = new GTest(9067, "PATCH /bo/board/recover/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        boardId = minBoardId + random.nextInt(maxBoardId - minBoardId + 1)
    }

    @Test
    public void test() {
        List<Header> headers = [new BasicHeader("Content-Type", "application/json")]

        HTTPResponse response = request.PATCH(
            targetHost + "/bo/board/recover/" + boardId,
            "".getBytes("UTF-8"),
            headers
        )

        if (!(response.statusCode in [200, 204, 400, 404, 409])) {
            grinder.logger.error("PATCH /bo/board/recover/{} failed. status={}, body={}", boardId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, anyOf(is(200), is(204), is(400), is(404), is(409)))
    }
}
```


---

여기까지가 **`backoffice-3` 23개 전문**이고, 이걸로 지금까지 뽑은 범위는 아래 전체야.

```plain text
meta/                  14개
user/                  28개
roommate/              13개
chat-request + chat/   12개
block + verification + roommate-request/ 13개
roommate-management/   17개
alarm + inquiry/       8개
backoffice-1           20개
backoffice-2           24개
backoffice-3           23개
```


총 **172개 수준의 API별 스크립트 세트**로 구성 완료.