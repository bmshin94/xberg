# Xberg 분석 & 활용 가이드 🚀

> Xberg 저장소를 직접 분석하고, 설치·사용법·수익화 아이디어·기술 스택까지 정리한 문서입니다.

## 🔗 관련 링크

| 구분 | 주소 |
|---|---|
| **이 저장소 (내 복사본)** | <https://github.com/bmshin94/xberg> |
| **원본 저장소** | <https://github.com/xberg-io/xberg> |
| 공식 문서 | <https://docs.xberg.io> |
| 라이브 데모 (브라우저/WASM) | <https://docs.xberg.io/demo.html> |
| 상용 제품 (Pro / Enterprise) | <https://xberg.io> |
| Discord 커뮤니티 | <https://discord.gg/xt9WY3GnKR> |
| PyPI (Python) | <https://pypi.org/project/xberg/> |
| npm (Node.js) | <https://www.npmjs.com/package/@xberg-io/xberg> |
| npm (WASM) | <https://www.npmjs.com/package/@xberg-io/xberg-wasm> |
| Packagist (PHP) | <https://packagist.org/packages/xberg-io/xberg> |
| crates.io (Rust) | <https://crates.io/crates/xberg> |
| Docker 이미지 | <https://github.com/xberg-io/xberg/pkgs/container/xberg> |

### 관련 프로젝트 (Xberg.io 생태계)

- [crawlberg](https://github.com/xberg-io/crawlberg) — 웹 크롤링 / 스크래핑 엔진
- [html-to-markdown](https://github.com/xberg-io/html-to-markdown) — HTML → Markdown 변환
- [liter-llm](https://github.com/xberg-io/liter-llm) — 165개 프로바이더 지원 LLM 클라이언트
- [tree-sitter-language-pack](https://github.com/xberg-io/tree-sitter-language-pack) — 코드 인텔리전스 문법팩
- [alef](https://github.com/xberg-io/alef) — 다국어 바인딩 생성기
- [Kreuzberg (구버전)](https://github.com/kreuzberg-dev/kreuzberg-v4-lts) — Xberg의 이전 이름

---

## 1. 이게 뭐야? (한 줄 요약)

> **"아무 파일이나 던지면 → 깔끔한 텍스트/마크다운/JSON으로 뱉어주는 만능 문서 추출 엔진"**

컴퓨터는 사실 PDF를 못 읽습니다. 우리 눈엔 글자로 보이지만 컴퓨터한테는 그냥 그림 덩어리예요.
엑셀, 한글파일, PPT, 스캔 사진도 마찬가지로 속이 전부 다릅니다.

**Xberg는 그 파일별 해독 도구를 전부 하나로 합쳐놓은 것입니다.**

```
📄 PDF      ┐
📊 엑셀     │
📝 한글     │  ➡️  [ Xberg 엔진 ]  ➡️  📃 깔끔한 텍스트 / 마크다운 / JSON
📸 사진     │
🎤 음성파일 │
🗜️ 압축파일 ┘
```

### 기본 정보

| 항목 | 내용 |
|---|---|
| 본체 언어 | **Rust** (매우 빠름) |
| 라이선스 | **MIT** (상업적 사용·판매 자유) |
| 이전 이름 | Kreuzberg → Xberg로 리브랜딩 (v1 라인) |
| 규모 | Rust 소스 파일 약 **1,759개**, 코어 크레이트 약 **22MB** |

---

## 2. 스펙 요약

| 항목 | 내용 |
|---|---|
| **문서 포맷** | **107종 / 확장자 141개** — PDF, Office, 이미지, 이메일(.eml/.msg/.pst), 전자책, 논문(LaTeX/BibTeX/JATS), DB파일 |
| **🇰🇷 한글 지원** | `.hwp`, `.hwpx` **정식 지원** |
| **OCR** | Tesseract / PaddleOCR / Candle / VLM(GPT-4V, Claude Vision 등) |
| **음성·영상** | mp3, m4a, wav, webm, mp4 → Whisper 자동 받아쓰기 |
| **압축파일** | zip/tar/7z 내부 문서까지 **재귀 추출** (zip bomb·압축률·중첩깊이 방어 내장) |
| **코드 분석** | **371개** 프로그래밍 언어에서 함수/클래스/import 추출 (tree-sitter) |
| **URL 수집** | http(s) URL 직접 추출 + 크롤링 (crawlberg 엔진) |
| **임베딩·검색** | 로컬 ONNX 또는 165개 프로바이더, 희소·late-interaction, 리랭킹 |
| **부가 기능** | NER, 키워드 추출(YAKE/RAKE), 요약, 번역, 마스킹, QR 인식, 언어 감지 |
| **출력 포맷** | 텍스트 / Markdown / Djot / HTML / JSON 트리 / DocTags (6종) |
| **언어 바인딩** | Rust, Python, Node, Go, Java, C#, Ruby, PHP, Elixir, Dart, Swift, Zig, Kotlin, WASM, C — **15개** |
| **실행 모드** | 라이브러리 / CLI(14개 명령) / REST API / MCP 서버 / Docker / Helm |

---

## 3. 폴더 구조 분석

```
xberg/
├── crates/          🦀 Rust 핵심 엔진
│                       xberg(코어), -cli, -py, -node, -wasm, -ffi, -jni, -php,
│                       -tesseract, -paddle-ocr, -candle-ocr, -native-pdf, -gliner 등
├── packages/        📦 언어별 배포 패키지
│                       python, go, java, ruby, php, swift, zig, elixir, dart, csharp, kotlin-android
├── plugin/          🤖 AI 코딩 어시스턴트용 플러그인 + skills/ (스킬 7종)
├── integrations/    🔌 외부 프레임워크 연동 (java / node / python)
├── e2e/             ✅ 15개 언어 전부 통합 테스트
├── docs-site/       📚 공식 문서 사이트 소스
├── charts/          ☸️ 쿠버네티스 Helm 차트
├── docker/          🐳 도커 이미지 정의
├── fixtures/        🧪 테스트용 샘플 문서
├── tools/           📊 벤치마크 / OCR 정확도 측정 도구
├── cli-proxy/       📮 npm·pypi용 CLI 래퍼
└── server.json      🔗 MCP 서버 정의
```

### 핵심만 보면 3덩어리

| 폴더 | 역할 |
|---|---|
| `crates/` | 🫀 엔진 본체 (Rust) |
| `packages/` | 🔌 언어별 어댑터 |
| `plugin/` | 🤖 AI가 이걸 잘 쓰도록 알려주는 설명서 |

> ⚠️ **이 저장소는 "공장"이고, 실제로 필요한 건 "완제품"입니다.**
> 소스에서 빌드하면 매우 오래 걸립니다. `pip install xberg` 등 패키지 설치를 권장합니다.

---

## 4. 설치 & 사용법

### 4-1. CLI (가장 쉬움) ⭐

```sh
# macOS
brew install xberg-io/tap/xberg

# Windows (Scoop)
scoop bucket add xberg https://github.com/xberg-io/scoop-bucket
scoop install xberg

# Docker
docker pull ghcr.io/xberg-io/xberg:latest
```

설치 확인:

```sh
xberg version
xberg doctor      # 👈 뭐가 빠졌는지 진단해줌
```

기본 사용:

```sh
xberg extract 문서.pdf                                   # 기본 추출
xberg extract 문서.pdf -o 결과.md                        # 파일로 저장
xberg extract 문서.pdf --content-format markdown         # 마크다운으로
xberg extract 문서.pdf --format json -o 결과.json        # JSON으로
xberg extract 스캔본.pdf --force-ocr --ocr-language kor  # OCR (한국어)
xberg extract 문서.pdf --force-ocr --ocr-language kor+eng # 한+영
xberg batch ./문서들/*.pdf --format json -o 전체.json     # 일괄 병렬 처리
xberg detect 정체불명파일                                 # 포맷 판별
xberg formats                                            # 지원 포맷 목록
```

자주 쓰는 옵션:

| 옵션 | 의미 |
|---|---|
| `-o` | 결과를 파일로 저장 |
| `--format text\|json` | CLI 출력 방식 |
| `--content-format markdown` | 본문을 마크다운으로 |
| `--force-ocr` | 무조건 OCR 실행 |
| `--ocr-language kor` | OCR 언어 (한국어=`kor`) |
| `--ocr-backend tesseract\|paddleocr` | OCR 엔진 선택 |
| `--chunk --chunk-size 1000` | AI용 청킹 |
| `--no-cache` | 캐시 무시 |

**14개 명령:** `extract` `batch` `detect` `formats` `version` `cache` `tree-sitter` `doctor` `serve` `mcp` `api` `embed` `chunk` `completions`

---

### 4-2. Python

```sh
pip install xberg
```

> 추가 extras 설치 불필요. OCR·레이아웃 감지·임베딩·청킹이 wheel에 전부 포함되어 있고,
> PDF 백엔드도 순수 Rust(`xberg-native-pdf`)라 별도 시스템 라이브러리가 필요 없습니다.

⚠️ **비동기(async) API입니다.**

```python
import asyncio
from xberg import ExtractInput, extract

async def main():
    output = await extract(ExtractInput(kind="uri", uri="문서.pdf"))
    doc = output.results[0]
    print(doc.content)

asyncio.run(main())
```

결과 객체:

```python
doc = output.results[0]

doc.content         # 본문 텍스트
doc.tables          # 표 목록 → table.markdown, table.cells, table.page_number
doc.metadata        # 제목, 언어, 포맷 등
doc.images          # 문서 내 이미지
doc.chunks          # 청킹된 조각

output.summary      # 처리 요약
output.errors       # 실패 목록
```

주요 레시피:

```python
# ① OCR (한국어)
from xberg import ExtractionConfig, OcrConfig
config = ExtractionConfig(
    ocr=OcrConfig(backend="tesseract", language="kor"),
    force_ocr=True,
)

# ② 표 추출
for table in output.results[0].tables:
    print(table.markdown, table.page_number)

# ③ 배치 처리
from pathlib import Path
from xberg import extract_batch
files = list(Path("문서들").glob("*.pdf"))
inputs = [ExtractInput(kind="uri", uri=str(f)) for f in files]
output = await extract_batch(inputs)

# ④ RAG용 청킹
from xberg import ChunkingConfig
config = ExtractionConfig(chunking=ChunkingConfig(max_chars=1000, max_overlap=200))

# ⑤ URL 직접 추출
output = await extract(ExtractInput(kind="uri", uri="https://example.com/보고서.pdf"))

# ⑥ 메모리 바이트 입력
output = await extract(ExtractInput(kind="bytes", bytes=data, mime_type="application/pdf"))

# ⑦ 암호 PDF
from xberg import PdfConfig
config = ExtractionConfig(pdf_options=PdfConfig(passwords=["비번1", "비번2"]))
```

예외 처리:

```python
from xberg import XbergError, ParsingError, OCRError, MissingDependencyError

try:
    output = await extract(ExtractInput(kind="uri", uri="문서.pdf"))
except OCRError as e:
    ...
except XbergError as e:   # 최상위 예외
    ...
```

주요 API:

- `await extract(input, config=None)`
- `await extract_batch(inputs, config=None)`
- `ExtractInput(kind="uri", uri=...)` / `ExtractInput(kind="bytes", bytes=..., mime_type=...)`
- 설정 클래스: `ExtractionConfig`, `OcrConfig`, `TesseractConfig`, `ChunkingConfig`, `HtmlOutputConfig`, `ImageExtractionConfig`, `PdfConfig`, `TokenReductionOptions`, `LanguageDetectionConfig`

---

### 4-3. Claude Code / AI 어시스턴트 연결 (추천 ⭐)

**① 플러그인 설치** — AI가 Xberg 코드를 정확히 작성하게 해줍니다 (스킬 7종 포함).

```text
/plugin marketplace add xberg-io/xberg
/plugin install xberg@xberg
```

**② MCP 서버 연결** — AI가 내 로컬 파일을 직접 읽을 수 있게 됩니다.

Claude Desktop / Cursor 설정:

```json
{
  "mcpServers": {
    "xberg": { "command": "xberg", "args": ["mcp"] }
  }
}
```

Claude Code:

```sh
claude mcp add xberg -- xberg mcp --transport stdio
```

**제공 도구 9개:** `extract` `extract_batch` `detect_mime_type` `list_formats` `cache_stats` `cache_clear` `cache_warm` `cache_manifest` `get_version`
**프롬프트 3개:** `extract_document` `extract_with_ocr` `semantic_search`

---

### 4-4. 추가 준비물

```sh
# OCR용 Tesseract (OCR 쓸 때 필수)
brew install tesseract && brew install tesseract-lang   # macOS (언어팩 포함)
sudo apt-get install tesseract-ocr tesseract-ocr-kor    # Ubuntu/Debian (한국어팩)
tesseract --version                                     # 확인

# 임베딩용 ONNX Runtime 1.24+ (임베딩 쓸 때만)
brew install onnxruntime
# 그 외: https://github.com/microsoft/onnxruntime/releases

# 일부 포맷용 Pandoc (선택)
brew install pandoc   /   sudo apt-get install pandoc
```

---

### 4-5. 서버 모드

```sh
# REST API 서버
xberg serve --host 0.0.0.0 --port 8000

# 도커로 한 방에
docker run -p 8000:8000 ghcr.io/xberg-io/xberg:latest serve
```

---

### 4-6. 문제 해결

| 증상 | 해결 |
|---|---|
| `No module named '_xberg'` | `pip install --force-reinstall --no-cache-dir xberg` |
| OCR이 동작 안 함 | `tesseract --version` 확인 → 없으면 설치 |
| 한글이 깨짐 | `--ocr-language kor` + 언어팩 설치 확인 |
| 큰 PDF에서 메모리 부족 | `ChunkingConfig(max_chars=1000)` 으로 청킹 |
| 원인 불명 | **`xberg doctor`** 실행 |

---

## 5. 라이선스 확인 (수익화 가능 여부) ✅

루트 `LICENSE` = **MIT License** (Copyright 2025-2026 Kreuzberg, Inc.)

```
...including without limitation the rights to use, copy, modify,
merge, publish, distribute, sublicense, and/or SELL copies...
```

👉 **상업적 사용·판매 가능. 소스 공개 의무 없음.**

> ⚠️ 단, `plugin/skills/xberg/SKILL.md` 는 `Elastic-2.0` 입니다.
> **그 스킬 문서 자체를 재판매**하는 것은 제한되며, 엔진을 갖다 쓰는 것과는 무관합니다.

### 한글(.hwp) 지원은 실제 구현 확인됨 🇰🇷

| 파일 | 줄 수 |
|---|---|
| `crates/xberg/src/extractors/hwpx.rs` | 1,390 |
| `crates/xberg/src/extraction/hwp/parser.rs` | 781 |
| `crates/xberg/src/extractors/hwp.rs` | 511 |
| `crates/xberg/src/extraction/hwp/equation.rs` (수식 파서) | 314 |
| `model.rs` / `summary.rs` / `mod.rs` / `reader.rs` / `error.rs` | 878 |
| **합계** | **약 3,904줄** |

수식 파서까지 있는 **제대로 된 구현**입니다. hwp 파싱이 가능한 오픈소스는 매우 드물어
**국내 시장에서 강력한 차별점**이 됩니다.

---

## 6. 수익화 아이디어 💰

### ⛔ 먼저, 하지 말아야 할 것

> **"Xberg 호스팅해서 API로 파는 것"** ❌

만든 회사가 이미 `Xberg Pro`(단일 컨테이너), `Xberg Enterprise`(쿠버네티스)를 상용으로 팔고 있습니다.
원작자와 정면승부는 승산이 없습니다.

### 💡 핵심 인사이트

> **Xberg는 "밀가루"입니다. 밀가루가 아니라 "빵"을 팔아야 합니다.**

`pip install xberg` 한 줄이면 누구나 쓸 수 있으므로 **Xberg 자체는 해자가 될 수 없습니다.**

```
Xberg (원재료·무료)
  + 도메인 지식 (내가 아는 업계 사정)
  + 워크플로우 (추출 후 무엇을 할 것인가)
  + 고객 접근 경로
  ─────────────────────────────
  = 💰
```

### 🥇 1위: 공공 입찰공고 인텔리전스 (`.hwp` 킬러앱)

- 나라장터 입찰공고 첨부파일이 **거의 다 `.hwp`**
- hwp 파싱 가능한 오픈소스가 거의 없음 → **해외 경쟁자가 못 들어오는 해자**

```
나라장터 공고 → hwp 첨부 추출 → 구조화
  → "우리 회사 업종/규모/지역에 맞는 공고만" 알림
  → 과거 낙찰가 분석, 경쟁사 동향
```

- **수익 모델:** B2B 월 구독 (중소기업 대상 월 5~30만원)
- **경쟁:** 비드톡·인포21 등 기존 업체 존재. 다만 가격이 높고 UI가 구형 →
  "AI가 공고문을 읽고 요약해준다" 각도로 차별화 가능

### 🥈 2위: 사내문서 RAG 구축 대행 (가장 빨리 현금화)

- **자본금 0원, 즉시 시작 가능**
- 모든 회사가 "우리 문서로 AI 챗봇"을 원하지만 **문서 전처리에서 막힘**
  (특히 hwp가 섞인 국내 기업은 대안이 없음)

```
회사 문서 (hwp + pdf + 엑셀 혼재)
  → Xberg로 전부 마크다운화
  → 청킹 + 임베딩 (Xberg 내장)
  → 사내 챗봇
```

- **수익:** 프로젝트당 500~3,000만원 + 유지보수 월 정액
- **장점:** SaaS와 달리 첫 달부터 매출 발생. 반복되는 문제를 발견하면 SaaS로 전환

### 🥉 3위: 이력서 파싱 (채용 ATS)

국내 이력서는 아직 hwp가 많음 → 경력·학력·스킬 자동 구조화 → 필터링
⚠️ 이력서는 개인정보보호법상 민감정보. 법적 준비 필수.

### 그 외 후보

| 아이디어 | 설명 | 난이도 |
|---|---|---|
| 🏠 부동산 서류 파싱 | 등기부등본·건축물대장 구조화 | ⭐⭐⭐ |
| ⚖️ 판결문/계약서 분석 | 조항 추출, 독소조항 탐지 | ⭐⭐⭐⭐ |
| 📚 학원 문제은행 | 문제지 PDF → 문항 분리 → 재조합 출제 | ⭐⭐ |
| 🔌 틈새 MCP 서버 | 특정 업계용 문서 MCP 판매 | ⭐⭐ |
| 🧾 세무 증빙 자동화 | 영수증 OCR → 회계 연동 (경쟁 심함) | ⭐⭐⭐ |

### 🎯 추천 전략

> **2위(RAG 대행)로 현금흐름을 만들면서, 1위(입찰공고 SaaS)를 개발.**

1. RAG 대행으로 당장의 매출 + 실제 고객 문제 파악
2. 병행하여 입찰공고 SaaS 개발
3. SaaS가 궤도에 오르면 대행 축소

### ⚠️ 리스크

| 리스크 | 대응 |
|---|---|
| Xberg는 차별점이 아님 | 경쟁사도 5분이면 도입 가능. 도메인·데이터·UX로 승부 |
| hwp 파싱 100%는 아님 | 구버전 `.hwp`는 실패 가능 → **실제 파일로 먼저 검증** |
| 크롤링 법적 이슈 | 나라장터는 **공공데이터 API 사용** (크롤링 지양) |
| 개인정보 | 이력서·의료·계약서 취급 시 법적 준비 필수 |
| 원작자의 시장 진입 | 한국 공공문서(hwp) 영역은 진입 가능성 낮음 → 안전 |

### ✅ 검증 우선 (개발 전에 할 일)

```
1️⃣  나라장터에서 hwp 공고문 5개 다운로드
2️⃣  xberg extract 공고문.hwp   ← 실제로 되는지 눈으로 확인
3️⃣  잘 되면 → 입찰공고 아이디어 진행
    안 되면 → RAG 대행으로 시작
```

---

## 7. React / PHP로 만들 수 있나? ⚛️🐘

| | 가능? | 방법 |
|---|---|---|
| **PHP** | ✅ **공식 지원** | 네이티브 확장 설치 → 서버에서 직접 추출 |
| **React** | ⚠️ **직접은 불가** | 브라우저 환경 → 백엔드 API 호출 구조로 |

### 7-1. PHP — 공식 바인딩 있음

`composer.json` 확인 결과:

```json
{
  "name": "xberg-io/xberg",
  "license": "MIT",
  "type": "php-ext",
  "require": { "php": ">=8.2" }
}
```

```sh
composer require xberg-io/xberg
# 또는 PIE로 확장 설치
pie install xberg-io/xberg
```

```php
<?php
declare(strict_types=1);
require_once __DIR__ . '/vendor/autoload.php';

$output = \Xberg\XbergApi::extract(
    \Xberg\ExtractInput::fromUri('공고문.hwp'),
    \Xberg\ExtractionConfig::default()
);

$result = $output->getResults()[0];

echo $result->content;                      // 본문
echo $result->metadata?->title;             // 제목
echo $result->metadata?->pdf?->page_count;  // 페이지 수

foreach ($result->tables as $table) {
    echo "페이지 {$table->pageNumber}\n";
    echo $table->markdown;
}
```

> 😊 Python은 async 필수였지만 **PHP는 동기 방식**이라 더 간단합니다.

**⚠️ 중요한 제약**

이건 일반 라이브러리가 아니라 **PHP 확장(extension)** 입니다. `.so` 파일을 PHP에 등록하는 방식이라
서버 관리자 권한이 필요합니다.

```
❌ 카페24, 가비아 등 공유호스팅 → 불가
✅ VPS, AWS EC2, 도커 → 가능
```

다행히 미리 컴파일된 바이너리를 내려받는 구조(`pre-packaged-binary`)라 직접 빌드할 필요는 없습니다.
**필요 스펙:** PHP 8.2+ (OCR 시 Tesseract, 임베딩 시 ONNX Runtime 1.24+)

### 7-2. React — 두 가지 방법

#### 방법 A: WASM으로 브라우저에서 직접

```sh
npm install @xberg-io/xberg-wasm
```

```tsx
import init, { extract } from "@xberg-io/xberg-wasm";

async function handleFile(file: File) {
  await init();                    // WASM 로딩 (최초 1회)

  const bytes = new Uint8Array(await file.arrayBuffer());
  const output = await extract({
    kind: "bytes",
    bytes,
    mimeType: file.type,
    filename: file.name,
  }, undefined);

  console.log(output.results[0].content);
}
```

- **장점:** 서버 불필요. 파일이 브라우저 밖으로 나가지 않아 **보안·개인정보에 유리**
- **단점 (공식 README 명시):** ONNX Runtime이 포함되지 않아
  **PaddleOCR·임베딩·리랭킹·네이티브 음성변환 불가**. OCR은 Tesseract WASM만 가능
  (레이아웃 감지는 순수 Rust `tract` 엔진으로 동작)
- **추가 현실 문제:** WASM 초기 로딩이 느리고, 큰 PDF는 브라우저 메모리 한계.
  **RAG를 만들 거라면 임베딩이 없다는 게 치명적**

#### 방법 B: 백엔드에 위임 (정석)

```
[React] ──파일 업로드──▶ [백엔드] ──▶ [Xberg] ──▶ 결과 JSON ──▶ [React]
```

백엔드는 PHP, Node, Python 무엇이든 가능합니다.

### 7-3. 추천 아키텍처

#### 🥇 가장 쉬운 길: `xberg serve` 활용 (바인딩 설치 불필요)

```sh
docker run -p 8000:8000 ghcr.io/xberg-io/xberg:latest serve
```

```tsx
const form = new FormData();
form.append("file", file);

const res = await fetch("http://localhost:8000/extract", {
  method: "POST",
  body: form,
});
const data = await res.json();
```

> PHP도 Python도 필요 없이 **HTTP 요청만으로 해결**. 설치 스트레스 0, 확장도 컨테이너 추가로 간단.

#### 🥈 PHP 팀이라면: Laravel + Xberg 확장

```
[React] ──▶ [Laravel API] ──▶ [Xberg PHP 확장] ──▶ DB
```

Laravel Queue로 무거운 추출을 백그라운드 처리하면 좋습니다.

#### 🥉 하이브리드 (가장 이상적)

```
[React] ─▶ [Laravel/Node: 로그인·결제·DB]
              └─▶ [Xberg 컨테이너: 추출 전담]   ← 분리
```

추출은 CPU를 많이 사용하므로 분리해두면 그 부분만 독립적으로 스케일업할 수 있습니다.

### 7-4. "입찰공고 서비스"용 추천 스택

```
프론트    React + Tailwind
백엔드    Laravel(PHP) 또는 Next.js API
추출      Xberg 컨테이너 (xberg serve)   ← hwp 담당
큐        Redis (공고 동시 처리)
DB        PostgreSQL
수집      나라장터 공공데이터 API (크롤링 지양)
```

**처리 흐름**

```
1. 나라장터 API로 공고 수집 (스케줄러)
2. 첨부 .hwp 다운로드 → 큐 등록
3. Xberg 추출 → 텍스트/표 구조화
4. 조건 매칭 → 회원 알림
5. React 대시보드에서 확인
```

---

## 8. 최종 정리

| 질문 | 답 |
|---|---|
| 이게 뭐야? | 107개 포맷을 텍스트/마크다운/JSON으로 바꿔주는 문서 추출 엔진 |
| 왜 유명해? | 이걸 **한 방에 · 무료로 · 매우 빠르게** 해주는 게 거의 없어서 |
| 나한테 좋은 점? | ① AI가 내 파일을 직접 읽게 됨 ② 문서 노가다 제거 ③ **.hwp 지원** |
| 수익화 가능? | ✅ MIT 라이선스 — 상업적 사용·판매 자유 |
| PHP로 돼? | ✅ 공식 바인딩 (단, VPS/도커 필요) |
| React로 돼? | ✅ 백엔드 경유 권장 (WASM은 제약 있음) |
| 가장 쉬운 시작? | `xberg serve` + `fetch` |

### 추천 시작 순서

```
1️⃣  brew install xberg-io/tap/xberg   (또는 도커)
2️⃣  xberg doctor                       ← 상태 점검
3️⃣  xberg extract 샘플.pdf              ← 감 잡기
4️⃣  MCP 연결                            ← AI와 연결
5️⃣  pip install xberg                   ← 코드로 자동화
```

---

*이 문서는 <https://github.com/bmshin94/xberg> 저장소를 직접 분석하여 작성되었습니다.*
