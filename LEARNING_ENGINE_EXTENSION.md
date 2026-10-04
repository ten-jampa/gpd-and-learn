# GPD Learning Engine Extension

This document separates the base **Get Physics Done (GPD)** framework from the learning-engine layer developed in this repository.

GPD is an open-source AI physics research workflow framework. The unique extension work here is the learning layer built on top of that framework: active recall, mastery assessment, persistent concept memory, review scheduling, and MCP-backed learning state management.

## What this extension adds

### 1. Mastery-bounded active recall

The learning workflow turns physics concepts into a loop:

1. generate a calibrated challenge;
2. accept an inline or file-based attempt;
3. independently assess mastery;
4. teach only the missing gaps;
5. repeat until Level 3+ understanding.

The key design choice is the Level 2 → Level 3 boundary: correct mechanical steps are not treated as understanding unless the user can explain assumptions, meaning, and limitations.

### 2. Learning-specific agent roles

The extension adds learning roles that sit alongside the research workflow agents:

- `gpd-tutor` — generates recall, derivation, and application challenges;
- `gpd-mastery-assessor` — grades attempts on a 0–4 mastery scale;
- `gpd-explainer` integration — teaches targeted gaps and reuses cached explanations.

These roles are designed for concept mastery, not paper-writing or research execution.

### 3. `gpd-learning` MCP server

Learning state is managed by a dedicated MCP server rather than ad hoc workflow file edits.

The server exposes 12 tools across:

- session management;
- concept memory queries;
- spaced repetition review scheduling;
- prerequisite graph handling.

Implementation anchors:

- `src/gpd/core/learning.py`
- `src/gpd/mcp/servers/learning_server.py`
- `tests/mcp/test_learning_server.py`
- `infra/gpd-learning.json`

### 4. FSRS-6 + Bjork memory model

When a concept reaches mastery, the extension initializes long-term review state:

- **FSRS-6** schedules future reviews.
- **Bjork dual-strength memory** tracks storage strength and retrieval strength separately.
- Statusline integration surfaces due-review pressure so learned concepts are not silently forgotten.

### 5. Explanation caching

Full explanations are cached under:

```text
.gpd/explanations/{slug}-EXPLAIN.md
```

Both `learn` and `explain` can reuse the same cached artifact. This avoids repeated long agent spawns for explanations the user has already generated.

### 6. Runtime-facing workflow integration

The extension updates the runtime command/workflow layer so learning is available through the normal GPD command surface:

```text
/gpd:learn "Ward identity" --type derive
/gpd:learn --review
/gpd:explain "unitary time evolution"
```

Learning artifacts are stored per concept:

```text
.gpd/learning/{slug}/
├── SESSION.json
├── MEMORY.json
├── CHALLENGE.md
├── ASSESSMENT-1.md
└── EXPLANATION-1.md
```

## Verification evidence

The learning extension was validated through both automated and manual checks:

- 45 learning MCP server tests;
- metadata, CLI, and runtime parity tests;
- end-to-end manual learning loops:
  - challenge generation;
  - handwritten/photo attempt transcription;
  - independent assessment;
  - targeted explanation;
  - re-attempt;
  - mastery detection;
  - FSRS review initialization;
  - cached explanation reuse.

## Resume-safe framing

Use this phrasing externally:

> Built the learning-engine layer on top of the open-source Get Physics Done framework: a mastery-bounded active-recall workflow with independent assessment agents, a 12-tool `gpd-learning` MCP server, FSRS/Bjork concept memory, explanation caching, and test coverage for learning-state management.

Avoid this phrasing:

> Built GPD.

That overstates ownership of the base framework. The specific contribution is the learning extension layered onto GPD.
