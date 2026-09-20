좋아. 그럼 **1차로 `meta/` 폴더 전체 14개 nGrinder Groovy 스크립트 전문**부터 뽑을게.

아래 파일들은 전부 **인증 없이 호출 가능한 GET API 기준**으로 작성했어.  
`targetHost`만 네 환경에 맞게 바꾸면 됨.

```groovy
public static String targetHost = "http://host.docker.internal:8080"
```


---

# `meta/TermsListGetTest.groovy`

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
class TermsListGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2001, "GET /terms")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/terms")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /terms failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/TermsDetailGetTest.groovy`

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
class TermsDetailGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minTermsId = 1
    public static int maxTermsId = 10

    private Random random = new Random()
    private Long termsId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2002, "GET /terms/{termsId}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        termsId = minTermsId + random.nextInt(maxTermsId - minTermsId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/terms/" + termsId)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /terms/{} failed. status={}, body={}", termsId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/PopularSearchGetTest.groovy`

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
class PopularSearchGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2003, "GET /search/popular")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/search/popular")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /search/popular failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/MetaLifestylePatternsGetTest.groovy`

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
class MetaLifestylePatternsGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2004, "GET /meta/lifestyle-patterns")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/meta/lifestyle-patterns")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /meta/lifestyle-patterns failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/MetaRoomTypesGetTest.groovy`

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
class MetaRoomTypesGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2005, "GET /meta/room-types")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/meta/room-types")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /meta/room-types failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/MetaRegionsGetTest.groovy`

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
class MetaRegionsGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(15000)

        test = new GTest(2006, "GET /meta/regions")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/meta/regions")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /meta/regions failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/MetaRoomAddOptionsGetTest.groovy`

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
class MetaRoomAddOptionsGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2007, "GET /meta/room-add-options")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/meta/room-add-options")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /meta/room-add-options failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/MetaFaqListGetTest.groovy`

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
class MetaFaqListGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2008, "GET /meta/faq")
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
        HTTPResponse response = request.GET(targetHost + "/meta/faq", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /meta/faq failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/MetaFaqAllGetTest.groovy`

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
class MetaFaqAllGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    private Random random = new Random()
    private List<NameValuePair> params

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2009, "GET /meta/faqAll")
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
        HTTPResponse response = request.GET(targetHost + "/meta/faqAll", params)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /meta/faqAll failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/MetaFaqDetailGetTest.groovy`

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
class MetaFaqDetailGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    public static int minFaqId = 1
    public static int maxFaqId = 100

    private Random random = new Random()
    private Long faqId

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2010, "GET /meta/faq/{id}")
        request = new HTTPRequest()
        test.record(request)
    }

    @Before
    public void before() {
        faqId = minFaqId + random.nextInt(maxFaqId - minFaqId + 1)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/meta/faq/" + faqId)

        if (response.statusCode != 200) {
            grinder.logger.error("GET /meta/faq/{} failed. status={}, body={}", faqId, response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/AppVersionAndroidGetTest.groovy`

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
class AppVersionAndroidGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2011, "GET /meta/app-version/android")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/meta/app-version/android")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /meta/app-version/android failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/AppVersionIosGetTest.groovy`

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
class AppVersionIosGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2012, "GET /meta/app-version/ios")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/meta/app-version/ios")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /meta/app-version/ios failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/AuthEmailListGetTest.groovy`

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
class AuthEmailListGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2013, "GET /meta/auth-email")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/meta/auth-email")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /meta/auth-email failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

# `meta/AuthSuccessGetTest.groovy`

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
class AuthSuccessGetTest {

    public static GTest test
    public static HTTPRequest request
    public static String targetHost = "http://host.docker.internal:8080"

    @BeforeProcess
    public static void beforeProcess() {
        HTTPRequestControl.setConnectionTimeout(5000)
        HTTPRequestControl.setReadTimeout(10000)

        test = new GTest(2014, "GET /auth/success")
        request = new HTTPRequest()
        test.record(request)
    }

    @Test
    public void test() {
        HTTPResponse response = request.GET(targetHost + "/auth/success")

        if (response.statusCode != 200) {
            grinder.logger.error("GET /auth/success failed. status={}, body={}", response.statusCode, response.bodyText)
            grinder.statistics.forLastTest.success = false
            return
        }

        assertThat(response.statusCode, is(200))
    }
}
```


---

여기까지가 **`meta/` 14개 전체 스크립트 전문**이야.

다음은 바로 **`user/` 전체 28개 스크립트**로 이어서 뽑으면 됨.