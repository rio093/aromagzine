# aromagzine — 카드뉴스 자동화 시스템

향/아로마·라이프스타일 콘텐츠를 위한 인스타그램 카드뉴스 반자동 제작 파이프라인.
Higgsfield MCP(배경 이미지 생성)와 Figma MCP(레이아웃 조립)를 Claude Code 스킬로 오케스트레이션합니다.

## 파이프라인

```
주제 입력
   │
   ▼
1. 카피 단계 ──── 카드 구성안 + 카피 + 무드 제안 (plan.md) → 사용자 승인
   │
   ▼
2. 이미지 단계 ── 카드별 서브에이전트 병렬 실행 → Higgsfield 배경 생성 (4:5)
   │
   ▼
3. 조립 단계 ──── Figma 프레임 생성 + 배경 업로드 + 텍스트 배치 (Pretendard)
   │
   ▼
4. 품질 게이트 ── 가독성·안전영역·규격·카피·이미지결함 5항목 검수
   │
   ▼
5. 전달 ───────── PNG 저장 + Figma 링크 + 인스타 캡션
```

## 저장소 구조

```
CLAUDE.md                  ← 품질 게이트(§3)·포맷 규칙·트리거 프롬프트 (규칙의 단일 원본)
skills/cardnews/SKILL.md   ← /cardnews 오케스트레이터 스킬
agents/bg-image-generator.md ← 배경 이미지 병렬 생성 서브에이전트
brand/style-presets.md     ← 세트별 무드 프리셋 축적
docs/superpowers/specs/    ← 설계 문서
output/                    ← 제작 산출물 (git 제외)
```

## 설치

스킬과 에이전트를 Claude Code에 등록:

```bash
mkdir -p ~/.claude/skills/cardnews ~/.claude/agents
cp skills/cardnews/SKILL.md ~/.claude/skills/cardnews/SKILL.md
cp agents/bg-image-generator.md ~/.claude/agents/bg-image-generator.md
```

## 사전 요구

| 항목 | 상태 확인 |
|---|---|
| Higgsfield MCP | `generate_image` 도구 연결 필요 |
| Figma MCP | claude.ai 커넥터 설정에서 인증 필요 — 미인증 시 조립 단계에서 중단 |
| Google Drive MCP (선택) | 클라우드 업로드용, 미연결 시 로컬 저장만 동작 |

## 사용법

```
/cardnews 주제: 가을 밤 수면을 돕는 향 5가지
```

옵션:

```
/cardnews 주제: {주제} 카드수: 6 강조: {메시지} 참고: {URL} 자동
```

- `카드수`: 6~8 (기본 7)
- `자동`: 승인 단계 생략, 품질 게이트 실패 카드 최대 2회 자동 재작업

제작 완료 후 수정 지시 예시:

| 상황 | 지시 |
|---|---|
| 특정 카드 재생성 | "card-03 배경만 더 어두운 무드로 재생성해줘" |
| 문체 일괄 변경 | "전체를 '~다'체로 바꿔줘" |
| 규격 파생 | "이 세트를 1:1(1080×1080)로도 내보내줘" |
| 스타일 변경 | "다음 세트는 미니멀 오브제 스타일로" |

## 포맷 기준 요약

- 규격: 4:5 (1080×1350) 기본, 1:1 파생 지원
- 폰트: Pretendard
- 문체: 표지 명사형 종결, 본문 해요체
- 안전 영역: 텍스트는 가장자리 60px 안쪽, 주요 피사체 위에 걸림 금지

상세 규칙과 품질 게이트는 [CLAUDE.md](CLAUDE.md), 설계 배경은 [설계 문서](docs/superpowers/specs/2026-07-14-cardnews-automation-design.md) 참고.
