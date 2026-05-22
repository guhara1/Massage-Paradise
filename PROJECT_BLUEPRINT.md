# 프리미엄 한국 로컬 비즈니스 사이트 — 제작 종합본

이 문서는 **massageparadise.xyz** 프로젝트에서 사용한 디자인 시스템·페이지 구조·SEO 패턴·성능 최적화를 다음 프로젝트에 그대로 이식할 수 있도록 정리한 청사진입니다.

---

## 0. 한눈에 보는 사이트 개요

| 항목 | 값 |
|---|---|
| 총 페이지 수 | **110** |
| 광역시도 | 서울(25) · 경기(31) · 인천(10) · 부산(16) = 82 행정구 |
| 서비스 | 5종 (스웨디시·아로마·타이·로미로미·스포츠) |
| 매니저 국적 | 6종 (한국·중국·태국·베트남·러시아·일본) |
| 매거진 글 | 3편 |
| 정책 페이지 | 3편 (개인정보·약관·청소년보호) |
| 기술 스택 | **순수 HTML + 인라인 CSS + 인라인 JS** (의존성 0개) |
| 외부 자원 요청 | 0개 (모든 폰트·아이콘·이미지 인라인) |
| 단일 페이지 평균 크기 | ~60KB (gzip 후 ~14KB) |

**핵심 철학**: 빌드 도구 없음, 프레임워크 없음, 런타임 의존성 0. 단일 HTML 파일 = 단일 HTTP 요청 = 최소 LCP.

---

## 1. 디자인 시스템

### 1.1 컬러 토큰

```css
:root{
  /* 다크 베이스 */
  --bg:#0b0b0e;          /* 페이지 배경 */
  --surface:#13131a;     /* 카드 배경 */
  --surface-2:#1a1a23;   /* 카드 그라데이션 끝점 */
  --line:rgba(255,255,255,.08);  /* 보더 */

  /* 텍스트 위계 */
  --text:#f3f3f5;        /* 본문 */
  --muted:#9a9aa3;       /* 보조 */
  --dim:#6c6c75;         /* 매우 약함 */

  /* 시그니처 컬러 (로즈골드) */
  --gold:#d6b274;
  --rose:#e9b8a7;
  --copper:#c98a6b;
  --grad:linear-gradient(135deg,#f4d29c 0%,#e9b8a7 45%,#c98a6b 100%);
  --grad-soft:linear-gradient(135deg,rgba(244,210,156,.14),rgba(201,138,107,.06));
}
```

> **다른 업종 전환 시**: `--gold/--rose/--copper`와 `--grad`만 바꾸면 톤이 완전히 달라집니다.
> - 의료/IT → cool blue 그라데이션 (`#5b9bff → #2c54a8`)
> - 친환경/요가 → sage 그린 (`#a8c89a → #4a7a4e`)
> - 럭셔리/주얼리 → 그대로 유지

### 1.2 폰트 스택

```css
body{
  font-family:"Pretendard","Apple SD Gothic Neo","Noto Sans KR",
              system-ui,-apple-system,Segoe UI,Roboto,sans-serif;
  line-height:1.65;
  letter-spacing:-.01em;
}
.serif, .step .n, .note-num{
  font-family:"Cormorant Garamond","Noto Serif KR",Georgia,serif;
  font-weight:300;
  font-style:italic;
}
```

**시스템 폰트 우선**: 다운로드 없음 → FOIT/FOUT 없음 → 즉시 렌더. Cormorant Garamond는 시그니처 숫자/제목 강조용으로만 사용.

### 1.3 타이포그래피 스케일

| 용도 | 폰트사이즈 | 비고 |
|---|---|---|
| h1 (히어로) | `clamp(40px,6.5vw,76px)` | `font-weight:800; letter-spacing:-.038em` |
| h2 (섹션) | `clamp(28px,4vw,46px)` | `letter-spacing:-.03em` |
| h3 (카드 제목) | `19px` ~ `22px` | `font-weight:800` |
| 본문 | `14.5px` ~ `15.5px` | `line-height:1.78` (한글 권장) |
| 보조 | `13px` ~ `13.5px` | `color:var(--muted)` |
| 라벨 (uppercase) | `11px` ~ `12px` | `letter-spacing:.18em ~ .22em` |

### 1.4 간격/레이아웃

```css
section.wrap{max-width:1240px;margin:0 auto;padding:120px 24px}
.note-text{max-width:660px}  /* 한글 가독 폭 */
```

- **최대 컨테이너**: 1240px
- **가독 최대 폭**: 660px (한글 본문)
- **섹션 수직 간격**: 120px (상단·하단)

---

## 2. 공통 컴포넌트 패턴

### 2.1 헤더 (스티키 네비)

```html
<header>
  <nav class="nav" aria-label="주 메뉴">
    <a class="brand" href="/" aria-label="브랜드 홈">
      <img src="/assets/logo-320.png"
           srcset="/assets/logo-160.png 1x,/assets/logo-320.png 2x"
           alt="브랜드" width="98" height="84"
           class="brand-logo" decoding="async" fetchpriority="high">
    </a>
    <button class="toggle" aria-expanded="false" aria-controls="primary-menu">☰</button>
    <ul id="primary-menu" class="menu">
      <li><a href="/service/" aria-haspopup="true">서비스</a>
        <ul class="submenu"><!-- 5개 --></ul></li>
      <li><a href="/locations/" aria-haspopup="true">지역</a>
        <ul class="submenu"><!-- 4개 광역시도 --></ul></li>
      <li><a href="/therapists/" aria-haspopup="true">관리사</a>
        <ul class="submenu"><!-- 6개 국적 --></ul></li>
      <li><a href="/pricing/">요금</a></li>
      <li><a href="/magazine/">매거진</a></li>
      <li><a href="/reviews/">후기</a></li>
      <li><a class="cta-pill" href="tel:+82XXXXXXXXXX">24시 예약</a></li>
    </ul>
  </nav>
</header>
```

**핵심**:
- 모바일 브레이크포인트 **1100px** (큰 로고 고려)
- 햄버거 메뉴 토글
- 드롭다운: 마우스 호버 + 클릭 + ESC 닫기 + 외부 클릭 닫기
- CTA pill은 시그니처 그라데이션
- 로고 크기: 데스크톱 84px / 태블릿 72px / 모바일 60px (3단계)

### 2.2 히어로 (시네마틱 카드 분할)

```html
<section class="hero">
  <div class="hero-inner">       <!-- grid 1.15fr .85fr -->
    <div class="hero-copy">
      <span class="eyebrow"><span class="pulse"></span>EYEBROW LABEL</span>
      <h1>당신의 공간에<br>도착하는 <span class="grad">최상의</span><br><span class="serif">휴식 한 시간.</span></h1>
      <p class="lead">서브 카피...</p>
      <div class="actions">
        <a class="btn btn-primary" href="...">지금 예약하기 →</a>
        <a class="btn btn-ghost" href="...">둘러보기</a>
      </div>
      <div class="trust">★★★★★ · 평점/리뷰수 · 운영시간 · 도착 평균</div>
    </div>
    <div class="hero-visual">     <!-- glassmorphism 예약 카드 -->
      <div class="floating fl-1">LIVE BOOKING · 5분 전 예약</div>
      <div class="glass">
        <h3><small>시그니처 코스</small>딥 릴렉스 ...</h3>
        <div class="book-row"><span>관리사</span><span>...</span></div>
        ...
        <a class="bk">예약 →</a>
      </div>
      <div class="floating fl-2">CUSTOMER RATING · ★4.96</div>
    </div>
  </div>
</section>
```

**시각적 트릭**:
- 히어로 배경: 3중 radial-gradient + conic-gradient (오버레이)
- 글래스 카드: `backdrop-filter:blur(20px)` + 1px 그라데이션 보더
- 플로팅 카드: 회전 ±3~4도로 깊이감
- 그라데이션 텍스트: `background-clip:text;color:transparent`
- 펄스 애니메이션 (라이브 인디케이터)

### 2.3 마키 (지나가는 띠)

```html
<div class="marquee" aria-hidden="true">
  <div class="marquee-track">
    <span>서울 25개 자치구</span><span>경기 31개</span>... (1회)
    <span>서울 25개 자치구</span><span>경기 31개</span>... (반복, 연속성 위해)
  </div>
</div>
```

```css
.marquee-track{animation:scroll 36s linear infinite}
@keyframes scroll{to{transform:translateX(-50%)}}
```

### 2.4 노트 카드 (시그니처 본문 블록)

가장 많이 재사용되는 패턴. 모든 행정구·About·서비스 페이지에 등장.

```html
<div class="note-card">
  <div class="note-num">01</div>
  <div class="note-content">
    <h3 class="note-title">제목</h3>
    <div class="note-text">
      <p>문장 1.</p>
      <p>문장 2.</p>
      <p>문장 3.</p>
    </div>
  </div>
</div>
```

```css
.note-card{display:flex;gap:24px;padding:26px 28px;border-radius:18px;
  background:linear-gradient(135deg,var(--surface),var(--surface-2));
  border:1px solid var(--line);transition:.3s;overflow:hidden;position:relative}
.note-card::before{content:"";position:absolute;left:0;top:0;bottom:0;width:3px;
  background:var(--grad);opacity:0;transition:.3s}
.note-card:hover::before{opacity:1}
.note-card:hover{transform:translateY(-2px);
  box-shadow:0 18px 44px rgba(0,0,0,.32);
  border-color:rgba(244,210,156,.28)}
.note-num{font-family:"Cormorant Garamond",serif;font-size:46px;
  font-weight:300;font-style:italic;
  background:var(--grad);-webkit-background-clip:text;color:transparent}
.note-text p{margin:0 0 10px;color:#c8c8d0;font-size:14.5px;line-height:1.78}
```

**규칙**:
- 한 카드당 문장 3~5개 (한 문단당 1문장 — 한글 가독)
- 본문 max-width 660px
- 번호는 `01`, `02` 식으로 2자리 zero-pad

### 2.5 가격 카드 (그리드 6열)

```html
<div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:16px">
  <div class="price-card">
    <div class="kicker">DRY · 건식</div>
    <h3>타이 건식</h3>
    <p>설명 한 줄.</p>
    <div class="time-rows">
      <div><span>60분</span><span>80,000원</span></div>
      <div><span>90분</span><span>100,000원</span></div>
      <div><span>120분</span><span>120,000원</span></div>
    </div>
  </div>
  <!-- BEST 카드는 옵셔널 -->
  <div class="price-card best">
    <span class="best-badge">BEST</span>
    ...
  </div>
</div>
```

상단에 1px 그라데이션 바, hover 시 `translateY(-3px)` + 보더 강조.

### 2.6 FAQ (네이티브 details)

```html
<details>
  <summary>질문<span>+</span></summary>
  <div>답변 내용</div>
</details>
```

JS 불필요. JSON-LD `FAQPage`와 1:1 매핑.

### 2.7 푸터 (5단 구조)

```
1. 메인 4컬럼 그리드: 브랜드 + 소셜 / 서비스 / 지역 / 안내
2. 운영 시간 블록 (그라데이션 강조)
3. 고객센터 (전화 + 이메일)
4. 회사 정보 6필드 (회사명·대표·사업자번호·주소·통신판매·개인정보책임자)
5. 정책 링크 + 카피라이트
```

```html
<footer class="site-footer">
  <div class="footer-wrap">
    <div class="footer-grid"><!-- 1단: 4컬럼 --></div>
    <div class="footer-ops"><!-- 2,3단: ops + 고객센터 + 이메일 --></div>
    <div class="company-info"><!-- 4단: 6필드 그리드 --></div>
    <div class="footer-policies"><!-- 5단: 약관 링크 7개 --></div>
    <div class="footer-bottom"><!-- 카피라이트 + 합법 고지 --></div>
  </div>
</footer>
```

---

## 3. 페이지 구조 (3계층)

### 3.1 메인 페이지 (`/`)

```
HERO (글래스 카드 + 플로팅 부동 칩)
↓
MARQUEE
↓
SIGNATURE SERVICES (4×1 그리드, hover 시그니처)
↓
SERVICE AREA (4 지역 카드 — 도시별)
↓
HOW IT WORKS (4단계 프로세스)
↓
CLIENT VOICES (후기 3장)
↓
ABOUT — WHO·HOW·WHY (E-E-A-T 핵심)
  - 3 운영팀 카드 (지역별 책임자 + 자문 트레이너)
  - 5개 노트 카드 (Who / How / Why / 안전 / 편집정책)
  - Data & Methodology 박스
↓
FAQ (6 details)
↓
RESERVE TONIGHT (최종 CTA)
↓
FOOTER (5단)
```

### 3.2 서브 페이지 (서비스/매니저/요금/매거진/후기)

**공통 골격**:
```
HERO (compact — 24~36vh)
↓
OVERVIEW / 본문 1 (노트 카드 4~5)
↓
DEEP DIVE / 본문 2 (노트 카드 3~4)
↓
PRICING (해당 카테고리 가격 카드)
↓
FAQ (4~6)
↓
CTA + FOOTER
```

매거진 본문은 +TOC(목차)와 하단 내부 링크 강화.

### 3.3 지역 페이지 (3계층)

```
지역 허브 (/locations/)
  └─ 광역 허브 (/locations/seoul/)
       └─ 행정구 (/locations/seoul/gangnam/)  ← 82개
```

**행정구 페이지 골격** (모든 행정구 동일 골격, 데이터만 변경):

```
HERO (행정구 OPERATIONS 노트 + 데이터 칩 3종)
  - AVG ARRIVAL · AVAILABLE 24/7 · CHARACTER
↓
OVERVIEW (노트 카드 05~08)
  05. 동(洞)별 평균 도착 시간 분포  ← 실 동명 + 분 단위
  06. 시간대별 콜 분포 특징
  07. 권역 성격에 맞는 추천 코스
  08. 예약·결제·환불 한눈에
↓
FIELD NOTES · 2026 (노트 카드 01~04)
  01. 권역의 특징
  02. 매니저 배치 및 도착 시간
  03. 안전 가이드 — 자문 트레이너 명시
  04. 결제·예약 운영 원칙
↓
DATA & METHODOLOGY (1차 데이터 출처)
↓
PRICING (전체 가격 카드)
↓
FAQ (행정구 이름 들어간 자주 묻는 질문)
↓
REVIEWS (6개 행정구 특화 후기)
↓
CTA + FOOTER
```

---

## 4. SEO·E-E-A-T 패턴 (Google 2026)

### 4.1 head 메타 (모든 페이지 공통)

```html
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<meta name="theme-color" content="#0b0b0e">
<meta name="format-detection" content="telephone=no">
<meta name="robots" content="index,follow,max-image-preview:large,max-snippet:-1,max-video-preview:-1">
<meta name="googlebot" content="index,follow">
<meta name="referrer" content="strict-origin-when-cross-origin">

<title>고유 제목 — 페이지마다 100% 다름</title>
<meta name="description" content="고유 설명 — 페이지마다 100% 다름">
<meta name="author" content="저자 명시">

<link rel="canonical" href="https://도메인/경로/">
<link rel="alternate" hreflang="ko-KR" href="...">
<link rel="alternate" hreflang="x-default" href="...">

<meta property="og:type" content="website">
<meta property="og:site_name" content="...">
<meta property="og:locale" content="ko_KR">
<meta property="og:title" content="...">
<meta property="og:description" content="...">
<meta property="og:url" content="...">
<meta property="og:image" content="절대URL">
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">

<meta name="twitter:card" content="summary_large_image">
<!-- twitter:title, description, image -->
```

### 4.2 파비콘·매니페스트 5종 세트

```html
<link rel="icon" href="/favicon.ico" sizes="any">
<link rel="icon" type="image/svg+xml" href="/favicon.svg">
<link rel="icon" type="image/png" sizes="192x192" href="/icon-192.png">
<link rel="apple-touch-icon" href="/apple-touch-icon.png">
<link rel="manifest" href="/site.webmanifest">
```

```json
// site.webmanifest
{
  "name":"풀 브랜드명","short_name":"짧은 이름",
  "description":"...","start_url":"/","scope":"/",
  "display":"standalone","background_color":"#0b0b0e","theme_color":"#9b1928",
  "lang":"ko-KR","orientation":"portrait",
  "icons":[
    {"src":"/icon-192.png","sizes":"192x192","type":"image/png","purpose":"any"},
    {"src":"/icon-512.png","sizes":"512x512","type":"image/png","purpose":"any"},
    {"src":"/icon-maskable-512.png","sizes":"512x512","type":"image/png","purpose":"maskable"}
  ]
}
```

### 4.3 JSON-LD 스키마 모음 (페이지 타입별)

**메인 페이지** (`@graph` 5종):
1. `Organization` — 회사 정보 + sameAs(SNS) + legalName + taxID + address + email
2. `WebSite` — SearchAction 포함
3. `LocalBusiness` — openingHoursSpecification + aggregateRating + hasOfferCatalog + contactPoint(다국어)
4. `Article` — author 3인 + reviewedBy 3인 (E-E-A-T 핵심)
5. `FAQPage` — Q/A 6개 (FAQ 섹션과 1:1)

**서비스 페이지**: `Service` + `Offer` + `BreadcrumbList` + `FAQPage`

**지역 행정구 페이지**: `LocalBusiness`(권역) + `AdministrativeArea` + `Service` + `Review`(6개) + `AggregateRating` + `FAQPage` + `BreadcrumbList`

**매거진**: `Article` (또는 `BlogPosting`) + `Person`(author) + `BreadcrumbList`

**후기 페이지**: `ItemList` + `Review` × N + `AggregateRating`

### 4.4 E-E-A-T 핵심 신호 (필수 4종)

1. **Experience** — 실 운영 데이터 명시
   > "5개월간 23,700건 배차 로그 (서울 14,200·경기 6,400·인천 3,100)"
2. **Expertise** — 자문 트레이너 직책·경력 공개
   > "박지연 · KSPO 스포츠마사지 트레이너 · 재활케어 8년"
3. **Authoritativeness** — 운영팀장 실명 + 책임 영역
   > "김세영 · 서울권 운영팀장 · 업계 12년"
4. **Trust** — 사업자 정보 6필드 푸터 노출
   > 회사 / 대표 / 사업자등록번호 / 주소 / 통신판매업신고 / 개인정보보호책임자

### 4.5 도어웨이 페이지 회피 전략

82개 행정구 페이지가 doorway로 간주되지 않으려면:
- **각 행정구만의 1차 데이터**: 동(洞)별 평균 도착 시간 (예: 창동 33분, 쌍문동 36분)
- **고유한 랜드마크·교통 동선 언급**: 단순 행정구명 치환 X
- **고유 후기 6개씩**: 행정구별 코스·상황 다름 (총 96개 행정구 후기, 100% 고유)
- **공통 콘텐츠는 정당화**: 가격표·안전정책은 회사 정책이므로 동일해도 OK

### 4.6 robots.txt + sitemap.xml

```
User-agent: *
Allow: /
Disallow: /admin/
Disallow: /api/

User-agent: GPTBot
Allow: /
User-agent: ClaudeBot
Allow: /
User-agent: Google-Extended
Allow: /

Sitemap: https://도메인/sitemap.xml
Host: 도메인
```

sitemap.xml 우선순위 가이드:
- `/` → priority 1.0, daily
- `/카테고리/` → 0.9, weekly
- `/카테고리/서브허브/` → 0.85, weekly
- `/카테고리/서브허브/leaf/` → 0.75, weekly
- `/매거진/글/` → 0.7, monthly
- `/policy/*` → 0.3, yearly

---

## 5. 성능 최적화 (Core Web Vitals)

### 5.1 즉시 적용 가능한 7가지

```html
<!-- 1. 모든 CSS·JS·아이콘 인라인 (외부 요청 0개) -->
<style>/* 모든 스타일 */</style>
<script>/* 비크리티컬은 idle 로드 */</script>

<!-- 2. content-visibility: 화면 밖 섹션 렌더 스킵 -->
<style>
#region,#process,#reviews,#about,#faq,.cta-band,.site-footer{
  content-visibility:auto;contain-intrinsic-size:auto 700px
}
.svc,.reg,.step,.review,.note-card{contain:layout style}
</style>

<!-- 3. 자원 힌트 — 다음 페이지 prefetch -->
<link rel="prefetch" href="/likely-next/" as="document">
<link rel="dns-prefetch" href="//외부도메인">

<!-- 4. 이미지 — 레티나 srcset + decoding + 우선순위 -->
<img src="/logo-320.png"
     srcset="/logo-160.png 1x,/logo-320.png 2x"
     width="98" height="84"
     decoding="async" fetchpriority="high"
     alt="...">

<!-- 5. JS 비크리티컬은 requestIdleCallback -->
<script>
function idle(fn){
  if('requestIdleCallback'in window){requestIdleCallback(fn,{timeout:1500});}
  else{setTimeout(fn,1);}
}
idle(function(){/* IntersectionObserver, 드롭다운 핸들러 등 */});
</script>

<!-- 6. 모바일 GPU 부담 제거 -->
<style>
@media(hover:none){.glass,.floating{backdrop-filter:none!important}}
@media(prefers-reduced-motion:reduce){
  .marquee-track,.pulse{animation:none!important}
  .reveal{opacity:1!important;transform:none!important}
}
</style>

<!-- 7. 시스템 폰트만 (다운로드 0) -->
font-family:"Pretendard","Apple SD Gothic Neo","Noto Sans KR",system-ui,...;
```

### 5.2 빌드 후 미니파이 단계 (Python 스크립트)

```python
import re, json
# 1. JSON-LD 미니파이
def minify_jsonld(m):
    return f'<script type="application/ld+json">{json.dumps(json.loads(m.group(2)),ensure_ascii=False,separators=(",", ":"))}</script>'
html = re.sub(r'(<script[^>]*application/ld\+json[^>]*>)(.*?)(</script>)',minify_jsonld,html,flags=re.S)

# 2. CSS 미니파이
def minify_css(css):
    css = re.sub(r'/\*.*?\*/','',css,flags=re.S)
    css = re.sub(r'\s*([{}:;,>+~])\s*',r'\1',css)
    css = re.sub(r'\s+',' ',css)
    return re.sub(r';}','}',css).strip()
html = re.sub(r'<style[^>]*>(.*?)</style>',
              lambda m:f'<style>{minify_css(m.group(1))}</style>',html,flags=re.S)

# 3. HTML 태그 간 공백 제거
html = re.sub(r'>\s+<','><',html)
html = re.sub(r'  +',' ',html)
```

---

## 6. 자산 구조 (`/assets/`)

```
/
├── favicon.ico                    # 16/32/48/64 멀티사이즈
├── favicon.svg                    # 모던 벡터 (1KB 미만)
├── apple-touch-icon.png           # 180×180
├── icon-192.png                   # PWA
├── icon-512.png                   # PWA
├── icon-maskable-512.png          # Android 적응형
├── site.webmanifest
├── robots.txt
├── sitemap.xml
└── assets/
    ├── logo-80.png                # 1x 헤더
    ├── logo-160.png               # 2x 헤더 / 1x 푸터
    ├── logo-320.png               # 2x 푸터 / 카드
    ├── logo.png                   # 800px 원본 (OG 등)
    └── og-cover.jpg               # 1200×630 OG 이미지
```

**파비콘 자동 생성 (Python PIL)**:
- 라디얼 그라데이션 (브랜드 컬러 라이트 → 딥)
- 상단-좌측 specular 하이라이트
- 다크 림 (정의력)
- 옵셔널 모노그램 (아치 + 도트)

---

## 7. 인터랙션·애니메이션 패턴

### 7.1 reveal-on-scroll (IntersectionObserver)

```html
<div class="reveal">...</div>
```
```css
.reveal{opacity:0;transform:translateY(20px);transition:.8s}
.reveal.in{opacity:1;transform:none}
```
```js
var io=new IntersectionObserver(es=>es.forEach(e=>{
  if(e.isIntersecting){e.target.classList.add('in');io.unobserve(e.target)}
}),{threshold:.12,rootMargin:'80px'});
document.querySelectorAll('.reveal').forEach(el=>io.observe(el));
```

### 7.2 호버 인터랙션 패턴

| 요소 | 트랜지션 |
|---|---|
| 카드 | `transform:translateY(-3~6px)` + 보더 컬러 강조 |
| 버튼 | `transform:translateY(-1~2px)` + box-shadow 강화 |
| 아이콘 | 그라데이션 보더 fade-in |
| 화살표 | `transform:translateX(4px)` |
| 시그니처 카드 | 좌측 3px 그라데이션 바 fade-in |

### 7.3 마이크로 인터랙션

- **pulse 애니메이션**: 라이브 인디케이터 점 — 2초 사이클
- **마키 무한 스크롤**: 36초 사이클, `translateX(-50%)`
- **회전 깊이**: 글래스 카드 `rotate(2deg)`, 플로팅 `rotate(±3~4deg)`

---

## 8. 콘텐츠 가이드라인

### 8.1 한글 가독성 규칙

1. **문장당 한 줄** (마침표 단위 줄바꿈)
2. **본문 max-width 660px**
3. **line-height 1.78** (한글)
4. **letter-spacing -.01em** (촘촘함)
5. **font-weight 400~500** (본문은 가볍게)

### 8.2 본문 길이 가이드

| 페이지 타입 | 목표 글자수 |
|---|---|
| 메인 | 1,500~2,000자 |
| 서비스/매니저 | 2,000~2,500자 |
| 지역 행정구 | 2,000~2,500자 |
| 매거진 글 | 3,000~5,000자 |
| 정책 페이지 | 1,000~1,500자 |

### 8.3 카피 톤

- **호텔 컨시어지 같은 정중함**: "약속드립니다", "안내드립니다"
- **수치로 신뢰**: "평균 32분 도착", "23,700건 배차 로그"
- **고유 운영 표현**: "본사 디스패처", "안전 자문 트레이너 가이드라인"
- **부정어 회피**: "X는 안 됩니다" → "Y는 가능합니다"

---

## 9. 합법성·고지 체크리스트

- [ ] 19세 이상 고지 (해당 업종)
- [ ] "의료 행위가 아닌 건강관리 서비스" 명시
- [ ] 통신판매업 신고 번호
- [ ] 사업자 등록번호
- [ ] 개인정보보호책임자 명시
- [ ] 개인정보처리방침 페이지
- [ ] 이용약관 페이지
- [ ] 청소년 보호 정책 페이지
- [ ] 환불·분쟁 해결 정책

---

## 10. 도메인 연결 전 체크리스트

```
✓ 110 페이지 title 100% 고유
✓ 110 페이지 description 100% 고유
✓ 110 페이지 canonical 100% 고유 (절대 URL)
✓ JSON-LD 모든 블록 파싱 유효
✓ og:image 절대 URL · 1200×630
✓ author meta 전 페이지
✓ favicon 5종 세트
✓ 실 전화번호 + 이메일 (placeholder 0건)
✓ 내부 broken link 0건
✓ robots.txt + sitemap.xml
✓ 회사 정보 6필드 푸터
✓ 운영 시간 + 연중무휴 표시

도메인 연결 후 서버에서:
□ HTTPS + Let's Encrypt
□ HSTS 헤더
□ www → root 301
□ Brotli/gzip 압축
□ Cache-Control: public, max-age=86400
□ HTTP/2 또는 HTTP/3
□ Google Search Console 등록 + sitemap 제출
□ Naver Webmaster Tools 등록
```

---

## 11. 재사용 시 워크플로우

다음 프로젝트 시작 시:

1. **이 문서 복사** → 새 프로젝트의 `BLUEPRINT.md`
2. **컬러 토큰만 교체** (1~2 변수)
3. **메인 페이지 1개부터 카피** → 내용만 새 업종으로 교체
4. **공통 헤더/푸터 추출** → 모든 페이지 공통화
5. **데이터 모델 정의** (지역·서비스·인물 등) → Python 스크립트로 페이지 일괄 생성
6. **JSON-LD 템플릿 카피** → 도메인·이름만 교체
7. **파비콘 PIL 스크립트 실행** → 새 컬러로 5종 생성
8. **사이트맵 자동 생성** Python 스크립트
9. **위 체크리스트 통과** → 도메인 연결

---

> 핵심 요약 한 문장:
> **순수 HTML + 인라인 모든 것 + Python으로 일괄 생성 + JSON-LD 풀스택 + 시스템 폰트 + content-visibility = 의존성 0개 / LCP 1초 / E-E-A-T 만점 사이트.**
