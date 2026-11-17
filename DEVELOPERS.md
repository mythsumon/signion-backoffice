# SIGNION BackOffice 개발 명세 (프로토타입 기준)

> **개발팀 안내** — 이 문서는 프로토타입(`index.html`)에서 자동으로 뽑은 화면별 명세입니다.
> - 프로토타입에서 오른쪽 위 메뉴 › **개발자 모드 켜기** → 각 화면 위쪽 「개발 정보」, 오른쪽 위 사용자 메뉴의 「개발 명세」에서 같은 내용을 볼 수 있습니다.
> - 화면이 바뀌면 「개발 명세 › Markdown 내보내기」로 이 파일을 다시 만드세요.
> - 고객 요청은 화면마다 「요청 남기기」로 받고, 「요청사항」에서 상태(접수 · 검토중 · 반영완료 · 보류)를 관리합니다.
생성일 2026-11-05 · 프로토타입에서 자동 생성됩니다. 화면이 바뀌면 「개발 명세 › Markdown 내보내기」로 다시 만드세요.

## 공통 규칙

- **조회**: 모든 목록은 서버 검색 · 정렬 · 페이지. 공통 파라미터 `from, to`(기간) `q, qf`(검색어 · 대상 필드) `del`(삭제자료 포함) `page, size, sort`. 검색 버튼(Enter)을 누르면 바로 결과 표시.
- **권한**: 역할(Role) → 권한 코드 `route|기능` 집합. 기능 = 조회 · 등록 · 수정 · 삭제 · 엑셀로 · 리포트 · 삭제자료표시 + 화면별 기능버튼. 조회가 없으면 나머지도 무효. 프로그램관리에서 「중지」한 메뉴 · 버튼은 모든 역할에서 차단. **서버에서도 같은 코드로 검사**해야 합니다(화면 숨김만으로는 부족).
- **감사LOG**: 등록 · 수정(변경 전/후) · 삭제(사유) · 기능버튼 · 엑셀 · 리포트 · 개인정보 보기 · 권한/프로그램 변경 · 권한 없음 시도를 기록 (누가 · 역할 · 언제 · 메뉴 · 대상 · 내용 · IP). 수정/삭제 불가.
- **삭제**: 사유 필수 · 논리삭제. 「삭제자료표시」 권한이 있으면 조회 가능.
- **개인정보**: 성명 · 여권번호 등은 서버에서 마스킹해서 내려줌. 「개인정보 보기」는 사유 입력 후 1건씩 원문 조회 + 감사LOG.
- **보안**: 내부망/허용 IP 전용, 로그인 2단계 인증, 15분 미사용 시 자동 로그아웃, 검색엔진 차단. 세부 보안 기준은 확정 후 반영.

- **대리점 과금 (KT 통합 Admin 정책)**: 계약 단위는 대리점 ID. 모든 관리자 계정 · 문서 · 사용량 · 인증 · 과금 이벤트에 대리점 ID를 붙이고 대리점별로 데이터 · 권한을 격리. 금액 · 한도는 「대리점 과금관리 › 계약 · 요금 설정」(`agency/setting`) 설정값에서만 읽음(고정 금액 구현 금지). 설정 저장 시 변경 이력 + 고객사 어드민 읽기 전용 반영.
- **집계 규칙**: 문서 처리 = 성공한 업로드 · 재업로드 · 다운로드 각 1건(실패 · 목록 조회 제외). 동시 보관 용량 = 실제 저장 바이트 합계(삭제 · 만료 시 차감). 여권인증 청구 = 결과 산출 건(성공 · 실패), 결과 미산출 제외. 외부 전송량 = 다운로드 등 외부로 보낸 바이트 합계(월 한도 100GB, 확장팩 산정 포함 — 문서 · 보관 · 전송 중 가장 많이 필요한 팩 수만 청구).
- **월 정산**: 기본료 + MAX(인증 건수 × 단가, 최소료) + 유료 추가 계정(당월 최초 고객정보 · 문서 조회/처리한 계정, 생성 · 로그인만으로는 미과금, 당월 삭제해도 유지) + 확장팩(신청 즉시 적용 · 신청 월 전액). 일할계산 없음. VAT = floor(공급가액 × 10%). 월 마감 확정 시 설정값 스냅샷 고정, 이후 변경은 「정정」(사유 · 조정액 · 감사LOG)으로만.
- **한도 제어**: 보관 잔여 10% 경고 · 초과 시 업로드/다운로드 차단 · 문서 월 한도 도달 시 처리 제한 · 확장팩 신청 즉시 해제. 필요 팩 수 = max(문서 초과분 기준, 용량 초과분 기준).

## 대시보드  `#/dashboard` · 프로그램ID 001

전용 화면


### 권한 코드 (역할별로 부여)

`dashboard|조회`

## 인증거래처관리 › 종합관리  `#/partner/overview` · 프로그램ID 002

SIGNION 인증 서비스를 이용하는 제휴사(거래처)의 등록·심사·접속 정보를 통합 관리합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `pid` | 파트너ID | string (코드) |  | 대표키 |
| `name` | 거래처명 | string |  |  |
| `nat` | 국가 | string (ISO 국가코드) (KOR, IDN, VNM, NPL, MNG, UZB, KHM, MMR, LAO, LKA, BGD, CHN …) | ✓ |  |
| `loc` | 소재지 | string (국내, 해외) | ✓ |  |
| `mode` | 서비스모드 | enum (운영, 테스트) | ✓ |  |
| `mods` | 인증 모듈 | array |  | 읽기전용(시스템) |
| `cnt` | 이번달 인증 | number |  | 읽기전용(시스템) |
| `status` | 등록상태 | enum (대기중, 등록요청접수, 등록심사중, 자료보완요청, 등록거부, 등록완료) | ✓ |  |
| `rg` | 등록일 | date / datetime | ✓ | 읽기전용(시스템) |
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `code` | 거래ID | string (코드) |  |  |
| `biz` | 사업자번호 | string (코드) |  |  |
| `ceo` | 대표자 | string |  |  |
| `grade` | 거래처등급 | string |  |  |
| `ptype` | 거래처종류 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/partner-overview?from&to&q&qf&status&mode&loc&nat&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/partner-overview/{id}` | 상세 |
| POST | `/api/v1/partner-overview` | 등록 |
| PUT | `/api/v1/partner-overview/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/partner-overview/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/partner-overview/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/partner-overview/{id}/actions/%EB%93%B1%EB%A1%9D%EC%83%81%ED%83%9C%EB%B3%80%EA%B2%BD` | 기능버튼 「등록상태변경」 |
| POST | `/api/v1/partner-overview/{id}/actions/%EA%B3%84%EC%95%BD%EC%84%9C%EB%A5%98%EC%97%85%EB%A1%9C%EB%93%9C` | 기능버튼 「계약서류업로드」 |
| POST | `/api/v1/partner-overview/{id}/actions/%EC%A0%9C%ED%9C%B4%EC%82%AC%EC%A0%91%EC%86%8D%EC%A0%95%EB%B3%B4%EB%93%B1%EB%A1%9D` | 기능버튼 「제휴사접속정보등록」 |
| POST | `/api/v1/partner-overview/{id}/actions/%EB%B9%84%EB%B0%80%EB%B2%88%ED%98%B8%EC%B4%88%EA%B8%B0%ED%99%94` | 기능버튼 「비밀번호초기화」 |
| POST | `/api/v1/partner-overview/{id}/actions/%EB%A9%94%EB%AA%A8%EB%93%B1%EB%A1%9D` | 기능버튼 「메모등록」 |
| POST | `/api/v1/partner-overview/{id}/actions/%EC%95%84%EC%9D%B4%EB%94%94%EC%88%98%EC%A0%95` | 기능버튼 「아이디수정」 |
| POST | `/api/v1/partner-overview/{id}/actions/%EB%A7%88%EC%8A%A4%ED%84%B0Token%EC%83%9D%EC%84%B1` | 기능버튼 「마스터Token생성」 |
| POST | `/api/v1/partner-overview/{id}/actions/%EC%95%94%ED%98%B8%ED%99%94KEY%EC%83%9D%EC%84%B1` | 기능버튼 「암호화KEY생성」 |

### 권한 코드 (역할별로 부여)

`partner/overview|조회` · `partner/overview|등록` · `partner/overview|수정` · `partner/overview|삭제` · `partner/overview|엑셀로` · `partner/overview|리포트` · `partner/overview|삭제자료표시` · `partner/overview|등록상태변경` · `partner/overview|계약서류업로드` · `partner/overview|제휴사접속정보등록` · `partner/overview|비밀번호초기화` · `partner/overview|메모등록` · `partner/overview|아이디수정` · `partner/overview|마스터Token생성` · `partner/overview|암호화KEY생성`

## 인증거래처관리 › 요금관리  `#/partner/fee` · 프로그램ID 003

회사별 거래정보(사용서비스 · 거래수수료 · 청구정보)를 보고 오른쪽에서 수정합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `name` | 거래처명 | string |  | 대표키 |
| `mode` | 서비스 종류 | enum (운영, 테스트) | ✓ |  |
| `svcN` | 사용 서비스 수 | number |  | 읽기전용(시스템) |
| `svcT` | 사용 서비스 | string |  | 읽기전용(시스템) |
| `fee_OCR_krw` | 여권 AI OCR 단가(원) | number (원) |  |  |
| `fee_MIN_krw` | 월간 기본 이용 금액(원) | number (원) |  |  |
| `from` | 서비스 시작 일자 | date / datetime |  |  |
| `to` | 서비스 종료 일자 | date / datetime |  |  |
| `billDay` | 청구 일자 | number |  |  |
| `status` | 등록상태 | enum (대기중, 등록요청접수, 등록심사중, 자료보완요청, 등록거부, 등록완료) | ✓ |  |
| `rg` | 등록일 | date / datetime | ✓ | 읽기전용(시스템) |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/partner-fee?from&to&q&qf&status&mode&loc&nat&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/partner-fee/{id}` | 상세 |
| PUT | `/api/v1/partner-fee/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/partner-fee/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/partner-fee/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`partner/fee|조회` · `partner/fee|수정` · `partner/fee|삭제` · `partner/fee|엑셀로` · `partner/fee|리포트` · `partner/fee|삭제자료표시`

## 인증거래처관리 › 접속정보관리  `#/partner/conn` · 프로그램ID 004

회사별 접속정보(Web 접속 URL · iOS · AOS · 서버 IP)를 한 줄로 보고, 오른쪽에서 접속정보 · 인증정보를 관리합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `name` | 거래처명 | string |  | 대표키 |
| `pid` | 로그인ID | string (코드) |  |  |
| `cWebN` | Web 접속 URL 수 | number |  | 읽기전용(시스템) |
| `cWebT` | Web 접속 URL | string (코드) |  | 읽기전용(시스템) |
| `cIos` | iOS | string (코드) |  | 읽기전용(시스템) |
| `cAos` | AOS (Android) | string (코드) |  | 읽기전용(시스템) |
| `cIp` | 서버 접속 IP | string (코드) |  | 읽기전용(시스템) |
| `status` | 등록상태 | enum (대기중, 등록요청접수, 등록심사중, 자료보완요청, 등록거부, 등록완료) | ✓ |  |
| `rg` | 등록일 | date / datetime | ✓ | 읽기전용(시스템) |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/partner-conn?from&to&q&qf&status&mode&loc&nat&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/partner-conn/{id}` | 상세 |
| POST | `/api/v1/partner-conn` | 등록 |
| PUT | `/api/v1/partner-conn/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/partner-conn/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/partner-conn/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/partner-conn/{id}/actions/%EB%A7%88%EC%8A%A4%ED%84%B0Token%EC%83%9D%EC%84%B1` | 기능버튼 「마스터Token생성」 |
| POST | `/api/v1/partner-conn/{id}/actions/%EC%95%94%ED%98%B8%ED%99%94KEY%EC%83%9D%EC%84%B1` | 기능버튼 「암호화KEY생성」 |

### 권한 코드 (역할별로 부여)

`partner/conn|조회` · `partner/conn|등록` · `partner/conn|수정` · `partner/conn|삭제` · `partner/conn|엑셀로` · `partner/conn|리포트` · `partner/conn|삭제자료표시` · `partner/conn|마스터Token생성` · `partner/conn|암호화KEY생성`

## 인증거래처관리 › 사업자정보관리  `#/partner/biz` · 프로그램ID 005

회사별 사업자정보를 보고 오른쪽에서 수정합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `name` | 거래처명 | string |  | 대표키 |
| `bname` | 상호 | string |  |  |
| `biz` | 사업자번호 | string (코드) |  |  |
| `corpNo` | 법인번호 | string (코드) |  |  |
| `bizType` | 업태 | string |  |  |
| `bizItem` | 종목 | string |  |  |
| `baddr` | 주소 | string |  |  |
| `status` | 등록상태 | enum (대기중, 등록요청접수, 등록심사중, 자료보완요청, 등록거부, 등록완료) | ✓ |  |
| `rg` | 등록일 | date / datetime | ✓ | 읽기전용(시스템) |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/partner-biz?from&to&q&qf&status&mode&loc&nat&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/partner-biz/{id}` | 상세 |
| PUT | `/api/v1/partner-biz/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/partner-biz/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/partner-biz/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`partner/biz|조회` · `partner/biz|수정` · `partner/biz|삭제` · `partner/biz|엑셀로` · `partner/biz|리포트` · `partner/biz|삭제자료표시`

## 인증거래처관리 › 업체대표자관리  `#/partner/ceo` · 프로그램ID 006

회사별 대표자정보를 보고 오른쪽에서 수정합니다. 목록의 개인정보는 가려서 표시합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `name` | 거래처명 | string |  | 대표키 |
| `ceo` | 대표자성명 | string |  |  |
| `ceoBirth` | 생년월일 | string |  |  |
| `ceoMobile` | 핸드폰번호 | string (코드) |  |  |
| `btel` | 전화번호 | string (코드) |  |  |
| `ceoMail` | E-MAIL | string |  |  |
| `status` | 등록상태 | enum (대기중, 등록요청접수, 등록심사중, 자료보완요청, 등록거부, 등록완료) | ✓ |  |
| `rg` | 등록일 | date / datetime | ✓ | 읽기전용(시스템) |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/partner-ceo?from&to&q&qf&status&mode&loc&nat&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/partner-ceo/{id}` | 상세 |
| PUT | `/api/v1/partner-ceo/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/partner-ceo/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/partner-ceo/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`partner/ceo|조회` · `partner/ceo|수정` · `partner/ceo|삭제` · `partner/ceo|엑셀로` · `partner/ceo|리포트` · `partner/ceo|삭제자료표시`

## KT통신가입관리 › 종합관리  `#/kt/overview` · 프로그램ID 007

KT → 대리점 본사(마스터) → 현지 총괄 → 현지 영업소(서브) 4단계 관리자 계정을 관리합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `tier` | 권한 | enum |  |  |
| `uid` | 계정ID | string (코드) |  | 대표키 |
| `org` | 소속 | string |  |  |
| `nat` | 국가 | string (ISO 국가코드) (KOR, IDN, VNM, NPL, MNG, UZB, KHM, MMR, LAO, LKA, BGD, CHN …) | ✓ |  |
| `man` | 담당자 | string |  |  |
| `mail` | 이메일 (2FA) | string |  |  |
| `children` | 하위계정 | number |  | 읽기전용(시스템) |
| `cust` | 등록 고객 | number |  | 읽기전용(시스템) |
| `status` | 등록상태 | enum (대기중, 등록요청접수, 등록심사중, 자료보완요청, 등록거부, 등록완료) | ✓ |  |
| `last` | 최근 접속 | date / datetime |  | 읽기전용(시스템) |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/kt-overview?from&to&q&qf&status&nat&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/kt-overview/{id}` | 상세 |
| POST | `/api/v1/kt-overview` | 등록 |
| PUT | `/api/v1/kt-overview/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/kt-overview/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/kt-overview/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/kt-overview/{id}/actions/%EB%93%B1%EB%A1%9D%EC%83%81%ED%83%9C%EB%B3%80%EA%B2%BD` | 기능버튼 「등록상태변경」 |
| POST | `/api/v1/kt-overview/{id}/actions/%EA%B3%84%EC%95%BD%EC%84%9C%EB%A5%98%EC%97%85%EB%A1%9C%EB%93%9C` | 기능버튼 「계약서류업로드」 |
| POST | `/api/v1/kt-overview/{id}/actions/%EB%B9%84%EB%B0%80%EB%B2%88%ED%98%B8%EC%B4%88%EA%B8%B0%ED%99%94` | 기능버튼 「비밀번호초기화」 |
| POST | `/api/v1/kt-overview/{id}/actions/%EB%A9%94%EB%AA%A8%EB%93%B1%EB%A1%9D` | 기능버튼 「메모등록」 |
| POST | `/api/v1/kt-overview/{id}/actions/%EC%95%84%EC%9D%B4%EB%94%94%EC%88%98%EC%A0%95` | 기능버튼 「아이디수정」 |

### 권한 코드 (역할별로 부여)

`kt/overview|조회` · `kt/overview|등록` · `kt/overview|수정` · `kt/overview|삭제` · `kt/overview|엑셀로` · `kt/overview|리포트` · `kt/overview|삭제자료표시` · `kt/overview|등록상태변경` · `kt/overview|계약서류업로드` · `kt/overview|비밀번호초기화` · `kt/overview|메모등록` · `kt/overview|아이디수정`

## KT통신가입관리 › 등록서류관리  `#/kt/docs` · 프로그램ID 008

여권인증 고객별 필수 서류를 등록·검수하고 KT 제출용 통합 PDF를 발급합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `no` | 고객번호 | string (코드) |  | 대표키, 읽기전용(시스템) |
| `name` | 고객명 (여권) | string · 개인정보(마스킹) |  | 개인정보 |
| `visa` | 비자 | string (E-9, E-7, D-2, D-4) | ✓ |  |
| `auth` | 여권인증 | object |  | 읽기전용(시스템) |
| `docs` | 필수서류 | array (서류 진행) |  | 읽기전용(시스템) |
| `stage` | 진행상태 | enum (서류미비, 검수대기, 검수완료, KT제출, 개통완료) | ✓ |  |
| `sub` | 등록계정 | string (코드) (HDI-JKT-01, HDI-JKT-02, HDI-SBY-01, BND-HAN-01, BND-HCM-01, GDK-KTM-01) | ✓ |  |
| `rg` | 등록일시 | date / datetime | ✓ | 읽기전용(시스템) |
| `ret` | 보관만료 | computed |  | 읽기전용(시스템) |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/kt-docs?from&to&q&qf&stage&visa&nat&sub&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/kt-docs/{id}` | 상세 |
| POST | `/api/v1/kt-docs` | 등록 |
| PUT | `/api/v1/kt-docs/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/kt-docs/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/kt-docs/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/kt-docs/{id}/actions/%EC%84%9C%EB%A5%98%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C` | 기능버튼 「서류 다운로드」 |
| POST | `/api/v1/kt-docs/{id}/actions/%EC%84%9C%EB%A5%98%20PDF%EB%B3%80%ED%99%98%20%ED%86%B5%ED%95%A9%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C` | 기능버튼 「서류 PDF변환 통합 다운로드」 |
| POST | `/api/v1/kt-docs/{id}/actions/%EC%84%9C%EB%A5%98%20%EC%97%85%EB%A1%9C%EB%93%9C` | 기능버튼 「서류 업로드」 |
| POST | `/api/v1/kt-docs/{id}/actions/%EA%B2%80%EC%88%98%20%EC%99%84%EB%A3%8C` | 기능버튼 「검수 완료」 |
| POST | `/api/v1/kt-docs/{id}/actions/%ED%86%B5%ED%95%A9%20PDF%20%EB%B0%9C%EA%B8%89` | 기능버튼 「통합 PDF 발급」 |
| POST | `/api/v1/kt-docs/{id}/actions/%EA%B0%9C%ED%86%B5%20%EC%99%84%EB%A3%8C%20%EC%B2%98%EB%A6%AC` | 기능버튼 「개통 완료 처리」 |
| POST | `/api/v1/kt-docs/{id}/actions/%EA%B0%9C%EC%9D%B8%EC%A0%95%EB%B3%B4%20%EB%B3%B4%EA%B8%B0` | 기능버튼 「개인정보 보기」 |

### 권한 코드 (역할별로 부여)

`kt/docs|조회` · `kt/docs|등록` · `kt/docs|수정` · `kt/docs|삭제` · `kt/docs|엑셀로` · `kt/docs|리포트` · `kt/docs|삭제자료표시` · `kt/docs|서류 다운로드` · `kt/docs|서류 PDF변환 통합 다운로드` · `kt/docs|서류 업로드` · `kt/docs|검수 완료` · `kt/docs|통합 PDF 발급` · `kt/docs|개통 완료 처리` · `kt/docs|개인정보 보기`

## KT통신가입관리 › 서류종류타입관리  `#/kt/doctype` · 프로그램ID 009

고객 구분(근로자 E-9·E-7 / 유학생 D-2·D-4 / 미성년자)별 필수 제출 서류와 통합 PDF 병합 순서를 정의합니다. (_documentType)

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `code` | 코드 | string (코드) |  | 대표키 |
| `name` | 서류명 | string |  |  |
| `target` | 제출 대상 | string |  |  |
| `req` | 구분 | enum (필수, 선택, 자동생성) | ✓ |  |
| `max` | 최대 파일수 | number |  |  |
| `order` | 병합 순서 | number |  |  |
| `use` | 사용 | enum (사용, 미사용) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/kt-doctype?from&to&q&qf&req&use&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/kt-doctype/{id}` | 상세 |
| POST | `/api/v1/kt-doctype` | 등록 |
| PUT | `/api/v1/kt-doctype/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/kt-doctype/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/kt-doctype/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`kt/doctype|조회` · `kt/doctype|등록` · `kt/doctype|수정` · `kt/doctype|삭제` · `kt/doctype|엑셀로` · `kt/doctype|리포트` · `kt/doctype|삭제자료표시`

## KT통신가입관리 › 요금타입관리  `#/kt/ratetype` · 프로그램ID 010

KT 해외 통신서비스 과금 체계 (VAT 별도) — 과금체계 시트 기준. 신규 대리점 설정의 기본값 템플릿이며, 실제 청구는 「대리점 과금관리 › 계약 · 요금 설정」의 대리점별 값을 사용합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `code` | 코드 | string (코드) |  | 대표키 |
| `name` | 요금 항목 | string |  |  |
| `price` | 단가 | number (원) |  |  |
| `unit` | 단위 | string |  |  |
| `desc` | 산정 기준 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/kt-ratetype?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/kt-ratetype/{id}` | 상세 |
| POST | `/api/v1/kt-ratetype` | 등록 |
| PUT | `/api/v1/kt-ratetype/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/kt-ratetype/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/kt-ratetype/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`kt/ratetype|조회` · `kt/ratetype|등록` · `kt/ratetype|수정` · `kt/ratetype|삭제` · `kt/ratetype|엑셀로` · `kt/ratetype|리포트` · `kt/ratetype|삭제자료표시`

## 인증내역관리  `#/trans` · 프로그램ID 011

kycTransMaster / kycTransDetail — 승인번호 단위의 KYC 인증 처리 내역과 단계별 결과를 조회합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 순번 | number |  | 읽기전용(시스템) |
| `dt` | 등록일자 | date / datetime | ✓ |  |
| `pid` | 제휴사ID | string (코드) (Lordsystem, KTGLOBAL, HDINFINITY, BOUNDARY, GDKOREA, KTMNS) | ✓ |  |
| `partner` | 제휴사명 | string |  |  |
| `no` | 승인번호 | string (코드) |  | 대표키 |
| `kyc` | 인증완료 | enum (완료, 중단, 진행중) | ✓ |  |
| `sent` | 결과전달 | string |  |  |
| `stMsg` | 인증상태메시지 | string |  |  |
| `nat` | 국가코드 | string (ISO 국가코드) (KOR, IDN, VNM, NPL, MNG, UZB, KHM, MMR, LAO, LKA, BGD, CHN …) | ✓ |  |
| `pass` | 여권번호 | string (코드) |  | 개인정보 |
| `name` | 여권성명 | string · 개인정보(마스킹) |  | 개인정보 |
| `fee` | 수수료(KRW) | number (원) |  |  |
| `feeUsd` | 수수료(USD) | number |  |  |
| `res` | 인증 모듈 | array (모듈별 결과) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/trans?from&to&q&qf&kyc&pid&nat&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/trans/{id}` | 상세 |
| DELETE | `/api/v1/trans/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/trans/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/trans/{id}/actions/%EC%9D%B8%EC%A6%9D%EC%88%98%EC%88%98%EB%A3%8C%EA%B3%84%EC%82%B0` | 기능버튼 「인증수수료계산」 |
| POST | `/api/v1/trans/{id}/actions/%EB%A9%94%EB%AA%A8%EB%93%B1%EB%A1%9D` | 기능버튼 「메모등록」 |
| POST | `/api/v1/trans/{id}/actions/%EA%B0%9C%EC%9D%B8%EC%A0%95%EB%B3%B4%20%EB%B3%B4%EA%B8%B0` | 기능버튼 「개인정보 보기」 |

### 권한 코드 (역할별로 부여)

`trans|조회` · `trans|삭제` · `trans|엑셀로` · `trans|리포트` · `trans|삭제자료표시` · `trans|인증수수료계산` · `trans|메모등록` · `trans|개인정보 보기`

## 인증수수료관리  `#/fee` · 프로그램ID 012

월별 인증 건수 × 단가와 월 최소이용금액을 비교해 청구 금액을 산출합니다. (VAT 별도)

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `name` | 거래처 | string |  | 대표키 |
| `cnt` | 인증 건수 | number |  |  |
| `use` | 실사용금액 | number (원) |  |  |
| `min` | 월 최소이용 | number (원) |  |  |
| `bill` | 청구 인증료 | string |  |  |
| `admin` | 관리자 사용료 | number (원) |  |  |
| `tot` | 청구 합계 | number (원) |  |  |
| `paid` | 입금액 | number (원) |  |  |
| `due` | 미수금 | string |  |  |
| `status` | 상태 | enum (입금완료, 부분입금, 미입금, 청구전) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/fee?from&to&q&qf&status&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/fee/{id}` | 상세 |
| GET | `/api/v1/fee/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/fee/{id}/actions/%EC%9D%B8%EC%A6%9D%EC%88%98%EC%88%98%EB%A3%8C%EC%83%9D%EC%84%B1` | 기능버튼 「인증수수료생성」 |
| POST | `/api/v1/fee/{id}/actions/%EC%9D%B8%EC%A6%9D%EC%88%98%EC%88%98%EB%A3%8C%EC%B2%AD%EA%B5%AC%EC%9E%90%EB%A3%8C%EC%9E%91%EC%84%B1` | 기능버튼 「인증수수료청구자료작성」 |
| POST | `/api/v1/fee/{id}/actions/%EC%9E%85%EA%B8%88%EB%93%B1%EB%A1%9D` | 기능버튼 「입금등록」 |
| POST | `/api/v1/fee/{id}/actions/%EC%9E%85%EA%B8%88%EB%82%B4%EC%97%ADLIST` | 기능버튼 「입금내역LIST」 |
| POST | `/api/v1/fee/{id}/actions/%EB%AF%B8%EC%88%98%EA%B8%88%EA%B3%84%EC%82%B0` | 기능버튼 「미수금계산」 |
| POST | `/api/v1/fee/{id}/actions/%EB%AF%B8%EC%88%98%EA%B8%88%EB%82%B4%EC%97%ADLIST` | 기능버튼 「미수금내역LIST」 |

### 권한 코드 (역할별로 부여)

`fee|조회` · `fee|엑셀로` · `fee|리포트` · `fee|삭제자료표시` · `fee|인증수수료생성` · `fee|인증수수료청구자료작성` · `fee|입금등록` · `fee|입금내역LIST` · `fee|미수금계산` · `fee|미수금내역LIST`

## 대리점 과금관리 › 계약 · 요금 설정  `#/agency/setting` · 프로그램ID 013

대리점 ID별 계약기간 · 월 기본료 · 여권인증 단가 · 월 최소 사용료 · 기본 계정 수(총괄 · 총괄별 서브) · 추가 계정 단가 · 문서/보관/외부 전송 한도 · 확장팩 조건을 설정합니다. 저장하면 변경 이력이 남고 고객사 어드민에 바로 반영됩니다 (고객사는 읽기 전용).

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `agid` | 대리점ID | string (코드) |  | 대표키 |
| `name` | 대리점명 | string |  |  |
| `from` | 계약 시작 | date / datetime | ✓ |  |
| `to` | 계약 종료 | date / datetime |  |  |
| `base` | 플랫폼 월 기본료 | number (원) |  |  |
| `unit` | 여권인증 건당 단가 | number (원) |  |  |
| `min` | 월 최소 사용료 | number (원) |  |  |
| `bm` | 기본 마스터 수 | number |  |  |
| `bg` | 기본 총괄 수 | number |  |  |
| `bs` | 기본 서브 수(총괄별) | number |  |  |
| `xg` | 추가 총괄 단가(월) | number (원) |  |  |
| `xs` | 추가 서브 단가(월) | number (원) |  |  |
| `docLim` | 문서 월 처리 한도(건) | number |  |  |
| `storGB` | 동시 보관 용량(GB) | number |  |  |
| `xferGB` | 월 외부 전송량(GB) | number |  |  |
| `packPrice` | 확장팩 단가 | number (원) |  |  |
| `packDocs` | 팩당 추가 문서(건) | number |  |  |
| `packGB` | 팩당 추가 용량(GB) | number |  |  |
| `packXfer` | 팩당 추가 전송량(GB) | number |  |  |
| `status` | 계약상태 | enum (계약중, 만료 예정, 만료) | ✓ | 읽기전용(시스템) |
| `upd` | 최종 변경 | date / datetime |  | 읽기전용(시스템) |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/agency-setting?from&to&q&qf&status&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/agency-setting/{id}` | 상세 |
| POST | `/api/v1/agency-setting` | 등록 |
| PUT | `/api/v1/agency-setting/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/agency-setting/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/agency-setting/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`agency/setting|조회` · `agency/setting|등록` · `agency/setting|수정` · `agency/setting|삭제` · `agency/setting|엑셀로` · `agency/setting|리포트` · `agency/setting|삭제자료표시`

## 대리점 과금관리 › 설정 변경 이력  `#/agency/hist` · 프로그램ID 014

대리점별 계약 · 요금 · 계정 · 한도 · 확장팩 조건의 변경 전/후, 작업자, 적용 시점, 고객사 어드민 반영 결과를 남깁니다. 확정된 월 정산에는 소급하지 않습니다. 기록은 고치거나 지울 수 없습니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 변경 일시 | date / datetime | ✓ | 대표키 |
| `agid` | 대리점ID | string (코드) (HDINFINITY, BOUNDARY, GDKOREA) | ✓ |  |
| `name` | 대리점 | string |  |  |
| `field` | 항목 | string |  |  |
| `before` | 변경 전 | string |  |  |
| `after` | 변경 후 | string |  |  |
| `who` | 작업자 | string (코드) |  |  |
| `apply` | 적용 시점 | string |  |  |
| `sync` | 고객사 반영 | enum (반영 완료, 반영 실패) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/agency-hist?from&to&q&qf&agid&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/agency-hist/{id}` | 상세 |
| GET | `/api/v1/agency-hist/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`agency/hist|조회` · `agency/hist|엑셀로` · `agency/hist|리포트` · `agency/hist|삭제자료표시`

## 대리점 과금관리 › 사용량 조회  `#/agency/usage` · 프로그램ID 015

대리점별 · 월별 문서 업로드 · 재업로드 · 다운로드 건수, 동시 보관 용량, 월 외부 전송량, 계정 · 확장팩 수를 조회하고 내보냅니다. 외부 전송량도 한도(기본 100GB/월)가 있어, 문서 처리 · 보관 용량 · 외부 전송 중 가장 많이 필요한 확장팩 수를 기준으로 안내합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `m` | 월 | string (코드) (2026-10, 2026-11) | ✓ | 대표키 |
| `agid` | 대리점ID | string (코드) (HDINFINITY, BOUNDARY, GDKOREA) | ✓ |  |
| `name` | 대리점 | string |  |  |
| `up` | 업로드 | number |  |  |
| `reup` | 재업로드 | number |  |  |
| `dl` | 다운로드 | number |  |  |
| `docTot` | 문서 처리 합계 | number |  |  |
| `docLim` | 처리 한도 | number |  |  |
| `stor` | 동시 보관(GB) | number |  |  |
| `storLim` | 보관 한도(GB) | number |  |  |
| `eg` | 외부 전송(GB) | number |  |  |
| `egLim` | 전송 한도(GB) | number |  |  |
| `acct` | 계정 수 | number |  |  |
| `paidAcct` | 유료 추가 계정 | number |  |  |
| `packs` | 확장팩 | number |  |  |
| `lstate` | 한도 상태 | enum (정상, 용량 경고, 용량 초과 차단, 처리 한도 도달, 전송량 초과) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/agency-usage?from&to&q&qf&m&agid&lstate&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/agency-usage/{id}` | 상세 |
| GET | `/api/v1/agency-usage/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`agency/usage|조회` · `agency/usage|엑셀로` · `agency/usage|리포트` · `agency/usage|삭제자료표시`

## 대리점 과금관리 › 확장팩 적용 이력  `#/agency/pack` · 프로그램ID 016

고객사 마스터가 신청해 즉시 적용된 확장팩의 수량 · 적용일 · 추가 한도 · 청구월을 조회합니다. 내부 승인 절차는 없으며 신청 월에 전액 과금됩니다 (일할계산 없음).

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 신청 · 적용 일시 | date / datetime | ✓ | 대표키 |
| `agid` | 대리점ID | string (코드) (HDINFINITY, BOUNDARY, GDKOREA) | ✓ |  |
| `name` | 대리점 | string |  |  |
| `qty` | 수량 | number |  |  |
| `addDocs` | 추가 문서(건) | number |  |  |
| `addGB` | 추가 용량(GB) | number |  |  |
| `addXfer` | 추가 전송(GB) | number |  |  |
| `price` | 적용 단가 | number (원) |  |  |
| `amt` | 청구 금액 | number (원) |  |  |
| `by` | 신청자(마스터) | string (코드) |  |  |
| `m` | 청구월 | string (코드) (2026-10, 2026-11) | ✓ |  |
| `how` | 처리 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/agency-pack?from&to&q&qf&agid&m&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/agency-pack/{id}` | 상세 |
| GET | `/api/v1/agency-pack/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`agency/pack|조회` · `agency/pack|엑셀로` · `agency/pack|리포트` · `agency/pack|삭제자료표시`

## 대리점 과금관리 › 월 정산 · 마감  `#/agency/bill` · 프로그램ID 017

대리점별 설정 단가와 확정된 이용 로그로 기본료 · 여권인증료 · 추가 계정료 · 확장팩료를 산정하고, 월 마감 확정 · 정정 · 정산서 다운로드를 합니다. 확정된 정산서는 그 시점 설정값으로 고정되어 이후 설정 변경의 영향을 받지 않습니다. (VAT 10% 별도 계산)

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `m` | 정산월 | string (코드) (2026-10, 2026-11) | ✓ | 대표키 |
| `agid` | 대리점ID | string (코드) (HDINFINITY, BOUNDARY, GDKOREA) | ✓ |  |
| `name` | 대리점 | string |  |  |
| `authN` | 인증 건수 | number |  |  |
| `base` | 기본료 | number (원) |  |  |
| `bill` | 여권인증 사용료 | string |  |  |
| `xg` | 추가 총괄 | number (원) |  |  |
| `xs` | 추가 서브 | number (원) |  |  |
| `pack` | 확장팩 | number (원) |  |  |
| `fix` | 정정 조정 | number (원) |  |  |
| `supply` | 공급가액 | number (원) |  |  |
| `vat` | 부가세 | number (원) |  |  |
| `total` | 총 청구금액 | number (원) |  |  |
| `status` | 상태 | enum (집계 중, 마감 대기, 확정, 정정) | ✓ |  |
| `closed` | 확정 일시 | date / datetime |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/agency-bill?from&to&q&qf&m&agid&status&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/agency-bill/{id}` | 상세 |
| GET | `/api/v1/agency-bill/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/agency-bill/{id}/actions/%EC%9B%94%20%EB%A7%88%EA%B0%90%20%ED%99%95%EC%A0%95` | 기능버튼 「월 마감 확정」 |
| POST | `/api/v1/agency-bill/{id}/actions/%EC%A0%95%EC%A0%95` | 기능버튼 「정정」 |
| POST | `/api/v1/agency-bill/{id}/actions/%EC%A0%95%EC%82%B0%EC%84%9C%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C` | 기능버튼 「정산서 다운로드」 |

### 권한 코드 (역할별로 부여)

`agency/bill|조회` · `agency/bill|엑셀로` · `agency/bill|리포트` · `agency/bill|삭제자료표시` · `agency/bill|월 마감 확정` · `agency/bill|정정` · `agency/bill|정산서 다운로드`

## 공통 서버 집계 › 문서 처리 이벤트  `#/srv/doclog` · 프로그램ID 018

성공한 업로드 · 재업로드 · 다운로드를 각 1건으로 집계합니다. 실패 작업은 집계에서 제외하고, 목록 조회는 처리 건수가 아닙니다 (미리보기 기능 없음). 다운로드 · 조회 시 실제 외부 송신 바이트를 함께 기록합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `agid` | 대리점ID | string (코드) (HDINFINITY, BOUNDARY, GDKOREA) | ✓ |  |
| `acct` | 계정ID | string (코드) |  |  |
| `doc` | 문서ID | string (코드) |  |  |
| `kind` | 처리 | enum (업로드, 재업로드, 다운로드) | ✓ |  |
| `result` | 결과 | enum (성공, 실패) | ✓ |  |
| `mb` | 파일(MB) | number |  |  |
| `egMB` | 외부 송신(MB) | number |  |  |
| `counted` | 처리 건수 집계 | enum (집계, 제외) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/srv-doclog?from&to&q&qf&agid&kind&result&counted&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/srv-doclog/{id}` | 상세 |
| GET | `/api/v1/srv-doclog/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`srv/doclog|조회` · `srv/doclog|엑셀로` · `srv/doclog|리포트` · `srv/doclog|삭제자료표시`

## 공통 서버 집계 › 보관 용량 증감  `#/srv/storage` · 프로그램ID 019

대리점 하위 전체 계정의 고객정보 · 제출 문서 · 여권인증 정보 실제 저장 바이트 합계와 증감 이력입니다. 삭제 · 만료 시 차감합니다. 월 업로드 누적량과는 별개입니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `agid` | 대리점ID | string (코드) (HDINFINITY, BOUNDARY, GDKOREA) | ✓ |  |
| `kind` | 증감 | enum (증가, 차감) | ✓ |  |
| `reason` | 사유 | string |  |  |
| `delta` | 변동(MB) | number |  |  |
| `after` | 변동 후(GB) | number |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/srv-storage?from&to&q&qf&agid&kind&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/srv-storage/{id}` | 상세 |
| GET | `/api/v1/srv-storage/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`srv/storage|조회` · `srv/storage|엑셀로` · `srv/storage|리포트` · `srv/storage|삭제자료표시`

## 공통 서버 집계 › 여권인증 청구 집계  `#/srv/auth` · 프로그램ID 020

정상적으로 인증 결과가 산출된 건은 성공 · 실패 모두 청구 대상 1건입니다. 중도 이탈 · 통신 오류처럼 결과가 산출되지 않은 요청은 제외합니다. 월 인증료 = MAX(결과 산출 건수 × 설정 단가, 설정 최소료).

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `agid` | 대리점ID | string (코드) (HDINFINITY, BOUNDARY, GDKOREA) | ✓ |  |
| `no` | 승인번호 | string (코드) |  |  |
| `result` | 처리 결과 | enum (성공, 실패, 결과 미산출) | ✓ |  |
| `code` | 코드 | string (코드) |  |  |
| `bill` | 청구 | enum (청구, 제외) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/srv-auth?from&to&q&qf&agid&result&bill&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/srv-auth/{id}` | 상세 |
| GET | `/api/v1/srv-auth/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`srv/auth|조회` · `srv/auth|엑셀로` · `srv/auth|리포트` · `srv/auth|삭제자료표시`

## 공통 서버 집계 › 유료 계정 최초 이용  `#/srv/acctuse` · 프로그램ID 021

유료 추가 계정이 그 달 처음 고객정보 · 문서를 조회 · 처리한 시점입니다 (생성 · 로그인만으로는 미과금). 사용 후 같은 달에 삭제 · 비활성화해도 과금은 유지되며, 이 기록은 계정 삭제 후에도 보존됩니다. 과금 근거 이력 — 접근 권한 분리.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `uid` | 계정ID | string (코드) |  | 대표키 |
| `agid` | 대리점ID | string (코드) (HDINFINITY, BOUNDARY, GDKOREA) | ✓ |  |
| `lvn` | 위계 | string |  |  |
| `m` | 청구월 | string (코드) (2026-10, 2026-11) | ✓ |  |
| `at` | 최초 실제 이용 | date / datetime | ✓ |  |
| `what` | 이용 내용 | string |  |  |
| `now` | 현재 계정 상태 | enum |  |  |
| `billed` | 과금 | enum |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/srv-acctuse?from&to&q&qf&agid&m&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/srv-acctuse/{id}` | 상세 |
| GET | `/api/v1/srv-acctuse/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`srv/acctuse|조회` · `srv/acctuse|엑셀로` · `srv/acctuse|리포트` · `srv/acctuse|삭제자료표시`

## 공통 서버 집계 › 한도 이벤트  `#/srv/limit` · 프로그램ID 022

보관 용량 잔여 10% 경고, 보관 용량 초과 시 업로드 · 다운로드 차단, 문서 처리 월 한도 도달 시 추가 처리 제한, 확장팩 신청 즉시 한도 확대 · 차단 해제 이벤트입니다. 제한 기준은 대리점별 설정 한도입니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `agid` | 대리점ID | string (코드) (HDINFINITY, BOUNDARY, GDKOREA) | ✓ |  |
| `ev` | 이벤트 | enum (용량 경고, 용량 초과 차단, 처리 한도 도달, 확장팩 적용 · 해제) | ✓ |  |
| `detail` | 내용 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/srv-limit?from&to&q&qf&agid&ev&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/srv-limit/{id}` | 상세 |
| GET | `/api/v1/srv-limit/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`srv/limit|조회` · `srv/limit|엑셀로` · `srv/limit|리포트` · `srv/limit|삭제자료표시`

## 공통 서버 집계 › 개인정보 자동 삭제  `#/srv/purge` · 프로그램ID 023

고객정보 · 여권인증 정보 · 제출 문서는 클라우드에만 저장합니다. 담당자가 「개통 완료」를 누르면 즉시 삭제하고, 미개통 고객정보는 최대 2개월 보관 후 자동 삭제하며 저장 용량에서 차감합니다. 실제 보관기간은 적용 정책 기준입니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 실행 일시 | date / datetime | ✓ | 대표키 |
| `agid` | 대리점ID | string (코드) (HDINFINITY, BOUNDARY, GDKOREA) | ✓ |  |
| `target` | 대상 고객번호 | string (코드) |  |  |
| `reason` | 사유 | enum (개통 완료, 미개통 2개월 경과) | ✓ |  |
| `items` | 삭제 항목 | string |  |  |
| `freed` | 차감(MB) | number |  |  |
| `result` | 결과 | enum (성공, 실패) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/srv-purge?from&to&q&qf&agid&reason&result&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/srv-purge/{id}` | 상세 |
| GET | `/api/v1/srv-purge/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`srv/purge|조회` · `srv/purge|엑셀로` · `srv/purge|리포트` · `srv/purge|삭제자료표시`

## 공통 서버 집계 › 백업 · 복구  `#/srv/backup` · 프로그램ID 024

클라우드 내 정기 백업과 장애 시 복구 수행 결과 · 오류 로그입니다. ※ 백업 주기 · 보관기간 및 개인정보 삭제 정책과의 정합성(삭제된 개인정보가 백업에서 되살아나지 않도록)은 기술팀이 설계 후 제안합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 시작 일시 | date / datetime | ✓ | 대표키 |
| `kind` | 구분 | enum (정기 백업, 복구) | ✓ |  |
| `scope` | 대상 | string |  |  |
| `size` | 크기(GB) | number |  |  |
| `dur` | 소요 | string |  |  |
| `result` | 결과 | enum (성공, 실패) | ✓ |  |
| `note` | 비고 · 오류 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/srv-backup?from&to&q&qf&kind&result&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/srv-backup/{id}` | 상세 |
| GET | `/api/v1/srv-backup/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`srv/backup|조회` · `srv/backup|엑셀로` · `srv/backup|리포트` · `srv/backup|삭제자료표시`

## PKD관리  `#/pkd` · 프로그램ID 025

ICAO PKD 전자여권 서명 인증서(CSCA)를 관리합니다. 전자여권 HASH · CSCA 검증에 사용됩니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `nat` | 발급국가 | string (ISO 국가코드) (KOR, IDN, VNM, NPL, MNG, UZB, KHM, MMR, LAO, LKA, BGD, CHN …) | ✓ | 대표키 |
| `issuer` | 발급기관 | string |  |  |
| `serial` | 인증서 일련번호 | string (코드) |  |  |
| `from` | 유효 시작 | date / datetime | ✓ |  |
| `to` | 유효 만료 | date / datetime |  |  |
| `status` | 상태 | enum (유효, 만료임박, 만료) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/pkd?from&to&q&qf&status&nat&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/pkd/{id}` | 상세 |
| POST | `/api/v1/pkd` | 등록 |
| PUT | `/api/v1/pkd/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/pkd/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/pkd/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/pkd/{id}/actions/ICAO%EC%9E%90%EB%A3%8C%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C` | 기능버튼 「ICAO자료 다운로드」 |

### 권한 코드 (역할별로 부여)

`pkd|조회` · `pkd|등록` · `pkd|수정` · `pkd|삭제` · `pkd|엑셀로` · `pkd|리포트` · `pkd|삭제자료표시` · `pkd|ICAO자료 다운로드`

## 발행토큰관리  `#/token` · 프로그램ID 026

접속정보마다 발행된 인증 Token 목록입니다. 목록에서 고르면 오른쪽에 접속정보와 tokenKey 전체가 보입니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `idx` | IDX | number |  | 읽기전용(시스템) |
| `rg` | 등록일 | date / datetime | ✓ |  |
| `pid` | 제휴사ID | string (코드) (Lordsystem, KTGLOBAL, HDINFINITY, BOUNDARY, GDKOREA, KTMNS, VISIONMB, WINDM, EASTERNTEL, CORENET) | ✓ | 대표키 |
| `pname` | 제휴사명 | string |  |  |
| `ctypeL` | 접속종류 | string (1=AOS APP 접속 ID, 2=IOS APP 접속 ID, 3=WEB 접속 URL, 4=서버 접속 IP) | ✓ |  |
| `info` | 접속정보 | string (코드) |  |  |
| `resp` | 데이터응답URL | string (코드) |  |  |
| `ip` | 서버접속IP | string (코드) |  |  |
| `ttype` | Token종류 | string |  |  |
| `keyS` | tokenKey | string (코드) |  |  |
| `ct` | 생성시간 | date / datetime |  |  |
| `stopL` | 삭제코드 | string (0=정상, 1=삭제) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/token?from&to&q&qf&ctypeL&pid&stopL&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/token/{id}` | 상세 |
| GET | `/api/v1/token/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`token|조회` · `token|엑셀로` · `token|리포트` · `token|삭제자료표시`

## 암호화KEY관리  `#/crypto` · 프로그램ID 027

거래처별 데이터 · 통신용 암호화 KEY(AES-CBC-256 · MasterKey / IvKey) 목록입니다. 재발급하면 이전 KEY는 1=삭제로 남습니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `idx` | IDX | number |  | 읽기전용(시스템) |
| `rg` | 등록일 | date / datetime | ✓ |  |
| `pid` | 제휴사ID | string (코드) (SIGNION-HQ, Lordsystem, KTGLOBAL, HDINFINITY, BOUNDARY, GDKOREA, KTMNS, VISIONMB, WINDM, EASTERNTEL, CORENET) | ✓ | 대표키 |
| `pname` | 제휴사명 | string |  |  |
| `scope` | 용도 | string (본사 통신, 거래처 데이터·통신) | ✓ |  |
| `ctype` | 암호화방식 | string (코드) |  |  |
| `mkeyS` | MasterKey | string (코드) |  |  |
| `ivS` | IvKey | string (코드) |  |  |
| `stopL` | 삭제코드 | string (0=정상, 1=삭제) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/crypto?from&to&q&qf&pid&scope&stopL&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/crypto/{id}` | 상세 |
| GET | `/api/v1/crypto/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`crypto|조회` · `crypto|엑셀로` · `crypto|리포트` · `crypto|삭제자료표시`

## 메시지코드관리  `#/msg` · 프로그램ID 028

프론트 · API · AI 모듈이 돌려주는 메시지 코드를 언어별로 관리합니다. 개별 등록 · 엑셀 일괄 등록 · 전체 초기화를 지원합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `idx` | IDX | number |  | 읽기전용(시스템) |
| `rg` | 등록일 | date / datetime | ✓ | 읽기전용(시스템) |
| `progL` | 프로그램서비스 | string (1=프론트WEB, 2=API-WEB, 3=AI-OCR, 4=안면인식, 5=안티스푸핑, 6=전자여권인식, 7=globalekycapi, 8=PKD 인증, 9=SystemError) | ✓ |  |
| `code` | 메세지코드 | string (코드) |  | 대표키 |
| `langL` | 사용언어 | string (KOR=대한민국, USA=미국(영어), CHN=중국, JPN=일본, VNM=베트남, THA=태국, MNG=몽골, TWN=대만) | ✓ |  |
| `title` | 메세지 타이틀 | string |  |  |
| `ret` | 메세지 내용 | string |  |  |
| `sys` | 시스템 메시지 | string |  |  |
| `st` | 상태 | string (0=정상, 1=사용안함) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/msg?from&to&q&qf&progL&langL&st&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/msg/{id}` | 상세 |
| POST | `/api/v1/msg` | 등록 |
| PUT | `/api/v1/msg/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/msg/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/msg/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/msg/{id}/actions/%EB%A9%94%EC%84%B8%EC%A7%80%EC%BD%94%EB%93%9C%EC%97%85%EB%A1%9C%EB%93%9C` | 기능버튼 「메세지코드업로드」 |
| POST | `/api/v1/msg/{id}/actions/%EB%A9%94%EC%84%B8%EC%A7%80%EC%BD%94%EB%93%9C%EC%B4%88%EA%B8%B0%ED%99%94` | 기능버튼 「메세지코드초기화」 |

### 권한 코드 (역할별로 부여)

`msg|조회` · `msg|등록` · `msg|수정` · `msg|삭제` · `msg|엑셀로` · `msg|리포트` · `msg|삭제자료표시` · `msg|메세지코드업로드` · `msg|메세지코드초기화`

## LOG관리 › 서버접속 LOG  `#/log/server` · 프로그램ID 029

관리자 페이지 접속 이력 (로그인 계정 · 접속 IP · 사용 로그) — 관리자 사용 로그 정책

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `lv` | 이벤트 | enum (로그인 성공, 로그인 실패, 2FA 인증, 로그아웃, 엑셀 다운로드, 리포트 출력, 개인정보 열람, 세션 만료) | ✓ |  |
| `who` | 계정 | string (코드) |  |  |
| `ref` | 승인번호 | string (코드) |  |  |
| `req` | 접근 메뉴 | string |  |  |
| `code` | 코드 | string (코드) |  |  |
| `ms` | 소요(ms) | number |  |  |
| `ip` | IP | string (코드) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/log-server?from&to&q&qf&lv&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/log-server/{id}` | 상세 |
| GET | `/api/v1/log-server/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`log/server|조회` · `log/server|엑셀로` · `log/server|리포트` · `log/server|삭제자료표시`

## LOG관리 › 인증요청 LOG  `#/log/api` · 프로그램ID 030

제휴사 서버가 보낸 인증 API 요청(globalekycapi) 원문 기록입니다. 비밀번호 · 토큰은 가려서 저장합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `idx` | IDX | number |  | 읽기전용(시스템) |
| `rg` | 등록일 | date / datetime | ✓ |  |
| `pid` | 제휴사ID | string (코드) (Lordsystem, KTGLOBAL, HDINFINITY, BOUNDARY, GDKOREA, KTMNS, local, dev-partner, SIGNION) | ✓ | 대표키 |
| `pname` | 제휴사명 | string |  |  |
| `cmd` | cmd | string (코드) |  |  |
| `cmdL` | API 커맨드 | string (4010=서버연동 token 등록, 4020=제휴사 정보 요청, 5010=인증 로그 등록, 9000=Kyc 인증 완료, 10010=BASE 정보 요청, 10020=errorCode 메시지 리턴) | ✓ |  |
| `ref` | 승인번호 | string (코드) |  |  |
| `sys` | 요청일시 | date / datetime |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/log-api?from&to&q&qf&cmdL&pid&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/log-api/{id}` | 상세 |
| GET | `/api/v1/log-api/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`log/api|조회` · `log/api|엑셀로` · `log/api|리포트` · `log/api|삭제자료표시`

## LOG관리 › DB 요청/응답 LOG  `#/log/db` · 프로그램ID 031

저장 프로시저 호출 이력

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `lv` | 결과 | enum (SUCCESS, WARN, FAIL) | ✓ |  |
| `who` | 거래처 | string (코드) |  |  |
| `ref` | 승인번호 | string (코드) |  |  |
| `req` | 프로시저 | string |  |  |
| `code` | 코드 | string (코드) |  |  |
| `ms` | 소요(ms) | number |  |  |
| `ip` | IP | string (코드) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/log-db?from&to&q&qf&lv&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/log-db/{id}` | 상세 |
| GET | `/api/v1/log-db/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`log/db|조회` · `log/db|엑셀로` · `log/db|리포트` · `log/db|삭제자료표시`

## LOG관리 › AIOCR LOG  `#/log/aiocr` · 프로그램ID 032

AI-OCR 여권 인식 · BIZ-MRZ 투팩터 검증 로그

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `lv` | 결과 | enum (SUCCESS, WARN, FAIL) | ✓ |  |
| `who` | 거래처 | string (코드) |  |  |
| `ref` | 승인번호 | string (코드) |  |  |
| `req` | 요청 | string |  |  |
| `code` | 코드 | string (코드) |  |  |
| `ms` | 소요(ms) | number |  |  |
| `ip` | IP | string (코드) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/log-aiocr?from&to&q&qf&lv&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/log-aiocr/{id}` | 상세 |
| GET | `/api/v1/log-aiocr/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`log/aiocr|조회` · `log/aiocr|엑셀로` · `log/aiocr|리포트` · `log/aiocr|삭제자료표시`

## LOG관리 › AINFC LOG  `#/log/ainfc` · 프로그램ID 033

전자여권 NFC 칩 판독 · HASH 인증 로그

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `lv` | 결과 | enum (SUCCESS, WARN, FAIL) | ✓ |  |
| `who` | 거래처 | string (코드) |  |  |
| `ref` | 승인번호 | string (코드) |  |  |
| `req` | 요청 | string |  |  |
| `code` | 코드 | string (코드) |  |  |
| `ms` | 소요(ms) | number |  |  |
| `ip` | IP | string (코드) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/log-ainfc?from&to&q&qf&lv&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/log-ainfc/{id}` | 상세 |
| GET | `/api/v1/log-ainfc/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`log/ainfc|조회` · `log/ainfc|엑셀로` · `log/ainfc|리포트` · `log/ainfc|삭제자료표시`

## LOG관리 › AIFACE LOG  `#/log/aiface` · 프로그램ID 034

안면인식 · 라이브니스 · 여권사진 대조 로그

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `lv` | 결과 | enum (SUCCESS, WARN, FAIL) | ✓ |  |
| `who` | 거래처 | string (코드) |  |  |
| `ref` | 승인번호 | string (코드) |  |  |
| `req` | 요청 | string |  |  |
| `code` | 코드 | string (코드) |  |  |
| `ms` | 소요(ms) | number |  |  |
| `ip` | IP | string (코드) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/log-aiface?from&to&q&qf&lv&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/log-aiface/{id}` | 상세 |
| GET | `/api/v1/log-aiface/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`log/aiface|조회` · `log/aiface|엑셀로` · `log/aiface|리포트` · `log/aiface|삭제자료표시`

## LOG관리 › PK인증 LOG  `#/log/pk` · 프로그램ID 035

PKD · CSCA 전자서명 검증 로그

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `lv` | 결과 | enum (SUCCESS, WARN, FAIL) | ✓ |  |
| `who` | 거래처 | string (코드) |  |  |
| `ref` | 승인번호 | string (코드) |  |  |
| `req` | 요청 | string |  |  |
| `code` | 코드 | string (코드) |  |  |
| `ms` | 소요(ms) | number |  |  |
| `ip` | IP | string (코드) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/log-pk?from&to&q&qf&lv&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/log-pk/{id}` | 상세 |
| GET | `/api/v1/log-pk/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`log/pk|조회` · `log/pk|엑셀로` · `log/pk|리포트` · `log/pk|삭제자료표시`

## LOG관리 › 대한민국여권인증 LOG  `#/log/korpass` · 프로그램ID 036

대한민국 정부 여권 인증 요청/응답 로그

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `lv` | 결과 | enum (SUCCESS, WARN, FAIL) | ✓ |  |
| `who` | 거래처 | string (코드) |  |  |
| `ref` | 승인번호 | string (코드) |  |  |
| `req` | 요청 | string |  |  |
| `code` | 코드 | string (코드) |  |  |
| `ms` | 소요(ms) | number |  |  |
| `ip` | IP | string (코드) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/log-korpass?from&to&q&qf&lv&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/log-korpass/{id}` | 상세 |
| GET | `/api/v1/log-korpass/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`log/korpass|조회` · `log/korpass|엑셀로` · `log/korpass|리포트` · `log/korpass|삭제자료표시`

## LOG관리 › 대한민국체류자격 LOG  `#/log/korvisa` · 프로그램ID 037

대한민국 정부 체류자격 인증 요청/응답 로그

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `lv` | 결과 | enum (SUCCESS, WARN, FAIL) | ✓ |  |
| `who` | 거래처 | string (코드) |  |  |
| `ref` | 승인번호 | string (코드) |  |  |
| `req` | 요청 | string |  |  |
| `code` | 코드 | string (코드) |  |  |
| `ms` | 소요(ms) | number |  |  |
| `ip` | IP | string (코드) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/log-korvisa?from&to&q&qf&lv&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/log-korvisa/{id}` | 상세 |
| GET | `/api/v1/log-korvisa/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`log/korvisa|조회` · `log/korvisa|엑셀로` · `log/korvisa|리포트` · `log/korvisa|삭제자료표시`

## 기초코드관리 › 국가코드관리  `#/code/nation` · 프로그램ID 038

국가코드(한글 · 영문 · 숫자 · 2자리 · 3자리)와 대사관번호 · 공관전화번호를 관리합니다. KT 해외 현지 여권 후불사업 15개국 포함.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `ko` | 한글국가명 | string |  | 대표키 |
| `en` | 영문국가명 | string |  |  |
| `num` | 숫자국가코드 | string (코드) |  |  |
| `iso2` | 2자리국가코드 | string (코드) |  |  |
| `iso3` | 3자리국가코드 | string (코드) |  |  |
| `call` | 대사관번호 | string (코드) |  |  |
| `ord` | 자료순서 | number |  |  |
| `memo` | 비고 | string |  |  |
| `st` | 상태 | string (정상, 중지) | ✓ |  |
| `by` | 자료등록자 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/code-nation?from&to&q&qf&st&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/code-nation/{id}` | 상세 |
| POST | `/api/v1/code-nation` | 등록 |
| PUT | `/api/v1/code-nation/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/code-nation/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/code-nation/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`code/nation|조회` · `code/nation|등록` · `code/nation|수정` · `code/nation|삭제` · `code/nation|엑셀로` · `code/nation|리포트` · `code/nation|삭제자료표시`

## 기초코드관리 › 은행코드관리  `#/code/bank` · 프로그램ID 039

은행코드 · 금융결제원 연합회코드를 관리합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `code` | 은행코드 | number |  | 대표키 |
| `ko` | 은행명 | string |  |  |
| `en` | 영문은행명 | string |  |  |
| `kftc` | 연합회코드 | string (코드) |  |  |
| `st` | 상태 | string (정상, 중지) | ✓ |  |
| `natN` | 국가명 | string |  |  |
| `by` | 자료등록자 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/code-bank?from&to&q&qf&st&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/code-bank/{id}` | 상세 |
| POST | `/api/v1/code-bank` | 등록 |
| PUT | `/api/v1/code-bank/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/code-bank/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/code-bank/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`code/bank|조회` · `code/bank|등록` · `code/bank|수정` · `code/bank|삭제` · `code/bank|엑셀로` · `code/bank|리포트` · `code/bank|삭제자료표시`

## 기초코드관리 › 카드사코드관리  `#/code/cardco` · 프로그램ID 040

카드사 코드 · VAN 코드를 관리합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `code` | 카드사코드 | string (코드) |  | 대표키 |
| `ko` | 카드사명 | string |  |  |
| `en` | 영문카드사명 | string |  |  |
| `van` | VAN 코드 | string (코드) |  |  |
| `st` | 상태 | string (정상, 중지) | ✓ |  |
| `natN` | 국가명 | string |  |  |
| `by` | 자료등록자 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/code-cardco?from&to&q&qf&st&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/code-cardco/{id}` | 상세 |
| POST | `/api/v1/code-cardco` | 등록 |
| PUT | `/api/v1/code-cardco/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/code-cardco/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/code-cardco/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`code/cardco|조회` · `code/cardco|등록` · `code/cardco|수정` · `code/cardco|삭제` · `code/cardco|엑셀로` · `code/cardco|리포트` · `code/cardco|삭제자료표시`

## 기초코드관리 › 우편번호관리  `#/code/zip` · 프로그램ID 041

주소타입(지번 · 도로명)별 우편번호와 지역번호를 관리합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `ztype` | 주소타입 | string (지번주소, 도로명주소) | ✓ |  |
| `zip` | 우편번호 | string (코드) |  | 대표키 |
| `addr` | 주소 | string |  |  |
| `area` | 지역번호 | string (코드) |  |  |
| `st` | 상태 | string (정상, 중지) | ✓ |  |
| `by` | 자료등록자 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/code-zip?from&to&q&qf&ztype&st&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/code-zip/{id}` | 상세 |
| POST | `/api/v1/code-zip` | 등록 |
| PUT | `/api/v1/code-zip/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/code-zip/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/code-zip/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`code/zip|조회` · `code/zip|등록` · `code/zip|수정` · `code/zip|삭제` · `code/zip|엑셀로` · `code/zip|리포트` · `code/zip|삭제자료표시`

## 기초코드관리 › 가상계좌번호채번관리  `#/code/vacct` · 프로그램ID 042

은행별로 미리 채번한 가상계좌번호 목록입니다. 계좌가 발급되면 상태가 바뀝니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `bank` | 발급은행 | string (신한, 국민, 기업, 우리, 하나) | ✓ |  |
| `vacct` | 가상계좌번호 | string (코드) |  | 대표키 |
| `inFee` | 입금수수료 | number |  |  |
| `outFee` | 출금수수료 | number |  |  |
| `issue` | 상태 | string (대기, 발급) | ✓ |  |
| `natN` | 국가명 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/code-vacct?from&to&q&qf&issue&bank&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/code-vacct/{id}` | 상세 |
| GET | `/api/v1/code-vacct/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`code/vacct|조회` · `code/vacct|엑셀로` · `code/vacct|리포트` · `code/vacct|삭제자료표시`

## 기초코드관리 › 가상카드번호채번관리  `#/code/vcard` · 프로그램ID 043

카드종류별로 미리 채번한 가상카드번호 목록입니다. 「생성」으로 새 번호를 만듭니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `ctype` | 카드종류 | string (민카드-선불, 일반카드, KB국민카드-선불실물) | ✓ |  |
| `cardNo` | 카드번호 | string (코드) |  | 대표키 |
| `barcode` | 바코드 | string (코드) |  |  |
| `issue` | 상태 | string (대기, 발급) | ✓ |  |
| `bank` | 은행명 | string |  |  |
| `linkAcct` | 연결계좌번호 | string (코드) |  |  |
| `natN` | 국가명 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/code-vcard?from&to&q&qf&ctype&issue&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/code-vcard/{id}` | 상세 |
| GET | `/api/v1/code-vcard/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/code-vcard/{id}/actions/%EC%83%9D%EC%84%B1` | 기능버튼 「생성」 |

### 권한 코드 (역할별로 부여)

`code/vcard|조회` · `code/vcard|엑셀로` · `code/vcard|리포트` · `code/vcard|삭제자료표시` · `code/vcard|생성`

## 발급관리 › 카드번호발급관리  `#/issue/card` · 프로그램ID 044

발급한 카드(회원 · 가맹점)의 번호 · 잔액 · 상태를 관리합니다. 「발급」으로 새 카드를 발급하고 「사용중지」로 막습니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `dt` | 발급날짜 | date / datetime | ✓ |  |
| `ctype` | 카드종류 | string (일반카드, 민카드-선불, KB국민카드-선불실물) | ✓ |  |
| `cardM` | 카드번호 | string (코드) |  | 대표키 |
| `bal` | 카드잔액 | number |  |  |
| `mtype` | 회원타입 | string (가맹점, 회원, 비회원) | ✓ |  |
| `mid` | 회원아이디 | string (코드) |  |  |
| `mname` | 회원명 | string |  |  |
| `rel` | 관련거래번호 | string (코드) |  |  |
| `itype` | 카드발급타입 | string |  |  |
| `st` | 상태 | string (정상, 사용중지) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/issue-card?from&to&q&qf&ctype&mtype&st&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/issue-card/{id}` | 상세 |
| GET | `/api/v1/issue-card/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/issue-card/{id}/actions/%EB%B0%9C%EA%B8%89` | 기능버튼 「발급」 |
| POST | `/api/v1/issue-card/{id}/actions/%EC%82%AC%EC%9A%A9%EC%A4%91%EC%A7%80` | 기능버튼 「사용중지」 |
| POST | `/api/v1/issue-card/{id}/actions/%EC%B9%B4%EB%93%9C%EA%B1%B0%EB%9E%98%ED%98%84%ED%99%A9` | 기능버튼 「카드거래현황」 |
| POST | `/api/v1/issue-card/{id}/actions/%EB%A9%94%EB%AA%A8%EB%93%B1%EB%A1%9D` | 기능버튼 「메모등록」 |

### 권한 코드 (역할별로 부여)

`issue/card|조회` · `issue/card|엑셀로` · `issue/card|리포트` · `issue/card|삭제자료표시` · `issue/card|발급` · `issue/card|사용중지` · `issue/card|카드거래현황` · `issue/card|메모등록`

## 발급관리 › 계좌번호발급관리  `#/issue/acct` · 프로그램ID 045

발급한 가상계좌의 잔액 · 이체 한도 · 수수료 · 상태를 관리합니다. 「발급」은 가상계좌번호채번관리의 대기 번호를 씁니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `dt` | 발급날짜 | date / datetime | ✓ |  |
| `bank` | 거래은행 | string |  |  |
| `acctF` | 계좌번호 | string (코드) |  | 대표키 |
| `bal` | 계좌잔액 | number |  |  |
| `minT` | 최소이체금액 | number |  |  |
| `maxT` | 최대이체금액 | number |  |  |
| `fee` | 이체수수료 | number |  |  |
| `limitT` | 거래제한 | string (0=비제한, 1=한도제한) | ✓ |  |
| `mtype` | 회원타입 | string (가맹점, 회원, 비회원) | ✓ |  |
| `mid` | 회원아이디 | string (코드) |  |  |
| `mname` | 회원명 | string |  |  |
| `st` | 상태 | string (정상, 사용중지) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/issue-acct?from&to&q&qf&limitT&mtype&st&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/issue-acct/{id}` | 상세 |
| GET | `/api/v1/issue-acct/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/issue-acct/{id}/actions/%EB%B0%9C%EA%B8%89` | 기능버튼 「발급」 |
| POST | `/api/v1/issue-acct/{id}/actions/%EC%82%AC%EC%9A%A9%EC%A4%91%EC%A7%80` | 기능버튼 「사용중지」 |
| POST | `/api/v1/issue-acct/{id}/actions/%EA%B3%84%EC%A2%8C%EA%B1%B0%EB%9E%98%ED%98%84%ED%99%A9` | 기능버튼 「계좌거래현황」 |
| POST | `/api/v1/issue-acct/{id}/actions/%EA%B3%84%EC%A2%8C%EA%B1%B0%EB%9E%98%ED%95%9C%EB%8F%84%EC%88%98%EC%A0%95` | 기능버튼 「계좌거래한도수정」 |
| POST | `/api/v1/issue-acct/{id}/actions/%EB%A9%94%EB%AA%A8%EB%93%B1%EB%A1%9D` | 기능버튼 「메모등록」 |

### 권한 코드 (역할별로 부여)

`issue/acct|조회` · `issue/acct|엑셀로` · `issue/acct|리포트` · `issue/acct|삭제자료표시` · `issue/acct|발급` · `issue/acct|사용중지` · `issue/acct|계좌거래현황` · `issue/acct|계좌거래한도수정` · `issue/acct|메모등록`

## 고객센터 › 상담관리  `#/cs/comment` · 프로그램ID 046

회원 · 가맹점 상담(1:1 문의) 내용과 처리 결과를 관리합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `dt` | 등록날짜 | date / datetime | ✓ |  |
| `mid` | 상담회원아이디 | string (코드) |  | 대표키 |
| `mname` | 상담회원명 | string |  |  |
| `cid` | 상담자아이디 | string (코드) |  |  |
| `cname` | 상담자명 | string |  |  |
| `ctype` | 상담종류 | string (1:1문의, 인증, 카드, 계좌, 요금, 기타) | ✓ |  |
| `title` | 상담제목 | string |  |  |
| `done` | 처리 | string (미처리, 처리완료) | ✓ |  |
| `st` | 상태 | string (정상, 중지) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-comment?from&to&q&qf&ctype&done&mtype&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-comment/{id}` | 상세 |
| POST | `/api/v1/cs-comment` | 등록 |
| DELETE | `/api/v1/cs-comment/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/cs-comment/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/cs-comment/{id}/actions/%EC%B2%98%EB%A6%AC%EA%B2%B0%EA%B3%BC%EB%93%B1%EB%A1%9D` | 기능버튼 「처리결과등록」 |

### 권한 코드 (역할별로 부여)

`cs/comment|조회` · `cs/comment|등록` · `cs/comment|삭제` · `cs/comment|엑셀로` · `cs/comment|리포트` · `cs/comment|삭제자료표시` · `cs/comment|처리결과등록`

## 고객센터 › 댓글관리  `#/cs/reply` · 프로그램ID 047

가맹점 상품에 회원이 남긴 댓글(후기 · 문의 · 신고)을 확인하고, 문제 있는 댓글을 삭제합니다. 댓글은 회원이 작성하므로 여기서 등록 · 수정하지 않습니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `dt` | 등록날짜 | date / datetime | ✓ |  |
| `uid` | 댓글자아이디 | string (코드) |  | 대표키 |
| `uname` | 댓글자명 | string |  |  |
| `sid` | 가맹점아이디 | string (코드) |  |  |
| `sname` | 가맹점명 | string |  |  |
| `pcode` | 상품코드 | string (코드) |  |  |
| `pname` | 상품명 | string |  |  |
| `kind` | 댓글종류 | string (상품후기, 상품문의, 신고) | ✓ |  |
| `txt` | 댓글 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-reply?from&to&q&qf&kind&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-reply/{id}` | 상세 |
| DELETE | `/api/v1/cs-reply/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/cs-reply/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`cs/reply|조회` · `cs/reply|삭제` · `cs/reply|엑셀로` · `cs/reply|리포트` · `cs/reply|삭제자료표시`

## 고객센터 › 챗봇관리  `#/cs/bot` · 프로그램ID 048

인증 화면 챗봇 FAQ 시나리오

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `intent` | 인텐트 | string (코드) |  | 대표키 |
| `q` | 대표 질문 | string |  |  |
| `a` | 답변 | string |  |  |
| `lang` | 언어 | string (코드) (KOR, USA, VNM, IDN) |  |  |
| `use` | 사용 | enum (사용, 미사용) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-bot?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-bot/{id}` | 상세 |
| POST | `/api/v1/cs-bot` | 등록 |
| PUT | `/api/v1/cs-bot/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/cs-bot/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/cs-bot/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`cs/bot|조회` · `cs/bot|등록` · `cs/bot|수정` · `cs/bot|삭제` · `cs/bot|엑셀로` · `cs/bot|리포트` · `cs/bot|삭제자료표시`

## 고객센터 › SMS전송관리  `#/cs/sms` · 프로그램ID 049

회원 · 가맹점에 보낸 SMS · LMS · MMS 전송 내역. 회원을 골라 보내거나 번호를 직접 입력해 보냅니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `mtype` | 메세지타입 | string (SMS, LMS, MMS) | ✓ |  |
| `dt` | 등록날짜 | date / datetime | ✓ |  |
| `tel` | 전화번호 | string (코드) |  | 대표키 |
| `uname` | 수신자정보 | string |  |  |
| `res` | 전송결과 | string (성공, 실패, 예약) | ✓ |  |
| `title` | 메세지제목 | string |  |  |
| `grade` | 직급 | string (SA, 회사, 가맹점, 피커, 여행사) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-sms?from&to&q&qf&mtype&res&grade&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-sms/{id}` | 상세 |
| GET | `/api/v1/cs-sms/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/cs-sms/{id}/actions/%ED%9A%8C%EC%9B%90%EC%84%A0%ED%83%9D%20SMS%EB%A9%94%EC%84%B8%EC%A7%80%EB%B3%B4%EB%82%B4%EA%B8%B0` | 기능버튼 「회원선택 SMS메세지보내기」 |
| POST | `/api/v1/cs-sms/{id}/actions/%EC%88%98%EB%8F%99%EC%9E%85%EB%A0%A5%20SMS%EB%A9%94%EC%84%B8%EC%A7%80%EB%B3%B4%EB%82%B4%EA%B8%B0` | 기능버튼 「수동입력 SMS메세지보내기」 |

### 권한 코드 (역할별로 부여)

`cs/sms|조회` · `cs/sms|엑셀로` · `cs/sms|리포트` · `cs/sms|삭제자료표시` · `cs/sms|회원선택 SMS메세지보내기` · `cs/sms|수동입력 SMS메세지보내기`

## 고객센터 › PUSH전송관리  `#/cs/push` · 프로그램ID 050

앱을 설치한 기기에 보낸 PUSH 메시지 내역. 「PUSH메세지보내기」로 기기를 골라 보냅니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `dt` | 등록날짜 | date / datetime | ✓ |  |
| `nat` | 국가 | string (코드) |  |  |
| `apk` | APK타입 | string (서울페이 IOS APP, 서울페이 AOS APP, Android TRIP-PASS (신버전), Android-LORDPOS 스마트폰) | ✓ |  |
| `mtype` | 메세지타입 | string (결제요청=1001, 공지사항=1002, 이벤트=1003, 인증안내=1004) | ✓ |  |
| `uid` | 회원아이디 | string (코드) |  | 대표키 |
| `uname` | 회원성명 | string |  |  |
| `tel` | 전송전화번호 | string (코드) |  |  |
| `title` | 제목 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-push?from&to&q&qf&apk&mtype&res&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-push/{id}` | 상세 |
| GET | `/api/v1/cs-push/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/cs-push/{id}/actions/PUSH%EB%A9%94%EC%84%B8%EC%A7%80%EB%B3%B4%EB%82%B4%EA%B8%B0` | 기능버튼 「PUSH메세지보내기」 |

### 권한 코드 (역할별로 부여)

`cs/push|조회` · `cs/push|엑셀로` · `cs/push|리포트` · `cs/push|삭제자료표시` · `cs/push|PUSH메세지보내기`

## 고객센터 › 공지사항관리  `#/cs/notice` · 프로그램ID 051

앱 서비스별 공지사항 게시물. 필수공지는 앱을 열 때 팝업으로 보입니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `dt` | 등록날짜 | date / datetime | ✓ |  |
| `ntype` | 공지타입 | string (필수공지, 일반공지, 이벤트, 점검) | ✓ |  |
| `svc` | APK서비스타입 | string (Android-Passport Mobile, Android TRIP-PASS, Android-LORDPOS, 서울페이 IOS APP, 서울페이 AOS APP) | ✓ |  |
| `title` | 공지제목 | string |  | 대표키 |
| `icon` | 공지아이콘 | string (코드) |  |  |
| `img` | 이미지공지파일 | string (코드) |  |  |
| `video` | 동영상공지주소 | string (코드) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-notice?from&to&q&qf&ntype&svc&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-notice/{id}` | 상세 |
| POST | `/api/v1/cs-notice` | 등록 |
| PUT | `/api/v1/cs-notice/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/cs-notice/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/cs-notice/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`cs/notice|조회` · `cs/notice|등록` · `cs/notice|수정` · `cs/notice|삭제` · `cs/notice|엑셀로` · `cs/notice|리포트` · `cs/notice|삭제자료표시`

## 고객센터 › 이용안내관리  `#/cs/guide` · 프로그램ID 052

앱 서비스별 이용안내(사용 방법 · 절차) 게시물. 앱의 「이용안내」 화면에 그대로 보입니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `dt` | 등록날짜 | date / datetime | ✓ |  |
| `svc` | APP서비스 명칭 | string (Android-Passport Mobile, Android TRIP-PASS, Android-LORDPOS, 서울페이 IOS APP, 서울페이 AOS APP) | ✓ |  |
| `title` | 이용안내제목 | string |  | 대표키 |
| `img` | 이미지파일 | string (코드) |  |  |
| `video` | 동영상주소 | string (코드) |  |  |
| `st` | 상태 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-guide?from&to&q&qf&svc&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-guide/{id}` | 상세 |
| POST | `/api/v1/cs-guide` | 등록 |
| PUT | `/api/v1/cs-guide/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/cs-guide/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/cs-guide/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`cs/guide|조회` · `cs/guide|등록` · `cs/guide|수정` · `cs/guide|삭제` · `cs/guide|엑셀로` · `cs/guide|리포트` · `cs/guide|삭제자료표시`

## 작업관리 › 직원관리  `#/work/staff` · 프로그램ID 053

SIGNION · 관리 회사 백오피스 직원 계정. 기본 · 세부 · 거래 · 기타정보와 직원별 서비스 권한을 관리합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `seq` | 전산코드 | number |  | 읽기전용(시스템) |
| `rg` | 등록날짜 | date / datetime | ✓ |  |
| `name` | 성명 | string |  |  |
| `uid` | 아이디 | string (코드) |  | 대표키 |
| `hp` | 핸드폰번호 | string (코드) |  |  |
| `grade` | 직급 | string (대표, 이사, 부장, 차장, 과장, 대리, 주임, 직원) | ✓ |  |
| `dept` | 부서 | string (운영팀, 연구소, CS, 영업팀, 기술연구소, 관리팀, 경영지원, 마케팅팀) | ✓ |  |
| `comp` | 소속회사 | string (코드) |  |  |
| `status` | 상태 | string (정상, 정지) | ✓ |  |
| `nat` | 국가명 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/work-staff?from&to&q&qf&dept&grade&status&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/work-staff/{id}` | 상세 |
| POST | `/api/v1/work-staff` | 등록 |
| PUT | `/api/v1/work-staff/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/work-staff/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/work-staff/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/work-staff/{id}/actions/%EC%A0%95%EC%A7%80` | 기능버튼 「정지」 |
| POST | `/api/v1/work-staff/{id}/actions/%EC%8B%A0%EB%B6%84%EC%A6%9D(%EC%9D%B4%EB%AF%B8%EC%A7%80)%EB%93%B1%EB%A1%9D` | 기능버튼 「신분증(이미지)등록」 |

### 권한 코드 (역할별로 부여)

`work/staff|조회` · `work/staff|등록` · `work/staff|수정` · `work/staff|삭제` · `work/staff|엑셀로` · `work/staff|리포트` · `work/staff|삭제자료표시` · `work/staff|정지` · `work/staff|신분증(이미지)등록`

## 작업관리 › 프로그램관리  `#/work/prog` · 프로그램ID 054

메뉴(대메뉴·소메뉴)와 기능버튼 권한 정의 — ProgList_ 테이블 (mainMenu · subMenu · funcBtn)

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `main` | 대메뉴 | string |  | 대표키 |
| `sub` | 소메뉴 | string |  |  |
| `fn` | 기능버튼 | string |  |  |
| `use` | 사용 | enum (사용, 미사용) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/work-prog?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/work-prog/{id}` | 상세 |
| PUT | `/api/v1/work-prog/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| GET | `/api/v1/work-prog/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`work/prog|조회` · `work/prog|수정`

## 작업관리 › 자료메모관리  `#/work/memo` · 프로그램ID 055

각 화면에서 등록한 메모 통합 조회

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `target` | 대상 | string (코드) |  | 대표키 |
| `menu` | 메뉴 | string |  |  |
| `memo` | 메모 | string |  |  |
| `who` | 작성자 | string |  |  |
| `dt` | 작성일 | date / datetime | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/work-memo?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/work-memo/{id}` | 상세 |
| POST | `/api/v1/work-memo` | 등록 |
| PUT | `/api/v1/work-memo/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/work-memo/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/work-memo/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`work/memo|조회` · `work/memo|등록` · `work/memo|수정` · `work/memo|삭제` · `work/memo|엑셀로` · `work/memo|리포트` · `work/memo|삭제자료표시`

## 작업관리 › 데이터수정LOG  `#/work/datalog` · 프로그램ID 056

관리자에 의한 데이터 변경 이력 (변경 전/후)

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `tbl` | 테이블 | string (코드) |  |  |
| `key` | 키 | string (코드) |  |  |
| `field` | 필드 | string (코드) |  |  |
| `before` | 변경 전 | string |  |  |
| `after` | 변경 후 | string |  |  |
| `who` | 작업자 | string (코드) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/work-datalog?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/work-datalog/{id}` | 상세 |
| GET | `/api/v1/work-datalog/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`work/datalog|조회` · `work/datalog|엑셀로` · `work/datalog|리포트` · `work/datalog|삭제자료표시`

## 작업관리 › 권한관리  `#/work/role` · 프로그램ID 057

전용 화면


### 권한 코드 (역할별로 부여)

`work/role|조회` · `work/role|수정`

## 작업관리 › 감사LOG  `#/work/audit` · 프로그램ID 058

누가 · 언제 · 어느 메뉴에서 · 무엇을 했는지 기록합니다 (등록 · 수정 · 삭제 · 기능버튼 · 출력 · 개인정보 열람 · 권한 변경 · 권한 없는 시도). 기록은 고치거나 지울 수 없습니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `dt` | 일시 | date / datetime | ✓ | 대표키 |
| `who` | 관리자 | string (코드) (signion.admin, lab.kim, cs.park, biz.jang, ops.choi) | ✓ |  |
| `role` | 역할 | string (SysAdmin, 운영 관리자, 상담원 (CS), 연구소 개발자, 조회 전용) | ✓ |  |
| `menu` | 메뉴 | string |  |  |
| `act` | 행위 | enum (등록, 수정, 삭제, 등록상태변경, 암호화KEY생성, 마스터Token생성, 비밀번호초기화, 메모등록, 아이디수정, 엑셀 다운로드, 리포트 출력, 개인정보 열람 …) | ✓ |  |
| `target` | 대상 | string (코드) |  |  |
| `detail` | 내용 | string |  |  |
| `ip` | IP | string (코드) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/work-audit?from&to&q&qf&act&who&role&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/work-audit/{id}` | 상세 |
| GET | `/api/v1/work-audit/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`work/audit|조회` · `work/audit|엑셀로` · `work/audit|리포트` · `work/audit|삭제자료표시`
