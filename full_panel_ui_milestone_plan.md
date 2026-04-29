# Full Panel UI Project Milestone Plan

## Purpose
This repository is the long-term full application built on the reusable core package. It should eventually support:
- pluggable agents
- pluggable challenge types
- per-challenge chat threads
- shared working-answer collaboration
- challenge browsing and switching
- evaluation workflows
- config-driven layout composition
- reusable widgets/modules for future projects

This is the ambitious repo. It should be built in careful milestones so each stage is testable and useful by itself.

---

## Product Vision
A guided agent workspace where the user can select a challenge, collaborate with an agent in chat, build a shared answer artifact, and submit that artifact for evaluation. Different challenge types can define different answer schemas and evaluation strategies. The UI itself should become modular enough that future projects can reuse widgets and arrange them with configuration.

---

## Design Goals
1. Prove the backend core can support a real multi-challenge application.
2. Keep challenge state, thread state, and working answer state visibly distinct.
3. Keep the UI modular enough for reuse in future repos.
4. Support a future config-driven layout scheme.
5. Build the project in safe baby-step milestones.

---

## Long-Term Capabilities
- challenge list / browser
- challenge detail pane
- chat pane bound to active challenge
- working answer pane bound to active challenge
- submission and evaluation history
- answer revision history
- provider status / controls
- thread switching and session reset tools
- persona/agent selection
- configurable widget regions
- future support for multiple projects reusing the same widget library

---

## Proposed Repository Layout

```text
agent-academy-panel/
  pyproject.toml
  README.md
  app.py
  configs/
    app.yaml
    layout.default.yaml
    layout.simple.yaml
  src/agent_academy/
    __init__.py
    bootstrap/
      app_bootstrap.py
      provider_factory.py
      challenge_seed.py
    application/
      app_state.py
      action_handlers.py
      view_models.py
    ui/
      shell/
        template_builder.py
        layout_loader.py
        region_registry.py
      widgets/
        challenge_list.py
        challenge_detail.py
        chat_panel.py
        answer_panel.py
        submission_panel.py
        evaluation_panel.py
        answer_history_panel.py
        provider_status_panel.py
        toolbar.py
        activity_log.py
        thread_context_panel.py
      pages/
        main_page.py
    services/
      ui_app_service.py
      notification_service.py
      refresh_service.py
  tests/
    unit/
    integration/
    ui_smoke/
```

---

## Core UX Model
The UI should eventually feel like a workbench with at least these conceptual surfaces:

### Challenge Surface
- browse/select challenges
- see title/instructions/type/status

### Collaboration Surface
- chat with the agent for the active challenge
- maintain thread continuity per challenge

### Answer Surface
- shared working answer editor/viewer
- clearly distinct from chat
- updated by user and/or agent

### Evaluation Surface
- submit current answer
- see results and submission history

### System Surface
- provider status
- active persona
- session reset controls
- logs/diagnostics

---

## UI Architecture Principles
- widgets should be independent and composable
- widget data should come from service/view-model layers, not raw core internals
- keep the UI framework-specific code near the outer edge
- prepare for layout-by-config, but do not force it too early
- every milestone should produce a runnable app

---

## Suggested Major Building Blocks

### 1. AppState
A central state container for UI-level concerns such as:
- selected challenge id
- expanded/collapsed panels
- current notifications
- active provider display state
- refresh/version markers

This should not replace the core backend state. It only manages UI state.

### 2. UIAppService
A thin facade over the core backend for UI usage.

Responsibilities:
- expose challenge lists and detail view models
- expose thread and answer data for active challenge
- send user messages
- apply answer edits
- submit answers
- retrieve evaluation/submission history

### 3. Widget Library
Each widget should have a narrow purpose and accept view-model style inputs.

### 4. Layout Loader
Eventually parse config files such as YAML to assign widgets to regions.

---

## Milestones

### Milestone 1: Repo Skeleton and Full-App Bootstrap
**Goal:** create the full app repo with a minimal but correct bootstrap path.

Build:
- pyproject
- app entry point
- config loading
- provider factory hookup to core package
- seed a few challenge definitions

Tests:
- app imports
- bootstrap builds initial state

Exit Criteria:
- full app launches, even if mostly empty

---

### Milestone 2: Multi-Challenge Data Flow Without Fancy UI
**Goal:** prove the app can load multiple challenge instances from the core package.

Build:
- create 3 to 5 seeded challenge definitions
- create instances and threads
- service methods to list challenges and retrieve active challenge data

Tests:
- each challenge has separate thread and answer state
- switching active challenge changes returned view-models

Exit Criteria:
- backend-backed multi-challenge app state works before investing in widgets

---

### Milestone 3: First Real Main Screen
**Goal:** render a simple but useful multi-pane app.

Build:
- challenge list widget
- challenge detail widget
- chat panel widget
- answer panel widget
- submission/evaluation placeholder area

Tests:
- widget construction tests
- active challenge render updates

Exit Criteria:
- app visually shows the main conceptual surfaces

---

### Milestone 4: Challenge Selection and Context Switching
**Goal:** make challenge switching fully functional.

Build:
- challenge list selection behavior
- UI state for active challenge id
- refresh/update mechanism for widgets after selection

Tests:
- switching from challenge A to B updates all relevant panels
- no cross-panel stale data

Exit Criteria:
- user can hop between challenges and maintain separate context per challenge

---

### Milestone 5: Chat Flow Per Challenge
**Goal:** complete basic challenge-bound conversation behavior.

Build:
- send message flow
- transcript display
- provider call through UI service layer
- provider results reflected in transcript

Tests:
- messages land in correct thread
- switching challenge preserves prior thread transcript

Exit Criteria:
- multi-thread chat works cleanly

---

### Milestone 6: Shared Working Answer Flow
**Goal:** make the answer surface a first-class collaborative artifact.

Build:
- editable answer panel
- user save/apply edits
- provider-driven answer updates
- answer metadata display (version, updated by)

Tests:
- answer edits do not pollute chat history
- answer updates bind to correct challenge instance

Exit Criteria:
- chat and working answer are clearly separate but coordinated

---

### Milestone 7: Submission and Evaluation History
**Goal:** support meaningful challenge progression.

Build:
- submit button and action flow
- submission history widget
- evaluation result widget
- challenge status badge

Tests:
- submission snapshot remains immutable
- multiple submissions can be stored and displayed

Exit Criteria:
- user can iteratively revise and resubmit

---

### Milestone 8: Add More Challenge Types
**Goal:** prove extensibility beyond a single essay challenge.

Build:
- exact-answer challenge in UI
- essay challenge in UI
- maybe one structured response challenge
- challenge-type-specific display handling where needed

Tests:
- switching across different challenge types works
- answer panel adapts gracefully or degrades sensibly

Exit Criteria:
- app supports multiple challenge modes without architectural pain

---

### Milestone 9: Provider Controls and Diagnostics
**Goal:** make backend modularity visible and debuggable.

Build:
- provider status panel
- config-driven provider selection
- health check action
- reset session action
- activity/log panel

Tests:
- provider status updates
- reset action affects only intended session

Exit Criteria:
- user can see and debug provider integration issues

---

### Milestone 10: Answer Revision History and Restore
**Goal:** support collaborative drafting more deeply.

Build:
- answer revision list widget
- compare/preview old revision
- restore selected revision

Tests:
- revisions stored correctly
- restore creates a new current revision safely

Exit Criteria:
- working answer behaves like a real draft workspace

---

### Milestone 11: Layout Refactor for Widget Modularity
**Goal:** prepare the UI for true reuse.

Build:
- extract widgets into cleaner independent modules
- define region names and shell builder
- decouple widgets from one specific page implementation

Tests:
- widgets can be composed in alternate layouts in tests

Exit Criteria:
- widget system is stable enough to become reusable across future repos

---

### Milestone 12: Config-Driven Layout v1
**Goal:** support layout by config instead of hard-coded composition.

Build:
- YAML or JSON layout format
- layout loader
- widget registry
- region assignment logic

Example concept:
```yaml
shell:
  template: standard
  regions:
    header:
      - provider_status
      - toolbar
    sidebar:
      - challenge_list
    main:
      - challenge_detail
      - chat_panel
      - answer_panel
    footer:
      - activity_log
```

Tests:
- config loads and instantiates expected widgets
- invalid widget names fail clearly

Exit Criteria:
- app layout can be rearranged without changing Python page code

---

### Milestone 13: Milestone Hardening and Developer UX
**Goal:** make the repo pleasant to evolve.

Build:
- README and architecture docs
- example config files
- dev scripts
- lint/type-check/test commands
- screenshots or usage notes

Tests:
- CI-ready test/lint steps

Exit Criteria:
- repo is practical for ongoing iterative work with Codex

---

### Milestone 14: Optional OpenClaw Deep Integration Pass
**Goal:** harden real provider behavior in the full app.

Build:
- better error surfaces
- session mapping diagnostics
- richer structured response parsing
- config for gateway/local mode

Tests:
- mock transport tests
- optional environment-gated real integration tests

Exit Criteria:
- full app is genuinely usable with OpenClaw for day-to-day experimentation

---

## Suggested Early Challenge Set
Use a small seeded challenge pack during development:
1. `2 + 2` exact-answer challenge
2. `Fruit Bats Essay` essay challenge
3. `Summarize this paragraph` structured text challenge
4. optional later: tiny code challenge

This gives enough variety to expose architectural weaknesses early.

---

## Testing Strategy by Stage
### Stage 1-4
Focus on bootstrap, selection, and rendering stability.

### Stage 5-7
Focus on correct data routing between thread, answer, and submission.

### Stage 8-10
Focus on extensibility and revision correctness.

### Stage 11-14
Focus on modularity, layout config, and real provider integration.

---

## Deliverables Codex Should Produce
- full Panel app with modular widgets
- service/view-model layer
- seeded challenge data
- milestone-friendly commits/PR-sized chunks if possible
- tests at each milestone
- layout config support in later milestones
- docs that explain how to extend challenge types and widgets

---

## Guardrails for Codex
- do not over-engineer styling early
- do not jump to layout config before the hard-coded composition works
- keep milestone scope disciplined
- favor clarity and testability over cleverness
- keep UI wrappers thin and backend logic in the core repo
- preserve ability to run with mock provider by default

---

## Definition of Success
This repo is successful when:
- multiple challenge types work in one app
- each challenge has its own thread and working answer
- user and agent collaborate on a shared answer artifact
- providers are swappable
- widgets are modular
- future projects can reuse both the backend core and selected UI widgets
