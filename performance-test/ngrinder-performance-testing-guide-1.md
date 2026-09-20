맞아. **16개만 뽑은 건 P0/P1 핵심만 준 거고, 전체 API 기준이면 훨씬 많음.**  
너가 원하는 건 문서가 아니라 **API 하나당 nGrinder 스크립트 파일**이니까 이렇게 가야 함.

아래처럼 **전체 API를 파일 단위로 쪼개서 생성**하는 게 맞다.

```plain text
ngrinder-scripts/
├── meta/
├── user/
├── roommate/
├── chat/
├── block/
├── verification/
├── roommate-request/
├── roommate-management/
├── alarm/
├── inquiry/
└── backoffice/
```


---

# 전체 API별 nGrinder 스크립트 파일 목록

## 1. 메타/일반 API

```plain text
meta/
├── TermsListGetTest.groovy                         # GET /terms
├── TermsDetailGetTest.groovy                       # GET /terms/{termsId}
├── PopularSearchGetTest.groovy                     # GET /search/popular
├── MetaLifestylePatternsGetTest.groovy             # GET /meta/lifestyle-patterns
├── MetaRoomTypesGetTest.groovy                     # GET /meta/room-types
├── MetaRegionsGetTest.groovy                       # GET /meta/regions
├── MetaRoomAddOptionsGetTest.groovy                # GET /meta/room-add-options
├── MetaFaqListGetTest.groovy                       # GET /meta/faq
├── MetaFaqAllGetTest.groovy                        # GET /meta/faqAll
├── MetaFaqDetailGetTest.groovy                     # GET /meta/faq/{id}
├── AppVersionAndroidGetTest.groovy                 # GET /meta/app-version/android
├── AppVersionIosGetTest.groovy                     # GET /meta/app-version/ios
├── AuthEmailListGetTest.groovy                     # GET /meta/auth-email
└── AuthSuccessGetTest.groovy                       # GET /auth/success
```


---

## 2. 유저/프로필 API

```plain text
user/
├── UserDeleteTest.groovy                           # DELETE /users/me
├── UserProfileBasicPostTest.groovy                 # POST /users/me/profile/basic
├── UserProfileLifestylePostTest.groovy             # POST /users/me/profile/lifestyle
├── UserProfileRoomInfoPostTest.groovy              # POST /users/me/profile/roominfo
├── UserProfileAllPostTest.groovy                   # POST /users/me/profile/all
├── UserProfileBasicPutMultipartTest.groovy         # PUT /users/me/profile/basic
├── UserProfileLifestylePutTest.groovy              # PUT /users/me/profile/lifestyle
├── UserProfileRoomInfoPutTest.groovy               # PUT /users/me/profile/roominfo
├── UserProfileAllPutTest.groovy                    # PUT /users/me/profile/all
├── UserPreferencesLifestylePostTest.groovy         # POST /users/me/preferences/lifestyle
├── UserPreferencesConditionsPostTest.groovy        # POST /users/me/preferences/conditions
├── UserPreferencesAllPostTest.groovy               # POST /users/me/preferences/all
├── UserPreferencesLifestylePutTest.groovy          # PUT /users/me/preferences/lifestyle
├── UserPreferencesConditionsPutTest.groovy         # PUT /users/me/preferences/conditions
├── UserPreferencesAllPutTest.groovy                # PUT /users/me/preferences/all
├── UserPreferencesAllGetTest.groovy                # GET /users/me/preferences/all
├── UserProfileGetTest.groovy                       # GET /users/me/profile
├── UserProfileAllGetTest.groovy                    # GET /users/me/profile/all
├── UserVisibilityPatchTest.groovy                  # PATCH /users/me/visibility
├── UserBoardsGetTest.groovy                        # GET /users/me/boards
├── UserVerificationsGetTest.groovy                 # GET /users/me/verifications
├── UserNotificationSettingsGetTest.groovy          # GET /users/me/notification-settings
├── UserNotificationSettingsPatchTest.groovy        # PATCH /users/me/notification-settings
├── UserNoticesGetTest.groovy                       # GET /users/me/notices
├── UserNoticeDetailGetTest.groovy                  # GET /users/me/notices/{id}
├── UserDevicePostTest.groovy                       # POST /users/me/devices
├── UserLogoutPostTest.groovy                       # POST /users/me/logout
└── UserAccountGetTest.groovy                       # GET /users/me/account
```


---

## 3. 룸메이트 게시글/매칭 API

```plain text
roommate/
├── RoommateBoardListGetTest.groovy                 # GET /roommate/boards
├── RoommateBoardDetailGetTest.groovy               # GET /roommate/boards/{boardId}
├── RoommateBoardLikePostTest.groovy                # POST /roommate/boards/{boardId}/likes
├── RoommateBoardEditFormGetTest.groovy             # GET /roommate/boards/{boardId}/edit
├── RoommateBoardCreateMultipartTest.groovy         # POST /roommate/boards
├── RoommateBoardUpdateMultipartTest.groovy         # PUT /roommate/boards/{boardId}
├── RoommateBoardReportPostTest.groovy              # POST /roommate/boards/{boardId}/reports
├── RoommateBoardDeleteTest.groovy                  # DELETE /roommate/boards/{boardId}
├── RoommateMatchListGetTest.groovy                 # GET /roommate/matches
├── RoommateMatchDetailGetTest.groovy               # GET /roommate/matches/{memberId}
├── RoommateMatchScoreGetTest.groovy                # GET /roommate/matches/score
├── RoommateMatchLikePostTest.groovy                # POST /roommate/matches/{memberId}/likes
└── RoommateMatchReportPostTest.groovy              # POST /roommate/matches/{memberId}/reports
```


---

## 4. 채팅 요청 API

```plain text
chat-request/
├── ChatRequestListGetTest.groovy                   # GET /chat-requests
├── ChatRequestDetailGetTest.groovy                 # GET /chat-requests/{requestId}
├── ChatRequestCreatePostTest.groovy                # POST /chat-requests
├── ChatRequestAcceptPostTest.groovy                # POST /chat-requests/{requestId}/accept
├── ChatRequestRejectPostTest.groovy                # POST /chat-requests/{requestId}/reject
└── ChatRequestCancelPostTest.groovy                # POST /chat-requests/{requestId}/cancel
```


---

## 5. 채팅 API

```plain text
chat/
├── ChatRoomListGetTest.groovy                      # GET /chats
├── ChatRoomDetailGetTest.groovy                    # GET /chats/{chatRoomId}
├── ChatRoomCreatePostTest.groovy                   # POST /chats
├── ChatRoomLeavePostTest.groovy                    # POST /chats/{chatRoomId}/leave
├── ChatRoomImageUploadMultipartTest.groovy         # POST /chats/{chatRoomId}/images
└── ChatMessageWebSocketTest.groovy                 # WS /app/chats/{chatRoomId}/messages
```


---

## 6. 차단 API

```plain text
block/
├── BlockCreatePostTest.groovy                      # POST /blocks
├── BlockListGetTest.groovy                         # GET /blocks
└── BlockDeleteTest.groovy                          # DELETE /blocks/{blockId}
```


---

## 7. 인증 API

```plain text
verification/
├── StudentVerifySendPostTest.groovy                # POST /auth/verify/student/send
├── CompanyVerifySendPostTest.groovy                # POST /auth/verify/company/send
├── StudentVerifyConfirmPostTest.groovy             # POST /auth/verify/student/confirm
├── CompanyVerifyConfirmPostTest.groovy             # POST /auth/verify/company/confirm
└── VerifyWebhookPostTest.groovy                    # POST /auth/verify/webhook
```


---

## 8. 룸메이트 요청 API

```plain text
roommate-request/
├── RoommateRequestCreatePostTest.groovy            # POST /roommate-requests
├── RoommateRequestAcceptPostTest.groovy            # POST /roommate-requests/{requestId}/accept
├── RoommateRequestRejectPostTest.groovy            # POST /roommate-requests/{requestId}/reject
├── RoommateRequestCancelPostTest.groovy            # POST /roommate-requests/{requestId}/cancel
└── RoommateRequestListGetTest.groovy               # GET /roommate-requests
```


---

## 9. 룸메이트 관리 API

```plain text
roommate-management/
├── MyRoommateGetTest.groovy                        # GET /roommates/me
├── MyRoommateDeleteTest.groovy                     # DELETE /roommates/me/{id}
├── HouseRuleCreatePostTest.groovy                  # POST /roommates/me/house-rule
├── HouseRuleListGetTest.groovy                     # GET /roommates/me/house-rule
├── HouseRuleDetailGetTest.groovy                   # GET /roommates/me/house-rule/{id}
├── HouseRuleUpdatePutTest.groovy                   # PUT /roommates/me/house-rule/{id}
├── HouseRuleDeleteTest.groovy                      # DELETE /roommates/me/house-rule/{id}
├── CalendarMonthListGetTest.groovy                 # GET /roommates/me/calendar
├── CalendarDayListGetTest.groovy                   # GET /roommates/me/calendar?year=&month=&day=
├── CalendarDetailGetTest.groovy                    # GET /roommates/me/calendar/{id}
├── CalendarCategoryGetTest.groovy                  # GET /roommates/me/calendar/categories
├── CalendarEditFormGetTest.groovy                  # GET /roommates/me/calendar/edit
├── CalendarCreatePostTest.groovy                   # POST /roommates/me/calendar
├── CalendarRepeatCreatePostTest.groovy             # POST /roommates/me/calendar/repeat
├── CalendarUpdatePutTest.groovy                    # PUT /roommates/me/calendar/{id}
├── CalendarRepeatUpdatePutTest.groovy              # PUT /roommates/me/calendar/repeat/{id}
└── CalendarDeleteTest.groovy                       # DELETE /roommates/me/calendar/{id}
```


---

## 10. 알림 API

```plain text
alarm/
├── AlarmListGetTest.groovy                         # GET /alarms
├── AlarmReadPatchTest.groovy                       # PATCH /alarms/{id}/read
├── AlarmReadAllPatchTest.groovy                    # PATCH /alarms/read-all
└── AlarmSseSubscribeGetTest.groovy                 # GET /alarms/subscribe
```


---

## 11. 문의 API

```plain text
inquiry/
├── InquiryListGetTest.groovy                       # GET /inquiries
├── InquiryDetailGetTest.groovy                     # GET /inquiries/{inquiryId}
├── InquiryCategoriesGetTest.groovy                 # GET /inquiries/categorys
└── InquiryCreatePostTest.groovy                    # POST /inquiries
```


---

## 12. 백오피스 API

```plain text
backoffice/
├── BoTermsTypeListGetTest.groovy                   # GET /bo/type/terms
├── BoTermTypeUpdatePutTest.groovy                  # PUT /bo/type/term/{termTypeId}
├── BoTermTypeCreatePostTest.groovy                 # POST /bo/type/terms
├── BoTermTypeDeleteTest.groovy                     # DELETE /bo/type/term/{termTypeId}
├── BoTermsCreatePostTest.groovy                    # POST /bo/terms
├── BoTermsDraftUpdatePutTest.groovy                # PUT /bo/terms/{termsId}/draft
├── BoTermsListGetTest.groovy                       # GET /bo/terms
├── BoTermsDetailGetTest.groovy                     # GET /bo/terms/{termsId}
├── BoTermsDeleteTest.groovy                        # DELETE /bo/terms/{termsId}
├── BoTermsPublishPutTest.groovy                    # PUT /bo/terms/{termsId}/publish
├── BoRoomAddOptionCreateMultipartTest.groovy       # POST /bo/room-add-options
├── BoRoomAddOptionListGetTest.groovy               # GET /bo/room-add-options
├── BoRoomAddOptionUpdateMultipartTest.groovy       # PUT /bo/room-add-options/{id}
├── BoRoomAddOptionDeleteTest.groovy                # DELETE /bo/room-add-options/{id}
├── BoRoomAddOptionDetailGetTest.groovy             # GET /bo/room-add-options/{id}
├── BoRoomTypeCreateMultipartTest.groovy            # POST /bo/room-types
├── BoRoomTypeListGetTest.groovy                    # GET /bo/room-types
├── BoRoomTypeUpdateMultipartTest.groovy            # PUT /bo/room-types/{id}
├── BoRoomTypeDeleteTest.groovy                     # DELETE /bo/room-types/{id}
├── BoRoomTypeDetailGetTest.groovy                  # GET /bo/room-types/{id}
├── BoLifestylePatternListGetTest.groovy            # GET /bo/lifestyle-patterns
├── BoLifestylePatternDetailGetTest.groovy          # GET /bo/lifestyle-patterns/{id}
├── BoLifestylePatternCreateMultipartTest.groovy    # POST /bo/lifestyle-patterns
├── BoLifestylePatternUpdateMultipartTest.groovy    # PUT /bo/lifestyle-patterns/{id}
├── BoLifestylePatternDeleteTest.groovy             # DELETE /bo/lifestyle-patterns/{id}
├── BoVerificationApproveListGetTest.groovy         # GET /bo/verifications/approve
├── BoVerificationCancelListGetTest.groovy          # GET /bo/verifications/cancel
├── BoVerificationWaitListGetTest.groovy            # GET /bo/verifications/wait
├── BoVerificationWaitDetailGetTest.groovy          # GET /bo/verifications/wait/{id}
├── BoVerificationApprovePatchTest.groovy           # PATCH /bo/verifications/wait/{id}/approve
├── BoVerificationCancelPatchTest.groovy            # PATCH /bo/verifications/wait/{id}/cancel
├── BoNoticeCreatePostTest.groovy                   # POST /bo/notices
├── BoNoticeListGetTest.groovy                      # GET /bo/notices
├── BoNoticeDetailGetTest.groovy                    # GET /bo/notices/{id}
├── BoNoticeUpdatePutTest.groovy                    # PUT /bo/notices/{id}
├── BoNoticeDeleteTest.groovy                       # DELETE /bo/notices/{id}
├── BoInquiryAnswerPostTest.groovy                  # POST /bo/inquiries
├── BoInquiryListGetTest.groovy                     # GET /bo/inquiries
├── BoInquiryDetailGetTest.groovy                   # GET /bo/inquiries/{id}
├── BoFaqCreatePostTest.groovy                      # POST /bo/faq
├── BoFaqUpdatePutTest.groovy                       # PUT /bo/faq
├── BoFaqDeleteTest.groovy                          # DELETE /bo/faq/{id}
├── BoFaqListGetTest.groovy                         # GET /bo/faq
├── BoFaqDetailGetTest.groovy                       # GET /bo/faq/{id}
├── BoAppVersionGetTest.groovy                      # GET /bo/app-version
├── BoAppVersionIosGetTest.groovy                   # GET /bo/app-version/ios
├── BoAppVersionAndroidGetTest.groovy               # GET /bo/app-version/android
├── BoAppVersionCreatePostTest.groovy               # POST /bo/app-version
├── BoAppVersionUpdatePutTest.groovy                # PUT /bo/app-version
├── BoAuthEmailListGetTest.groovy                   # GET /bo/auth-email
├── BoAuthEmailCreatePostTest.groovy                # POST /bo/auth-email
├── BoAuthEmailUpdatePutTest.groovy                 # PUT /bo/auth-email
├── BoAuthEmailDeleteTest.groovy                    # DELETE /bo/auth-email/{id}
├── BoMemberListGetTest.groovy                      # GET /bo/member
├── BoMemberDetailGetTest.groovy                    # GET /bo/member/{id}
├── BoMemberCancelPatchTest.groovy                  # PATCH /bo/member/cancel/{id}
├── BoMemberUncancelPatchTest.groovy                # PATCH /bo/member/uncancel/{id}
├── BoMemberAuthPatchTest.groovy                    # PATCH /bo/member/auth/{id}
├── BoReportWaitListGetTest.groovy                  # GET /bo/report/wait
├── BoReportDoneListGetTest.groovy                  # GET /bo/report/done
├── BoReportHiddenPostTest.groovy                   # POST /bo/report/hidden
├── BoReportNoActionPostTest.groovy                 # POST /bo/report/no-action
├── BoReportSuspendedPostTest.groovy                # POST /bo/report/suspended
├── BoBoardListGetTest.groovy                       # GET /bo/boards
├── BoBoardDetailGetTest.groovy                     # GET /bo/board/{id}
├── BoBoardDeleteTest.groovy                        # DELETE /bo/board/{id}
└── BoBoardRecoverPatchTest.groovy                  # PATCH /bo/board/recover/{id}
```


---

# 결론

전체로 보면 대략 이렇게임.

```plain text
메타/일반: 14개
유저/프로필: 28개
룸메이트 게시글/매칭: 13개
채팅 요청: 6개
채팅: 6개
차단: 3개
인증: 5개
룸메이트 요청: 5개
룸메이트 관리: 17개
알림: 4개
문의: 4개
백오피스: 66개 전후
```


총합은 **170개 가까이** 스크립트 파일로 나누는 게 맞음.

다만 현실적으로 답변 한 번에 170개 Groovy 전문을 다 넣으면 너무 길어서 못 써먹음.  
그래서 내가 바로 다음부터는 이렇게 쪼개서 줄게.

```plain text
1차: meta 전체 14개
2차: user 전체 28개
3차: roommate 전체 13개
4차: chat + chat-request 전체 12개
5차: block + verification + roommate-request 전체 13개
6차: roommate-management 전체 17개
7차: alarm + inquiry 전체 8개
8차: backoffice 전체
```


원하면 다음 답변에서 바로 **`meta/` 14개 Groovy 스크립트 전문**부터 뽑아줄게.  
그리고 그다음부터는 폴더별로 계속 이어서 주면 됨.