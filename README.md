# Cochl Release Notes Agent

A Claude Code skill that automatically drafts release notes from GitHub PRs or a local file — formatted for the right audience, in Cochl's voice.

Supports four output formats: **docs**, **newsletter (launch)**, **newsletter (monthly)**, and **internal**.

---

## Prerequisites

Before setting up, make sure you have:

1. **Claude Code** installed  
   → [claude.ai/code](https://claude.ai/code) or run `npm install -g @anthropic-ai/claude-code`

2. **GitHub CLI** installed and authenticated  
   ```bash
   brew install gh
   gh auth login
   ```
   Required only if you use `--repo` to fetch PRs from GitHub.

---

## Installation

Copy the skill file into your Claude Code commands directory:

```bash
mkdir -p ~/.claude/commands
curl -o ~/.claude/commands/draft-release-notes.md \
  https://raw.githubusercontent.com/meanmin/release-note-agent/main/draft-release-notes.md
```

That's it. Claude Code picks up skills from `~/.claude/commands/` automatically — no restart needed.

---

## Usage

Open Claude Code and run the command with the arguments below.

### From GitHub PRs (`--repo`)

The skill fetches PRs from the specified repository and uses them as source material.

```
# Generate docs release notes from a GitHub repo
/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience docs

# Generate an internal changelog
/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience internal

# Generate a newsletter announcement (new feature launch)
/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience newsletter --style launch

# Generate a newsletter update (monthly digest)
/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience newsletter --style monthly
```

The skill checks open PRs first. If none are found, it retries with merged PRs automatically.

### From a local file (`--file`)

Pass any PDF, TXT, or Markdown file as the source instead of GitHub PRs.

```
/draft-release-notes --file ~/Downloads/update.pdf --version v1.7.0 --audience newsletter --style launch
```

Claude Code reads the file directly — no conversion needed.

### Arguments

| Argument | Required | Description |
|---|---|---|
| `--repo` | One of `--repo` or `--file` | GitHub repo to pull PRs from (e.g. `cochlearai/sense-sdk`) |
| `--file` | One of `--repo` or `--file` | Local file path (PDF, TXT, or Markdown) |
| `--version` | Yes | Release version (e.g. `v1.7.0`) |
| `--audience` | Yes | `docs`, `newsletter`, or `internal` |
| `--style` | Only for newsletter | `launch` or `monthly` — see below |

---

## Output formats

### `--audience docs`
Short and scannable. Written for the Cochl documentation site. No marketing language — this is a reference document.

**Example:**
```
April 7, 2026
Cochl.Sense v1.7.0 released

• Added adaptive audio activity filtering — skips inference on silent frames to reduce CPU usage on edge devices; controlled via a single `activity_sensitivity` parameter
• Added Automatic Gain Control (AGC) — normalizes mic input levels across environments; controlled via `target_mav`
```

---

### `--audience newsletter --style launch`
A dedicated feature announcement. Written like Cochl's Custom Sound launch newsletter — benefit-first, empowering, with a "How it works" walkthrough.

Best for: introducing a brand-new capability for the first time.

**Example:**
```
Run Cochl.Sense smarter — not harder.

• Lightweight: No buffer required — adapts on the fly with minimal overhead
• Adaptive: Adjusts automatically based on the last 10 minutes of audio trends
• Simple to use: One parameter per module — integration takes minutes

How it works
01 Drop the module in front of your inference pipeline
02 Set your sensitivity or gain target with a single parameter
03 Let it adapt automatically

Try it now → [GitHub]
```

---

### `--audience newsletter --style monthly`
A concise update section for the monthly Cochl Bites newsletter. Written for developers — technical enough to be useful, short enough to fit in a digest.

Best for: regular version updates.

**Example:**
```
Cochl.Sense Audio Preprocessing Module — now available

We've released a new open-source utility library with two modules for edge and streaming deployments.

Key changes:
- Adaptive Audio Activity Filtering: skips inference on low-activity frames; parameter: `activity_sensitivity`
- Automatic Gain Control: normalizes gain without buffering; parameter: `target_mav`

Refer to the GitHub repo for integration examples.
```

---

### `--audience internal`
Full technical changelog for the engineering team. Includes PR references, root causes, a migration checklist, and a separate "Pending / Not yet shipped" section.

**Example:**
```
## v1.7.0 Internal Release Notes — April 7, 2026

### Features
- **Adaptive Audio Activity Filtering**: Buffer-free pre-filter using rolling mean amplitude over 600 samples (~10 min). Exposes `activity_sensitivity` parameter.
- **Automatic Gain Control (AGC)**: Adaptive gain normalization, same 10-min rolling window. Exposes `target_mav`.

### Breaking Changes & Migration Checklist
- [ ] No breaking changes — standalone opt-in module

### Pending / Not yet shipped
- Tag Hierarchy auto-propagation — hardcoded in Sense API, cleanup and docs still pending
```

---

## Output files

Generated files are saved to `~/release-notes/` with this naming convention:

| Source | Filename |
|---|---|
| `--repo cochlearai/sense-sdk` | `~/release-notes/sense-sdk-v1.7.0-docs.md` |
| `--repo` + newsletter | `~/release-notes/sense-sdk-v1.7.0-newsletter-launch.md` |
| `--file update.pdf` + internal | `~/release-notes/update-internal.md` |

---

## Tips

- If you use `--file` with a PDF, Claude Code reads the file directly — no conversion needed.
- When pulling from `--repo`, the skill checks open PRs first. If none are found, it retries with merged PRs.
- If your source mixes shipped features with planned items (roadmap, backlog), the agent will flag planned items separately in the output — it won't include them as released.
- The skill follows Cochl's tone & manner for all audience types. No "We are excited to announce."

---

## Beta feedback

This skill is in beta. If something looks off — wrong tone, missing info, confusing output — please share your feedback:

- Open an issue in this repo
- Or contact the maintainer directly at **msjang@cochl.ai**

We're iterating on this based on real usage. Your examples and edge cases help a lot.

---

---

# Cochl 릴리즈 노트 에이전트

GitHub PR이나 로컬 파일을 읽고, 대상 독자에 맞게 Cochl 톤으로 릴리즈 노트를 자동으로 작성해주는 Claude Code 스킬입니다.

**docs**, **newsletter (launch)**, **newsletter (monthly)**, **internal** 네 가지 포맷을 지원합니다.

---

## 사전 준비

설치 전에 아래 두 가지가 준비되어 있는지 확인하세요.

1. **Claude Code** 설치  
   → [claude.ai/code](https://claude.ai/code) 또는 터미널에서 `npm install -g @anthropic-ai/claude-code`

2. **GitHub CLI** 설치 및 로그인  
   ```bash
   brew install gh
   gh auth login
   ```
   GitHub PR을 불러오는 `--repo` 옵션을 사용할 때만 필요합니다.

---

## 설치

스킬 파일을 Claude Code commands 폴더에 복사하면 끝입니다.

```bash
mkdir -p ~/.claude/commands
curl -o ~/.claude/commands/draft-release-notes.md \
  https://raw.githubusercontent.com/meanmin/release-note-agent/main/draft-release-notes.md
```

Claude Code는 `~/.claude/commands/` 안의 파일을 자동으로 인식합니다. 재시작 없이 바로 사용 가능합니다.

---

## 사용 방법

Claude Code를 열고 아래 인자 형식에 맞게 실행하세요.

### GitHub PR 기반 (`--repo`)

지정한 레포의 PR을 자동으로 불러와 소스로 사용합니다.

```
# 문서용 릴리즈 노트 생성
/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience docs

# 내부 기술 변경 이력 생성
/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience internal

# 뉴스레터용 — 새 기능 런치 공지
/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience newsletter --style launch

# 뉴스레터용 — 월간 업데이트 요약
/draft-release-notes --repo cochlearai/sense-sdk --version v1.7.0 --audience newsletter --style monthly
```

열린 PR(open)을 먼저 확인하고, 없으면 병합된 PR(merged)으로 자동 재시도합니다.

### 로컬 파일 기반 (`--file`)

GitHub PR 대신 PDF, TXT, Markdown 파일을 소스로 사용할 수 있습니다.

```
/draft-release-notes --file ~/Downloads/update.pdf --version v1.7.0 --audience newsletter --style launch
```

Claude Code가 파일을 직접 읽습니다. 별도 변환 불필요.

### 인자 설명

| 인자 | 필수 여부 | 설명 |
|---|---|---|
| `--repo` | `--repo` 또는 `--file` 중 하나 필수 | PR을 가져올 GitHub 레포 (예: `cochlearai/sense-sdk`) |
| `--file` | `--repo` 또는 `--file` 중 하나 필수 | 로컬 파일 경로 (PDF, TXT, Markdown) |
| `--version` | 필수 | 릴리즈 버전 (예: `v1.7.0`) |
| `--audience` | 필수 | `docs`, `newsletter`, `internal` 중 선택 |
| `--style` | newsletter일 때만 | `launch` 또는 `monthly` — 아래 설명 참고 |

---

## 출력 포맷

### `--audience docs`
Cochl 문서 사이트용. 짧고 읽기 쉽게. 마케팅 문구 없이 사실 위주로 작성됩니다.

**예시:**
```
April 7, 2026
Cochl.Sense v1.7.0 released

• Added adaptive audio activity filtering — 무음 구간에서 추론을 건너뛰어 엣지 디바이스 CPU 사용량 감소; `activity_sensitivity` 파라미터로 제어
• Added Automatic Gain Control (AGC) — 환경별로 다른 마이크 입력 레벨을 자동 정규화; `target_mav` 파라미터로 제어
```

---

### `--audience newsletter --style launch`
새 기능을 처음 공개할 때 사용하는 단독 공지 포맷입니다. Cochl Custom Sound 런치 뉴스레터 스타일로, 고객이 얻는 혜택 중심으로 작성됩니다.

**언제 사용**: 완전히 새로운 기능을 처음 소개할 때

**예시:**
```
Run Cochl.Sense smarter — not harder.

• Lightweight: 버퍼 없이 작동 — 최소한의 리소스로 실시간 적응
• Adaptive: 최근 10분 오디오 트렌드를 기반으로 자동 조정
• Simple to use: 모듈당 파라미터 하나 — 몇 분이면 연동 완료

How it works
01 추론 파이프라인 앞에 모듈을 추가
02 파라미터 하나로 민감도 또는 게인 목표 설정
03 나머지는 모듈이 자동으로 처리

Try it now → [GitHub]
```

---

### `--audience newsletter --style monthly`
월간 Cochl Bites 뉴스레터 안에 들어가는 업데이트 섹션입니다. 개발자 대상으로 기술적인 내용을 간결하게 정리합니다.

**언제 사용**: 정기 버전 업데이트를 월간 뉴스레터에 포함할 때

**예시:**
```
Cochl.Sense Audio Preprocessing Module — now available

스트리밍 및 엣지 배포 환경을 위한 오픈소스 Python 유틸리티 라이브러리를 출시했습니다.

Key changes:
- Adaptive Audio Activity Filtering: 저활성 구간 추론 스킵; 파라미터: `activity_sensitivity`
- Automatic Gain Control: 버퍼 없는 게인 정규화; 파라미터: `target_mav`

통합 예시 및 파라미터 상세 내용은 GitHub 레포를 참고하세요.
```

---

### `--audience internal`
엔지니어링 팀을 위한 상세 기술 변경 이력입니다. PR 번호, 버그 원인, 마이그레이션 체크리스트, "미출시 항목" 섹션이 포함됩니다.

**예시:**
```
## v1.7.0 Internal Release Notes — April 7, 2026

### Features
- **Adaptive Audio Activity Filtering**: 600 샘플(~10분) 롤링 평균 진폭 기반 버퍼리스 프리필터. `activity_sensitivity` 파라미터 노출.
- **Automatic Gain Control (AGC)**: 동일한 10분 롤링 통계 기반 게인 정규화. `target_mav` 파라미터 노출.

### Breaking Changes & Migration Checklist
- [ ] 브레이킹 체인지 없음 — 독립형 옵트인 모듈

### Pending / Not yet shipped
- Tag Hierarchy 자동 전파 — Sense API에 하드코딩된 상태, 정리 및 문서화 미완
```

---

## 저장 위치

생성된 파일은 `~/release-notes/` 폴더에 아래 규칙으로 저장됩니다.

| 소스 | 파일명 |
|---|---|
| `--repo cochlearai/sense-sdk` | `~/release-notes/sense-sdk-v1.7.0-docs.md` |
| `--repo` + newsletter | `~/release-notes/sense-sdk-v1.7.0-newsletter-launch.md` |
| `--file update.pdf` + internal | `~/release-notes/update-internal.md` |

---

## 알아두면 좋은 것들

- `--file`로 PDF를 넘기면 Claude Code가 직접 파일을 읽습니다. 별도 변환 불필요.
- `--repo` 사용 시 열린 PR(open)을 먼저 확인하고, 없으면 병합된 PR(merged)으로 자동 재시도합니다.
- 소스에 출시된 기능과 예정된 항목이 섞여 있으면, 에이전트가 미출시 항목을 별도로 분리합니다. 로드맵 항목이 릴리즈 노트에 포함되지 않습니다.
- 모든 포맷에 Cochl 톤 & 매너가 적용됩니다. "We are excited to announce" 같은 표현은 사용하지 않습니다.

---

## 베타 피드백

이 스킬은 현재 베타 단계입니다. 톤이 맞지 않거나, 내용이 빠지거나, 출력이 이상하다면 알려주세요.

- 이 레포에 Issue 등록
- 또는 담당자에게 직접 연락: **msjang@cochl.ai**

실제 사용 사례와 엣지 케이스를 기반으로 계속 개선해 나가겠습니다.
