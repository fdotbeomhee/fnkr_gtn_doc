# 스킬 (Skills)

스킬은 에이전트에게 추가 지침이나 도메인 지식을 제공하는 마크다운 파일입니다. 시작 시 자동으로 로드되고 실행할 때마다 다시 로드되므로, 엔진을 재시작하지 않고도 수정한 내용이 즉시 적용됩니다.

Griptape Nodes에는 워크플로우 구축 및 실행을 위한 기본 스킬이 내장되어 있습니다. 이와 함께 사용자 정의 스킬을 추가할 수 있습니다.

## 스킬 파일 위치

워크스페이스 디렉터리 내에 `.agents/skills/` 폴더를 생성하고, 각 스킬마다 개별 폴더를 만든 후 내부에 `SKILL.md` 파일을 배치합니다:

```
<workspace_directory>/
└── .agents/
    └── skills/
        ├── my-skill/
        │   └── SKILL.md
        └── another-skill/
            └── SKILL.md
```

기본 워크스페이스 디렉터리는 엔진을 실행한 위치 내부의 `GriptapeNodes/`입니다. 정확한 경로는 **Settings → File System → Workspace Directory**에서 확인할 수 있습니다.

## 스킬 파일 형식

각 `SKILL.md`는 YAML 프론트매터 블록이 포함된 마크다운 파일입니다:

```markdown
---
name: my-skill-name
description: What this skill does and when the agent should use it.
---

# Skill title

Instructions, reference material, or domain knowledge the agent should apply
when this skill is relevant. Write in plain English — the agent reads this as
part of its context.
```

`name`은 스킬의 폴더 이름과 일치해야 하며, `description`은 에이전트가 스킬을 언제 사용해야 하는지 알려줍니다. 본문은 코드 스니펫, 단계별 지침, 참조 표 등 필요에 따라 자유롭게 작성할 수 있습니다.

## 예제: 사내 스타일 가이드

```markdown
---
name: writing-style
description: Apply our house style when drafting or editing text.
---

# Writing Style Guide

- Use sentence case for headings, not title case.
- Prefer active voice.
- Avoid jargon; explain technical terms on first use.
- Maximum sentence length: 25 words.
```

## 핫 리로드 (Hot reload)

!!! tip "팁"

    스킬은 자동으로 감지되므로 엔진을 재시작할 필요가 없습니다. `.agents/skills/` 아래에 새 스킬 폴더를 추가하면 다음에 보내는 메시지부터 바로 적용됩니다.

## 관련 문서

- [Agent Skills 문서](https://agentskills.io/home) — 스킬 형식 및 기능에 대한 전체 참조 문서
