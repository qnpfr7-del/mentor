# 주사님 도와주세요 — Firebase 설정

대상 프로젝트: `mentoring-a83f0`

홈페이지 코드는 Firebase SDK를 사용합니다. 아래 설정을 완료해야 실제 회원가입과 데이터 저장이 작동합니다. 웹 앱의 firebaseConfig는 콘솔 관리 권한이 아니므로, Firebase 프로젝트 소유자가 콘솔 설정을 완료해야 합니다.

## 1. 이메일 로그인 켜기

https://console.firebase.google.com/project/mentoring-a83f0/authentication/providers

Authentication에서 시작하기를 누르고 이메일/비밀번호 제공업체를 사용 설정하세요. 이메일 링크 로그인은 필요하지 않습니다.

Authentication → 설정 → 승인된 도메인에 `qnpfr7-del.github.io`를 등록하세요. 경로나 https:// 접두어를 넣지 않습니다.

## 2. Firestore 준비

https://console.firebase.google.com/project/mentoring-a83f0/firestore

데이터베이스가 없다면 Cloud Firestore 기본 데이터베이스 `(default)`를 생성하세요. 위치는 기관 정책에 맞게 선택하세요. 테스트 모드로 모두에게 공개하지 마세요.

규칙 탭에서 이 저장소의 `firestore.rules` 내용을 적용하고 게시하세요. 이 규칙은 이 서비스 전용 jusa_ 컬렉션을 보호합니다. 기존 다른 앱을 운영 중이라면 기존 규칙과 병합해야 합니다. 웹 설정값이나 API 키만으로는 규칙을 게시할 수 없습니다.

CLI를 사용할 경우 프로젝트 권한이 있는 계정으로 로그인한 후 다음을 실행합니다.

```
npx firebase-tools login
npx firebase-tools deploy --only firestore:rules,firestore:indexes --project mentoring-a83f0
```

## 3. 첫 관리자 등록

1. 홈페이지에서 사용할 이메일로 회원가입하세요. 이름·부서·닉네임·역할을 입력합니다.
2. 가입 승인 대기 화면에 표시된 계정 UID를 복사합니다. Authentication → 사용자에서도 확인할 수 있습니다.
3. Firestore → 데이터에서 컬렉션 `jusa_admins`를 만듭니다.
4. 문서 ID에 해당 UID를 정확히 넣습니다.
5. 필드 `enabled`를 **boolean** 유형의 `true`로 저장합니다. 문자열 `"true"`가 아닙니다.
6. 홈페이지에서 로그아웃 후 다시 로그인합니다. 관리자 메뉴가 나타납니다.
7. 관리자 → 회원 승인 관리에서 본인 계정을 승인한 후 다른 회원을 승인합니다.

첫 관리자를 자동으로 지정하거나, 처음 가입한 사람에게 관리자 권한을 주지 않습니다. 브라우저에서 관리자 권한을 만들 수 없습니다.

## 4. 실제 동작 확인

- 승인 대기 회원은 질문 목록과 대화를 읽을 수 없습니다.
- 멘티가 질문을 작성하면 제목·분야·태그만 승인된 회원에게 보입니다.
- 멘토가 신청하고 멘티가 선택하면 두 사람에게만 상세 질문과 실시간 대화가 열립니다.
- 멘티가 상담 완료를 누르면 멘토에게 20 XP가 원자적으로 한 번 지급됩니다.
- 다른 회원의 완료 사례 열람 신청은 해당 멘토와 멘티 계정에서 각각 승인해야 합니다. 신청자는 직접 승인할 수 없습니다.
- 한쪽이 동의를 철회하면 이후 서버 읽기 요청이 다시 차단됩니다. 이미 읽거나 저장한 내용을 회수할 수는 없습니다.

개인정보·비공개 업무자료를 올리지 마세요. 초기 데이터는 비어 있으며, 이전 브라우저 체험 데이터는 실제 서버에 자동 업로드하지 않습니다.

## 테스트

`npm ci` 후 `node ui.test.cjs`로 화면 분기를 검사할 수 있습니다. `npm run test:rules`는 별도의 demo-jusa Firestore 에뮬레이터에서 회원가입, 권한, 매칭, 채팅, 경험치, 양측 동의·철회를 검증합니다. 운영 프로젝트에는 테스트 계정이나 샘플 데이터를 만들지 않습니다.
