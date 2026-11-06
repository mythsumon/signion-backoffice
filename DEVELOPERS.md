# SIGNION BackOffice 개발 명세 (프로토타입 기준)

> **개발팀 안내** — 이 문서는 프로토타입(`index.html`)에서 자동으로 뽑은 화면별 명세입니다.
> - 프로토타입에서 오른쪽 위 메뉴 › **개발자 모드 켜기** → 각 화면 위쪽 「개발 정보」, 왼쪽 아래 「개발 명세」에서 같은 내용을 볼 수 있습니다.
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

거래처별 인증 단가·월 최소이용금액·관리자 사용료를 설정합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `pid` | 파트너ID | string (코드) |  | 대표키 |
| `name` | 거래처명 | string |  |  |
| `rtype` | 요금타입 | string (건당, 건당+월정액, 월정액, 테스트(무과금)) | ✓ |  |
| `unit` | 건당 단가 | number (원) |  |  |
| `min` | 월 최소이용금액 | number (원) |  |  |
| `admin` | 관리자 사용료(월) | number (원) |  |  |
| `from` | 적용 시작일 | date / datetime | ✓ |  |
| `memo` | 비고 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/partner-fee?from&to&q&qf&rtype&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/partner-fee/{id}` | 상세 |
| POST | `/api/v1/partner-fee` | 등록 |
| PUT | `/api/v1/partner-fee/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/partner-fee/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/partner-fee/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`partner/fee|조회` · `partner/fee|등록` · `partner/fee|수정` · `partner/fee|삭제` · `partner/fee|엑셀로` · `partner/fee|리포트` · `partner/fee|삭제자료표시`

## 인증거래처관리 › 접속정보관리  `#/partner/conn` · 프로그램ID 004

거래처별 접속 타입(APP ID · WEB URL · 서버 IP)과 응답 URL을 관리합니다. (_connectType)

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `pid` | 파트너ID | string (코드) |  | 대표키 |
| `ctype` | 접속타입 | string (AOS APP 접속 ID, IOS APP 접속 ID, WEB 접속 URL, 서버 접속 IP) | ✓ |  |
| `info` | 접속정보 | string (코드) |  |  |
| `resp` | 응답 URL | string (코드) |  |  |
| `ip` | 허용 IP | string (코드) |  |  |
| `rg` | 등록일 | date / datetime | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/partner-conn?from&to&q&qf&ctype&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/partner-conn/{id}` | 상세 |
| POST | `/api/v1/partner-conn` | 등록 |
| PUT | `/api/v1/partner-conn/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/partner-conn/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/partner-conn/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`partner/conn|조회` · `partner/conn|등록` · `partner/conn|수정` · `partner/conn|삭제` · `partner/conn|엑셀로` · `partner/conn|리포트` · `partner/conn|삭제자료표시`

## 인증거래처관리 › 사업자정보관리  `#/partner/biz` · 프로그램ID 005

거래처 사업자 등록 정보와 세금계산서 발행 정보를 관리합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `pid` | 파트너ID | string (코드) |  | 대표키 |
| `name` | 상호 | string |  |  |
| `biz` | 사업자등록번호 | string (코드) |  |  |
| `kind` | 업태 / 종목 | string |  |  |
| `addr` | 소재지 | string |  |  |
| `tax` | 세금계산서 이메일 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/partner-biz?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/partner-biz/{id}` | 상세 |
| POST | `/api/v1/partner-biz` | 등록 |
| PUT | `/api/v1/partner-biz/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/partner-biz/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/partner-biz/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`partner/biz|조회` · `partner/biz|등록` · `partner/biz|수정` · `partner/biz|삭제` · `partner/biz|엑셀로` · `partner/biz|리포트` · `partner/biz|삭제자료표시`

## 인증거래처관리 › 업체대표자관리  `#/partner/ceo` · 프로그램ID 006

거래처 대표자 정보를 관리합니다. 개인정보는 마스킹되어 표시됩니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `pid` | 파트너ID | string (코드) |  | 대표키 |
| `ceo` | 대표자명 | string |  |  |
| `tel` | 연락처 | string (코드) |  |  |
| `mail` | 이메일 | string |  |  |
| `rg` | 등록일 | date / datetime | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/partner-ceo?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/partner-ceo/{id}` | 상세 |
| POST | `/api/v1/partner-ceo` | 등록 |
| PUT | `/api/v1/partner-ceo/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/partner-ceo/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/partner-ceo/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`partner/ceo|조회` · `partner/ceo|등록` · `partner/ceo|수정` · `partner/ceo|삭제` · `partner/ceo|엑셀로` · `partner/ceo|리포트` · `partner/ceo|삭제자료표시`

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

KT 해외 통신서비스 과금 체계 (VAT 별도) — 과금체계 시트 기준.

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

kycTransMaster / kycTransDetail — 승인번호 단위의 KYC 인증 처리 내역과 모듈별 결과를 조회합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `no` | 승인번호 | string (코드) |  | 대표키 |
| `dt` | 인증일시 | date / datetime | ✓ |  |
| `partner` | 거래처 | string |  |  |
| `nat` | 국가 | string (ISO 국가코드) (KOR, IDN, VNM, NPL, MNG, UZB, KHM, MMR, LAO, LKA, BGD, CHN …) | ✓ |  |
| `name` | 여권 성명 | string · 개인정보(마스킹) |  | 개인정보 |
| `pass` | 여권번호 | string (코드) |  | 개인정보 |
| `res` | 인증 모듈 | array (모듈별 결과) |  |  |
| `status` | 상태 | enum (완료, 실패, 진행중) | ✓ |  |
| `err` | 오류코드 | string (코드) |  |  |
| `sec` | 처리(초) | number |  |  |
| `fee` | 수수료 | number (원) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/trans?from&to&q&qf&status&pid&nat&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/trans/{id}` | 상세 |
| GET | `/api/v1/trans/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/trans/{id}/actions/%EC%9D%B8%EC%A6%9D%EC%88%98%EC%88%98%EB%A3%8C%EA%B3%84%EC%82%B0` | 기능버튼 「인증수수료계산」 |
| POST | `/api/v1/trans/{id}/actions/%EA%B0%9C%EC%9D%B8%EC%A0%95%EB%B3%B4%20%EB%B3%B4%EA%B8%B0` | 기능버튼 「개인정보 보기」 |

### 권한 코드 (역할별로 부여)

`trans|조회` · `trans|엑셀로` · `trans|리포트` · `trans|삭제자료표시` · `trans|인증수수료계산` · `trans|개인정보 보기`

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

## PKD관리  `#/pkd` · 프로그램ID 013

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

## 발행토큰관리  `#/token` · 프로그램ID 014

거래처별 masterToken / connectToken 발행 이력을 관리합니다. 모든 통신은 E2E 암호화 및 토큰 정책으로 운영됩니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `pid` | 파트너ID | string (코드) |  | 대표키 |
| `uid` | 사용자ID | string (코드) |  |  |
| `ttype` | 토큰종류 | string |  |  |
| `master` | masterToken | string (코드) |  |  |
| `mt` | master 생성 | date / datetime | ✓ |  |
| `ct` | connect 생성 | date / datetime |  |  |
| `ip` | 접속IP | string (코드) |  |  |
| `status` | 상태 | enum (활성, 만료됨, 중지) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/token?from&to&q&qf&status&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/token/{id}` | 상세 |
| DELETE | `/api/v1/token/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/token/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/token/{id}/actions/%EB%A7%88%EC%8A%A4%ED%84%B0Token%EB%B0%9C%EA%B8%89` | 기능버튼 「마스터Token발급」 |

### 권한 코드 (역할별로 부여)

`token|조회` · `token|삭제` · `token|엑셀로` · `token|리포트` · `token|삭제자료표시` · `token|마스터Token발급`

## 암호화KEY관리  `#/crypto` · 프로그램ID 015

AES-CBC 256bit · masterKey / IvKey. 본사 통신용 KEY와 거래처별 데이터·통신용 KEY를 분리하여 관리합니다.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `pid` | 파트너ID | string (코드) |  | 대표키 |
| `scope` | 용도 | string |  |  |
| `ctype` | 암호화 방식 | string (코드) |  |  |
| `mkey` | masterKey | string (코드) |  |  |
| `iv` | IvKey | string (코드) |  |  |
| `rg` | 발급일 | date / datetime | ✓ |  |
| `status` | 상태 | enum (사용, 미사용, 중지) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/crypto?from&to&q&qf&status&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/crypto/{id}` | 상세 |
| DELETE | `/api/v1/crypto/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/crypto/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |
| POST | `/api/v1/crypto/{id}/actions/%EC%95%94%ED%98%B8%ED%99%94KEY%EB%B0%9C%EA%B8%89` | 기능버튼 「암호화KEY발급」 |

### 권한 코드 (역할별로 부여)

`crypto|조회` · `crypto|삭제` · `crypto|엑셀로` · `crypto|리포트` · `crypto|삭제자료표시` · `crypto|암호화KEY발급`

## 메시지코드관리  `#/msg` · 프로그램ID 016

프론트·API·AI 모듈에서 반환하는 메시지 코드를 다국어(KOR·USA·CHN·JPN·VNM·THA·MNG·TWN)로 관리합니다. (_msgCode 888건)

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `prog` | 서비스 프로그램 | string (프론트WEB, API-WEB, AI-OCR, 안면인식, 안티스푸핑, 전자여권인식, globalekycapi, PKD 인증, SystemError) | ✓ |  |
| `code` | 메시지코드 | string (코드) |  | 대표키 |
| `lang` | 언어 | string (코드) (KOR, USA, CHN, JPN, VNM, THA, MNG, TWN) | ✓ |  |
| `title` | 제목 | string |  |  |
| `ret` | 사용자 메시지 | string |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/msg?from&to&q&qf&prog&lang&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/msg/{id}` | 상세 |
| POST | `/api/v1/msg` | 등록 |
| PUT | `/api/v1/msg/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/msg/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/msg/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`msg|조회` · `msg|등록` · `msg|수정` · `msg|삭제` · `msg|엑셀로` · `msg|리포트` · `msg|삭제자료표시`

## LOG관리 › 서버접속 LOG  `#/log/server` · 프로그램ID 017

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

## LOG관리 › API 요청 LOG  `#/log/api` · 프로그램ID 018

globalekycapi 요청/응답 이력. 백오피스도 DB에 직접 접속하지 않고 API를 경유합니다.

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
| GET | `/api/v1/log-api?from&to&q&qf&lv&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/log-api/{id}` | 상세 |
| GET | `/api/v1/log-api/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`log/api|조회` · `log/api|엑셀로` · `log/api|리포트` · `log/api|삭제자료표시`

## LOG관리 › DB 요청/응답 LOG  `#/log/db` · 프로그램ID 019

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

## LOG관리 › AIOCR LOG  `#/log/aiocr` · 프로그램ID 020

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

## LOG관리 › AINFC LOG  `#/log/ainfc` · 프로그램ID 021

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

## LOG관리 › AIFACE LOG  `#/log/aiface` · 프로그램ID 022

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

## LOG관리 › PK인증 LOG  `#/log/pk` · 프로그램ID 023

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

## LOG관리 › 대한민국여권인증 LOG  `#/log/korpass` · 프로그램ID 024

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

## LOG관리 › 대한민국체류자격 LOG  `#/log/korvisa` · 프로그램ID 025

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

## 기초코드관리 › 국가코드관리  `#/code/nation` · 프로그램ID 026

ISO 3166 국가코드 — KT 해외 현지 여권 후불사업 진행 15개국 포함.

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `iso3` | ISO3 | string (코드) |  | 대표키 |
| `iso2` | ISO2 | string (코드) |  |  |
| `ko` | 국가명 | string |  |  |
| `en` | 영문명 | string |  |  |
| `tel` | 국가번호 | string (코드) |  |  |
| `use` | 사용 | enum (사용, 미사용) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/code-nation?from&to&q&qf&use&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/code-nation/{id}` | 상세 |
| POST | `/api/v1/code-nation` | 등록 |
| PUT | `/api/v1/code-nation/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/code-nation/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/code-nation/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`code/nation|조회` · `code/nation|등록` · `code/nation|수정` · `code/nation|삭제` · `code/nation|엑셀로` · `code/nation|리포트` · `code/nation|삭제자료표시`

## 기초코드관리 › 은행코드관리  `#/code/bank` · 프로그램ID 027

금융결제원 은행코드

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `code` | 코드 | string (코드) |  | 대표키 |
| `name` | 은행명 | string |  |  |
| `swift` | SWIFT | string (코드) |  |  |
| `use` | 사용 | enum (사용, 미사용) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/code-bank?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/code-bank/{id}` | 상세 |
| POST | `/api/v1/code-bank` | 등록 |
| PUT | `/api/v1/code-bank/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/code-bank/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/code-bank/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`code/bank|조회` · `code/bank|등록` · `code/bank|수정` · `code/bank|삭제` · `code/bank|엑셀로` · `code/bank|리포트` · `code/bank|삭제자료표시`

## 기초코드관리 › 카드사코드관리  `#/code/cardco` · 프로그램ID 028

신용카드사 코드

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `code` | 코드 | string (코드) |  | 대표키 |
| `name` | 카드사 | string |  |  |
| `van` | VAN 코드 | string (코드) |  |  |
| `use` | 사용 | enum (사용, 미사용) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/code-cardco?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/code-cardco/{id}` | 상세 |
| POST | `/api/v1/code-cardco` | 등록 |
| PUT | `/api/v1/code-cardco/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/code-cardco/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/code-cardco/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`code/cardco|조회` · `code/cardco|등록` · `code/cardco|수정` · `code/cardco|삭제` · `code/cardco|엑셀로` · `code/cardco|리포트` · `code/cardco|삭제자료표시`

## 기초코드관리 › 우편번호관리  `#/code/zip` · 프로그램ID 029

도로명주소 우편번호 (ZipCode)

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `zip` | 우편번호 | string (코드) |  | 대표키 |
| `sido` | 시도 | string |  |  |
| `sgg` | 시군구 | string |  |  |
| `road` | 도로명주소 | string |  |  |
| `use` | 사용 | enum (사용, 미사용) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/code-zip?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/code-zip/{id}` | 상세 |
| POST | `/api/v1/code-zip` | 등록 |
| PUT | `/api/v1/code-zip/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/code-zip/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/code-zip/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`code/zip|조회` · `code/zip|등록` · `code/zip|수정` · `code/zip|삭제` · `code/zip|엑셀로` · `code/zip|리포트` · `code/zip|삭제자료표시`

## 기초코드관리 › 가상계좌번호채번관리  `#/code/vacct` · 프로그램ID 030

은행별 가상계좌번호 채번 범위

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `bank` | 은행 | string |  | 대표키 |
| `range` | 채번 범위 | string (코드) |  |  |
| `used` | 사용 | number |  |  |
| `remain` | 잔여 | number |  |  |
| `status` | 상태 | enum (사용, 만료임박, 중지) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/code-vacct?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/code-vacct/{id}` | 상세 |
| POST | `/api/v1/code-vacct` | 등록 |
| PUT | `/api/v1/code-vacct/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/code-vacct/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/code-vacct/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`code/vacct|조회` · `code/vacct|등록` · `code/vacct|수정` · `code/vacct|삭제` · `code/vacct|엑셀로` · `code/vacct|리포트` · `code/vacct|삭제자료표시`

## 기초코드관리 › 가상카드번호채번관리  `#/code/vcard` · 프로그램ID 031

BIN별 가상카드번호 채번 범위

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `bin` | BIN | string (코드) |  | 대표키 |
| `range` | 채번 범위 | string (코드) |  |  |
| `used` | 사용 | number |  |  |
| `remain` | 잔여 | number |  |  |
| `status` | 상태 | enum (사용, 미사용, 중지) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/code-vcard?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/code-vcard/{id}` | 상세 |
| POST | `/api/v1/code-vcard` | 등록 |
| PUT | `/api/v1/code-vcard/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/code-vcard/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/code-vcard/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`code/vcard|조회` · `code/vcard|등록` · `code/vcard|수정` · `code/vcard|삭제` · `code/vcard|엑셀로` · `code/vcard|리포트` · `code/vcard|삭제자료표시`

## 발급관리 › 카드번호발급관리  `#/issue/card` · 프로그램ID 032

발급된 카드번호 이력

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `no` | 카드번호 | string (코드) |  | 대표키 |
| `holder` | 소지자 | string |  |  |
| `pid` | 거래처 | string (코드) |  |  |
| `dt` | 발급일 | date / datetime | ✓ |  |
| `status` | 상태 | enum (발급완료, 회수, 중지) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/issue-card?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/issue-card/{id}` | 상세 |
| POST | `/api/v1/issue-card` | 등록 |
| PUT | `/api/v1/issue-card/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/issue-card/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/issue-card/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`issue/card|조회` · `issue/card|등록` · `issue/card|수정` · `issue/card|삭제` · `issue/card|엑셀로` · `issue/card|리포트` · `issue/card|삭제자료표시`

## 발급관리 › 계좌번호발급관리  `#/issue/acct` · 프로그램ID 033

발급된 가상계좌 이력

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `bank` | 은행 | string |  | 대표키 |
| `no` | 계좌번호 | string (코드) |  |  |
| `holder` | 예금주 | string |  |  |
| `pid` | 거래처 | string (코드) |  |  |
| `dt` | 발급일 | date / datetime | ✓ |  |
| `status` | 상태 | enum (발급완료, 회수, 중지) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/issue-acct?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/issue-acct/{id}` | 상세 |
| POST | `/api/v1/issue-acct` | 등록 |
| PUT | `/api/v1/issue-acct/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/issue-acct/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/issue-acct/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`issue/acct|조회` · `issue/acct|등록` · `issue/acct|수정` · `issue/acct|삭제` · `issue/acct|엑셀로` · `issue/acct|리포트` · `issue/acct|삭제자료표시`

## 고객센터 › 상단관리  `#/cs/top` · 프로그램ID 034

관리자/서비스 화면 상단 배너·공지 띠 노출 관리

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `title` | 제목 | string |  | 대표키 |
| `pos` | 노출 위치 | string |  |  |
| `period` | 노출 기간 | string |  |  |
| `use` | 상태 | enum (게시, 숨김) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-top?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-top/{id}` | 상세 |
| POST | `/api/v1/cs-top` | 등록 |
| PUT | `/api/v1/cs-top/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/cs-top/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/cs-top/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`cs/top|조회` · `cs/top|등록` · `cs/top|수정` · `cs/top|삭제` · `cs/top|엑셀로` · `cs/top|리포트` · `cs/top|삭제자료표시`

## 고객센터 › 댓글관리  `#/cs/comment` · 프로그램ID 035

공지/이용안내 댓글 관리

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `target` | 게시글 | string |  | 대표키 |
| `who` | 작성자 | string |  |  |
| `txt` | 내용 | string |  |  |
| `dt` | 작성일 | date / datetime | ✓ |  |
| `status` | 상태 | enum (답변완료, 미답변, 숨김) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-comment?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-comment/{id}` | 상세 |
| POST | `/api/v1/cs-comment` | 등록 |
| PUT | `/api/v1/cs-comment/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/cs-comment/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/cs-comment/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`cs/comment|조회` · `cs/comment|등록` · `cs/comment|수정` · `cs/comment|삭제` · `cs/comment|엑셀로` · `cs/comment|리포트` · `cs/comment|삭제자료표시`

## 고객센터 › 챗봇관리  `#/cs/bot` · 프로그램ID 036

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

## 고객센터 › SMS전송관리  `#/cs/sms` · 프로그램ID 037

관리자 2FA 인증번호 · 안내 문자 발송 이력

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `to` | 수신번호 | string (코드) |  | 대표키 |
| `tpl` | 템플릿 | string |  |  |
| `txt` | 내용 | string |  |  |
| `dt` | 발송일시 | date / datetime | ✓ |  |
| `status` | 결과 | enum (발송완료, 발송실패, 예약) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-sms?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-sms/{id}` | 상세 |
| POST | `/api/v1/cs-sms` | 등록 |
| PUT | `/api/v1/cs-sms/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/cs-sms/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/cs-sms/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`cs/sms|조회` · `cs/sms|등록` · `cs/sms|수정` · `cs/sms|삭제` · `cs/sms|엑셀로` · `cs/sms|리포트` · `cs/sms|삭제자료표시`

## 고객센터 › PUSH전송관리  `#/cs/push` · 프로그램ID 038

APP PUSH 발송 관리

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `target` | 대상 | string |  | 대표키 |
| `title` | 제목 | string |  |  |
| `dt` | 발송일시 | date / datetime | ✓ |  |
| `open` | 오픈율 | number |  |  |
| `status` | 상태 | enum (발송완료, 예약, 발송실패) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-push?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-push/{id}` | 상세 |
| POST | `/api/v1/cs-push` | 등록 |
| PUT | `/api/v1/cs-push/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/cs-push/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/cs-push/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`cs/push|조회` · `cs/push|등록` · `cs/push|수정` · `cs/push|삭제` · `cs/push|엑셀로` · `cs/push|리포트` · `cs/push|삭제자료표시`

## 고객센터 › 공지사항관리  `#/cs/notice` · 프로그램ID 039

관리자 공지사항

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `title` | 제목 | string |  | 대표키 |
| `target` | 대상 | string |  |  |
| `who` | 작성자 | string |  |  |
| `dt` | 등록일 | date / datetime | ✓ |  |
| `views` | 조회 | number |  |  |
| `use` | 상태 | enum (게시, 숨김) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-notice?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-notice/{id}` | 상세 |
| POST | `/api/v1/cs-notice` | 등록 |
| PUT | `/api/v1/cs-notice/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/cs-notice/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/cs-notice/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`cs/notice|조회` · `cs/notice|등록` · `cs/notice|수정` · `cs/notice|삭제` · `cs/notice|엑셀로` · `cs/notice|리포트` · `cs/notice|삭제자료표시`

## 고객센터 › 이용안내관리  `#/cs/guide` · 프로그램ID 040

관리자/고객 이용 가이드

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `title` | 제목 | string |  | 대표키 |
| `cat` | 분류 | string |  |  |
| `lang` | 언어 | string (코드) |  |  |
| `dt` | 수정일 | date / datetime | ✓ |  |
| `use` | 상태 | enum (게시, 숨김) |  |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/cs-guide?from&to&q&qf&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/cs-guide/{id}` | 상세 |
| POST | `/api/v1/cs-guide` | 등록 |
| PUT | `/api/v1/cs-guide/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/cs-guide/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/cs-guide/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`cs/guide|조회` · `cs/guide|등록` · `cs/guide|수정` · `cs/guide|삭제` · `cs/guide|엑셀로` · `cs/guide|리포트` · `cs/guide|삭제자료표시`

## 작업관리 › 직원관리  `#/work/staff` · 프로그램ID 041

SIGNION 본사 백오피스 직원 계정 · 권한

### 필드

| key | 이름 | 타입 | 검색조건 | 비고 |
|---|---|---|---|---|
| `uid` | 계정 | string (코드) |  | 대표키 |
| `name` | 이름 | string |  |  |
| `dept` | 부서 | string |  |  |
| `role` | 권한 | string (SysAdmin, 운영 관리자, 상담원 (CS), 연구소 개발자, 조회 전용) | ✓ |  |
| `mail` | 이메일 (2FA) | string |  |  |
| `last` | 최근 접속 | date / datetime | ✓ |  |
| `status` | 상태 | enum (정상, 중지) | ✓ |  |

### API 초안

| 메서드 | 경로 | 설명 |
|---|---|---|
| GET | `/api/v1/work-staff?from&to&q&qf&role&status&del&page&size&sort` | 조회 (목록 · 건수 · 정렬 · 페이지) |
| GET | `/api/v1/work-staff/{id}` | 상세 |
| POST | `/api/v1/work-staff` | 등록 |
| PUT | `/api/v1/work-staff/{id}` | 수정 (변경 항목 → 데이터수정LOG) |
| DELETE | `/api/v1/work-staff/{id}` | 삭제 — 사유 필수 · 논리삭제(삭제자료표시로 조회) |
| GET | `/api/v1/work-staff/export?(조회와 같은 조건)` | 엑셀로 (서버에서 마스킹 · 감사LOG) |

### 권한 코드 (역할별로 부여)

`work/staff|조회` · `work/staff|등록` · `work/staff|수정` · `work/staff|삭제` · `work/staff|엑셀로` · `work/staff|리포트` · `work/staff|삭제자료표시`

## 작업관리 › 프로그램관리  `#/work/prog` · 프로그램ID 042

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

## 작업관리 › 자료메모관리  `#/work/memo` · 프로그램ID 043

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

## 작업관리 › 데이터수정LOG  `#/work/datalog` · 프로그램ID 044

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

## 작업관리 › 권한관리  `#/work/role` · 프로그램ID 045

전용 화면


### 권한 코드 (역할별로 부여)

`work/role|조회` · `work/role|수정`

## 작업관리 › 감사LOG  `#/work/audit` · 프로그램ID 046

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
