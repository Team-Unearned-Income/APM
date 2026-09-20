확인했어. 결론부터 말하면 **`docs/ngrinder-performance-testing-*.md`에 정리된 “테스트 대상 API 목록” 자체는 현재 Controller 기준으로 거의 맞다.**  
특히 **누락된 Controller API는 없어 보임.**

다만 **“nGrinder로 실제 부하 테스트 해야 하는 목록”으로 보면 조정해야 할 항목이 몇 개 있음.**

---

## 1. 점검한 문서 파일

`docs/` 안에서 확인된 파일은 아래 11개야.

```plain text
docs/ngrinder-performance-testing-alarm-inquiry.md
docs/ngrinder-performance-testing-backoffice-1.md
docs/ngrinder-performance-testing-backoffice-2.md
docs/ngrinder-performance-testing-backoffice-3.md
docs/ngrinder-performance-testing-block-verification-roommate_request.md
docs/ngrinder-performance-testing-chat.md
docs/ngrinder-performance-testing-guide.md
docs/ngrinder-performance-testing-meta.md
docs/ngrinder-performance-testing-roommate.md
docs/ngrinder-performance-testing-roommate_management.md
docs/ngrinder-performance-testing-user.md
```


이 중에서 실제 스크립트 전문 문서는 아래 10개고,

```plain text
ngrinder-performance-testing-meta.md
ngrinder-performance-testing-user.md
ngrinder-performance-testing-roommate.md
ngrinder-performance-testing-chat.md
ngrinder-performance-testing-block-verification-roommate_request.md
ngrinder-performance-testing-roommate_management.md
ngrinder-performance-testing-alarm-inquiry.md
ngrinder-performance-testing-backoffice-1.md
ngrinder-performance-testing-backoffice-2.md
ngrinder-performance-testing-backoffice-3.md
```


`ngrinder-performance-testing-guide.md`는 **가이드/매트릭스 문서**라서 “스크립트 목록”으로 세면 안 됨.

---

## 2. Controller 기준 API 커버리지 결과

현재 Controller 기준으로 문서에 잡힌 API 수는 대략 아래와 맞음.

| 영역 | Controller 기준 API 수 | 문서 스크립트 수 | 판정 |
|---|---:|---:|---|
| 메타/일반 | 14 | 14 | 맞음 |
| 유저/프로필 | 28 | 28 | 맞음 |
| 룸메이트 게시글/매칭 | 13 | 13 | 맞음 |
| 채팅 요청 | 6 | 6 | 맞음 |
| 채팅 REST + WebSocket | 6 | 6 | 맞음 |
| 차단 | 3 | 3 | 맞음 |
| 인증/검증 | 5 | 5 | 맞음 |
| 룸메이트 요청 | 5 | 5 | 맞음 |
| 룸메이트 관리 | 17 | 17 | 맞음 |
| 알림 | 4 | 4 | 맞음 |
| 문의 | 4 | 4 | 맞음 |
| 백오피스 | 67 | 67 | 맞음 |

**총합: 172개 수준으로 Controller 엔드포인트와 문서 스크립트 목록은 맞게 잡혀 있음.**

---

## 3. “목록” 기준으로는 맞지만, “nGrinder 부하 테스트 대상”으로는 분리 필요

전체 API가 문서에 들어간 건 맞는데, **전부 같은 강도로 nGrinder 부하 테스트하면 안 됨.**

아래는 분리해야 함.

---

## 4. nGrinder Load/Stress 대상으로 적절한 API

얘네는 실제로 nGrinder로 부하 테스트 대상 맞음.

### 메타 조회

```plain text
GET /terms
GET /terms/{termsId}
GET /search/popular
GET /meta/lifestyle-patterns
GET /meta/room-types
GET /meta/regions
GET /meta/room-add-options
GET /meta/faq
GET /meta/faqAll
GET /meta/faq/{id}
GET /meta/app-version/android
GET /meta/app-version/ios
GET /meta/auth-email
```


### 유저 조회

```plain text
GET /users/me/preferences/all
GET /users/me/profile
GET /users/me/profile/all
GET /users/me/boards
GET /users/me/verifications
GET /users/me/notification-settings
GET /users/me/notices
GET /users/me/notices/{id}
GET /users/me/account
```


### 룸메이트 핵심

```plain text
GET /roommate/boards
GET /roommate/boards/{boardId}
GET /roommate/boards/{boardId}/edit
GET /roommate/matches
GET /roommate/matches/{memberId}
GET /roommate/matches/score
```


### 채팅 조회

```plain text
GET /chats
GET /chats/{chatRoomId}
```


### 룸메이트 관리 조회

```plain text
GET /roommates/me
GET /roommates/me/house-rule
GET /roommates/me/house-rule/{id}
GET /roommates/me/calendar
GET /roommates/me/calendar?year=&month=&day=
GET /roommates/me/calendar/{id}
GET /roommates/me/calendar/categories
GET /roommates/me/calendar/edit
```


### 알림/문의 조회

```plain text
GET /alarms
GET /inquiries
GET /inquiries/{inquiryId}
GET /inquiries/categorys
```


### 백오피스 조회

```plain text
GET /bo/type/terms
GET /bo/terms
GET /bo/terms/{termsId}
GET /bo/room-add-options
GET /bo/room-add-options/{id}
GET /bo/room-types
GET /bo/room-types/{id}
GET /bo/lifestyle-patterns
GET /bo/lifestyle-patterns/{id}
GET /bo/verifications/approve
GET /bo/verifications/cancel
GET /bo/verifications/wait
GET /bo/verifications/wait/{id}
GET /bo/notices
GET /bo/notices/{id}
GET /bo/inquiries
GET /bo/inquiries/{id}
GET /bo/faq
GET /bo/faq/{id}
GET /bo/app-version
GET /bo/app-version/ios
GET /bo/app-version/android
GET /bo/auth-email
GET /bo/member
GET /bo/member/{id}
GET /bo/report/wait
GET /bo/report/done
GET /bo/boards
GET /bo/board/{id}
```


---

## 5. nGrinder로 가능하지만 “주의해서 제한적 테스트” 해야 하는 API

얘네는 문서에 있는 게 맞긴 한데, **무작정 Load/Stress 돌리면 데이터가 계속 쌓이거나 상태가 꼬임.**

### 쓰기 API

```plain text
POST /users/me/profile/basic
POST /users/me/profile/lifestyle
POST /users/me/profile/roominfo
POST /users/me/profile/all
PUT /users/me/profile/lifestyle
PUT /users/me/profile/roominfo
PUT /users/me/profile/all
POST /users/me/preferences/lifestyle
POST /users/me/preferences/conditions
POST /users/me/preferences/all
PUT /users/me/preferences/lifestyle
PUT /users/me/preferences/conditions
PUT /users/me/preferences/all
PATCH /users/me/visibility
PATCH /users/me/notification-settings
POST /users/me/devices
POST /roommate/boards/{boardId}/reports
POST /roommate/matches/{memberId}/reports
POST /roommate-requests
POST /roommates/me/house-rule
PUT /roommates/me/house-rule/{id}
POST /roommates/me/calendar
POST /roommates/me/calendar/repeat
PUT /roommates/me/calendar/{id}
PUT /roommates/me/calendar/repeat/{id}
POST /inquiries
```


이건 **테스트 계정/fixture 분리**가 필요함.

---

## 6. Concurrency 테스트로 따로 빼야 하는 API

얘네는 목록에 있는 게 맞고, 일반 부하보다 **동시성 검증용**으로 따로 돌리는 게 맞음.

```plain text
POST /roommate/boards/{boardId}/likes
POST /roommate/matches/{memberId}/likes
POST /blocks
DELETE /blocks/{blockId}
POST /roommate-requests/{requestId}/accept
POST /roommate-requests/{requestId}/reject
POST /roommate-requests/{requestId}/cancel
POST /chat-requests/{requestId}/accept
POST /chat-requests/{requestId}/reject
POST /chat-requests/{requestId}/cancel
PATCH /bo/verifications/wait/{id}/approve
PATCH /bo/verifications/wait/{id}/cancel
PATCH /bo/member/cancel/{id}
PATCH /bo/member/uncancel/{id}
```


---

## 7. nGrinder 대량 부하 테스트에서 빼거나 Smoke만 해야 하는 API

문서에 있는 건 맞지만, **nGrinder로 강하게 치면 안 되는 것들**이 있음.

### 삭제 API

```plain text
DELETE /users/me
DELETE /roommate/boards/{boardId}
DELETE /roommates/me/{id}
DELETE /roommates/me/house-rule/{id}
DELETE /roommates/me/calendar/{id}
DELETE /bo/type/term/{termTypeId}
DELETE /bo/terms/{termsId}
DELETE /bo/room-add-options/{id}
DELETE /bo/room-types/{id}
DELETE /bo/lifestyle-patterns/{id}
DELETE /bo/notices/{id}
DELETE /bo/faq/{id}
DELETE /bo/auth-email/{id}
DELETE /bo/board/{id}
```


이건 **Smoke 또는 fixture 전용 데이터에서만** 해야 함.

### 외부 의존성/이메일

```plain text
POST /auth/verify/student/send
POST /auth/verify/company/send
POST /auth/verify/student/confirm
POST /auth/verify/company/confirm
POST /auth/verify/webhook
```


이건 **Mock 환경 아니면 대량 부하 금지**가 맞음.

### 로그아웃/상태 변경

```plain text
POST /users/me/logout
```


이것도 반복 부하 돌리면 토큰/디바이스 상태가 꼬일 수 있어서 **Smoke 권장**.

---

## 8. SSE/WebSocket은 문서에 있으나 nGrinder 일반 HTTP 테스트로 보면 안 됨

아래 2개는 목록에 있는 건 맞는데, **일반 HTTP TPS 테스트와 분리해야 함.**

```plain text
GET /alarms/subscribe
WebSocket /app/chats/{chatRoomId}/messages
```


특히 `GET /alarms/subscribe`는 TPS보다 이걸 봐야 함.

```plain text
동시 연결 수
연결 유지 시간
서버 메모리
Emitter 정리 여부
끊김/재연결
```


WebSocket은 문서에 스크립트가 있긴 한데, 실제 nGrinder 환경에서 `jakarta.websocket` 구현체가 classpath에 없으면 바로 안 돌 수 있음.  
그래서 **목록에는 포함 맞음**, 하지만 실행 도구는 nGrinder보다 **k6 / Artillery / Gatling** 쪽이 더 안정적일 수 있음.

---

## 9. 문서상 수정이 필요한 포인트

목록 자체는 맞는데, 문서 표현상 아래는 정리하는 게 좋음.

### 1) `ngrinder-performance-testing-guide.md`는 스크립트 목록에서 제외

이 파일은 가이드라서 스크립트 파일 카운트에 넣으면 중복됨.

권장 분류:

```plain text
스크립트 전문 문서:
- ngrinder-performance-testing-meta.md
- ngrinder-performance-testing-user.md
- ngrinder-performance-testing-roommate.md
- ngrinder-performance-testing-chat.md
- ngrinder-performance-testing-block-verification-roommate_request.md
- ngrinder-performance-testing-roommate_management.md
- ngrinder-performance-testing-alarm-inquiry.md
- ngrinder-performance-testing-backoffice-1.md
- ngrinder-performance-testing-backoffice-2.md
- ngrinder-performance-testing-backoffice-3.md

가이드 문서:
- ngrinder-performance-testing-guide.md
```


### 2) 백오피스 일부 API는 인증 헤더가 빠져 있음

현재 Controller 상으로는 일부 백오피스 메서드만 `@AuthenticationPrincipal`을 직접 쓰고, 일부는 안 쓰고 있음.  
그런데 실제 Security 설정에서 `/bo/**` 전체가 Admin 인증이면, 아래 스크립트들에도 `Authorization` 헤더가 필요함.

특히 백오피스 전체에 Admin 인증이 걸려 있다면 모든 `backoffice/*.groovy`에 이걸 넣어야 함.

```groovy
public static final List<String> ADMIN_TOKEN_POOL = [
    "TOKEN_ADMIN_1",
    "TOKEN_ADMIN_2"
]
```


```groovy
List<Header> headers = [
    new BasicHeader("Authorization", "Bearer " + adminToken),
    new BasicHeader("Content-Type", "application/json")
]
```


### 3) 4xx를 성공으로 잡은 스크립트가 많음

지금 문서 스크립트는 Smoke/동시성 편의를 위해 `400`, `403`, `404`, `409`를 허용한 게 많음.

예:

```groovy
if (!(response.statusCode in [200, 201, 400, 404, 409])) {
```


근데 **성능 테스트 지표용이면 4xx를 성공으로 잡으면 안 됨.**

권장:

- Smoke/fixture 확인용: `400/403/404/409` 허용 가능
- Load/Stress 성능 측정용: 보통 `200/201/204`만 성공 처리
- 동시성 중복 검증용: `409`는 별도 카운팅하거나 로그 분리

즉, 목록은 맞지만 **스크립트 성공 기준은 테스트 목적별로 분리 필요**함.

### 4) POST/PUT body는 DTO 기준 재검증 필요

문서 스크립트의 request body는 기본 템플릿이라서 실제 DTO 필드명과 다를 수 있음.  
특히 아래 영역은 반드시 DTO 기준으로 맞춰야 함.

```plain text
/users/me/profile/**
/users/me/preferences/**
/roommate/boards multipart
/roommates/me/calendar/**
/bo/**
/auth/verify/**
```


---

## 최종 판정

**목록 기준 판정: 맞음.**

```plain text
Controller API 대비 누락된 큰 영역 없음.
문서 분할도 meta/user/roommate/chat/block/verification/roommate-request/roommate-management/alarm/inquiry/backoffice 구조로 맞음.
총 API 커버리지도 현재 Controller 기준과 맞음.
```


다만 실제 실행 기준으로는 이렇게 정리해야 함.

```plain text
Load/Stress 대상:
- 조회 API
- 핵심 목록/상세 API
- 매칭/게시글/채팅/알림/백오피스 검색 API

Concurrency 대상:
- likes
- accept/reject/cancel
- block
- 승인/반려/정지 상태 변경

Smoke만 권장:
- delete 계열
- logout
- 이메일 발송
- webhook
- 백오피스 저장/삭제 일부

별도 테스트:
- SSE
- WebSocket
- multipart upload
```


즉, **문서의 “API별 스크립트 목록”은 맞고**,  
다음으로 해야 할 건 **각 스크립트에 `Load / Stress / Concurrency / Smoke / Multipart / SSE / WebSocket` 라벨을 붙여서 실행 대상군을 분리하는 것**이야.