GitHub: https://github.com/songhwangcross-svg/ledger-api · Render: (배포 후 기입)

# 가계부 API (ledger-api) — 클라우드컴퓨팅실습 W4

FastAPI + SQLAlchemy로 만든 가계부 API를 Render에 배포하고, 데이터는 Supabase(클라우드 PostgreSQL)에 저장한다.

## 엔드포인트

| 메서드 | 경로 | 기능 |
|---|---|---|
| POST | `/accounts` | 계좌 생성 |
| GET | `/accounts` | 계좌 목록 |
| GET | `/accounts/{account_id}` | 계좌 단건 조회 |
| POST | `/transactions` | 거래 생성 (계좌 외래키 검사) |
| GET | `/accounts/{account_id}/detail` | 계좌 + 거래 목록 중첩 응답 |
| GET | `/stats/by-category` | 카테고리별 지출 합계 (GROUP BY) |

## 실습 기록

### ① 결과 확인
- Supabase Table Editor에 `accounts` · `categories` · `transactions` 세 테이블이 생성되어 있다.
- Render 배포 주소의 `/docs`에서 `GET /accounts`가 Supabase의 계좌를 돌려주고, `POST /accounts`로 만든 「배포테스트」 계좌가 Supabase Table Editor에 나타나는 것을 확인했다.

### ② 핵심 개념 되새김
- **계좌·거래를 두 테이블로 나눈 이유(1:N)**: 계좌 하나에 거래가 여러 건 붙으므로, 계좌 정보는 한 번만 저장하고 거래는 `account_id`(외래키)로 계좌를 가리키게 해야 중복과 불일치가 없다.
- **모델 클래스와 테이블의 대응**: `models.py`의 클래스 하나가 테이블 하나이고, `mapped_column` 속성 하나가 열 하나다. `__tablename__`이 실제 테이블 이름이 된다.
- **접속 문자열을 .env로 분리하는 이유**: DB 비밀번호가 들어 있어 GitHub에 올리면 안 되고, 로컬(.env)과 배포 서버(Render 환경변수)에서 값만 바꿔 같은 코드를 쓰기 위해서다.

### ③ 자유 로그
- 수업에서 단계 1~4(SQLite로 SQL 기초 → Supabase 연결 → 관계·중첩·집계)를 진행했고, 과제로 단계 5(GitHub → Render 배포 + Supabase 연동)를 했다.
- 배포 서버에는 .env 파일이 없으므로 `DATABASE_URL`을 Render 환경변수로 넣어야 한다는 점이 핵심이었다. 빠뜨리면 오류 없이 SQLite로 넘어가 Supabase와 연결되지 않는다.
- AI 활용: Claude에게 워크북 코드를 바탕으로 파일 작성과 배포 절차 안내를 요청했고, 로컬 테스트(계좌·거래 생성, 중첩 조회, 집계)와 Render `/docs` → Supabase Table Editor 확인으로 결과를 검증했다.
