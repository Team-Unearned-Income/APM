아래처럼 **현재 `docs/ngrinder-performance-testing-*.md` 기준 스크립트들을 실행 목적별 라벨로 다시 분류**하면 됨.

라벨은 이렇게 잡는 게 좋음.

```plain text
Smoke       : 정상 호출/인증/요청 형식 확인
Load        : 일반적인 부하 테스트 대상
Stress      : 한계점 확인 대상
Concurrency : 동시성/중복/상태 전이 검증
Multipart   : 파일 업로드 부하 테스트
SSE         : 장기 연결 테스트
WebSocket   : 실시간 메시지 부하 테스트
External    : 외부 의존성 포함, Mock 권장
Danger      : 삭제/탈퇴/로그아웃 등 데이터 훼손 주의
```


---

# 1. `ngrinder-performance-testing-meta.md`

| Script | API | 권장 라벨 |
|---|---|---|
| `TermsListGetTest.groovy` | `GET /terms` | `Load` |
| `TermsDetailGetTest.groovy` | `GET /terms/{termsId}` | `Load` |
| `PopularSearchGetTest.groovy` | `GET /search/popular` | `Load`, `Stress` |
| `MetaLifestylePatternsGetTest.groovy` | `GET /meta/lifestyle-patterns` | `Load` |
| `MetaRoomTypesGetTest.groovy` | `GET /meta/room-types` | `Load` |
| `MetaRegionsGetTest.groovy` | `GET /meta/regions` | `Load`, `Stress` |
| `MetaRoomAddOptionsGetTest.groovy` | `GET /meta/room-add-options` | `Load` |
| `MetaFaqListGetTest.groovy` | `GET /meta/faq` | `Load` |
| `MetaFaqAllGetTest.groovy` | `GET /meta/faqAll` | `Load` |
| `MetaFaqDetailGetTest.groovy` | `GET /meta/faq/{id}` | `Load` |
| `AppVersionAndroidGetTest.groovy` | `GET /meta/app-version/android` | `Smoke`, `Load` |
| `AppVersionIosGetTest.groovy` | `GET /meta/app-version/ios` | `Smoke`, `Load` |
| `AuthEmailListGetTest.groovy` | `GET /meta/auth-email` | `Smoke`, `Load` |
| `AuthSuccessGetTest.groovy` | `GET /auth/success` | `Smoke` |

---

# 2. `ngrinder-performance-testing-user.md`

| Script | API | 권장 라벨 |
|---|---|---|
| `UserDeleteTest.groovy` | `DELETE /users/me` | `Smoke`, `Danger` |
| `UserProfileBasicPostTest.groovy` | `POST /users/me/profile/basic` | `Smoke`, `Load` |
| `UserProfileLifestylePostTest.groovy` | `POST /users/me/profile/lifestyle` | `Smoke`, `Load` |
| `UserProfileRoomInfoPostTest.groovy` | `POST /users/me/profile/roominfo` | `Smoke`, `Load` |
| `UserProfileAllPostTest.groovy` | `POST /users/me/profile/all` | `Load`, `Stress` |
| `UserProfileBasicPutMultipartTest.groovy` | `PUT /users/me/profile/basic` | `Multipart`, `Load` |
| `UserProfileLifestylePutTest.groovy` | `PUT /users/me/profile/lifestyle` | `Load` |
| `UserProfileRoomInfoPutTest.groovy` | `PUT /users/me/profile/roominfo` | `Load` |
| `UserProfileAllPutTest.groovy` | `PUT /users/me/profile/all` | `Load`, `Stress` |
| `UserPreferencesLifestylePostTest.groovy` | `POST /users/me/preferences/lifestyle` | `Load` |
| `UserPreferencesConditionsPostTest.groovy` | `POST /users/me/preferences/conditions` | `Load` |
| `UserPreferencesAllPostTest.groovy` | `POST /users/me/preferences/all` | `Load`, `Stress` |
| `UserPreferencesLifestylePutTest.groovy` | `PUT /users/me/preferences/lifestyle` | `Load` |
| `UserPreferencesConditionsPutTest.groovy` | `PUT /users/me/preferences/conditions` | `Load` |
| `UserPreferencesAllPutTest.groovy` | `PUT /users/me/preferences/all` | `Load`, `Stress` |
| `UserPreferencesAllGetTest.groovy` | `GET /users/me/preferences/all` | `Load`, `Stress` |
| `UserProfileGetTest.groovy` | `GET /users/me/profile` | `Load` |
| `UserProfileAllGetTest.groovy` | `GET /users/me/profile/all` | `Load`, `Stress` |
| `UserVisibilityPatchTest.groovy` | `PATCH /users/me/visibility` | `Load`, `Concurrency` |
| `UserBoardsGetTest.groovy` | `GET /users/me/boards` | `Load` |
| `UserVerificationsGetTest.groovy` | `GET /users/me/verifications` | `Load` |
| `UserNotificationSettingsGetTest.groovy` | `GET /users/me/notification-settings` | `Load` |
| `UserNotificationSettingsPatchTest.groovy` | `PATCH /users/me/notification-settings` | `Load` |
| `UserNoticesGetTest.groovy` | `GET /users/me/notices` | `Load` |
| `UserNoticeDetailGetTest.groovy` | `GET /users/me/notices/{id}` | `Load` |
| `UserDevicePostTest.groovy` | `POST /users/me/devices` | `Smoke`, `Load` |
| `UserLogoutPostTest.groovy` | `POST /users/me/logout` | `Smoke`, `Danger` |
| `UserAccountGetTest.groovy` | `GET /users/me/account` | `Load` |

---

# 3. `ngrinder-performance-testing-roommate.md`

| Script | API | 권장 라벨 |
|---|---|---|
| `RoommateBoardListGetTest.groovy` | `GET /roommate/boards` | `Load`, `Stress` |
| `RoommateBoardDetailGetTest.groovy` | `GET /roommate/boards/{boardId}` | `Load`, `Stress` |
| `RoommateBoardLikePostTest.groovy` | `POST /roommate/boards/{boardId}/likes` | `Concurrency`, `Stress` |
| `RoommateBoardEditFormGetTest.groovy` | `GET /roommate/boards/{boardId}/edit` | `Load` |
| `RoommateBoardCreateMultipartTest.groovy` | `POST /roommate/boards` | `Multipart`, `Load` |
| `RoommateBoardUpdateMultipartTest.groovy` | `PUT /roommate/boards/{boardId}` | `Multipart`, `Load` |
| `RoommateBoardReportPostTest.groovy` | `POST /roommate/boards/{boardId}/reports` | `Smoke`, `Load` |
| `RoommateBoardDeleteTest.groovy` | `DELETE /roommate/boards/{boardId}` | `Smoke`, `Danger` |
| `RoommateMatchListGetTest.groovy` | `GET /roommate/matches` | `Load`, `Stress` |
| `RoommateMatchDetailGetTest.groovy` | `GET /roommate/matches/{memberId}` | `Load`, `Stress` |
| `RoommateMatchScoreGetTest.groovy` | `GET /roommate/matches/score` | `Smoke` |
| `RoommateMatchLikePostTest.groovy` | `POST /roommate/matches/{memberId}/likes` | `Concurrency`, `Stress` |
| `RoommateMatchReportPostTest.groovy` | `POST /roommate/matches/{memberId}/reports` | `Smoke`, `Load` |

---

# 4. `ngrinder-performance-testing-chat.md`

## chat-request

| Script | API | 권장 라벨 |
|---|---|---|
| `ChatRequestListGetTest.groovy` | `GET /chat-requests` | `Smoke`, `Load` |
| `ChatRequestDetailGetTest.groovy` | `GET /chat-requests/{requestId}` | `Smoke`, `Load` |
| `ChatRequestCreatePostTest.groovy` | `POST /chat-requests` | `Smoke`, `Load` |
| `ChatRequestAcceptPostTest.groovy` | `POST /chat-requests/{requestId}/accept` | `Smoke`, `Concurrency` |
| `ChatRequestRejectPostTest.groovy` | `POST /chat-requests/{requestId}/reject` | `Smoke`, `Concurrency` |
| `ChatRequestCancelPostTest.groovy` | `POST /chat-requests/{requestId}/cancel` | `Smoke`, `Concurrency` |

## chat

| Script | API | 권장 라벨 |
|---|---|---|
| `ChatRoomListGetTest.groovy` | `GET /chats` | `Load`, `Stress` |
| `ChatRoomDetailGetTest.groovy` | `GET /chats/{chatRoomId}` | `Load`, `Stress` |
| `ChatRoomCreatePostTest.groovy` | `POST /chats` | `Load`, `Concurrency` |
| `ChatRoomLeavePostTest.groovy` | `POST /chats/{chatRoomId}/leave` | `Smoke`, `Danger` |
| `ChatRoomImageUploadMultipartTest.groovy` | `POST /chats/{chatRoomId}/images` | `Multipart`, `Load` |
| `ChatMessageWebSocketTest.groovy` | `WS /app/chats/{chatRoomId}/messages` | `WebSocket`, `Stress` |

---

# 5. `ngrinder-performance-testing-block-verification-roommate_request.md`

## block

| Script | API | 권장 라벨 |
|---|---|---|
| `BlockCreatePostTest.groovy` | `POST /blocks` | `Concurrency`, `Load` |
| `BlockListGetTest.groovy` | `GET /blocks` | `Load` |
| `BlockDeleteTest.groovy` | `DELETE /blocks/{blockId}` | `Concurrency`, `Danger` |

## verification

| Script | API | 권장 라벨 |
|---|---|---|
| `StudentVerifySendPostTest.groovy` | `POST /auth/verify/student/send` | `Smoke`, `External` |
| `CompanyVerifySendPostTest.groovy` | `POST /auth/verify/company/send` | `Smoke`, `External` |
| `StudentVerifyConfirmPostTest.groovy` | `POST /auth/verify/student/confirm` | `Smoke` |
| `CompanyVerifyConfirmPostTest.groovy` | `POST /auth/verify/company/confirm` | `Smoke` |
| `VerifyWebhookPostTest.groovy` | `POST /auth/verify/webhook` | `Smoke`, `External` |

## roommate-request

| Script | API | 권장 라벨 |
|---|---|---|
| `RoommateRequestCreatePostTest.groovy` | `POST /roommate-requests` | `Load`, `Concurrency` |
| `RoommateRequestAcceptPostTest.groovy` | `POST /roommate-requests/{requestId}/accept` | `Concurrency`, `Stress` |
| `RoommateRequestRejectPostTest.groovy` | `POST /roommate-requests/{requestId}/reject` | `Concurrency` |
| `RoommateRequestCancelPostTest.groovy` | `POST /roommate-requests/{requestId}/cancel` | `Concurrency` |
| `RoommateRequestListGetTest.groovy` | `GET /roommate-requests` | `Load` |

---

# 6. `ngrinder-performance-testing-roommate_management.md`

| Script | API | 권장 라벨 |
|---|---|---|
| `MyRoommateGetTest.groovy` | `GET /roommates/me` | `Load` |
| `MyRoommateDeleteTest.groovy` | `DELETE /roommates/me/{id}` | `Smoke`, `Danger` |
| `HouseRuleCreatePostTest.groovy` | `POST /roommates/me/house-rule` | `Load` |
| `HouseRuleListGetTest.groovy` | `GET /roommates/me/house-rule` | `Load` |
| `HouseRuleDetailGetTest.groovy` | `GET /roommates/me/house-rule/{id}` | `Load` |
| `HouseRuleUpdatePutTest.groovy` | `PUT /roommates/me/house-rule/{id}` | `Load` |
| `HouseRuleDeleteTest.groovy` | `DELETE /roommates/me/house-rule/{id}` | `Smoke`, `Danger` |
| `CalendarMonthListGetTest.groovy` | `GET /roommates/me/calendar` | `Load`, `Stress` |
| `CalendarDayListGetTest.groovy` | `GET /roommates/me/calendar?year=&month=&day=` | `Load`, `Stress` |
| `CalendarDetailGetTest.groovy` | `GET /roommates/me/calendar/{id}` | `Smoke`, `Load` |
| `CalendarCategoryGetTest.groovy` | `GET /roommates/me/calendar/categories` | `Load` |
| `CalendarEditFormGetTest.groovy` | `GET /roommates/me/calendar/edit` | `Load` |
| `CalendarCreatePostTest.groovy` | `POST /roommates/me/calendar` | `Load` |
| `CalendarRepeatCreatePostTest.groovy` | `POST /roommates/me/calendar/repeat` | `Load`, `Stress` |
| `CalendarUpdatePutTest.groovy` | `PUT /roommates/me/calendar/{id}` | `Load` |
| `CalendarRepeatUpdatePutTest.groovy` | `PUT /roommates/me/calendar/repeat/{id}` | `Load`, `Stress` |
| `CalendarDeleteTest.groovy` | `DELETE /roommates/me/calendar/{id}` | `Smoke`, `Danger` |

---

# 7. `ngrinder-performance-testing-alarm-inquiry.md`

## alarm

| Script | API | 권장 라벨 |
|---|---|---|
| `AlarmListGetTest.groovy` | `GET /alarms` | `Load`, `Stress` |
| `AlarmReadPatchTest.groovy` | `PATCH /alarms/{id}/read` | `Load`, `Concurrency` |
| `AlarmReadAllPatchTest.groovy` | `PATCH /alarms/read-all` | `Load` |
| `AlarmSseSubscribeGetTest.groovy` | `GET /alarms/subscribe` | `SSE`, `Stress` |

## inquiry

| Script | API | 권장 라벨 |
|---|---|---|
| `InquiryListGetTest.groovy` | `GET /inquiries` | `Load` |
| `InquiryDetailGetTest.groovy` | `GET /inquiries/{inquiryId}` | `Load` |
| `InquiryCategoriesGetTest.groovy` | `GET /inquiries/categorys` | `Load` |
| `InquiryCreatePostTest.groovy` | `POST /inquiries` | `Load` |

---

# 8. `ngrinder-performance-testing-backoffice-1.md`

| Script | API | 권장 라벨 |
|---|---|---|
| `BoTermsTypeListGetTest.groovy` | `GET /bo/type/terms` | `Smoke`, `Load` |
| `BoTermTypeUpdatePutTest.groovy` | `PUT /bo/type/term/{termTypeId}` | `Smoke` |
| `BoTermTypeCreatePostTest.groovy` | `POST /bo/type/terms` | `Smoke` |
| `BoTermTypeDeleteTest.groovy` | `DELETE /bo/type/term/{termTypeId}` | `Smoke`, `Danger` |
| `BoTermsCreatePostTest.groovy` | `POST /bo/terms` | `Smoke` |
| `BoTermsDraftUpdatePutTest.groovy` | `PUT /bo/terms/{termsId}/draft` | `Smoke` |
| `BoTermsListGetTest.groovy` | `GET /bo/terms` | `Load` |
| `BoTermsDetailGetTest.groovy` | `GET /bo/terms/{termsId}` | `Load` |
| `BoTermsDeleteTest.groovy` | `DELETE /bo/terms/{termsId}` | `Smoke`, `Danger` |
| `BoTermsPublishPutTest.groovy` | `PUT /bo/terms/{termsId}/publish` | `Smoke` |
| `BoRoomAddOptionCreateMultipartTest.groovy` | `POST /bo/room-add-options` | `Smoke`, `Multipart` |
| `BoRoomAddOptionListGetTest.groovy` | `GET /bo/room-add-options` | `Load` |
| `BoRoomAddOptionUpdateMultipartTest.groovy` | `PUT /bo/room-add-options/{id}` | `Smoke`, `Multipart` |
| `BoRoomAddOptionDeleteTest.groovy` | `DELETE /bo/room-add-options/{id}` | `Smoke`, `Danger` |
| `BoRoomAddOptionDetailGetTest.groovy` | `GET /bo/room-add-options/{id}` | `Load` |
| `BoRoomTypeCreateMultipartTest.groovy` | `POST /bo/room-types` | `Smoke`, `Multipart` |
| `BoRoomTypeListGetTest.groovy` | `GET /bo/room-types` | `Load` |
| `BoRoomTypeUpdateMultipartTest.groovy` | `PUT /bo/room-types/{id}` | `Smoke`, `Multipart` |
| `BoRoomTypeDeleteTest.groovy` | `DELETE /bo/room-types/{id}` | `Smoke`, `Danger` |
| `BoRoomTypeDetailGetTest.groovy` | `GET /bo/room-types/{id}` | `Load` |

---

# 9. `ngrinder-performance-testing-backoffice-2.md`

| Script | API | 권장 라벨 |
|---|---|---|
| `BoLifestylePatternListGetTest.groovy` | `GET /bo/lifestyle-patterns` | `Load` |
| `BoLifestylePatternDetailGetTest.groovy` | `GET /bo/lifestyle-patterns/{id}` | `Load` |
| `BoLifestylePatternCreateMultipartTest.groovy` | `POST /bo/lifestyle-patterns` | `Smoke`, `Multipart` |
| `BoLifestylePatternUpdateMultipartTest.groovy` | `PUT /bo/lifestyle-patterns/{id}` | `Smoke`, `Multipart` |
| `BoLifestylePatternDeleteTest.groovy` | `DELETE /bo/lifestyle-patterns/{id}` | `Smoke`, `Danger` |
| `BoVerificationApproveListGetTest.groovy` | `GET /bo/verifications/approve` | `Load` |
| `BoVerificationCancelListGetTest.groovy` | `GET /bo/verifications/cancel` | `Load` |
| `BoVerificationWaitListGetTest.groovy` | `GET /bo/verifications/wait` | `Load` |
| `BoVerificationWaitDetailGetTest.groovy` | `GET /bo/verifications/wait/{id}` | `Load` |
| `BoVerificationApprovePatchTest.groovy` | `PATCH /bo/verifications/wait/{id}/approve` | `Smoke`, `Concurrency` |
| `BoVerificationCancelPatchTest.groovy` | `PATCH /bo/verifications/wait/{id}/cancel` | `Smoke`, `Concurrency` |
| `BoNoticeCreatePostTest.groovy` | `POST /bo/notices` | `Smoke` |
| `BoNoticeListGetTest.groovy` | `GET /bo/notices` | `Load` |
| `BoNoticeDetailGetTest.groovy` | `GET /bo/notices/{id}` | `Load` |
| `BoNoticeUpdatePutTest.groovy` | `PUT /bo/notices/{id}` | `Smoke` |
| `BoNoticeDeleteTest.groovy` | `DELETE /bo/notices/{id}` | `Smoke`, `Danger` |
| `BoInquiryAnswerPostTest.groovy` | `POST /bo/inquiries` | `Smoke` |
| `BoInquiryListGetTest.groovy` | `GET /bo/inquiries` | `Load` |
| `BoInquiryDetailGetTest.groovy` | `GET /bo/inquiries/{id}` | `Load` |
| `BoFaqCreatePostTest.groovy` | `POST /bo/faq` | `Smoke` |
| `BoFaqUpdatePutTest.groovy` | `PUT /bo/faq` | `Smoke` |
| `BoFaqDeleteTest.groovy` | `DELETE /bo/faq/{id}` | `Smoke`, `Danger` |
| `BoFaqListGetTest.groovy` | `GET /bo/faq` | `Load` |
| `BoFaqDetailGetTest.groovy` | `GET /bo/faq/{id}` | `Load` |

---

# 10. `ngrinder-performance-testing-backoffice-3.md`

| Script | API | 권장 라벨 |
|---|---|---|
| `BoAppVersionGetTest.groovy` | `GET /bo/app-version` | `Load` |
| `BoAppVersionIosGetTest.groovy` | `GET /bo/app-version/ios` | `Load` |
| `BoAppVersionAndroidGetTest.groovy` | `GET /bo/app-version/android` | `Load` |
| `BoAppVersionCreatePostTest.groovy` | `POST /bo/app-version` | `Smoke` |
| `BoAppVersionUpdatePutTest.groovy` | `PUT /bo/app-version` | `Smoke` |
| `BoAuthEmailListGetTest.groovy` | `GET /bo/auth-email` | `Load` |
| `BoAuthEmailCreatePostTest.groovy` | `POST /bo/auth-email` | `Smoke` |
| `BoAuthEmailUpdatePutTest.groovy` | `PUT /bo/auth-email` | `Smoke` |
| `BoAuthEmailDeleteTest.groovy` | `DELETE /bo/auth-email/{id}` | `Smoke`, `Danger` |
| `BoMemberListGetTest.groovy` | `GET /bo/member` | `Load`, `Stress` |
| `BoMemberDetailGetTest.groovy` | `GET /bo/member/{id}` | `Load` |
| `BoMemberCancelPatchTest.groovy` | `PATCH /bo/member/cancel/{id}` | `Smoke`, `Concurrency` |
| `BoMemberUncancelPatchTest.groovy` | `PATCH /bo/member/uncancel/{id}` | `Smoke`, `Concurrency` |
| `BoMemberAuthPatchTest.groovy` | `PATCH /bo/member/auth/{id}` | `Smoke` |
| `BoReportWaitListGetTest.groovy` | `GET /bo/report/wait` | `Load` |
| `BoReportDoneListGetTest.groovy` | `GET /bo/report/done` | `Load` |
| `BoReportHiddenPostTest.groovy` | `POST /bo/report/hidden` | `Smoke` |
| `BoReportNoActionPostTest.groovy` | `POST /bo/report/no-action` | `Smoke` |
| `BoReportSuspendedPostTest.groovy` | `POST /bo/report/suspended` | `Smoke` |
| `BoBoardListGetTest.groovy` | `GET /bo/boards` | `Load`, `Stress` |
| `BoBoardDetailGetTest.groovy` | `GET /bo/board/{id}` | `Load` |
| `BoBoardDeleteTest.groovy` | `DELETE /bo/board/{id}` | `Smoke`, `Danger` |
| `BoBoardRecoverPatchTest.groovy` | `PATCH /bo/board/recover/{id}` | `Smoke` |

---

# 바로 실행 우선순위로 줄이면

## P0: Stress까지 무조건

```plain text
GET /roommate/boards
GET /roommate/boards/{boardId}
POST /roommate/boards/{boardId}/likes
GET /roommate/matches
GET /roommate/matches/{memberId}
POST /roommate/matches/{memberId}/likes
POST /roommate-requests/{requestId}/accept
GET /chats
GET /chats/{chatRoomId}
GET /alarms
GET /alarms/subscribe
WS /app/chats/{chatRoomId}/messages
```


---

## P1: Load 필수

```plain text
GET /search/popular
GET /meta/regions
GET /users/me/profile/all
GET /users/me/preferences/all
POST /users/me/profile/all
PUT /users/me/profile/all
POST /users/me/preferences/all
PUT /users/me/preferences/all
GET /users/me/boards
GET /roommates/me
GET /roommates/me/calendar
GET /roommates/me/calendar?year=&month=&day=
POST /roommates/me/calendar/repeat
PUT /roommates/me/calendar/repeat/{id}
GET /inquiries
GET /bo/member
GET /bo/boards
GET /bo/report/wait
GET /bo/verifications/wait
```


---

## P2: Load 또는 제한적 Load

```plain text
GET /terms
GET /terms/{termsId}
GET /meta/lifestyle-patterns
GET /meta/room-types
GET /meta/room-add-options
GET /meta/faq
GET /meta/faqAll
GET /meta/faq/{id}
GET /users/me/profile
GET /users/me/verifications
GET /users/me/notification-settings
GET /users/me/notices
GET /users/me/notices/{id}
GET /users/me/account
POST /users/me/devices
GET /roommate/boards/{boardId}/edit
POST /roommate/boards
PUT /roommate/boards/{boardId}
POST /chats
POST /chats/{chatRoomId}/images
GET /blocks
GET /roommate-requests
GET /roommates/me/house-rule
GET /roommates/me/calendar/edit
GET /bo/terms
GET /bo/terms/{termsId}
GET /bo/inquiries
GET /bo/inquiries/{id}
GET /bo/faq
GET /bo/faq/{id}
GET /bo/app-version
```


---

## P3: Smoke만 권장

```plain text
DELETE /users/me
POST /users/me/logout
POST /auth/verify/student/send
POST /auth/verify/company/send
POST /auth/verify/student/confirm
POST /auth/verify/company/confirm
POST /auth/verify/webhook
DELETE /roommate/boards/{boardId}
POST /chat-requests
POST /chat-requests/{requestId}/accept
POST /chat-requests/{requestId}/reject
POST /chat-requests/{requestId}/cancel
POST /chats/{chatRoomId}/leave
DELETE /roommates/me/{id}
DELETE /roommates/me/house-rule/{id}
DELETE /roommates/me/calendar/{id}
DELETE /bo/**
POST /bo/**
PUT /bo/**
PATCH /bo/member/**
PATCH /bo/verifications/**
PATCH /bo/board/recover/{id}
```


---

# 문서에 추가하면 좋은 공통 섹션

각 `ngrinder-performance-testing-*.md` 맨 위에 이런 표를 추가하면 깔끔함.

```markdown
## 테스트 라벨 기준

| Label | 의미 |
|---|---|
| `Smoke` | 1~2 Vuser로 정상 호출, 인증, 요청 형식 확인 |
| `Load` | 일반 예상 트래픽 수준 부하 테스트 |
| `Stress` | Vuser를 점진적으로 올려 한계점 확인 |
| `Concurrency` | 동일 리소스 동시 요청으로 중복/락/상태 전이 확인 |
| `Multipart` | 파일 업로드 I/O 부하 확인 |
| `SSE` | 장기 연결 수, 유지 시간, 메모리 사용량 확인 |
| `WebSocket` | 실시간 메시지 송수신 부하 확인 |
| `External` | 이메일/웹훅 등 외부 의존성. Mock 환경 권장 |
| `Danger` | 삭제/탈퇴/로그아웃 등 테스트 데이터 훼손 주의 |
```


그리고 각 스크립트 제목 아래에 이런 식으로 붙이면 됨.

```markdown
**API**: `GET /roommate/boards`  
**Label**: `Load`, `Stress`  
**Priority**: `P0`
```


---

정리하면, **현재 목록 자체는 맞고**,  
실제로는 위 라벨대로 **P0/P1/P2/P3로 실행군을 나누는 게 맞음.**